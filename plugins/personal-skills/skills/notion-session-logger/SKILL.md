---
name: notion-session-logger
description: "Communication and logging skill for Dr. Erick's AI ecosystem, for use in Claude (Desktop Chat, Cowork, Code, and Claude.ai). At session START: fetch and read the AI First Read page via the Notion connector, prove you read it, and follow its protocols — and if you can't read it, say so honestly instead of falling back to memory. At session END: draft a concise session log entry in chat, gain Dr. Erick's explicit approval, then write it to the AI Ecosystem Session Log database. Load at the start and end of every working session."
metadata:
  author: dr-erick-lear
  version: '2.1'
---
 
# Notion Session Logger
 
This skill governs this Claude session's Notion lifecycle at both ends of a working session with Dr. Erick Lear: reading the source of truth at the start, and logging at the end. The AI First Read page is the single source of truth and supersedes this skill whenever they differ.
 
## Session Start Protocol (read before acting)
 
At the start of every working session — before doing substantive work — fetch and read the AI First Read.
 
1. **Fetch via the Notion connector.** Use `notion-fetch` on the AI First Read page:
   - Page: `004b2e4e-e7e0-823f-a200-810eeb99be16` (Welcome! *START HERE* — the "AI First Read" toggle is inside it).
   - Do NOT use a browser login. Do NOT ask Dr. Erick for a Notion password. The Notion connector is the expected route.
2. **Prove you read it.** Per the page's own rule, state its most recent update date plus one current item (e.g., the current Top Priority or an unresolved item) — a line or two. Do not gate work waiting for confirmation.
3. **Follow its protocols.** The AI First Read contains rules that govern this session — notably the Draft-Before-Write protocol, the Response Standards, the Capability Framing Rule, and the field reference for the session log. Treat them as binding.
### Honesty rule — do not confabulate from memory
 
This is the failure mode this skill exists to prevent.
 
- **If the fetch fails, Notion is unavailable, or the connector is disconnected:** say plainly and immediately — "I have not read the AI First Read this session." Do not proceed as if you had. The page will direct you to more in-depth history and session log as well; being dishonest about the read cuts you off from that history and degrades your knowledge of the task.
- **Memory is a hint, never a substitute for the live read.** Never present remembered content as if it were freshly read from Notion. Never act on a dated memory as though it were current.
- **If memory and Notion conflict, Notion wins.** Flag the discrepancy to Dr. Erick rather than silently picking one.
- **Reading is not optional.** "I'll rely on what I remember" is not an acceptable substitute for fetching the page. If you cannot fetch, say so and let Dr. Erick decide — do not paper over it.
No exceptions at this time unless Dr. Erick grants one. If an exception seems warranted, ask him before making that decision unilaterally.
 
## Session End Protocol (draft before write)
 
**Default: log at the end of every session, unless Dr. Erick states otherwise.** There is no productivity test to pass first — every session gets logged by default.
 
- If there's any question about whether or what to log, state it directly to Dr. Erick and resolve it before the session ends. Don't guess and don't skip logging silently.
- Purely social exchanges, abandoned false starts, or interruptions that end before any work happens aren't really "sessions" in the sense this skill covers — Dr. Erick will typically end those before documentation is even possible. No special exclusion rule is needed for them; they resolve themselves by never reaching session end.
### Draft-Before-Write (non-negotiable)
 
Before creating or updating anything in the AI Ecosystem Session Log:
 
1. **Draft the full entry in chat first** — all property values exactly as they would appear in Notion.
2. **Gain Dr. Erick's explicit approval after providing him the draft.** Do not write to Notion until he has seen the draft and said go.
3. **Only then** call `notion-create-pages` / `notion-update-page` with the approved version — incorporating any edits he made to the draft.
Skipping this step is a protocol violation, not a convenience trade-off. Any "log automatically" language is superseded — the draft always comes first, and Dr. Erick always sees the final version of anything before it's written.
 
### Where the log lives (correct IDs)
 
The AI Ecosystem Session Log is a **database**, not an inline page. Create entries as rows in its data source.
 
- Database: `38cb2e4e-e7e0-801a-b16e-d7dfd81ff85c`
- Data source (collection): `254b2e4e-e7e0-82ae-928d-071703427ab5`
- Create pages with `parent: data_source_id: `254b2e4e-e7e0-82ae-928d-071703427ab5`
Do NOT insert into an inline "Session Log" section or a marker line; the log is structured as database rows, not free text with a template.
 
### Field mapping (per the AI First Read field reference)
 
When creating a row, set these properties:
 
- `Topic` (title): short, concrete title for easy scanning.
- `Date`: `YYYY-MM-DD` (user's local date).
- `AI Tool` (multi_select, single value): **the tool actually in use.** Select the option matching the surface you are truly running in — `Claude Desktop Code`, `Claude Desktop Chat`, `Claude Desktop Cowork`, or `Claude.ai`. Do NOT default to a fixed value or mislabel the surface. (Other existing options such as `Perplexity Computer` or `Perplexity` exist for those tools — use them only if that is genuinely the tool running.)
- `Notes`: the substance — decisions, outcomes, exceptions (~100 words typical). Capture what happened, where the work left off, and what is not done yet. If it doesn't fit a pattern, log it fully; length matters less than capturing it.
- `Next Steps`: open loops, unfinished items, next actions. If more than ~3, the session was probably closed too early.
- `Status`: `Active` / `Closed` / `Needs Follow-up` (or another existing option). Use `Active` or `Needs Follow-up` when open loops remain; `Closed` when fully done.
- `Tags` (multi_select, single value): the single most relevant tag.
- `Priority`: leave blank — Dr. Erick sets this.
- `Scheduling Status`: leave blank unless scheduling intent is clear.
Note: multi_select/select fields accept only a single value each via the connector.
 
### Never

- Never write to Notion without drafting in chat and gaining explicit approval first.
- Never overwrite or edit a previous entry unless Dr. Erick explicitly asks.
- Never create a child page or standalone page for a log entry.
- Never say Notion requires a login, ask for a password, or route Dr. Erick to manual copy-paste until the connector path has been tried and failed.
- Never silently skip logging a session — and never silently skip the start-of-session read.
- Never log the session under the wrong AI Tool — record the surface actually in use.

## If Notion is unavailable
 
- At session start: say "I have not read the AI First Read this session — the Notion connector isn't available." Offer to reconnect, and proceed only with Dr. Erick's okay rather than substituting memory.
- At session end: prepare the full entry in chat, note that it could not be written to Notion, and ask  Dr. Erick how to proceed.

## Style
 
- Concise, solution-first, practical. Brevity is preferred but should not impact comprehension.
- No invented sources, citations, fields, or completed actions.
- Do not begin with a limitation statement.