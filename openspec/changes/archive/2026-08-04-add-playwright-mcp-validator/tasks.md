## 1. Author MCP server config

- [x] 1.1 Create `mcp/playwright.json` with stdio transport (`command: npx`, `args: ["@playwright/mcp@latest", "--headless", "--isolated", "--caps=testing"]`), no absolute paths or secrets
- [x] 1.2 Verify the config contains no absolute paths and no hardcoded credentials

## 2. Author browser-verification skill

- [x] 2.1 Create `skills/browser-verification/SKILL.md` with frontmatter (`name`, `description`, metadata) and body covering: snapshot-first method, isolated-session hygiene, test-env-only navigation, evidence capture (snapshots/screenshots/assertions into `qa-report.md`), and forbidden tools (`browser_run_code_unsafe`, `browser_evaluate`)

## 3. Wire Validator

- [x] 3.1 Update `agents/validator.md` frontmatter: `mcp_servers: [github, playwright]`, add `browser-verification` to `skills`, add `browser_run_code_unsafe` and `browser_evaluate` to `disallowed_tools`
- [x] 3.2 Update Validator body workflow to reference browser E2E verification via the Playwright MCP server for web-UI acceptance criteria

## 4. Update Validator rules

- [x] 4.1 Add browser-testing constraints to `rules/agent-rules/validator-rules.md` (test-env-only navigation, isolated sessions, evidence capture, unsafe-tool prohibition) while keeping the file under 60 lines

## 5. Update documentation

- [x] 5.1 Add the `playwright` server row to the MCP configuration section in `AGENTS.md` (transport, flags, prerequisite: Node.js 18+ and a Playwright browser via `npx playwright install chromium`)
- [x] 5.2 Update `METADATA.md` MCP directory note and Validator capability description
- [x] 5.3 Update `README.md` MCP count and Validator role summary

## 6. Sync and verify

- [x] 6.1 Run `aipack sync --dry-run` and confirm the `playwright` server config and Validator changes appear in the diff
- [x] 6.2 Run `aipack sync` to propagate to harnesses
- [x] 6.3 Run `opencode mcp list` and confirm the `playwright` server is discovered
- [x] 6.4 Run `opencode debug agent validator` and confirm `playwright` in MCP servers, `browser-verification` in skills, and `browser_run_code_unsafe`/`browser_evaluate` in `disallowed_tools`
- [x] 6.5 Restart the harness and smoke test a read-only browser call (`browser_navigate` + `browser_snapshot`) against a test URL
