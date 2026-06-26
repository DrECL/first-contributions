# Claude File Organization Audit — Execution Protocol
**Created:** 2026-06-26
**For:** Any Claude session on Vortex_POC (Chat, Cowork, or Code)
**Rule:** After this audit is complete and approved, anything found outside the approved layout is deleted regardless of content.

---

## Phase 1: Scan (read-only — no deletions)

Run all of the following and collect results into a single inventory.
These searches must cover the ENTIRE C: drive, not just the user profile.
Random spinups can create Claude files in unexpected locations.

### Step 1: Find ALL .claude directories — full C: drive
```
dir /s /b /ad "C:\.claude" 2>nul
```
This single command from C:\ root with /s will recurse the entire drive.
It will find .claude directories wherever they exist — user profile,
project folders, GitHub clones, Desktop, Downloads, temp dirs, everywhere.

Also search for any directory with "claude" in the name:
```
dir /s /b /ad "C:\*claude*" 2>nul
```

### Step 2: Find ALL CLAUDE.md and SKILL.md files — full C: drive
```
dir /s /b "C:\CLAUDE.md" 2>nul
dir /s /b "C:\SKILL.md" 2>nul
```

### Step 3: Find ALL Claude config, state, and settings files — full C: drive
```
dir /s /b "C:\*claude*.json" 2>nul
dir /s /b "C:\*claude*.log" 2>nul
dir /s /b "C:\.claude.json" 2>nul
dir /s /b "C:\settings.json" 2>nul
dir /s /b "C:\settings.local.json" 2>nul
```
Note: settings.json and settings.local.json are Claude Code config files
found inside .claude/ directories. Finding them helps locate spinups
even if the directory wasn't caught in Step 1.

### Step 4: Find ALL .skill files outside canonical location
```
dir /s /b "C:\*.skill" 2>nul
```
Canonical location is C:\Users\Eclin\.claude\skills\. Anything
outside that is either a copy, a stale package, or a spinup artifact.

### Step 5: Find ALL Anthropic-named files and directories
Not everything uses "claude" in the name. Some use "anthropic."
```
dir /s /b "C:\*anthropic*" 2>nul
dir /s /b /ad "C:\*anthropic*" 2>nul
```

### Step 6: Claude Desktop application data
These AppData locations may contain legitimate app data AND remnants.
The executing session must distinguish between the two.
```
dir /s /b /ad "C:\Users\Eclin\AppData\*claude*" 2>nul
dir /s /b /ad "C:\Users\Eclin\AppData\*Squirrel*" 2>nul
dir /s /b /ad "C:\Users\Eclin\AppData\*anthropic*" 2>nul
dir /s /b "C:\Users\Eclin\AppData\*cowork*" 2>nul
```

For each AppData location found, determine:
- Is this the active Claude Desktop installation? (KEEP — APP DATA)
- Is this an old version stack or installer remnant? (DELETE)
- Is this a log file that has grown large? (TRUNCATE)

Specifically locate and report on:
- **cowork-service.log** — Was 439 MB as of April 2026 cleanup. Where
  does it live? How large is it now? It will regrow.
- **Cowork plugin cache** — Separate from C:\Users\Eclin\.claude\skills\.
  The June 24 session confirmed skills were "saved via the Save Skill
  button into Cowork's live plugin cache." Locate this cache directory.

### Step 7: Resolve the \claude vs \.claude question
Both reported as existing on Vortex POC — documented as unresolved since 6/24.
```
dir "C:\claude" 2>nul
dir "C:\.claude" 2>nul
dir "C:\Users\Eclin\claude" 2>nul
dir "C:\Users\Eclin\.claude" 2>nul
```
For each that exists:
- List full contents (files and subdirectories, with sizes and dates)
- Check if symlink or junction: `fsutil reparsepoint query <path>` or `dir /AL`
- Determine which is actively written to (most recent file modification)
- Determine which Claude product uses which (Desktop? Code? Cowork?)

### Step 8: Check Z: drive (if connected)
```
dir /s /b "Z:\*CLAUDE.md" 2>nul
dir /s /b "Z:\*SKILL.md" 2>nul
dir /s /b /ad "Z:\*.claude" 2>nul
dir /s /b "Z:\*.skill" 2>nul
```

### Step 9: Check for GitHub clones with embedded Claude configs
GitHub repos often ship with their own .claude/ directories and CLAUDE.md
files. These are not Dr. Erick's — they belong to the cloned project.
Any found in Steps 1-2 that live inside a git repository (check for
.git directory in parent chain) should be classified as CLONE, not ORPHAN.

```
For each .claude/ or CLAUDE.md found, check:
  dir /b "<parent>\.git" 2>nul
If .git exists, this is a cloned repo's config, not a spinup.
```

---

## Phase 2: Categorize

For every item found, classify as one of:

| Category | Meaning | Default Action |
|----------|---------|----------------|
| CANONICAL | Lives in approved location, actively used | Keep |
| APP DATA | Claude Desktop/Code application files in AppData | Keep (legitimate install) |
| PROJECT | CLAUDE.md/SKILL.md in an active project directory | Keep |
| CLONE | .claude/ or CLAUDE.md inside a cloned git repository | Keep (belongs to the repo) |
| COWORK CACHE | Cowork's plugin cache (separate from .claude\skills\) | Keep but document location |
| ORPHAN | .claude dir, config, or skill file from a spinup no longer active | Delete |
| INSTALLER | SquirrelTemp, update cache, old version stack artifacts | Delete |
| LOG | cowork-service.log or similar that has grown large | Truncate |
| DEAD PROJECT | CLAUDE.md in a project directory for a dead/shelved project | Flag for Dr. Erick |
| UNKNOWN | Cannot determine origin or purpose | Flag for Dr. Erick |

---

## Phase 3: Propose (do not act yet)

Present findings as a table:

| # | Path | Size | Last Modified | Category | Recommendation |
|---|------|------|---------------|----------|----------------|
| 1 | ... | ... | ... | ... | ... |

Also report:
- **Total number of Claude-related items found**
- **Total disk space consumed** by items recommended for deletion
- **Cowork plugin cache location** (newly documented)
- **cowork-service.log location and current size**
- **Resolution of \claude vs \.claude question**

Wait for Dr. Erick's explicit approval before any deletions.

---

## Phase 4: Execute

Delete approved items. Truncate approved logs. Document what was removed
with path, size recovered, and timestamp.

---

## Phase 5: Lock the Approved Layout

After cleanup, this is the canonical structure. Anything found outside
it in future sessions should be flagged immediately.

```
APPROVED CLAUDE FILE LOCATIONS:

C:\Users\Eclin\.claude\              <- Claude context home (THE canonical location)
├── agents\                          <- Custom skills (source, 33+ files)
├── skills\                          <- Packaged .skill files
├── context\                         <- Reference files (14 files)
├── scratch\                         <- Working scratch space
└── handoff\                         <- Local backup (Notion is primary)

C:\Users\Eclin\AppData\[Local or Roaming]\
└── [Claude Desktop app directory]\  <- Legitimate application data (KEEP)
    └── cowork-service.log           <- Monitor size, truncate when large

[Cowork plugin cache]\               <- Location TBD by this audit
                                        Document here once found

C:\Users\Eclin\AI-Scratch\           <- Dev workspace
├── Skills-Dev\                      <- Skill development
├── Reports\                         <- Generated reports
└── MacroMindedBlog\                 <- Blog HTML site

C:\Users\Eclin\Desktop\ProjectDashboard\  <- React app (deprioritized)
└── CLAUDE.md                        <- Project-specific config

Z:\My Drive\.Working Folder\MacroMinded-Content-System\
└── CLAUDE.MD                        <- Grace's project config

[Any active project dir]\
└── CLAUDE.md                        <- Project-specific config (if project is active)

EVERYTHING ELSE -> should not exist. Flag or delete.
```

### What does NOT belong (examples of things to delete):
- Random `.claude/` directories created by Claude Code spinups in project folders
- CLAUDE.md or SKILL.md files in directories that are not active projects
- .skill files outside C:\Users\Eclin\.claude\skills\
- Stale claude*.json or claude*.log files outside canonical locations
- SquirrelTemp or installer remnants in AppData
- Old Claude Desktop version stacks in AppData
- Duplicate `\claude` or `\.claude` directories at drive root (once resolved)
- Any Claude artifacts in temp directories, Downloads, Documents, etc.
- settings.json or settings.local.json inside orphaned .claude/ directories

### What DOES belong but should be monitored:
- cowork-service.log — check size monthly, truncate if over 100 MB
- Cowork plugin cache — should match what's in .claude\skills\
