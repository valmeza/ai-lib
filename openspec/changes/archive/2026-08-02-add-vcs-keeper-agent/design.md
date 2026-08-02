## Context

The repo is a pack SSOT for AI agent workflows. Four agents exist (Pathfinder, Forge, Validator, Orchestrator), all referencing `mcp_servers: [github]` and all currently getting the full GitHub toolset. Forge has broad native write access and commits code directly. There is no dedicated, restricted operator for routine GitHub version-control work.

The GitHub MCP server is configured at `mcp/github.json` (remote, PAT-authenticated) and in `~/.config/opencode/opencode.json`. Enforcement is per-agent: the sync adapter renders pack agent frontmatter (`tools`, `disallowed_tools`, `mcp_servers`) to OpenCode agent files. The aipack capability matrix records OpenCode MCP tool control as `server_toolname: true/false` booleans; the pack `disallowed_tools` deny-list is copied through on sync.

## Goals / Non-Goals

**Goals:**
- Add a `keeper` agent that performs GitHub version-control operations (commits, PRs, issues, release/tag tracking) only.
- Restrict keeper's native tools to read-only (`Read`, `Grep`, `Glob`) — no code edits, no shell.
- Deny destructive/unneeded GitHub MCP tools via `disallowed_tools`.
- Gate all mutations (including PR merge) behind a MUTATION stop-and-confirm.
- Document keeper in `AGENTS.md` and `METADATA.md`.
- Verify enforcement after sync (`aipack sync --dry-run`, `opencode debug agent keeper`).

**Non-Goals:**
- Changing `mcp/github.json` — the server config is unchanged; restriction is per-agent.
- Changing Forge's commit behavior — keeper is a standalone ops agent; Forge keeps committing.
- Release/tag creation — the GitHub MCP server exposes no create/update-release tool, so keeper tracks and reports releases/tags only.
- Strict allow-only MCP tooling — would require a sync-tool adapter change; the deny-list is the mechanism available today.
- Adding skills or workflows for keeper.

## Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Agent name | `keeper` | Matches single-word persona pattern of existing agents. |
| Native tools | `Read`, `Grep`, `Glob` only | Can inspect `tasks.md`/`qa-report.md` and local context; cannot modify code or run shell. |
| MCP access | `mcp_servers: [github]` | Same server as other agents; restriction is per-agent, not server-level. |
| Tool enforcement | `disallowed_tools` deny-list with exact tool names | The mechanism the pack schema and sync adapter render today (agent-constraints rule: deny-list with glob patterns). Allow-only would need adapter changes. |
| Hard-denied MCP tools | `github_delete_file`, `github_fork_repository`, `github_create_repository`, `github_run_secret_scanning`, `github_get_teams`, `github_get_team_members`, `github_list_repository_collaborators`, `github_search_users` | Destructive (delete/fork/create repo) or unneeded for VCS ops (team/user reads). |
| PR merge | Gated allow | `github_merge_pull_request` stays available; keeper-rules rule + agent body require MUTATION stop-and-confirm before any merge. |
| Mutation posture | Built-in gate: label `MUTATION`, name target/action/outcome/rollback, wait for explicit `yes` | agent-constraints rule requires any mutating agent to carry its own mutation gate. |
| Mode | Harness-neutral (no mode field); invoked on demand as subagent | Matches existing agent files; no pipeline integration. |
| Companion rule | `rules/agent-rules/keeper-rules.md` (<60 lines) | Always-on behavioral constraints; enforces the gate in every keeper interaction. |

## Risks / Trade-offs

| Risk | Mitigation |
|------|------------|
| `disallowed_tools` deny-list may not be enforced by the OpenCode adapter/render | Verify with `opencode debug agent keeper` after sync; if denial doesn't render, fix the sync adapter (per AGENTS.md, rendering belongs in the sync tool, not pack source). |
| Deny-list leaks access to newly-added GitHub server tools (allow-only is stricter) | Acceptable trade-off for today's mechanism; documented in proposal. Revisit allow-only if server toolset grows. |
| Agent ignores the mutation gate under pressure | Rule + body both state the gate with explicit confirmation wording; rationalization table counters in keeper-rules.md. |
| PAT scope grants more than keeper needs | Token scope is set at the GitHub level by the user; keeper restrictions are enforced in-config. Documented for user awareness. |
