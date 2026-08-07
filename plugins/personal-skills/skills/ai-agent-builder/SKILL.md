---
name: ai-agent-builder
description: >
  Design, build, and deploy AI agents — from single-tool bots to production
  multi-agent systems. Covers ReAct, Plan-Execute, and Reflexion patterns; MCP
  server development in TypeScript and Python; RAG pipelines; subagent
  orchestration; prompt engineering; ML integration; backend persistence;
  deployment monitoring; and Perplexity Computer's 400+ service integrations.
  Trigger on any request involving agents, agentic workflows, MCP tools, RAG,
  subagent dispatch, or Perplexity Computer automation — even if the user doesn't
  use those exact terms.
---

# AI Agent Builder Super-Skill

A comprehensive reference for designing, building, and deploying AI agents.
Read the relevant reference file(s) based on your task — don't load all of them.

---

## Quick Reference — When to Use Which Section

| Situation | Reference File |
|:---|:---|
| Build an agent from scratch | `02-agent-architecture.md` — pick ReAct, Plan-Execute, or Reflexion |
| Integrate an external API as an agent tool | `03-mcp-server-development.md` |
| Agent needs to answer from private documents | `04-rag-system.md` |
| Multiple tasks that can run independently | `05-subagent-coordination.md` §5.5 Parallel Dispatch |
| Sequential tasks with quality gates | `05-subagent-coordination.md` §5.1 Subagent-Driven Dev |
| Implementing a written plan with checkpoints | `06-execution-planning.md` §6.3 Batch Execution |
| Prompt producing inconsistent output | `07-prompt-engineering.md` |
| Need to swap LLM providers or add cost controls | `08-ml-integration.md` §8.1 Provider Abstraction |
| Package a workflow as a reusable skill | `09-skill-creation.md` |
| Persistent agent state across sessions | `10-backend-infrastructure.md` §10.1 SQLite/CGI-bin |
| Agent-to-agent communication | `10-backend-infrastructure.md` §10.3 Message Bus |
| Deploy agent to production | `11-deployment-monitoring.md` |
| Perplexity Computer integrations / scheduled agents | `12-perplexity-computer.md` |
| Evaluating an MCP server | `references/appendices.md` — Appendix C |

---

## Reference Files

Load only the file(s) relevant to the current task.

| File | Contents |
|:---|:---|
| `01-gap-analysis.md` | Coverage map across all capability domains |
| `02-agent-architecture.md` | ReAct, Plan-Execute, Reflexion, Tool-Use patterns + selection guide + multi-agent topologies |
| `03-mcp-server-development.md` | Four-phase build process, TypeScript + Python templates, tool design checklist, error patterns |
| `04-rag-system.md` | Full RAG pipeline, Python implementation, vector DB selection, chunking strategy, evaluation metrics |
| `05-subagent-coordination.md` | Subagent-driven development, implementer/reviewer prompt templates, red flags |
| `06-execution-planning.md` | Parallel dispatch, focused task prompt structure, batch execution with checkpoints, verification workflow |
| `07-prompt-engineering.md` | Core patterns, chain-of-thought, structured output, optimization workflow, meta-prompting, few-shot design |
| `08-ml-integration.md` | LLM provider abstraction, model deployment, monitoring, serving strategy selection |
| `09-skill-creation.md` | SKILL.md format spec, validation pipeline, quality checklist, packaging, trigger design |
| `10-backend-infrastructure.md` | SQLite/CGI-bin memory persistence, webhook receivers, agent message bus, JS client patterns |
| `11-deployment-monitoring.md` | Deployment checklist, structured logging, health dashboard schema, scaling strategies |
| `12-perplexity-computer.md` | 400+ service integrations, integration-driven agents, scheduled monitoring, research-backed responses, live deployment |
| `appendices.md` | Quick reference table, Architecture Decision Records, MCP eval question patterns, common failure modes & fixes |

---

## Common Failure Modes (Quick Scan)

| Failure | Symptoms | Fix |
|:---|:---|:---|
| Context window overflow | Agent truncates history, loses tool results | Sliding window memory; summarize old turns |
| Tool call hallucination | Agent calls nonexistent tools | Enumerate tools explicitly in system prompt; use native tool calling API |
| Prompt injection | User input overrides agent instructions | Wrap user input in XML tags: `<user_input>{input}</user_input>` |
| Infinite ReAct loop | Agent never reaches Final Answer | Add iteration counter; "if unsure after N steps, state limitations" |
| Parallel agent conflicts | Two agents edit same file | Map files to agents before dispatch; run `check_result_conflicts()` |
| RAG hallucination | Agent answers outside retrieved context | Add "Only answer from context. Say 'I don't know' if not in context." |
| Spec creep | Implementer adds unasked-for features | Spec reviewer checks for EXTRA features; reject scope creep explicitly |
| Cost overrun | Agent exceeds budget | Set `max_tokens` per request; add total-session token budget |
| Stale memory | Agent uses outdated facts | Add TTL to fact store; validate facts against current context |
| MCP tool discovery failure | Agent can't find right tool | Use consistent naming: `service_verb_noun`; add synonyms to description |

---

## Architecture Decision Records (Quick Ref)

- **ReAct vs. Plan-Execute**: ReAct for open-ended tasks; Plan-Execute for structured workflows
- **Vector DB**: Pinecone for managed production; Chroma for local dev
- **MCP language**: TypeScript as default; Python (FastMCP) when team is Python-only
- **Subagent review order**: Spec compliance ALWAYS before code quality
- **Agent memory**: SQLite via CGI-bin for dev; PostgreSQL/Redis for production

Full ADRs with rationale → `appendices.md`
