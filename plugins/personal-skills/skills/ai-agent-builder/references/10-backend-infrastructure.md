## 10. Backend Infrastructure for Agents

### 10.1 Agent Memory Persistence with SQLite (CGI-bin)

Agents need persistent memory across sessions. The CGI-bin pattern lets agents store and retrieve state via HTTP endpoints without a dedicated backend server.

#!/usr/bin/env python3

# cgi-bin/agent_memory.py

# Agent memory store: conversations, tool results, learned facts

import json

import os

import sqlite3

import sys

from datetime import datetime

DB_PATH = "agent_memory.db"

def init_db(conn):

    conn.executescript("""

        CREATE TABLE IF NOT EXISTS sessions (

            session_id  TEXT PRIMARY KEY,

            created_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

            metadata    TEXT DEFAULT '{}'

        );

        

        CREATE TABLE IF NOT EXISTS messages (

            id          INTEGER PRIMARY KEY AUTOINCREMENT,

            session_id  TEXT NOT NULL,

            role        TEXT NOT NULL CHECK(role IN ('user','assistant','tool','system')),

            content     TEXT NOT NULL,

            tool_name   TEXT,

            tool_input  TEXT,

            tool_result TEXT,

            created_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

            FOREIGN KEY (session_id) REFERENCES sessions(session_id)

        );

        

        CREATE TABLE IF NOT EXISTS facts (

            id          INTEGER PRIMARY KEY AUTOINCREMENT,

            session_id  TEXT,

            key         TEXT NOT NULL,

            value       TEXT NOT NULL,

            confidence  REAL DEFAULT 1.0,

            created_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

            expires_at  TIMESTAMP,

            UNIQUE(session_id, key)

        );

        

        CREATE INDEX IF NOT EXISTS idx_messages_session ON messages(session_id);

        CREATE INDEX IF NOT EXISTS idx_facts_key ON facts(key);

    """)

    conn.commit()

conn = sqlite3.connect(DB_PATH)

conn.row_factory = sqlite3.Row

init_db(conn)

method      = os.environ.get("REQUEST_METHOD", "GET")

query       = os.environ.get("QUERY_STRING", "")

path_info   = os.environ.get("PATH_INFO", "")

def respond(data, status=200):

    print(f"Status: {status}")

    print("Content-Type: application/json")

    print()

    print(json.dumps(data))

def parse_qs(qs):

    params = {}

    for part in qs.split("&"):

        if "=" in part:

            k, v = part.split("=", 1)

            params[k] = v

    return params

# -- Routes --------------------------------------------------------------------

if path_info == "/sessions" and method == "POST":

    body = json.loads(sys.stdin.read() or "{}")

    sid  = body.get("session_id") or f"sess_{datetime.utcnow().strftime('%Y%m%d_%H%M%S_%f')}"

    conn.execute("INSERT OR IGNORE INTO sessions (session_id, metadata) VALUES (?,?)",

                 [sid, json.dumps(body.get("metadata", {}))])

    conn.commit()

    respond({"session_id": sid}, 201)

elif path_info == "/messages" and method == "POST":

    body = json.loads(sys.stdin.read())

    conn.execute(

        "INSERT INTO messages (session_id, role, content, tool_name, tool_input, tool_result) "

        "VALUES (?,?,?,?,?,?)",

        [body["session_id"], body["role"], body["content"],

         body.get("tool_name"), body.get("tool_input"), body.get("tool_result")]

    )

    conn.commit()

    msg_id = conn.execute("SELECT last_insert_rowid()").fetchone()[0]

    respond({"id": msg_id}, 201)

elif path_info == "/messages" and method == "GET":

    params = parse_qs(query)

    sid    = params.get("session_id", "")

    limit  = int(params.get("limit", 50))

    rows   = conn.execute(

        "SELECT * FROM messages WHERE session_id=? ORDER BY created_at LIMIT ?",

        [sid, limit]

    ).fetchall()

    respond([dict(r) for r in rows])

elif path_info == "/facts" and method == "PUT":

    body = json.loads(sys.stdin.read())

    conn.execute(

        "INSERT OR REPLACE INTO facts (session_id, key, value, confidence) VALUES (?,?,?,?)",

        [body.get("session_id"), body["key"], json.dumps(body["value"]),

         body.get("confidence", 1.0)]

    )

    conn.commit()

    respond({"status": "ok"})

elif path_info == "/facts" and method == "GET":

    params = parse_qs(query)

    sid    = params.get("session_id", "")

    rows   = conn.execute(

        "SELECT key, value, confidence FROM facts WHERE session_id=? OR session_id IS NULL",

        [sid]

    ).fetchall()

    respond({r["key"]: {"value": json.loads(r["value"]), "confidence": r["confidence"]}

             for r in rows})

else:

    respond({"error": f"Unknown route: {method} {path_info}"}, 400)

### 10.2 Webhook Receiver for Agent Triggers

#!/usr/bin/env python3

# cgi-bin/webhook_receiver.py

# Receives external events and queues them for agent processing

import hashlib

import hmac

import json

import os

import sqlite3

import sys

import time

DB_PATH     = "webhook_events.db"

WEBHOOK_SECRET = os.environ.get("WEBHOOK_SECRET", "")

conn = sqlite3.connect(DB_PATH)

conn.execute("""

    CREATE TABLE IF NOT EXISTS events (

        id          INTEGER PRIMARY KEY AUTOINCREMENT,

        source      TEXT NOT NULL,

        event_type  TEXT NOT NULL,

        payload     TEXT NOT NULL,

        processed   INTEGER DEFAULT 0,

        received_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP

    )

""")

conn.commit()

method = os.environ.get("REQUEST_METHOD", "GET")

def verify_signature(body: str, signature: str, secret: str) -> bool:

    """Verify HMAC-SHA256 webhook signature."""

    if not secret:

        return True  # Skip verification if no secret configured

    expected = "sha256=" + hmac.new(

        secret.encode(), body.encode(), hashlib.sha256

    ).hexdigest()

    return hmac.compare_digest(signature, expected)

if method == "POST":

    raw_body = sys.stdin.read()

    sig      = os.environ.get("HTTP_X_HUB_SIGNATURE_256", "")

    

    if not verify_signature(raw_body, sig, WEBHOOK_SECRET):

        print("Status: 401")

        print("Content-Type: application/json")

        print()

        print('{"error": "Invalid signature"}')

        sys.exit(0)

    

    body = json.loads(raw_body)

    conn.execute(

        "INSERT INTO events (source, event_type, payload) VALUES (?,?,?)",

        [body.get("source", "unknown"), body.get("type", "unknown"), raw_body]

    )

    conn.commit()

    

    print("Status: 202")

    print("Content-Type: application/json")

    print()

    print('{"status": "accepted"}')

elif method == "GET":

    # Dequeue unprocessed events for agent polling

    rows = conn.execute(

        "SELECT * FROM events WHERE processed=0 ORDER BY received_at LIMIT 50"

    ).fetchall()

    

    events = [

        {"id": r[0], "source": r[1], "event_type": r[2],

         "payload": json.loads(r[3]), "received_at": r[5]}

        for r in rows

    ]

    print("Content-Type: application/json")

    print()

    print(json.dumps(events))

### 10.3 Agent-to-Agent Communication via Message Bus

#!/usr/bin/env python3

# cgi-bin/message_bus.py

# Simple pub/sub message bus for multi-agent coordination

import json

import os

import sqlite3

import sys

import uuid

DB_PATH = "message_bus.db"

conn = sqlite3.connect(DB_PATH)

conn.executescript("""

    CREATE TABLE IF NOT EXISTS messages (

        id          TEXT PRIMARY KEY,

        from_agent  TEXT NOT NULL,

        to_agent    TEXT,           -- NULL = broadcast

        topic       TEXT NOT NULL,

        payload     TEXT NOT NULL,

        ack         INTEGER DEFAULT 0,

        created_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP

    );

    CREATE TABLE IF NOT EXISTS subscriptions (

        agent_id    TEXT NOT NULL,

        topic       TEXT NOT NULL,

        PRIMARY KEY (agent_id, topic)

    );

""")

conn.commit()

method    = os.environ.get("REQUEST_METHOD", "GET")

path_info = os.environ.get("PATH_INFO", "")

query     = os.environ.get("QUERY_STRING", "")

def respond(data, status=200):

    print(f"Status: {status}")

    print("Content-Type: application/json")

    print()

    print(json.dumps(data))

if path_info == "/publish" and method == "POST":

    body = json.loads(sys.stdin.read())

    msg_id = str(uuid.uuid4())

    conn.execute(

        "INSERT INTO messages (id, from_agent, to_agent, topic, payload) VALUES (?,?,?,?,?)",

        [msg_id, body["from_agent"], body.get("to_agent"),

         body["topic"], json.dumps(body["payload"])]

    )

    conn.commit()

    respond({"message_id": msg_id}, 201)

elif path_info == "/subscribe" and method == "POST":

    body = json.loads(sys.stdin.read())

    conn.execute(

        "INSERT OR IGNORE INTO subscriptions VALUES (?,?)",

        [body["agent_id"], body["topic"]]

    )

    conn.commit()

    respond({"status": "subscribed"})

elif path_info == "/poll" and method == "GET":

    params = dict(p.split("=") for p in query.split("&") if "=" in p)

    agent_id = params.get("agent_id", "")

    

    # Get messages for this agent (direct + subscribed topics)

    rows = conn.execute("""

        SELECT m.* FROM messages m

        LEFT JOIN subscriptions s ON s.agent_id=? AND s.topic=m.topic

        WHERE m.ack=0 AND (m.to_agent=? OR (m.to_agent IS NULL AND s.agent_id IS NOT NULL))

        ORDER BY m.created_at LIMIT 20

    """, [agent_id, agent_id]).fetchall()

    

    messages = [{"id": r[0], "from": r[1], "topic": r[3],

                 "payload": json.loads(r[4])} for r in rows]

    

    # Mark as acked

    if messages:

        ids = [m["id"] for m in messages]

        conn.execute(f"UPDATE messages SET ack=1 WHERE id IN ({','.join('?'*len(ids))})", ids)

        conn.commit()

    

    respond(messages)

### 10.4 JavaScript Client for Agent Backend

// Agent backend client — use __CGI_BIN__ placeholder in production

const CGI_BIN = "__CGI_BIN__";

class AgentMemoryClient {

  async createSession(metadata = {}) {

    const res = await fetch(`${CGI_BIN}/agent_memory.py/sessions`, {

      method: "POST",

      headers: { "Content-Type": "application/json" },

      body: JSON.stringify({ metadata }),

    });

    return res.json();

  }

  async addMessage(sessionId, role, content, toolData = {}) {

    const res = await fetch(`${CGI_BIN}/agent_memory.py/messages`, {

      method: "POST",

      headers: { "Content-Type": "application/json" },

      body: JSON.stringify({ session_id: sessionId, role, content, ...toolData }),

    });

    return res.json();

  }

  async getHistory(sessionId, limit = 50) {

    const res = await fetch(

      `${CGI_BIN}/agent_memory.py/messages?session_id=${sessionId}&limit=${limit}`

    );

    return res.json();

  }

  async setFact(sessionId, key, value, confidence = 1.0) {

    const res = await fetch(`${CGI_BIN}/agent_memory.py/facts`, {

      method: "PUT",

      headers: { "Content-Type": "application/json" },

      body: JSON.stringify({ session_id: sessionId, key, value, confidence }),

    });

    return res.json();

  }

  async getFacts(sessionId) {

    const res = await fetch(

      `${CGI_BIN}/agent_memory.py/facts?session_id=${sessionId}`

    );

    return res.json();

  }

}

class MessageBusClient {

  async publish(fromAgent, topic, payload, toAgent = null) {

    const res = await fetch(`${CGI_BIN}/message_bus.py/publish`, {

      method: "POST",

      headers: { "Content-Type": "application/json" },

      body: JSON.stringify({ from_agent: fromAgent, topic, payload, to_agent: toAgent }),

    });

    return res.json();

  }

  async subscribe(agentId, topic) {

    const res = await fetch(`${CGI_BIN}/message_bus.py/subscribe`, {

      method: "POST",

      headers: { "Content-Type": "application/json" },

      body: JSON.stringify({ agent_id: agentId, topic }),

    });

    return res.json();

  }

  async poll(agentId) {

    const res = await fetch(`${CGI_BIN}/message_bus.py/poll?agent_id=${agentId}`);

    return res.json();

  }

}

---