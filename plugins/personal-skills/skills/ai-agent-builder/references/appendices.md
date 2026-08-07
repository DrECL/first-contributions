## Appendix A: Quick Reference — When to Use Which Pattern

| Situation | Use This |
| :---- | :---- |
| Need to build an agent from scratch | §2.2 Architecture Patterns — pick ReAct, Plan-Execute, or Reflexion |
| Need to integrate an external API as an agent tool | §3 MCP Server Development |
| Need the agent to answer from private documents | §4 RAG System Construction |
| Have multiple tasks that can run independently | §5.5 Parallel Agent Dispatch (dispatching-parallel-agents) |
| Have sequential tasks with quality gates | §5.1 Subagent-Driven Development |
| Have a written plan to implement | §6.3 Batch Execution with Checkpoints (executing-plans) |
| Prompt producing inconsistent or low-quality output | §7 Prompt Engineering & Optimization |
| Need to swap LLM providers or add cost controls | §8.1 LLM Provider Abstraction Layer |
| Need to package a workflow as a reusable skill | §9.1 SKILL.md Format Specification |
| Need persistent agent state across sessions | §10.1 Agent Memory Persistence (SQLite/CGI-bin) |
| Need agents to communicate with each other | §10.3 Agent Message Bus |
| Deploying an agent to production | §11 Agent Deployment & Monitoring |
| Using Perplexity Computer for agent automation | §12 Unique Perplexity Computer Capabilities |

---

---

## Appendix B: Architecture Decision Records (ADR)

### ADR-001: ReAct vs. Plan-Execute

**Context:** Choosing architecture for a new agent. **Decision:** Use ReAct for open-ended tasks; Plan-Execute for structured workflows. **Rationale:** ReAct handles unknowns gracefully. Plan-Execute gives auditability and checkpointing needed for long-running structured tasks.

### ADR-002: Vector DB Selection for RAG

**Context:** Choosing vector database for production RAG. **Decision:** Pinecone for managed production; Chroma for local development. **Rationale:** Pinecone eliminates ops burden at production scale. Chroma has zero-setup for dev/test.

### ADR-003: MCP Language Choice

**Context:** TypeScript vs. Python for MCP servers. **Decision:** TypeScript as default; Python (FastMCP) when team is Python-only. **Rationale:** TypeScript SDK has broader client compatibility. Static typing catches tool schema errors at compile time.

### ADR-004: Subagent Review Order

**Context:** Should spec review or code quality review happen first? **Decision:** Spec compliance ALWAYS before code quality. **Rationale:** Code quality review on spec-non-compliant code wastes review cycles. Fixing spec gaps may invalidate quality feedback.

### ADR-005: Agent Memory Architecture

**Context:** How should agents persist state in Perplexity Computer? **Decision:** SQLite via CGI-bin for development; PostgreSQL/Redis for production. **Rationale:** CGI-bin SQLite requires zero infrastructure, deploys with the frontend. Swap to PostgreSQL when multi-instance or high-traffic.

---

---

## Appendix C: Evaluation Question Patterns for MCP Servers

When creating the 10 evaluation questions required by Phase 4 of MCP development:

GOOD EVALUATION QUESTIONS:

OK Multi-step: requires 3+ tool calls to answer

OK Read-only: only non-destructive operations

OK Verifiable: single correct answer, checkable by string comparison

OK Realistic: something a real user would ask

OK Stable: answer won't change over time

OK Independent: not dependent on other questions

BAD EVALUATION QUESTIONS:

NO Single-step: answerable with one tool call

NO Write operations: creates, updates, or deletes data

NO Ambiguous: multiple valid answers

NO Unstable: answer changes (e.g., "latest version")

NO Dependent: requires previous question's state

**Example good evaluation question:**

\<qa_pair>

  \<question>

    Find all issues labeled "bug" and "high-priority" in the repository.

    What is the title of the oldest open one, and which user has been assigned

    the most bugs in that same repository?

  \</question>

  \<answer>Oldest: "Memory leak in session handler" | Most assigned: alice (7 bugs)\</answer>

\</qa_pair>

---

---

## Appendix D: Common Failure Modes & Fixes

| Failure Mode | Symptoms | Fix |
| :---- | :---- | :---- |
| Context window overflow | Agent truncates history, loses tool results | Implement sliding window memory, summarize old turns |
| Tool call hallucination | Agent calls nonexistent tools | Enumerate tools explicitly in system prompt; use native tool calling API |
| Prompt injection | User input overrides agent instructions | Wrap user input in XML tags: \<user_input>{input}\</user_input> |
| Infinite ReAct loop | Agent never reaches Final Answer | Add explicit iteration counter; add "if unsure after N steps, state limitations" |
| Parallel agent conflicts | Two agents edit same file | Map files to agents before dispatch; check check_result_conflicts() |
| RAG hallucination | Agent answers outside retrieved context | Add "Only answer from context. Say 'I don't know' if not in context." |
| Spec creep | Implementer adds unasked-for features | Spec reviewer checks for EXTRA features; reject scope creep explicitly |
| Cost overrun | Agent exceeds budget | Set max_tokens per request; add total-session token budget |
| Stale memory | Agent uses outdated facts | Add TTL to fact store; validate facts against current context |
| MCP tool discovery failure | Agent can't find right tool | Use consistent naming: service_verb_noun; add synonyms to description |