# playwright-mcp-server Specification

## Purpose
TBD - created by archiving change add-playwright-mcp-validator. Update Purpose after archive.
## Requirements
### Requirement: Playwright MCP server is reachable
The Playwright MCP server SHALL be configured in `mcp/playwright.json` as a stdio server launched via `npx @playwright/mcp@latest` and MUST respond to an MCP initialize request with a valid tool list when the prerequisite Node.js runtime and Playwright browser are available.

#### Scenario: Server responds to tool list request
- **WHEN** OpenCode launches the Playwright MCP server and sends an MCP list request
- **THEN** the server responds with a list of available browser automation tools

#### Scenario: Server launches headless
- **WHEN** OpenCode launches the Playwright MCP server
- **THEN** the browser runs in headless mode (no display required)

### Requirement: Config is portable across machines
The Playwright MCP config SHALL contain no absolute paths or hardcoded secrets. All machine-specific values SHALL use portable defaults or `{env:...}` placeholders.

#### Scenario: No absolute paths in config
- **WHEN** inspecting `mcp/playwright.json`
- **THEN** there are no absolute filesystem paths (e.g., `/Users/...`)

#### Scenario: No secrets in config
- **WHEN** inspecting `mcp/playwright.json`
- **THEN** there are no literal tokens, passwords, or credentials

### Requirement: Sessions are isolated
The Playwright MCP server SHALL run with the `--isolated` flag so that each verification session starts with a fresh browser profile and SHALL NOT persist login state, cookies, or storage across sessions.

#### Scenario: Fresh session per run
- **WHEN** Validator starts a browser verification session
- **THEN** no persisted profile, cookies, or storage from previous sessions is loaded

### Requirement: Tool surface is minimal
The Playwright MCP server SHALL expose only core browser tools and `--caps=testing` assertion tools. The `network`, `storage`, `devtools`, `vision`, and `pdf` capability groups SHALL NOT be enabled.

#### Scenario: Testing assertions enabled
- **WHEN** the server is launched
- **THEN** `--caps=testing` enables the `browser_verify_*` assertion tools

#### Scenario: High-risk caps disabled
- **WHEN** the server is launched
- **THEN** `network`, `storage`, `devtools`, `vision`, and `pdf` capability groups are not enabled

### Requirement: RCE-equivalent tools are denied
The Playwright MCP server's `browser_run_code_unsafe` tool SHALL be denied for Validator, and `browser_evaluate` SHALL be denied, because they execute arbitrary code and SHALL NOT be available during verification.

#### Scenario: Unsafe execution tools denied
- **WHEN** Validator's rendered tool surface is inspected
- **THEN** `browser_run_code_unsafe` and `browser_evaluate` are denied via `disallowed_tools`

