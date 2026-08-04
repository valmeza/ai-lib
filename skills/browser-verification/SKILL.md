---
name: browser-verification
description: Use when verifying web-UI acceptance criteria through a real browser — navigate, interact, and assert page behavior via the Playwright MCP server, then record evidence.
metadata:
  owner: shrug-labs
  last_updated: 2026-08-03
---

Browser E2E verification via the Playwright MCP server. Every interaction is
driven through accessibility snapshots — not coordinates, not screenshots.

1. **Read the target from `tasks.md`** — identify the web-UI acceptance criteria and the URL/environment to verify against. If the criteria don't specify a URL, use the project's local dev/staging URL.

2. **Navigate to the test environment** — `browser_navigate` to the URL. Never navigate to a production environment unless `tasks.md` explicitly names it.

3. **Snapshot before acting** — call `browser_snapshot` to get the accessibility tree. Use the element `ref` values from the snapshot for every interaction. Never guess selectors; the snapshot is the source of truth.

4. **Interact** — `browser_click`, `browser_type`, `browser_select_option`, `browser_check`, `browser_fill_form` using refs. After each action, take a fresh `browser_snapshot` and verify the page state changed as expected.

5. **Assert** — use the `browser_verify_*` assertion tools (`browser_verify_text_visible`, `browser_verify_element_visible`, `browser_verify_list_visible`) to check each acceptance criterion. A failed assertion is a FAIL with evidence, exactly like a failed test.

6. **Capture evidence** — for each criterion, record in `qa-report.md`:
   - the assertion outcome (pass/fail)
   - a `browser_snapshot` or `browser_take_screenshot` showing the verified state
   - what was expected vs what was observed on failure

7. **Close the session** — `browser_close` when verification is complete. Do not leave a browser running between tasks.

## Session hygiene

- The server runs with `--isolated` — every session starts fresh. Do not rely on cookies, login state, or storage persisting between sessions.
- If a scenario needs authenticated state, set it up inside the session (fill the login form, mock the auth API) rather than expecting persisted state.
- Keep one browser session per task group. Close it before moving on.

## Navigation boundaries

- Test environments only: local dev servers, staging, or sandbox URLs.
- NEVER run destructive actions on shared systems (delete, bulk-update, publish).
- NEVER enter real credentials. Use test users/fixtures or mocked routes.

## Forbidden tools

- `browser_run_code_unsafe` — RCE-equivalent. Never call it.
- `browser_evaluate` — arbitrary JS execution. Never call it.
- The `network`, `storage`, `devtools`, `vision`, and `pdf` capability groups are not enabled. If a criterion needs them, report the blocker — do not work around it.

## Output

Append browser-verification results to `qa-report.md` alongside unit/integration results, using the standard table (Task ID / Description / Status / Evidence). Browser evidence counts toward the overall PASS/FAIL verdict.
