---
name: spark-to-steel
description: Full lifecycle — requirements gathering → implementation → QA verification.
---

Run the spark-to-steel pipeline from start to finish. You will coordinate three specialized sub-agents in sequence, looping on failure.

## Steps

1. **Spawn Pathfinder** — delegate the user's request using the `pathfinder` agent type. Tell Pathfinder to investigate the codebase, clarify requirements, and write `tasks.md` with effort estimates. Wait for `tasks.md` to exist.

2. **Spawn Forge** — delegate implementation using the `forge` agent type. Pass the contents of `tasks.md` as context. Forge reads tasks, implements each one, runs lint/tests, and commits. Wait for completion.

3. **Spawn Validator** — delegate verification using the `validator` agent type. Pass the implementation context. Validator runs the test plan, checks acceptance criteria, writes `qa-report.md`. Wait for `qa-report.md`.

4. **Check result** — read `qa-report.md`:
   - If any task FAILED: pass the report back to Forge with instruction to fix the identified issues. Loop back to step 3.
   - If ALL tasks PASS: report completion to the user.

5. **Update `pipeline-state.yaml`** after each step:
   ```yaml
   phase: pathfinding | forging | validating | fixing | complete
   current_task: <task-id or iteration>
   qa_status: pending | pass | fail
   ```

## Agents

| Agent | Role | Type to use |
|-------|------|------------|
| Pathfinder | Discovery, requirements, task breakdown | `pathfinder` |
| Forge | Implementation, testing, commits | `forge` |
| Validator | Verification, test execution, QA report | `validator` |

## User interaction

- Keep the user informed: "Pathfinder is analyzing...", "Forge is implementing task 3/5...", "Validator found 2 issues..."
- If blocked at any stage (ambiguous requirements, Forge flags a blocker, etc.), report clearly and wait for guidance.
- On completion, present a summary of what was done.
