---
name: verify-implementation
description: Execute verification and report results.
---

When verifying an implementation:

1. **Execute the test plan** — run tests, check outputs, verify behavior.
2. **Check each acceptance criterion** from `tasks.md` — mark pass/fail with evidence.
3. **Investigate failures** — document what failed, why, and what needs to change.
4. **Write `qa-report.md`**:

```markdown
# QA Report

| Task ID | Description | Status | Evidence |
|---------|-------------|--------|----------|
| 1       | ...         | PASS   | Test X passed, output matches expected |
| 2       | ...         | FAIL   | Test Y failed: expected Z, got W |

## Failed Tasks
### Task 2 — <description>
- **Issue**: <what went wrong>
- **Root cause**: <why it happened>
- **Fix needed**: <what Forge should change>

## Summary
- Passed: N/M
- Failed: N/M
- Overall: PASS / FAIL
```
