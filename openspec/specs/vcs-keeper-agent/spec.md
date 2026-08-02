# vcs-keeper-agent Specification

## Purpose
TBD - created by archiving change add-vcs-keeper-agent. Update Purpose after archive.
## Requirements
### Requirement: Keeper agent definition exists
The pack SHALL define a `keeper` agent at `agents/keeper.md` that performs GitHub version-control operations (commits, PRs, issues, release/tag tracking) with a curated tool allowlist and MUST NOT modify local code.

#### Scenario: Agent file exists
- **WHEN** the pack is inspected
- **THEN** `agents/keeper.md` exists with frontmatter matching the keeper name

#### Scenario: Native tools are read-only
- **WHEN** keeper's rendered OpenCode tool surface is inspected
- **THEN** `Read`, `Grep`, `Glob` are available and `Write`, `Edit`, `Bash`, `Terminal` are denied via the `permission` deny-list

### Requirement: Keeper MCP tool restrictions
The keeper agent SHALL reference the `github` MCP server and SHALL deny destructive or unneeded GitHub tools via a `disallowed_tools` deny-list.

#### Scenario: Destructive tools denied
- **WHEN** keeper's `disallowed_tools` is inspected
- **THEN** it includes `github_delete_file`, `github_fork_repository`, and `github_create_repository`

#### Scenario: Unneeded read tools denied
- **WHEN** keeper's `disallowed_tools` is inspected
- **THEN** it includes `github_get_teams`, `github_get_team_members`, `github_list_repository_collaborators`, `github_search_users`, and `github_run_secret_scanning`

#### Scenario: Merge remains available but gated
- **WHEN** keeper's `disallowed_tools` is inspected
- **THEN** `github_merge_pull_request` is NOT denied

### Requirement: Keeper enforces a mutation gate
The keeper agent SHALL gate every state-changing action by labeling it `MUTATION`, naming the target, action, expected outcome, and rollback, and waiting for explicit user confirmation before proceeding.

#### Scenario: Mutation requires confirmation
- **WHEN** keeper is asked to merge a pull request, commit work, create a PR, or update an issue
- **THEN** keeper labels the action `MUTATION`, states target/action/outcome/rollback, and waits for explicit `yes` before executing

#### Scenario: Companion rule exists
- **WHEN** the pack is inspected
- **THEN** `rules/agent-rules/keeper-rules.md` exists and contains the mutation-gate constraints

### Requirement: Keeper is documented
The pack documentation SHALL list keeper in the agent inventory.

#### Scenario: Docs list keeper
- **WHEN** `AGENTS.md` and `METADATA.md` are inspected
- **THEN** keeper appears in the agent inventory with its role and restriction summary

### Requirement: Enforcement verified after sync
The pack SHALL verify keeper's rendered tool surface after `aipack sync` using `opencode debug agent keeper`.

#### Scenario: Sync propagates keeper
- **WHEN** `aipack sync` runs
- **THEN** the OpenCode harness agent for `keeper` renders with read-only native tools (via the `permission` deny-list) and the `disallowed_tools` deny-list

