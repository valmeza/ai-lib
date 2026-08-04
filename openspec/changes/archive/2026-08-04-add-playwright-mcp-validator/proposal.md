## Why

The pack's QA agent (Validator) can only verify code-level behavior — unit/integration tests, static inspection. Downstream projects with web UIs have no browser E2E verification capability in the pipeline, and the repo has no portable Playwright MCP server config. Playwright browser automation is a verification concern, so it belongs inside Validator's existing mandate ("Running tests and verification"), not as a new pipeline agent.

## What Changes

- Add MCP server config `mcp/playwright.json` — stdio transport running `npx @playwright/mcp@latest` with a minimal, portable flag set (`--headless`, `--isolated`, `--caps=testing`). No absolute paths or secrets.
- Restrict Playwright tool surface to a minimal allowlist: core browser tools (navigation, snapshot, interaction, screenshot) plus `--caps=testing` assertion tools (`browser_verify_*`). Deny RCE-equivalent tools `browser_run_code_unsafe` and `browser_evaluate`, and opt-out caps `network`, `storage`, `devtools`, `vision`, `pdf`.
- Wire Validator (`agents/validator.md`) to reference the `playwright` MCP server and preload a new browser-verification skill; enforce denials via `disallowed_tools`.
- Add skill `skills/browser-verification/SKILL.md` — snapshot-first browser verification method, isolated-session hygiene, evidence capture, test-environment-only navigation.
- Update always-on rule `rules/agent-rules/validator-rules.md` with browser-testing constraints (stays under 60 lines).
- Document the server, its prerequisite, and Validator's new capability in `AGENTS.md`, `METADATA.md`, and `README.md`.
- Propagate via `aipack sync` to harnesses and verify the rendered config.

## Capabilities

### New Capabilities
- `playwright-mcp-server`: Portable Playwright MCP server config with a minimal, verification-scoped tool surface — reachable, secret-free, and RCE-safe.
- `validator-browser-verification`: Validator performs browser E2E verification via the Playwright MCP server using a dedicated skill, with enforced tool restrictions and behavioral rules.

### Modified Capabilities
- `github-mcp-server`: Unchanged — server config and per-agent wiring for GitHub are untouched. This change adds a second MCP server and references it only in Validator.

## Impact

- `mcp/playwright.json` — new file (pack SSOT MCP config).
- `agents/validator.md` — frontmatter (`mcp_servers`, `skills`, `disallowed_tools`) and body workflow update.
- `skills/browser-verification/SKILL.md` — new on-demand skill.
- `rules/agent-rules/validator-rules.md` — add browser-testing constraints (<60 lines).
- `AGENTS.md`, `METADATA.md`, `README.md` — MCP server table, agent inventory, and usage notes updated.
- `aipack sync` — propagates the new MCP server config and Validator changes to harnesses (OpenCode). Verified via `aipack sync --dry-run`, `opencode mcp list`, and `opencode debug agent validator`.
- Downstream projects that use the pack need Node.js 18+ and a Playwright browser installed (`npx playwright install chromium`) for the server to launch; prerequisite documented in the pack.
- No changes to the other four agents (Pathfinder, Forge, Orchestrator, Keeper), the spark-to-steel workflow, or `mcp/github.json`.
