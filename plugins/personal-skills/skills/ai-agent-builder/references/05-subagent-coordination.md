## 5. Subagent Coordination

### 5.1 Subagent-Driven Development

**Core principle:** Fresh subagent per task + two-stage review (spec compliance, then code quality) = high quality, fast iteration.

This pattern runs entirely within the current session — no context switch to parallel sessions.

PROCESS FLOW:

1. Read plan -> extract all tasks with full text -> create TodoList

2. FOR EACH TASK:

   a. Dispatch Implementer subagent (full task text + context injected)

      +-> Subagent asks questions? -> Answer -> Re-dispatch

      +-> Subagent implements, tests, self-reviews, signals done

   

   b. Dispatch Spec Compliance Reviewer

      +-> Reviewer finds issues? -> Implementer fixes -> Re-review

      +-> OK Spec compliant -> proceed

   

   c. Dispatch Code Quality Reviewer (ONLY after spec review passes)

      +-> Reviewer finds issues? -> Implementer fixes -> Re-review

      +-> OK Quality approved -> mark task complete

   

3. After all tasks: Dispatch Final Code Reviewer for full implementation

4. Use finishing-a-development-branch workflow

### 5.2 Implementer Subagent Prompt Template

# Implementer Subagent

## Context

You are implementing one task from a larger plan. You have been given full task text below.

Do NOT read plan files — the controller has already provided all necessary context.

## Project Context

{project_description}

Repository: {repo_path}

Branch: {branch_name}

Tech stack: {stack}

## Your Task

{full_task_text}

## Requirements

1. Ask questions BEFORE beginning if anything is unclear

2. Follow TDD: write failing test first, then implementation

3. Run all tests and verify they pass

4. Self-review: check for edge cases, naming, error handling

5. Commit with a descriptive message

## Output When Done

- Summary of what you implemented

- Test results (pass/fail counts)

- Any concerns or trade-offs you made

- Commit SHA

### 5.3 Spec Reviewer Prompt Template

# Spec Compliance Reviewer

## Your Role

You are a spec compliance reviewer — NOT a code quality reviewer.

Your ONLY job: verify the implementation matches the spec. Nothing more.

## Task Spec

{task_spec}

## Implementation to Review

Git SHAs of new commits: {commit_shas}

## Review Criteria

Check for:

1. MISSING: Requirements in the spec not implemented

2. EXTRA: Features implemented that were NOT requested (scope creep)

3. WRONG: Implementation that contradicts the spec

## Output Format

STATUS: OK COMPLIANT or NOT COMPLIANT

If non-compliant, list each issue as:

- MISSING: [description]

- EXTRA: [description]  

- WRONG: [description]

Do NOT comment on code quality, style, or performance.

### 5.4 Code Quality Reviewer Prompt Template

# Code Quality Reviewer

## Your Role

You are a code quality reviewer. The spec compliance reviewer has already confirmed

this implementation matches the spec — your job is code quality ONLY.

## Implementation to Review

Git SHAs: {commit_shas}

## Review Criteria

For each finding, classify as:

- CRITICAL: Must fix before merge (security, correctness, data loss)

- IMPORTANT: Should fix (maintainability, performance)

- SUGGESTION: Nice to have (style, naming)

## What to Check

- Error handling completeness

- Edge cases (null, empty, boundary values)

- Naming clarity

- Magic numbers/strings (extract to constants)

- DRY violations

- Security issues (injection, auth bypass, data exposure)

- Test coverage adequacy

## Output Format

STRENGTHS: [what's well done]

CRITICAL ISSUES: [list or "None"]

IMPORTANT ISSUES: [list or "None"]

VERDICT: OK APPROVED or CHANGES REQUIRED

### 5.5 Red Flags in Subagent Coordination

**Never do these:**

* Start code quality review before spec compliance passes — wrong order produces wasted cycles  
* Dispatch multiple implementer subagents in parallel on the same codebase — merge conflicts guaranteed  
* Let subagent read plan files — provide full task text in the prompt instead (eliminates file-reading overhead)  
* Accept "close enough" spec compliance — reviewer found issues means the task is not done  
* Skip the re-review after fixes — don't trust the fix without verification  
* Skip scene-setting context in subagent prompts — subagent needs to understand where the task fits

---