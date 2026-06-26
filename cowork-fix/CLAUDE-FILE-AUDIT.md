# Claude File Organization Audit — Execution Protocol
**Created:** 2026-06-26
**For:** Any Claude session on Vortex_POC (Chat, Cowork, or Code)
**Rule:** After this audit is complete and approved, anything found outside the approved layout is deleted regardless of content.

---

## Phase 1: Scan (read-only — no deletions)

Run all of the following and collect results into a single inventory.

### Step 1: Find ALL .claude directories on C:
```
dir /s /b /ad "C:\.claude" 2>nul
dir /s /b /ad "C:\Users\.claude" 2>nul
dir /s /b /ad "C:\Users\Eclin\*claude*" 2>nul
```

### Step 2: Find ALL CLAUDE.md files on C:
```
dir /s /b "C:\Users\Eclin\CLAUDE.md" 2>nul
dir /s /b "C:\CLAUDE.md" 2>nul
```

### Step 3: Find Claude config/state files
```
dir /s /b "C:\Users\Eclin\*claude*.json" 2>nul
dir /s /b "C:\Users\Eclin\*claude*.log" 2>nul
dir /s /b "C:\Users\Eclin\.claude.json" 2>nul
```

### Step 4: Check for Claude installer/update remnants
```
dir /s /b /ad "C:\Users\Eclin\AppData\*claude*" 2>nul
dir /s /b /ad "C:\Users\Eclin\AppData\*Squirrel*" 2>nul
dir /s /b /ad "C:\Users\Eclin\AppData\*anthropic*" 2>nul
```

### Step 5: Resolve the \claude vs \.claude question
Both exist on Vortex POC — this is documented as unresolved since 6/24.
```
dir "C:\claude" 2>nul
dir "C:\.claude" 2>nul
dir "C:\Users\Eclin\claude" 2>nul
dir "C:\Users\Eclin\.claude" 2>nul
```
For each that exists:
- List contents (files and subdirectories)
- Check last-modified dates
- Check if symlink or junction: `fsutil reparsepoint query <path>` or `dir /AL`

### Step 6: Check Z: drive (if connected)
```
dir /s /b "Z:\*CLAUDE.md" 2>nul
dir /s /b /ad "Z:\*.claude" 2>nul
```

---

## Phase 2: Categorize

For every item found, classify as one of:

| Category | Meaning | Default Action |
|----------|---------|----------------|
| CANONICAL | Lives in approved location, actively used | Keep |
| PROJECT | CLAUDE.md in a real project directory | Keep if project active; flag if project dead |
| ORPHAN | .claude dir or config from a spinup no longer active | Delete |
| INSTALLER | SquirrelTemp, update cache, old version artifacts | Delete |
| LOG | cowork-service.log or similar that has grown large | Truncate |
| UNKNOWN | Cannot determine origin or purpose | Flag for Dr. Erick |

---

## Phase 3: Propose (do not act yet)

Present findings as a table:

| # | Path | Size | Last Modified | Category | Recommendation |
|---|------|------|---------------|----------|----------------|
| 1 | ... | ... | ... | ... | ... |

Wait for Dr. Erick's explicit approval before any deletions.

---

## Phase 4: Execute

Delete approved items. Truncate approved logs. Document what was removed.

---

## Phase 5: Lock the Approved Layout

After cleanup, this is the canonical structure. Anything found outside it in future sessions should be flagged immediately.

```
APPROVED CLAUDE FILE LOCATIONS:

C:\Users\Eclin\.claude\              <- Claude context home (THE canonical location)
├── agents\                          <- Custom skills (source, 33+ files)
├── skills\                          <- Packaged .skill files
├── context\                         <- Reference files (14 files)
├── scratch\                         <- Working scratch space
└── handoff\                         <- Local backup (Notion is primary)

C:\Users\Eclin\AI-Scratch\           <- Dev workspace
├── Skills-Dev\                      <- Skill development
├── Reports\                         <- Generated reports
└── MacroMindedBlog\                 <- Blog HTML site

C:\Users\Eclin\Desktop\ProjectDashboard\  <- React app (deprioritized but not deleted)
└── CLAUDE.md                        <- Project-specific config

Z:\My Drive\.Working Folder\MacroMinded-Content-System\
└── CLAUDE.MD                        <- Grace's project config

EVERYTHING ELSE -> should not exist. Flag or delete.
```

### What does NOT belong (examples of things to delete):
- Random `.claude/` directories created by Claude Code spinups in project folders
- CLAUDE.md files in directories that are not active projects
- Stale claude*.json or claude*.log files outside `.claude\`
- SquirrelTemp or installer remnants in AppData
- Duplicate `\claude` directories at drive root (unless resolved as canonical)
- Any `.claude/` or `CLAUDE.md` in temp directories, Downloads, etc.
