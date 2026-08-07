## 3. MCP Server Development

### 3.1 Four-Phase MCP Build Process

Building a production MCP server follows four phases. Do not skip phases — each builds on the previous.

**Phase 1: Research & Planning**

1. Fetch MCP spec: https://modelcontextprotocol.io/sitemap.xml then pages with .md suffix  
2. Study the target API's documentation — auth requirements, rate limits, key endpoints  
3. Decide: TypeScript (recommended) or Python (FastMCP)  
4. List all tools, prioritizing comprehensive API coverage over convenience wrappers

**Phase 2: Implementation**

**Phase 3: Review & Test** — use MCP Inspector

**Phase 4: Create Evaluations** — 10 read-only, complex, verifiable questions

### 3.2 TypeScript MCP Server Template

import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";

import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

import { z } from "zod";

// --- Server Initialization ---------------------------------------------------

const server = new McpServer({

  name: "my-service-mcp",

  version: "1.0.0",

});

// --- Shared API Client --------------------------------------------------------

interface ApiConfig {

  baseUrl: string;

  apiKey: string;

}

class ServiceClient {

  constructor(private config: ApiConfig) {}

  async request<T>(

    endpoint: string,

    options: RequestInit = {}

  ): Promise<T> {

    const url = `${this.config.baseUrl}${endpoint}`;

    const response = await fetch(url, {

      ...options,

      headers: {

        "Authorization": `Bearer ${this.config.apiKey}`,

        "Content-Type": "application/json",

        ...options.headers,

      },

    });

    if (!response.ok) {

      const error = await response.text();

      throw new Error(

        `API error ${response.status}: ${error}. ` +

        `Suggestion: Check your API key or verify the resource exists.`

      );

    }

    return response.json() as Promise<T>;

  }

}

const client = new ServiceClient({

  baseUrl: process.env.SERVICE_BASE_URL ?? "https://api.example.com",

  apiKey: process.env.SERVICE_API_KEY ?? "",

});

// --- Tool: List Items ---------------------------------------------------------

server.registerTool(

  "service_list_items",

  {

    description: "List items with optional filtering and pagination.",

    inputSchema: z.object({

      page:     z.number().int().min(1).default(1).describe("Page number (1-indexed)"),

      per_page: z.number().int().min(1).max(100).default(20).describe("Items per page"),

      filter:   z.string().optional().describe("Optional keyword filter"),

    }),

    annotations: {

      readOnlyHint:  true,

      destructiveHint: false,

      idempotentHint: true,

    },

  },

  async (params) => {

    const query = new URLSearchParams({

      page:     String(params.page),

      per_page: String(params.per_page),

      ...(params.filter ? { q: params.filter } : {}),

    });

    const data = await client.request<{ items: unknown[]; total: number }>(

      `/items?${query}`

    );

    return {

      content: [{

        type: "text",

        text: JSON.stringify(data, null, 2),

      }],

      structuredContent: data,

    };

  }

);

// --- Tool: Get Item -----------------------------------------------------------

server.registerTool(

  "service_get_item",

  {

    description: "Get a single item by ID.",

    inputSchema: z.object({

      id: z.string().describe("Item ID"),

    }),

    annotations: {

      readOnlyHint: true,

      destructiveHint: false,

      idempotentHint: true,

    },

  },

  async (params) => {

    const item = await client.request(`/items/${params.id}`);

    return {

      content: [{ type: "text", text: JSON.stringify(item, null, 2) }],

      structuredContent: item,

    };

  }

);

// --- Tool: Create Item --------------------------------------------------------

server.registerTool(

  "service_create_item",

  {

    description: "Create a new item. Returns the created item with its assigned ID.",

    inputSchema: z.object({

      name:        z.string().min(1).describe("Item name"),

      description: z.string().optional().describe("Optional item description"),

      tags:        z.array(z.string()).optional().describe("Tag list"),

    }),

    annotations: {

      readOnlyHint:    false,

      destructiveHint: false,

      idempotentHint:  false,

    },

  },

  async (params) => {

    const item = await client.request("/items", {

      method: "POST",

      body: JSON.stringify(params),

    });

    return {

      content: [{ type: "text", text: JSON.stringify(item, null, 2) }],

      structuredContent: item,

    };

  }

);

// --- Transport ----------------------------------------------------------------

const transport = new StdioServerTransport();

await server.connect(transport);

console.error("MCP server running on stdio");

### 3.3 Python FastMCP Server Template

#!/usr/bin/env python3

"""FastMCP server template for Python-based MCP servers."""

import json

import os

from typing import Any

import httpx

from fastmcp import FastMCP

from pydantic import BaseModel, Field

# --- Server Initialization ---------------------------------------------------

mcp = FastMCP("my-service-mcp")

# --- API Client ---------------------------------------------------------------

BASE_URL = os.environ.get("SERVICE_BASE_URL", "https://api.example.com")

API_KEY  = os.environ.get("SERVICE_API_KEY", "")

async def api_request(endpoint: str, method: str = "GET", body: dict | None = None) -> Any:

    headers = {

        "Authorization": f"Bearer {API_KEY}",

        "Content-Type": "application/json",

    }

    async with httpx.AsyncClient() as client:

        response = await client.request(

            method,

            f"{BASE_URL}{endpoint}",

            headers=headers,

            json=body,

        )

        if not response.is_success:

            raise ValueError(

                f"API error {response.status_code}: {response.text}. "

                "Check your credentials or verify the resource exists."

            )

        return response.json()

# --- Input Models -------------------------------------------------------------

class ListParams(BaseModel):

    page:     int   = Field(default=1,  ge=1,          description="Page number (1-indexed)")

    per_page: int   = Field(default=20, ge=1,  le=100, description="Items per page")

    filter:   str | None = Field(default=None,         description="Keyword filter")

class CreateParams(BaseModel):

    name:        str        = Field(...,       description="Item name")

    description: str | None = Field(None,      description="Optional description")

    tags:        list[str]  = Field(default=[], description="Tag list")

# --- Tools --------------------------------------------------------------------

@mcp.tool(description="List items with optional filtering and pagination.")

async def service_list_items(params: ListParams) -> str:

    query = f"?page={params.page}&per_page={params.per_page}"

    if params.filter:

        query += f"&q={params.filter}"

    data = await api_request(f"/items{query}")

    return json.dumps(data, indent=2)

@mcp.tool(description="Get a single item by ID.")

async def service_get_item(id: str) -> str:

    """id: Item ID to retrieve"""

    item = await api_request(f"/items/{id}")

    return json.dumps(item, indent=2)

@mcp.tool(description="Create a new item. Returns the created item with its assigned ID.")

async def service_create_item(params: CreateParams) -> str:

    item = await api_request("/items", method="POST", body=params.model_dump())

    return json.dumps(item, indent=2)

if __name__ == "__main__":

    mcp.run()

### 3.4 MCP Tool Design Checklist

Before shipping any MCP server, verify every tool against this checklist:

* [ ] Tool name uses service_verb_noun convention (e.g., github_create_issue)  
* [ ] Description is a single sentence — concise, action-oriented  
* [ ] All input fields have description populated  
* [ ] Required vs. optional fields are correctly marked  
* [ ] Numeric fields have min/max constraints  
* [ ] Enum fields use z.enum()/Literal instead of free strings  
* [ ] Annotations set: readOnlyHint, destructiveHint, idempotentHint  
* [ ] Error messages suggest a remediation action  
* [ ] Pagination supported for list endpoints  
* [ ] structuredContent returned alongside text content  
* [ ] Build compiles without errors: npm run build or python -m py_compile  
* [ ] Tested with MCP Inspector: npx @modelcontextprotocol/inspector

### 3.5 MCP Error Message Patterns

Good error messages are diagnostic and actionable:

// Bad

throw new Error("Not found");

// Good

throw new Error(

  `Item '${id}' not found. ` +

  `Use service_list_items to find valid IDs, or verify the item exists in the service.`

);

// Bad

throw new Error("Unauthorized");

// Good

throw new Error(

  `Authentication failed. ` +

  `Check that SERVICE_API_KEY is set and has the required 'items:read' scope. ` +

  `Generate a new key at https://service.example.com/settings/api-keys`

);

---