# Macro Memo Coordinator — Agent Spec

**Owner:** Dr. Erick Lear (erickclintonlear@gmail.com)
**Shape:** INITIATOR — drafts and drives the newsletter cycle; loops Dr. Erick in at decision points only.
**Built:** 2026-07-31 (Claude Code on the web session)
**Status:** LIVE — scheduled routine created (see Trigger below)

## Decisions (confirmed by Dr. Erick, 2026-07-31)

| Decision | Answer |
|---|---|
| Cadence | **Monthly.** Wakes the Friday before the 3rd Tuesday, 8:00 AM Phoenix. Send date = 3rd Tuesday, 7:00 AM MST (per Macro Memo Brains sheet). |
| Drafts location | Own subfolder in the Macro Memo family tree: `.Macro Memo/Macro Memo Drafts` (Google Drive) |
| Sent issues location | `.Macro Memo/Macro Memo Sent Issues` (Google Drive) |
| Auto-publish | **Never.** ("Absolutely not.") Hand-off only — Dr. Erick publishes to Substack + LinkedIn himself. |

Note on "G:": the coordinator runs in the cloud and writes to the **Google Drive cloud** `.Macro Memo` folder, which mirrors to the desktop via Drive for Desktop. The physical `G:` USB drive is a stale offline copy no cloud session can reach (per AI First Read / Risk & Safety).

## Key IDs

| Thing | ID |
|---|---|
| `.Macro Memo` folder (Drive) | `1UIz7-ANPhV-kU_TFFM-xJrpRmh_R3k3J` |
| `Macro Memo Drafts` folder | `15T9Nsbop5ZdszjhJwM09phdGmh71nZdU` |
| `Macro Memo Sent Issues` folder | `1P86GchZo8FFjoYhjJL1mb7pREWlydFN_` |
| Macro Memo Main Story v.1 (doc) | `1NQA0tJzWeHujZFy2bk-DUXc62yraqDXV6F82E7N85Sk` |
| Macro Memo Brains (sheet, newer) | `14FAhRC4rPt1O8yiy3vKiE9Fe9opn8PY_PcJRNvQBl7E` |
| Hope Based Comms Quick Guide (pdf) | `1wYoT1EIDmPWZdgSvT-DPk6LuhiFhnr5u` |
| Macro Memo Final 6.2026 (doc) | `1rqSKthkC7j4mP3eq02VqdNsOPUoQFxBl47EiADYaohk` |
| Notion "Welcome! START HERE" page | `004b2e4e-e7e0-823f-a200-810eeb99be16` |
| AI Ecosystem Session Log data source | `254b2e4e-e7e0-82ae-928d-071703427ab5` |

## Trigger configuration

- **Cron:** `0 15 * * 5` (every Friday 15:00 UTC = 8:00 AM Phoenix, UTC-7 year-round).
  The prompt's STEP 0 date guard makes only the Friday with day-of-month 11–17
  (the unique Friday before the 3rd Tuesday) do any work; other Fridays exit
  silently. This avoids relying on ambiguous cron day-of-month + day-of-week
  AND/OR semantics.
- **Mode:** fresh session per fire (`create_new_session_on_fire: true`)
- **Trigger ID:** `trig_018Kni2q8apX22DUnDuVDzeL`
- **Completion notifications:** off — the agent sends its own phone pushes at
  decision points via PushNotification.
- **First real fire:** Friday 2026-08-14 (3rd Tuesday of August = Aug 18).
- ⚠️ **OPEN ITEM — connectors:** the platform would not store connector grants
  on a trigger created from this session, so fired sessions currently wake
  WITHOUT Google Drive and Notion tools. Fix: Dr. Erick opens
  claude.ai/code/routines → "📰 Macro Memo Coordinator" → enable the
  Google Drive and Notion connectors. Until then, a real fire will push a
  "blocked: connector unavailable" notice and exit (per the prompt's
  connector-failure rule) instead of drafting.

## Cycle design

1. **Friday 8am** — date guard passes → gather sources (Notion research digests,
   Drive planning docs, prior issues, Hope-Based guide) → draft Google Doc
   "Macro Memo — Issue [NN] — Draft" with the six sections, each marked
   ✅ / ⚠️ / 🚫 → phone push #1 with ready/need summary.
2. **Edit watch** — the fired session re-wakes itself daily via `send_later`,
   checks the draft doc's modifiedTime. On Dr. Erick's edits → assemble full
   draft, Hope-Based pass → push #2 "ready for approval."
3. **Approval** — Dr. Erick types YES on the doc's APPROVAL line → agent
   produces publish-ready text → push #3 → hand-off (no posting).
4. **After send** — Dr. Erick marks SENT → agent copies final to Sent Issues,
   files the pre-approved session-log row to Notion, pushes confirmation, stops.

Approval and send signals live **in the draft doc itself** (APPROVAL / SENT
lines) because phone pushes are one-way. The session-log entry is drafted in
the doc footer so Dr. Erick's YES covers it — satisfying Draft-Before-Write.

## Known-unreachable sources (flagged, not silently skipped)

- Claude Projects content — no connector path from a scheduled session.
- Perplexity Spaces — same.
- `~/.claude/brand/hope-based-framework.md` — desktop-only file; cloud sessions
  use the Hope Based Comms Quick Guide PDF in `.Macro Memo` instead.

The agent lists anything it needed from these under "NEED FROM YOU" in the
draft rather than pretending it read them.

## Routine prompt (verbatim)

The exact prompt stored in the trigger is kept in
[`routine-prompt.md`](./routine-prompt.md). Edit it at
claude.ai/code/routines (or via `update_trigger`), then mirror the change here.
