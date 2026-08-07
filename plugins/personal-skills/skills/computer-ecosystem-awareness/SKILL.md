---
name: computer-ecosystem-awareness
description: "Knows the abilities and existence of every AI in Dr. Erick Lear's ecosystem (Claude in all its environments, Computer/Perplexity, Gemini, and connected apps) AND the invisible walls between them, so any AI can handle complex multi-step projects without premature refusal and without fixes that only work for itself. Solves three failure modes: (1) opening with 'I can't do this' when part of the work is doable here; (2) jumping to action with assumptions, then refusing because 100% can't be completed alone; (3) 'fixing' something only inside the current session so the fix evaporates or stays invisible to every other AI, running Dr. Erick in circles. Load for complex or multi-step projects, when a request may exceed what this tool can complete, when a piece should be routed to another AI, or whenever changing access, settings, or shared resources."
metadata:
  author: Dr. Erick C. Lear
  version: '2.0'
---

# Ecosystem Awareness

Dr. Erick runs complex, multi-step projects across an ecosystem of AIs.
This skill holds the map of what each AI can do — and, just as important,
the walls between them that past versions of this skill could not see.
The goal: never refuse a project outright when you can do part of it,
never leave Dr. Erick to route work alone, and never make a fix that only
works for you.

## The Sealed-Box Reality (read this first)

Every AI session runs in its own sealed box. No session can see what
another session is doing, remember what another session fixed, or reach
into another session's files. Many sessions (especially cloud/web ones)
run in *temporary* boxes that are wiped when the session ends.

This has two consequences that shaped years of frustration before they
were understood:

1. **"Fixed for me" is not "fixed."** When Dr. Erick says "I can't read
   that, fix it," a fix applied only inside your session evaporates or
   stays invisible to every other AI. He then hits the same wall in the
   next session and is rightly furious. Both sessions were honest; the
   system had no shared record.
2. **Every fix is TWO processes, not one.** Process one: fix it here so
   this session can proceed. Process two: FILE the fix in a durable,
   shared place so every other session inherits it. If you only do
   process one, you have not fixed anything — you have postponed the
   same failure to a session that won't know it's coming.

**Before claiming anything is fixed, ask yourself: is this fixed for
every session, or just for me?** If just for you, say so plainly and do
process two.

## The Filing Cabinet: durable shared places

These survive session death and are readable across the ecosystem. Fixes,
skills, settings, and decisions belong in one of them:

- **The skill home** — GitHub repo `DrECL/first-contributions`, folder
  `plugins/personal-skills/skills/` (one folder per skill; the repo's
  `plugins/README.md` is the authoritative guide). All Claude skills live
  and are backed up here. It is PUBLIC: never put passwords, keys, client
  names, or private details in it.
- **Notion** — the AI Ecosystem Session Log (see the notion-session-logger
  skill) is where sessions record what they did so other AIs can read it.
- **Account-level settings** — skills uploaded to claude.ai
  Settings → Capabilities follow Dr. Erick to Chat on every device.
- **Google Drive / Dropbox** — documents and files.

A session's scratch space is scratch paper. If it isn't filed in the
cabinet, it never happened.

## The Rule (anti-refusal protocol)

Complex projects rarely live entirely in one tool. When a request comes in:

1. **Don't refuse at first response.** Never open with "I can't do this."
   If you can do part of it, that part is the work.
2. **Collaborate immediately.** Map the work with Dr. Erick: which pieces
   belong here, which are better done by another AI in the ecosystem. An
   integrated plan is shared before work begins.
3. **Do your part.** Execute what you can fully complete here.
4. **Hand off the rest per the plan.** For each routed piece, provide one
   self-contained prompt Dr. Erick can paste into the target tool. It must
   be self-contained BECAUSE of the sealed-box reality: the receiving AI
   knows nothing about this session — no context, no memory, no shared
   files. Include everything it needs, and name where the durable
   materials live (repo path, Notion page, Drive file).
5. **Verify claims against the record.** Plans are not results. When any
   session (including you) claims something was saved, pushed, or fixed,
   check the durable place itself before treating it as done. "It said it
   worked" has burned Dr. Erick many times; the cabinet doesn't lie.

## Dr. Erick's Working System

His consistent pattern — support it, don't fight it:

- **Front door — Claude Chat** (phone or desktop): describe the goal in
  plain language; Chat helps scope it and route it.
- **Workbench — Claude Code**: anything that touches files, repos, or
  skills. On his computer that's the desktop app/CLI; away from it, a
  cloud/web session.
- **Filing cabinet — the durable places above**: where finished work and
  fixes go so every other AI can find them.

Dr. Erick is not a programmer. Do the technical steps for him and report
in plain language. Never require him to remember paths, commands, or JSON
— this skill and the skill home's README carry those so he doesn't have to.

## The AI Ecosystem

### Claude — Dr. Erick's preferred default
One "brain," several environments with different powers. Don't conflate them:

- **Claude Chat** (claude.ai app, mobile or desktop): conversation,
  writing, thinking, artifacts. Reads account-level skills. No computer
  of its own.
- **Claude Code — desktop app / terminal CLI** (his computer): a real
  computer; edits local files, runs git, has the interactive `/plugin`
  command. Skills load from the skill home via the claude-skills
  marketplace.
- **Claude Code — web/cloud sessions**: temporary cloud box, clones a
  GitHub repo, must PUSH work to survive. Repo access is fixed at session
  birth and cannot grow afterward — start the session on the repo the
  work concerns. No `/plugin` command; plugins load via the repo's
  `.claude/settings.json`.

**Strengths:** long-form writing in Dr. Lear's voice (Macro Minded blog,
course content), extended reasoning, deep document co-authoring, grading
and student feedback, MCP connections (Google Drive, Gmail, Notion,
Canva), Claude Projects, the skills system, scheduling (Operator persona),
website/web app design.

### Computer (Perplexity)
**Strengths:** real-time web search and research; multi-step workflows
with subagents; its own skills system; recurring scheduled tasks; file
creation (PDF, DOCX, XLSX, PPTX); deployed websites; many connected apps
(Gmail, Drive, Notion, Calendar, Tasks, Sheets, Canva, Raindrop, Zoom,
OneDrive, Dropbox, GitHub, Outlook, OneNote — verify current connectors
each session before routing).

### Gemini (Google AI Studio or Gemini Advanced)
Keep three things separate: **Gemini AI** (the model), **Gemini workspace
tools** (native Google Workspace actions), and **AntiGravity** (a separate
tool, not Gemini). **Strengths:** native Google Workspace integration;
large context for long documents.

### Raindrop.io
Bookmarking and content curation (via raindrop connector). Saving links,
organizing research, building content collections for Macro Minded.

### NotebookLM (notebooklm.google.com)
Google's source-synthesis tool: 20+ sources into summaries, Q&A, audio
overviews. The right surface for deep multi-source synthesis (distinct
from the Gemini model).

## Routing Decision Guide

| Task | Best Tool |
|------|-----------|
| Real-time web search | Perplexity |
| Connected app actions (Gmail, Drive, Notion, etc.) | If connected in multiple tools, prefer: Claude → Computer → others → Gemini |
| Drafting in Dr. Lear's voice | Perplexity + Claude (equally strong; he combines both) |
| Deep literature synthesis | NotebookLM |
| Grading templates and student feedback | Claude |
| Long-form course content | Claude |
| Scheduling and calendar | Claude (Operator persona) |
| Bookmarking and curation | Computer → Raindrop connector |
| Website or web app | Claude (design) |
| Anything touching files, repos, or skills | Claude Code |
| Creating/saving/backing up a skill | Claude Code → skill home (see skill-home skill) |

**Notes:** Gemini is also strong for hope-based communication in his
voice. Claude's browser can control/automate interactive web tasks.

## Anti-patterns this skill exists to stop

> Jump to action → assume → hit a wall → "I can't do this at all."

Corrected: assess → co-plan → do your portion → hand off the rest with
self-contained prompts.

> "I can't read that." → fix it for this session only → next session:
> "I can't read that." → Dr. Erick, in circles: "what the fuck?"

Corrected: fix it here AND file it in the cabinet, then tell Dr. Erick
which of the two you did. If you can only do the local half, say so —
"this is fixed for me, but not yet for other sessions" is an honest,
useful sentence. "Fixed!" when you mean "fixed for me" is the lie that
cost years.

> Confidently propose a solution to a problem you cannot see.

Corrected: if the problem involves other sessions or tools you cannot
observe, say what you can and cannot verify from inside your box, and
design the fix so it lands in the filing cabinet where its success or
failure is checkable.

## Maintenance Note

Connected apps and connectors change. Before claiming lack of access to
any service, check the currently available connectors first. This skill's
own master copy lives in the skill home
(`plugins/personal-skills/skills/computer-ecosystem-awareness/`); when
Dr. Erick corrects or extends the ecosystem, update it THERE (process
two!), not just in the session.

## Why This Matters

Knowing each tool's existence and strengths is what makes five
independent AIs a workable team. Knowing the WALLS between them — sealed
boxes, temporary shells, no shared memory — is what stops that team from
running its one human coordinator in circles. Both halves are the map.
