# validator-browser-verification Specification

## Purpose
TBD - created by archiving change add-playwright-mcp-validator. Update Purpose after archive.
## Requirements
### Requirement: Validator references the Playwright MCP server
The `validator` agent definition at `agents/validator.md` SHALL reference the `playwright` MCP server in addition to `github`, so browser automation tools are available during verification.

#### Scenario: Playwright server listed for Validator
- **WHEN** `agents/validator.md` frontmatter is inspected
- **THEN** `mcp_servers` includes both `github` and `playwright`

### Requirement: Validator loads the browser-verification skill
The `validator` agent definition SHALL preload the `browser-verification` skill so browser E2E verification follows the documented snapshot-first method with isolated-session hygiene.

#### Scenario: Skill listed for Validator
- **WHEN** `agents/validator.md` frontmatter is inspected
- **THEN** `skills` includes `browser-verification`

#### Scenario: Skill file exists
- **WHEN** the pack is inspected
- **THEN** `skills/browser-verification/SKILL.md` exists with frontmatter matching the skill name

### Requirement: Validator enforces browser-testing constraints
The pack SHALL include always-on browser-testing constraints in `rules/agent-rules/validator-rules.md` that restrict navigation to test/staging environments, require isolated sessions, mandate evidence capture, and prohibit unsafe execution tools.

#### Scenario: Browser constraints exist in rules
- **WHEN** `rules/agent-rules/validator-rules.md` is inspected
- **THEN** it contains trigger-action constraints for browser verification (test-env-only navigation, evidence capture, unsafe-tool prohibition)

### Requirement: Browser verification captures evidence
Validators SHALL record browser-verification results — snapshots, screenshots, and assertion outcomes — as evidence in `qa-report.md` alongside unit/integration test results.

#### Scenario: Evidence recorded in QA report
- **WHEN** Validator completes a browser verification session
- **THEN** `qa-report.md` includes browser-verification evidence (assertion pass/fail with screenshots or snapshots)

### Requirement: Playwright wiring is enforced after sync
The pack SHALL verify Validator's rendered tool surface and the Playwright server's availability after `aipack sync` using `opencode debug agent validator` and `opencode mcp list`.

#### Scenario: Sync propagates Playwright to Validator
- **WHEN** `aipack sync` runs
- **THEN** the OpenCode harness agent for `validator` renders with `playwright` in its MCP servers and the `disallowed_tools` deny-list

#### Scenario: Server discovered by harness
- **WHEN** the harness MCP server list is queried after sync
- **THEN** the `playwright` server appears as a configured server

