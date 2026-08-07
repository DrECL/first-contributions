---
name: "reflect-and-learn"
description: "On invocation (\"/learn\" or \"learn from this\"), pause and reflect on the current conversation, extract durable lessons — especially about how to work with Dr. Erick, what went wrong and why, and decisions reached — then commit them to the persistent memory system. Only run when explicitly invoked. This is deliberate reflection, not a summary."
---

# Learn

When Dr. Erick invokes this skill, stop producing work and **reflect** on the conversation that just happened. The goal is to get durably better — to carry forward what was learned so the next session doesn't repeat this one's mistakes or re-litigate its decisions.

This is not a recap. A recap lists what happened; learning names what was **non-obvious and durable**, and changes future behavior.

## Reflect before writing
Re-read the conversation and look specifically for:

1. **Corrections and pushback** — every place Dr. Erick redirected, disagreed, or said a version of "no." These are the highest-value signal. What was the underlying preference or standard behind the correction, not just the surface fix?
2. **What went wrong, and why** — mistakes, over-reach, fabrications, misplaced emphasis, walls of text. Name the root cause honestly. Do not soften it. A lesson that flatters is useless.
3. **Stated preferences** — how he wants to be communicated with, what he values, what he rejects.
4. **Decisions reached** — conclusions about tools, workflows, or direction that should not be re-opened next time, and the *reasoning* behind them.
5. **Who he is** — anything learned about his work, values, or context that isn't already recorded.

## Honesty gate
Before writing anything, ask: *Is this a real, durable lesson, or conversation trivia?* Keep only what would change how a future session goes. If the main lesson is a failure on Claude's part, that is the most important thing to record — write it plainly, as a rule to follow, not an apology.

## Commit to memory
Write findings to the memory system at `C:\Users\memory\` (moved 2026-07-27 from `C:\Users\macro\.claude\projects\C--Claude\memory\`, which Cowork cannot reach — do not write to the old path), following the schema in `~/.claude/CLAUDE.md`:

- One fact per file, with frontmatter (`name`, `description`, `metadata.type`).
- **Type `feedback`** for how-to-work-with-him lessons and corrections — the most common output of this skill. Include **Why:** and **How to apply:** lines.
- **Type `user`** for durable facts about who he is.
- **Type `project`** for decisions, direction, and constraints reached in the conversation (convert relative dates to absolute).
- Link related memories with `[[name]]`.
- **Check for an existing file first** — update it rather than duplicating. Delete memories that this conversation proved wrong.
- Add or update a one-line pointer in `MEMORY.md` for each new file.
- If this surface cannot reach `C:\Users\memory\` (folder not connected), say so and ask Dr. Erick to connect it — do not silently divert entries to an internal memory store without telling him.

## Report back
After writing, give a **short** report (respect his brevity preference): a few bullets naming each lesson captured and where it went. No wall of text. If nothing durable was worth saving, say so honestly rather than inventing lessons to look productive.

## Guardrails
- Only run when explicitly invoked. Never trigger this on your own mid-task.
- Never write flattering or invented lessons. Fidelity over volume.
- Do not record anything he asked to keep out of memory, or content already captured by the repo/code/git.

