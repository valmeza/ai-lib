---
name: spark-to-steel
description: Full lifecycle — requirements gathering → implementation → QA verification.
---

## Pipeline

Pathfinder writes tasks.md → Forge writes code → Validator checks results

If Validator finds issues, Forge fixes them and the loop repeats. When all pass, the pipeline completes.

## Steps

1. **Pathfinder** — gathers requirements, breaks into tasks with effort estimates, writes `tasks.md`
2. **Forge** — reads `tasks.md`, implements each task, runs lint/tests, commits code
3. **Validator** — reads `tasks.md` and implementation, runs verification, writes `qa-report.md`
4. **On failure** — Forge receives `qa-report.md`, fixes issues, loop to step 3
5. **On pass** — report completion

## Agents involved
- Pathfinder (discovery)
- Forge (implementation)
- Validator (verification)
- Orchestrator (pipeline manager)
