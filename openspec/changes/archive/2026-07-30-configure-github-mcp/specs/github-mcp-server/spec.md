## ADDED Requirements

### Requirement: GitHub MCP server is reachable
The GitHub MCP server SHALL be configured as a remote endpoint at `https://api.githubcopilot.com/mcp/` and MUST respond to requests with a valid HTTP 200 status when the correct auth token is provided.

#### Scenario: Server responds to connectivity check
- **WHEN** OpenCode sends an MCP list request to the GitHub server
- **THEN** the server responds with a list of available GitHub tools

#### Scenario: Server rejects unauthenticated requests
- **WHEN** a request is sent without an `Authorization` header
- **THEN** the server responds with HTTP 401 Unauthorized

### Requirement: Authentication uses environment variable
The server SHALL authenticate via `Authorization: Bearer {env:GITHUB_PERSONAL_ACCESS_TOKEN}` header. The token value MUST NOT appear in any config file — only the `{env:...}` placeholder.

#### Scenario: Auth uses env var interpolation
- **WHEN** OpenCode loads the MCP config
- **THEN** the `GITHUB_PERSONAL_ACCESS_TOKEN` environment variable value is substituted for the `{env:GITHUB_PERSONAL_ACCESS_TOKEN}` placeholder at runtime

#### Scenario: Auth bypasses OAuth flow
- **WHEN** OpenCode connects to the server
- **THEN** `oauth: false` prevents OpenCode from attempting its auto-OAuth discovery flow

### Requirement: Config is portable across machines
The MCP config SHALL contain no absolute paths or hardcoded secrets. All machine-specific values SHALL use `{env:...}` placeholders.

#### Scenario: No absolute paths in config
- **WHEN** inspecting `mcp/github.json`
- **THEN** there are no absolute filesystem paths (e.g., `/Users/...`)

#### Scenario: No secrets in config
- **WHEN** inspecting `mcp/github.json`
- **THEN** there are no literal tokens, passwords, or credentials

### Requirement: All agents have GitHub access
All 4 agent definitions (Pathfinder, Forge, Validator, Orchestrator) reference `mcp_servers: [github]`. Once the server is configured, all agents SHALL be able to use GitHub tools without additional per-agent configuration.

#### Scenario: Agent uses GitHub tool
- **WHEN** an agent with `mcp_servers: [github]` is invoked
- **THEN** the GitHub MCP tools are available to that agent
