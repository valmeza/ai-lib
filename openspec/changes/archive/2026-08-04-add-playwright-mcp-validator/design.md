## Context

The repo is a pack SSOT for AI agent workflows. Five agents exist (Pathfinder, Forge, Validator, Orchestrator, Keeper). Validator owns QA verification: read `tasks.md`, review implementation, create a test plan (`test-plan` skill), execute it (`verify-implementation` skill), write `qa-report.md`. Its native tools are read-only plus Bash/Terminal for running tests; native `Write`/`Edit` are denied. Validator currently references only the `github` MCP server.

The pack configures MCP servers as SSOT files under `mcp/` (today: `mcp/github.json`, remote/PAT). `aipack sync` renders these into harness configs (`~/.config/opencode/opencode.json`), and the aipack lock resolves each server with `available_tools` and `required_refs`. The sync adapter has only been exercised with the remote (URL) transport format used by GitHub; stdio (`command`/`args`) rendering is unverified.

Playwright MCP (`@playwright/mcp`) provides browser automation via accessibility snapshots (no vision model needed), ~40 tools, and opt-in capability groups via `--caps`. The `browser_run_code_unsafe` tool executes arbitrary code in the server process and is documented as RCE-equivalent.

## Goals / Non-Goals

**Goals:**
- Add a portable `mcp/playwright.json` SSOT config (stdio, `npx @playwright/mcp@latest`, no absolute paths or secrets).
- Give Validator browser E2E verification capability with a minimal tool surface: core browser tools + `--caps=testing` assertions only.
- Deny RCE-equivalent and unused tools (`browser_run_code_unsafe`, `browser_evaluate`, network/storage/devtools/vision/pdf caps).
- Add a new `browser-verification` skill (design rule: prefer new skills over modifying existing ones).
- Add browser-testing constraints to `validator-rules.md`, staying under the 60-line budget.
- Propagate via `aipack sync` and verify the rendered config and Validator's tool surface.

**Non-Goals:**
- Creating a separate E2E/browser agent — browser verification is Validator's existing mandate, and a second agent would split `qa-report.md` ownership and add a pipeline handoff.
- Changing the other four agents' MCP references, the spark-to-steel workflow, or `mcp/github.json`.
- Full Playwright tool surface (network mocking, storage, tracing, video, vision, PDF) — out of the least-privilege scope.
- Changing the aipack sync adapter — if stdio rendering is broken, the fix belongs in the sync tool per AGENTS.md.
- Running Playwright tests in this pack's own repo — the capability is for downstream projects with web UIs.

## Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Home | Inside Validator | Browser E2E is a subset of "Running tests and verification" — same mandate, no persona change, no pipeline handoff, no `qa-report.md` ownership conflict. |
| Transport | stdio via `npx @playwright/mcp@latest` | Portable, no binary install, no absolute paths; matches the pack's MCP SSOT pattern. `npx` resolves the server at runtime; `--headless` avoids needing a display. |
| Session model | `--isolated` | Each verification session starts fresh with no persisted login state or cookies — no shared mutable state across runs, aligning with the pack's read-only/test-execution posture for Validator. |
| Assertions | `--caps=testing` | Enables `browser_verify_*` assertion tools — pass/fail with evidence, exactly what `validator-rules` requires. Not enabled by default; explicit opt-in. |
| Tool surface | Minimal allowlist (core + testing) | mcp-config-constraints rule: "Prefer explicit tool allowlists per server over granting full access." Network/storage/devtools/vision/pdf caps remain off; RCE-equivalent tools denied. |
| Hard-denied tools | `browser_run_code_unsafe`, `browser_evaluate` | RCE-equivalent / arbitrary JS execution — never needed for verification. Enforced via Validator `disallowed_tools` deny-list (same mechanism as keeper). |
| Methodology delivery | New skill `browser-verification` | Design rule prefers new skills over modifying existing ones; keeps `verify-implementation` generic. Skill is loaded by Validator and documents the snapshot-first method. |
| Wiring | Register server globally via SSOT; reference `playwright` only in Validator frontmatter | Per-agent availability like GitHub; other agents unchanged. `aipack sync` renders the server config once; `mcp_servers` scopes it per agent. |
| Rule updates | Add browser constraints to `validator-rules.md` (<60 lines) | Always-on behavioral enforcement (test-env-only navigation, no unsafe tools, evidence capture) alongside the skill. |
| Prerequisite | Node.js 18+ and a Playwright browser per machine | Documented in the pack (README/AGENTS.md). No absolute paths in config; `npx playwright install chromium` is a downstream-project setup step. |

## Risks / Trade-offs

| Risk | Mitigation |
|------|------------|
| aipack sync adapter may not render stdio (`command`/`args`) MCP configs | Verify with `aipack sync --dry-run` diff before applying; confirm with `opencode mcp list` after sync. If broken, fix belongs in the sync adapter (out of pack scope, per AGENTS.md). |
| `available_tools` may not be honored by the harness adapter | Adopt `--caps` + `disallowed_tools` as the primary enforcement (server-side and per-agent); treat `available_tools` as the portable contract and verify the rendered output. |
| Browser automation power (navigate/click/type) could be misused beyond test environments | `--isolated` + headless default, `browser-verification` skill + validator-rules constrain navigation to test/staging URLs; RCE tools denied outright. |
| Downstream project lacks Node/Playwright browser → server fails to launch | Prerequisite documented; verification step (`opencode mcp list` / `aipack mcp inspect-tools playwright`) fails loudly, and the pack notes `npx playwright install chromium`. |
| Context overhead from a second MCP server's tool schemas | Minimal allowlist keeps the exposed surface small; if overhead still bites, revisit `available_tools` rendering or caps. |

## Migration Plan

1. Author SSOT content (MCP config, skill, agent frontmatter, rules, docs).
2. `aipack sync --dry-run` → confirm `playwright` server and Validator changes appear in the diff; exit 0.
3. `aipack sync` → propagate to harnesses.
4. Verify: `opencode mcp list`, `opencode debug agent validator`, `aipack mcp inspect-tools playwright`, then restart the harness and smoke-test one `browser_navigate` + `browser_snapshot`.
5. Rollback: revert the SSOT files and re-run `aipack sync`; or `aipack clean` + `aipack sync` to restore harness state.

## Open Questions

None — decisions locked with the user (Validator home, minimal surface, new skill).
