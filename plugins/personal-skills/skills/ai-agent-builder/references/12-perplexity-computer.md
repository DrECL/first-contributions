## 12. Unique Perplexity Computer Capabilities

### 12.1 400+ Service Integrations

Perplexity Computer exposes connections to 400+ external services via the list_external_tools / call_external_tool pattern. This makes it uniquely suited for building **integration-heavy agents** without writing custom connectors.

**Discovery pattern:**

1. list_external_tools(queries=["github", "repo"])

   -> Returns connected tools with source_id and status

2. describe_external_tools(source_id="github", tool_names=["create_issue"])

   -> Returns full JSON schema for tool inputs

3. call_external_tool(tool_name="create_issue", source_id="github", arguments={...})

   -> Executes against live service

**Key integration categories available:**

| Category | Example Services |
| :---- | :---- |
| Communication | Gmail, Slack, Teams, Discord, Outlook |
| Project management | GitHub, Jira, Linear, Asana, Notion |
| CRM/Sales | Salesforce, HubSpot, Pipedrive |
| Data/Analytics | Google Sheets, Airtable, BigQuery |
| Storage | Google Drive, Dropbox, S3 |
| Calendar | Google Calendar, Outlook Calendar |
| Social | Twitter/X, LinkedIn |
| Payments | Stripe |
| Databases | PostgreSQL, MySQL, MongoDB |

### 12.2 Building Integration-Driven Agents

An agent in Perplexity Computer that uses external integrations:

# Pattern: Research -> Enrich -> Store -> Notify

# Example: Competitor monitoring agent

COMPETITOR_AGENT_FLOW = """

1. search_web(queries=["[competitor] product update", "[competitor] pricing 2026"])

   -> Gather news about target competitor

2. search_social(query="from:[competitor_twitter] -is:retweet", only_recent=True)

   -> Collect recent social posts

3. call_external_tool(source_id="notion_mcp", tool_name="create_page", arguments={

       "title": f"Competitor Intel: {date}",

       "content": compiled_findings

   })

   -> Store findings in Notion

4. call_external_tool(source_id="gmail", tool_name="send_email", arguments={

       "to": "team@company.com",

       "subject": f"Weekly Competitor Brief: {date}",

       "body": executive_summary

   })

   -> Notify stakeholders

"""

### 12.3 Scheduled Monitoring Agents

Perplexity Computer supports recurring tasks that trigger agents on a schedule. Use this for:

* Price monitoring: scrape competitor pricing daily, alert on changes  
* Brand mention tracking: search social + news, weekly digest  
* Performance monitoring: check site metrics, alert on regressions  
* Data drift detection: compare model input distributions, trigger retraining

**Scheduled agent pattern:**

MONITORING_AGENT_PROMPT = """

You are a scheduled monitoring agent running at: {timestamp}

Your monitoring target: {target_description}

## Steps

1. Collect current data from: {data_sources}

2. Compare to baseline stored at: {baseline_reference}

3. Calculate delta metrics

4. If any metric exceeds threshold: {alert_thresholds}

   -> Call alert tool with details

5. Update baseline with today's snapshot

6. Output summary report

## Output

Provide a structured report:

- Status: NORMAL | WARNING | CRITICAL

- Changes detected: [list]

- Metrics compared to baseline: [table]

- Actions taken: [list]

- Next check: {next_check_time}

"""

### 12.4 Research-Backed Agent Responses

Perplexity Computer's search_web, search_vertical, and fetch_url tools give agents access to current, real-world information that LLMs alone cannot provide.

**Research agent pattern:**

RESEARCH_AGENT_STEPS = [

    # Step 1: Broad search

    "search_web(queries=[query, related_query_1, related_query_2])",

    

    # Step 2: Deep dive on key sources

    "fetch_url(url=top_result_url, prompt='Extract key facts and data')",

    

    # Step 3: Academic grounding (for technical claims)

    "search_vertical(vertical='academic', query=technical_query)",

    

    # Step 4: Visual evidence

    "search_vertical(vertical='image', query=visual_query)",

    

    # Step 5: Synthesize with citations

    "write synthesis with inline markdown citations linking to sources",

]

### 12.5 Live Deployment for Agent Interfaces

Agents built on Perplexity Computer can be deployed as live, publicly accessible web applications using the deploy_website workflow.

Agent Interface Deployment Stack:

Frontend (HTML/JS) --> deploy_website(project_path)

     |

     | fetch(__CGI_BIN__/agent_memory.py/...)

     |

Backend (CGI-bin Python) --> auto-deployed with frontend

     |

     | sqlite3 / flat files

     |

Persistent Storage --> lives in project directory

Result: A live URL with full agent interface accessible from anywhere.

No separate server provisioning needed.

**Full-stack agent UI deployment checklist:**

* [ ] index.html contains the agent chat interface  
* [ ] cgi-bin/agent_memory.py handles session/message persistence  
* [ ] cgi-bin/agent_api.py proxies LLM calls (keeps API key server-side)  
* [ ] All CGI scripts are marked executable: chmod +x cgi-bin/*.py  
* [ ] Client JavaScript uses __CGI_BIN__ as base URL (replaced at deploy time)  
* [ ] CORS is handled by the CGI proxy layer (no browser CORS issues)  
* [ ] Deployed with deploy_website tool

---