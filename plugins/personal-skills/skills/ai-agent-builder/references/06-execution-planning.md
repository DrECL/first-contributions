## 6. Execution Planning & Verification

### 6.1 Parallel Agent Dispatch Pattern

Use when 2+ independent tasks can proceed without shared state or sequential dependencies.

**Decision tree:**

Multiple independent tasks?

+-- YES: Can they write to the same files/resources?

|   +-- YES -> Sequential agents (avoid conflict)

|   +-- NO  -> Parallel dispatch OK

+-- NO: Tasks are related -> Single agent investigates all

**Parallel dispatch template:**

import asyncio

from typing import Callable, Any

async def dispatch_parallel_agents(

    tasks: list[dict],

    agent_fn: Callable[[dict], Any],

    max_concurrent: int = 5,

) -> list[dict]:

    """

    Dispatch multiple agent tasks in parallel with a concurrency limit.

    

    tasks: list of dicts, each with 'id', 'description', 'context', 'constraints'

    agent_fn: async function(task) -> result

    """

    semaphore = asyncio.Semaphore(max_concurrent)

    

    async def run_with_semaphore(task: dict) -> dict:

        async with semaphore:

            try:

                result = await agent_fn(task)

                return {"task_id": task["id"], "status": "success", "result": result}

            except Exception as e:

                return {"task_id": task["id"], "status": "failed", "error": str(e)}

    

    results = await asyncio.gather(*[run_with_semaphore(t) for t in tasks])

    return list(results)

def check_result_conflicts(results: list[dict]) -> list[str]:

    """

    Scan parallel agent results for potential conflicts before integration.

    Checks: same files modified, same database records mutated.

    """

    conflicts = []

    files_modified = {}

    

    for result in results:

        if result["status"] != "success":

            continue

        files = result.get("result", {}).get("files_modified", [])

        for f in files:

            if f in files_modified:

                conflicts.append(

                    f"Conflict: '{f}' modified by both task "

                    f"{files_modified[f]} and {result['task_id']}"

                )

            else:

                files_modified[f] = result["task_id"]

    

    return conflicts

### 6.2 Focused Agent Task Prompt Structure

Good parallel agent task prompts are self-contained, specific, and constrained:

## Task: {task_title}

### Problem Statement

{specific_error_messages_or_failure_description}

### Scope

Files/subsystems in scope: {explicit_list}

Files/subsystems OUT of scope: {explicit_exclusions}

### Goal

{single_clear_success_criterion}

### Constraints

- Do NOT modify: {protected_files}

- Do NOT add new dependencies without flagging it

- {other_constraints}

### Required Output

Return:

1. Root cause analysis

2. What you changed and why

3. Verification output (test results, logs)

4. Summary of changes as a git diff or commit SHA

### 6.3 Batch Execution with Checkpoints

EXECUTING-PLANS PROCESS:

1. LOAD & REVIEW

   - Read plan file once

   - Identify concerns or blockers -> raise with human BEFORE starting

   - Create TodoList from all tasks

   - Announce: "Using executing-plans to implement this plan."

2. EXECUTE BATCH (default: 3 tasks per batch)

   For each task in batch:

   - Mark in_progress

   - Follow steps exactly as written

   - Run all verifications specified in plan

   - Mark completed

3. CHECKPOINT REPORT

   - Show: what was implemented, verification output

   - Say: "Ready for feedback."

   - Wait for approval before next batch

4. REPEAT until all tasks complete

5. FINISH

   - Use finishing-a-development-branch workflow

   - Verify all tests pass, no regressions

STOP IMMEDIATELY if:

- A blocker appears mid-batch

- Verification fails repeatedly

- Instructions are ambiguous

-> Ask for clarification; never guess.

### 6.4 Verification Workflow

Every implementation task should define explicit verification steps:

VERIFICATION_LEVELS = {

    "smoke": [

        "Application starts without errors",

        "All previously-passing tests still pass",

        "No new lint errors",

    ],

    "functional": [

        "New unit tests written and passing",

        "Integration tests pass against staging",

        "Edge cases tested (null, empty, boundary)",

    ],

    "acceptance": [

        "Feature works end-to-end as specified",

        "Performance within targets (latency, throughput)",

        "Security review passed",

        "Documentation updated",

    ],

}

def build_verification_prompt(task: str, level: str = "functional") -> str:

    checks = "\\n".join(f"- [ ] {c}" for c in VERIFICATION_LEVELS[level])

    return f"""

After implementing: {task}

Run these verification checks:

{checks}

Report each check as OK PASS, FAIL, or SKIPPED (with reason).

If any check FAILS, stop and report before proceeding.

"""

---