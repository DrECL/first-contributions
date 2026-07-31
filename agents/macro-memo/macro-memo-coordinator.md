# Macro Memo Coordinator — Agent Spec

**Owner:** Dr. Erick Lear (erickclintonlear@gmail.com)
**Shape:** Fully human-in-the-loop collaborator — Dr. Erick authors direction and voice at every stage; the agent keeps the monthly rhythm and does the labor between his decisions. It initiates the *process* on time, never the *content*.
**Built:** 2026-07-31 (Claude Code on the web session; redesigned same day after Dr. Erick's review — v1 drafted before asking him, which failed collaboration; v2 puts his input first at every stage)
**Status:** PAUSED — trigger exists but is disabled pending Dr. Erick's read-through and go

## Decisions (Dr. Erick, 2026-07-31)

| Decision | Answer |
|---|---|
| Cycle | **Monthly, four-week arc anchored to the calendar month.** Agent works Mondays; his response target is Friday with the weekend as the built-in catch-up window before the agent returns Monday. |
| Send day | **First Tuesday of each month, 7:00 AM MST** (his call: "make it work" — first Tuesday keeps his Tuesday-morning convention; Issue 01 lands exactly on his requested Sept 1). Final version locked ~week 3; last week is tweaks-only buffer. |
| His ideas come first | A **Macro Memo Ideas** folder (with a standing **Idea Jots** doc) that he fills all month. The agent reads it before anything else each cycle. Ideas not used roll forward — nothing is silently dropped. |
| Drafts location | `.Macro Memo/Macro Memo Drafts` (Google Drive cloud; mirrors to desktop) |
| Sent issues location | `.Macro Memo/Macro Memo Sent Issues` |
| Auto-publish | **Never.** ("Absolutely not.") He publishes to Substack + LinkedIn himself. |
| Voice | Learned from **his 15-year body of work** (read-only voice corpus: `.Blogging` published pieces, Active Hope blog, Hope Theory writing projects, blog/newsletter topics doc), not just prior issues. Required reading before any drafting. |
| Brand canon | `brand-voice.md` (`1Lq6SqIACAQADqdNDxvRorAADjm80mlNsghyvxyGl8q4`) and `audience-profiles.md` (`1GTby4wAaYzn4-MwiVhRO5y7x5WPEfVf-X26C16RQ5u8`) in the Drive `.claude` folder — canonical (2026-07-19), supersede the older Brand Voice Guide / Audience Profiles docs in the Newsletter subfolder. Old Workflow Guide pdf + 2025 automation docx = superseded by this agent; not to be followed. |
| Nudging | At most one nudge per waiting stage after a full quiet week; one "cycle at risk" push after a second week; then silence. Tune with experience. |

## The monthly cycle

| When | What | Whose move |
|---|---|---|
| Week 1 Monday | Read **Ideas folder first**, then digests + planning docs → Planning page (a 10-minute decision: story options, idea triage, DIRECTION line) → push | Agent |
| Week 1 Fri–Sun | Gut check: pick direction, add his take and seed lines, mark GO | Dr. Erick |
| Week 2 Monday | First draft built only from his direction → push | Agent |
| Week 2 Fri–Sun | Red pen | Dr. Erick |
| Week 3 Monday | Edits folded in + Hope-Based pass → near-final → push | Agent |
| Week 3 Fri–Sun | YES on the APPROVAL line | Dr. Erick |
| Week 4 Monday | Publish-ready Substack + LinkedIn text locked; tweaks only | Agent |
| Monday before 1st Tuesday | Send-eve check + reminder; schedules send-morning push | Agent |
| **First Tuesday, 7am MST** | **He publishes.** Marks DONE on SENT line | Dr. Erick |
| Next Monday | Files FINAL to Sent Issues, writes the pre-approved log row to Notion, confirms, closes cycle | Agent |

State-driven, not calendar-blind: each Monday the agent reads the actual docs to see where things stand. If he moved fast it advances early; if a week slipped, the Week 4 buffer absorbs it; five-Monday months just extend the buffer. Approval (YES) and send (DONE) signals live in the Draft doc itself because phone pushes are one-way. His YES also authorizes the one Notion session-log row (drafted in the doc footer) — satisfying Draft-Before-Write.

## Key IDs

| Thing | ID |
|---|---|
| `.Macro Memo` folder (Drive) | `1UIz7-ANPhV-kU_TFFM-xJrpRmh_R3k3J` |
| `Macro Memo Drafts` folder | `15T9Nsbop5ZdszjhJwM09phdGmh71nZdU` |
| `Macro Memo Sent Issues` folder | `1P86GchZo8FFjoYhjJL1mb7pREWlydFN_` |
| `Macro Memo Ideas` folder + `Idea Jots` doc | ⚠️ pending — Drive approval prompt blocked creation; create before enabling |
| Macro Memo Main Story v.1 (doc) | `1NQA0tJzWeHujZFy2bk-DUXc62yraqDXV6F82E7N85Sk` |
| Macro Memo Brains (sheet, newer) | `14FAhRC4rPt1O8yiy3vKiE9Fe9opn8PY_PcJRNvQBl7E` |
| Hope Based Comms Quick Guide (pdf) | `1wYoT1EIDmPWZdgSvT-DPk6LuhiFhnr5u` |
| Macro Memo Final 6.2026 (doc) | `1rqSKthkC7j4mP3eq02VqdNsOPUoQFxBl47EiADYaohk` |
| Notion "Welcome! START HERE" page | `004b2e4e-e7e0-823f-a200-810eeb99be16` |
| AI Ecosystem Session Log data source | `254b2e4e-e7e0-82ae-928d-071703427ab5` |
| Trigger | `trig_018Kni2q8apX22DUnDuVDzeL` |

## Trigger configuration

- **Cron:** `0 16 * * 1` — every Monday 16:00 UTC = 9:00 AM Phoenix (UTC-7
  year-round), one hour after the Weekly Research Digest lands at 8, so the
  same-morning digest is always included.
- **Mode:** fresh session per fire; state-driven stage logic in the prompt.
- **Enabled:** NO — stays off until Dr. Erick's read-through and explicit go.
- **Completion notifications:** off — the agent sends its own pushes at
  decision points via PushNotification.
- **First cycle:** Monday 2026-08-03 if enabled by then (→ Issue 01 sends
  Tuesday 2026-09-01); otherwise the late-start rule opens the cycle on the
  first Monday it runs and compresses into the buffer.
- ⚠️ **OPEN ITEM — connectors:** the platform would not store connector grants
  on this trigger from this session, so fired sessions currently wake WITHOUT
  Google Drive and Notion tools. Fix: claude.ai/code/routines →
  "📰 Macro Memo Coordinator" → enable Google Drive and Notion. Until then a
  real fire pushes a "blocked: connector unavailable" notice and exits.

## Known-unreachable sources (flagged, not silently skipped)

- Claude Projects content — no connector path from a scheduled session.
- Perplexity Spaces — same.
- `~/.claude/brand/hope-based-framework.md` — desktop-only file; cloud sessions
  use the Hope Based Comms Quick Guide PDF in `.Macro Memo` instead.

## Routine prompt (verbatim)

The exact prompt stored in the trigger is kept in
[`routine-prompt.md`](./routine-prompt.md). Edit it at
claude.ai/code/routines (or via `update_trigger`), then mirror the change here.
