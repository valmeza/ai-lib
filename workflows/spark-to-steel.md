---
name: spark-to-steel
description: Full lifecycle — requirements gathering → implementation → QA verification.
metadata:
  owner: shrug-labs
  last_updated: 2026-07-29
---

## Pipeline

Pathfinder writes tasks.md → Forge writes code → Validator checks results

If Validator finds issues, Forge fixes them and the loop repeats. When all pass, the pipeline completes.

## Steps

1. **Invoke Orchestrator** — Use the `Task` tool with `subagent_type: "orchestrator"` to run the full pipeline. The orchestrator will coordinate Pathfinder, Forge, and Validator in sequence, tracking state in `pipeline-state.yaml`.

2. **Review results** — When the orchestrator completes, read `pipeline-state.yaml` and `qa-report.md`:
   - All tasks PASS → report completion to the user.
   - Blocked → present the blocker clearly and wait for guidance.

## Delegation

| To invoke | Use Task subagent_type |
|-----------|----------------------|
| Orchestrator | `orchestrator` |
| Pathfinder | `pathfinder` |
| Forge | `forge` |
| Validator | `validator` |

## User interaction

- Keep the user informed: "Orchestrator is running the pipeline..."
- If the orchestrator reports a blocker, present it clearly and wait for guidance.
- On completion, present a summary of what was done.
