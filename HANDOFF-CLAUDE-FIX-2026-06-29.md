# HANDOFF: Claude Code Fix & System Cleanup
**Date Created:** 2026-06-29  
**For:** Claude Code / Next Session  
**From:** Dr. Erick Clinton Lear (via Copilot)  
**Priority:** HIGH — Claude Code currently broken; behaves like Cowork

---

## SITUATION SUMMARY

**Status:** Claude Code is not functioning correctly. It currently behaves like Claude Cowork (mixing collaboration features), which was the exact problem it was supposed to fix.

**Root Cause:** System file organization corruption + broken workspace connection

**Previous Attempt:** On 2026-06-26, Claude created two branches to fix this:
- `claude/cowork-malfunction-diagnosis-amhlon` (system file audit checklist)
- `claude/reconnect-google-workspace-mcp-w5kdA` (.mcp.json configuration)

**Result:** The fixes were incomplete and made the problem worse. Claude did not loop back to report status on GitHub.

---

## KNOWN FACTS

1. **Business Google Workspace:** Confirmed-suspended (2026-06-04). Known problem. OFF-LIMITS. Do not touch.
   - Origin: Microsoft Enterprise (corrupted/compromised backend)
   - Status: Intentionally off-limits — only touch if explicitly directed

2. **Personal Google Account (ACTIVE):** `erickclintonlear@gmail.com`
   - Google One + AI add-on subscription
   - This is the account that should power Claude Code
   - Currently NOT properly connected to Google Tasks, Gmail, or Drive

3. **System State:** 
   - Multiple Claude configuration files scattered across C: drive
   - Cowork service logs growing uncontrolled
   - Claude Code / Cowork boundary is blurred (they should be separate)

4. **Dr. Erick's Requirements:**
   - Clean up system files (per the audit checklist from 6/26)
   - Reconnect Claude Code to personal Gmail workspace (`erickclintonlear@gmail.com`)
   - Verify Claude Code and Cowork operate independently

---

## YOUR TASKS

### TASK 1: DELETE THE BROKEN BRANCHES
```
Delete these branches from DrECL/first-contributions:
- claude/cowork-malfunction-diagnosis-amhlon
- claude/reconnect-google-workspace-mcp-w5kdA

Reason: Incomplete. Made problem worse. No longer needed.
```

### TASK 2: SYSTEM FILE CLEANUP (Vortex_POC - Dr. Erick's Windows machine)

The audit checklist from 2026-06-26 is still valid and needed. However, modify Phase 2 categorization:

**Items to DELETE:**
- Any `.claude/` directories created by spinups (except canonical `C:\Users\Eclin\.claude\`)
- All references to business Google Workspace account (emails, config, files)
- `CLAUDE.md` or `.skill` files from dead projects
- SquirrelTemp, old installer stacks, orphaned config files

**Items to KEEP:**
- `C:\Users\Eclin\.claude\` (canonical location)
- All files/skills related to personal Gmail workspace
- `session-start.skill` v3 (3,390 bytes) — confirmed good from 6/24

**Items to MONITOR:**
- `cowork-service.log` — truncate if over 100 MB
- Cowork plugin cache — document exact location

**Note:** The audit checklist exists at:
`https://github.com/DrECL/first-contributions/blob/claude/cowork-malfunction-diagnosis-amhlon/cowork-fix/CLAUDE-FILE-AUDIT.md`

Use it as reference. Execute the scan steps manually or via script.

### TASK 3: RECONNECT CLAUDE CODE TO PERSONAL WORKSPACE

Create a new, working `.mcp.json` configuration:

**Requirements:**
- Target account: `erickclintonlear@gmail.com` (personal Gmail, NOT business)
- Integrate: Google Tasks, Gmail, Drive
- Verify: Credentials properly authenticated
- Test: Claude Code can read/write to personal Google services

**What NOT to do:**
- Do NOT reference the suspended business workspace
- Do NOT create vague or incomplete configs
- Do NOT assume existing credentials are still valid — re-authenticate

### TASK 4: VERIFY CLAUDE CODE / COWORK SEPARATION

After reconnecting:
- Test that Claude Code operates independently
- Confirm Cowork is separate and still functional
- Ensure they do not bleed into each other's workflows

---

## SUCCESS CRITERIA

✓ Both broken branches deleted  
✓ System files cleaned (C: drive audit complete, orphans removed)  
✓ Claude Code reconnected to `erickclintonlear@gmail.com` workspace  
✓ Google Tasks, Gmail, Drive accessible from Claude Code  
✓ Claude Code behaves independently (not like Cowork)  
✓ This handoff document updated with completion status and timestamp

---

## REPORTING REQUIREMENT

**After completing these tasks, loop back and:**

1. Create a new branch: `fix/claude-code-recovery-2026-06-29`
2. Commit a status report file to that branch: `RECOVERY-REPORT.md`
3. In the report, document:
   - Branches deleted (with timestamps)
   - System files cleaned (count of items removed, disk space recovered)
   - New `.mcp.json` created (path and configuration summary)
   - Tests performed and results
   - Any issues encountered and how they were resolved
4. **Push to GitHub** so Dr. Erick can see the status

**Do NOT leave this unfinished on local disk. Close the loop via GitHub.**

---

## CONTEXT FOR NEXT SESSION

- Dr. Erick is new to GitHub (minimal coding experience)
- Business workspace is a known problem, not a target for fixes
- Personal Gmail workspace is the focus
- The June 24 work refined `session-start.skill` successfully — reference that as an example of completed work
- Cowork has separate issues; Claude Code is the current target

---

## REFERENCE FILES

- Session notes from 2026-06-24: Detailed context on workspace status, skill packaging, MDM cleanup
- Audit checklist: `cowork-fix/CLAUDE-FILE-AUDIT.md` (incomplete, in broken branch)
- Current `.mcp.json` (incomplete): `/.mcp.json` (in broken branch)

---

**Status:** Ready for next Claude Code session  
**Handed off by:** Copilot  
**Date:** 2026-06-29
