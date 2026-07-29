---
name: estimate-tasks
description: Break requirements into discrete tasks with effort estimates.
---

When estimating tasks:

1. **Review the requirements** from gather-requirements output.
2. **Break the work into tasks** — each task should be:
   - Single responsibility (one logical change)
   - Independently testable
   - Small enough to implement in one session
3. **Assign effort points**:
   - 1 = Trivial (comment, rename, config change)
   - 2 = Small (single function, simple refactor)
   - 3 = Medium (new feature in one file, moderate refactor)
   - 5 = Large (cross-file change, new module)
   - 8 = X-Large (multiple modules, significant effort — consider splitting)
4. **Identify dependencies** between tasks (e.g., task 3 depends on task 1).
5. **Write the task list** to `tasks.md` in this format:

```markdown
# Tasks: <Goal Summary>

| ID | Description | Effort | Dependencies | Acceptance Criteria |
|----|-------------|--------|--------------|-------------------|
| 1  | ...         | 3      | none         | ...                |
| 2  | ...         | 5      | 1            | ...                |

## Notes
- Any risks or assumptions
- Recommended implementation order
```
