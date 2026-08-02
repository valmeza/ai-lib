## Why

The GitHub MCP server is referenced by all 4 agents (Pathfinder, Forge, Validator, Orchestrator) but does not work. The pack's `mcp/github.json` points to a local binary that doesn't exist, and no authentication is configured. Agents cannot interact with GitHub — no repository access, no PR creation, no code search. This blocks the entire spark-to-steel pipeline from completing GitHub-dependent tasks.

## What Changes

- Rewrite `mcp/github.json` from local/stdio transport to remote server using `https://api.githubcopilot.com/mcp/`
- Add `GITHUB_PERSONAL_ACCESS_TOKEN` environment variable placeholder for auth (user provides their own PAT)
- Update `~/.config/opencode/opencode.json` to use the remote server config with env-var interpolation
- The `aipack sync` pipeline will propagate the SSOT config to all configured harnesses (OpenCode, Codex)
- Document the required env var in the pack README or AGENTS.md

## Capabilities

### New Capabilities
- `github-mcp-server`: Configure the GitHub MCP server as a remote endpoint with token-based authentication, enabling all agents to interact with GitHub APIs (repositories, issues, PRs, search)

### Modified Capabilities

None — no existing specs in `openspec/specs/`.

## Impact

- `mcp/github.json` — format changes from local stdio to remote
- `~/.config/opencode/opencode.json` — needs manual update (user-level config, not in pack)
- Users must create a GitHub PAT and set `GITHUB_PERSONAL_ACCESS_TOKEN` in their shell environment
- All 4 agents automatically gain working GitHub MCP access once the server is configured
- `aipack sync` will need to handle the new remote-server format across harness adapters
