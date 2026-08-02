## Context

All 4 agents (Pathfinder, Forge, Validator, Orchestrator) reference `mcp_servers: [github]` but the server is non-functional. The pack's `mcp/github.json` uses local stdio transport pointing to `{env:HOME}/.local/bin/github-mcp-server` which does not exist. The OpenCode harness at `~/.config/opencode/opencode.json` has a hardcoded absolute path to the same missing binary. No GitHub authentication is configured — `gh` CLI is installed but not logged in, and no `GITHUB_TOKEN` or `GITHUB_PERSONAL_ACCESS_TOKEN` env var is set.

The sync pipeline (`aipack sync`) propagates the pack's MCP config to OpenCode and Codex harnesses via harness adapters.

## Goals / Non-Goals

**Goals:**
- Switch the GitHub MCP server from local/stdio to remote endpoint (`https://api.githubcopilot.com/mcp/`)
- Use `{env:GITHUB_PERSONAL_ACCESS_TOKEN}` for portable, secret-free config
- Set `oauth: false` to prevent OpenCode from attempting OAuth fallback
- Update the pack SSOT (`mcp/github.json`) to the new format
- Update the OpenCode harness config (`~/.config/opencode/opencode.json`)
- Propagate via `aipack sync` to all configured harnesses (OpenCode, Codex)
- Document the required env var in the pack

**Non-Goals:**
- Creating or managing GitHub PATs (user responsibility)
- Configuring local/Docker-based server (remote is the recommended approach)
- Modifying agent definitions or skill logic
- Changing the aipack sync adapters themselves

## Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Server type | Remote over local | No binary to install, no Docker dependency, simpler auth via PAT. |
| Auth method | PAT via `Authorization` header | `oauth: false` prevents OpenCode's auto-OAuth flow. Env-var interpolation (`{env:GITHUB_PERSONAL_ACCESS_TOKEN}`) keeps secrets out of config files. |
| SSOT location | Pack `mcp/github.json` | Follows aipack architecture — write once, sync to all harnesses. |
| Harness config update | Direct edit of `~/.config/opencode/opencode.json` | User-level config not in pack scope. Will be updated manually as part of implementation, or via sync if the pack render covers OpenCode's MCP section. |

## Risks / Trade-offs

| Risk | Mitigation |
|------|------------|
| PAT expires or is revoked | Document that users must regenerate and update their env var. Add instruction to verify periodically via `opencode mcp debug github`. |
| aipack sync adapter doesn't support remote format | Validate after sync. If adapter doesn't render remote correctly, configure OpenCode harness directly and document the limitation. |
| All 4 agents get full GitHub tool access (context overhead) | The GitHub MCP server registers many tools. If context becomes an issue, explore toolset filtering via `X-MCP-Toolsets` header or disable globally and enable per-agent. |
