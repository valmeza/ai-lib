## Why

The repo is a shared GitHub target with no restricted operator. Existing agents (e.g., Forge) carry broad write access (Edit, Bash, direct commits), and every agent with `mcp_servers: [github]` gets the full GitHub toolset. There is no agent dedicated to routine version-control operations (commits, PRs, issues, releases) that cannot touch local code or perform destructive repo actions. The user wants an extremely controlled VCS operator: capable of doing the GitHub work they normally do by hand, restricted to a curated tool allowlist.

## What Changes

- Add agent `agents/keeper.md` — GitHub-only VCS operator. Native tools restricted to read-only (`Read`, `Grep`, `Glob`); no Write/Edit/Bash/Terminal.
- Add always-on rule `rules/agent-rules/keeper-rules.md` — mutation gate, no destructive operations, confirmation-before-merge, rollback planning.
- Deny destructive/unneeded GitHub MCP tools via `disallowed_tools` deny-list: `github_delete_file`, `github_fork_repository`, `github_create_repository`, `github_run_secret_scanning`, `github_get_teams`, `github_get_team_members`, `github_list_repository_collaborators`, `github_search_users`.
- Merge capability is gated (available, but requires MUTATION stop-and-confirm) — not denied.
- Releases/tags: read/track/report only. The GitHub MCP server exposes no create/update-release tool, so release creation is out of scope.
- Document the agent in `AGENTS.md` and `METADATA.md`.
- Standalone ops agent: Forge keeps committing. No pipeline changes.

## Capabilities

### New Capabilities

- `vcs-keeper-agent`: Definition of the keeper agent — a GitHub-only version-control operator with read-only native tools, a curated GitHub MCP allowlist, a built-in mutation gate, and a defined output contract.

### Modified Capabilities

None — no existing specs in `openspec/specs/` are behaviorally changed.

## Impact

- `agents/keeper.md` — new file (pack SSOT agent definition).
- `rules/agent-rules/keeper-rules.md` — new file (always-on behavioral rule, <60 lines).
- `AGENTS.md`, `METADATA.md` — agent inventory and directory tables updated.
- `aipack sync` — propagates the new agent and rule to configured harnesses (OpenCode).
- OpenCode enforcement verified via `opencode debug agent keeper` after sync; per-tool MCP denial relies on the deny-list mechanism the sync adapter renders today. A stricter allow-only model would require a sync-tool adapter change (out of scope; rendering belongs in the sync tool per AGENTS.md).
- No changes to `mcp/github.json` — the server config stays as-is; restriction is per-agent.
