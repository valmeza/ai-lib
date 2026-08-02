---
name: ai-lib
description: AGENTS.md — project context, directory structure, agents, skills, rules, workflows, MCP, and usage guide for the ai-lib modular AI workflow library pack.
metadata:
  owner: shrug-labs
  last_updated: 2026-07-28
---

# Project Purpose

ai-lib is a modular AI workflow library / agent pack. It defines a pipeline of specialized sub-agents (Pathfinder → Forge → Validator) coordinated by Orchestrator, with supporting skills, behavioral rules, and MCP server configurations. The repo is the portable SSOT — harness-specific sync targets (`.opencode/`, `.agent/`) are rendered from here.

# Directory Structure

| Path | Description |
|------|-------------|
| `pack.json` | aipack manifest (name, version, schema) |
| `METADATA.md` | SSOT for repository structure and agent protocol |
| `agents/` | 5 agent definitions with YAML frontmatter (Pathfinder, Forge, Validator, Orchestrator, Keeper) |
| `skills/` | 6 on-demand skill instructions (one SKILL.md per directory) |
| `rules/agent-rules/` | 3 behavioral constraint files (forge-rules, pathfinder-rules, validator-rules) |
| `rules/style-guides/` | 1 coding style guide (clean-code) |
| `workflows/` | 1 multi-step workflow (spark-to-steel) |
| `mcp/` | 1 MCP server config (github.json) |
| `openspec/` | OpenSpec schema, config, and change artifacts |
| `.opencode/` | OpenCode harness integration (managed, do not edit manually) |
| `.agent/` | OpenSpec agent harness integration (managed, do not edit manually) |

# Agents

All agents reference MCP servers and skills by ID (not path). Tool restrictions are enforced via `disallowed_tools` or explicit `tools` allowlists in frontmatter. Check actual frontmatter as SSOT for current skill/tool bindings.

## Pathfinder (`agents/pathfinder.md`)

- **Role**: Discovery and planning. Gathers requirements, maps terrain, writes `tasks.md`.
- **Persona**: Senior architect asking clarifying questions until the path is clear.
- **Skills loaded**: `gather-requirements`, `estimate-tasks`
- **MCP servers**: github
- **Tools**: Read, Grep, Glob, WebSearch, WebFetch
- **Restrictions**: No Write, Edit, Bash, Terminal — read-only observer.

## Forge (`agents/forge.md`)

- **Role**: Implementation. Reads tasks from `tasks.md`, writes code, runs lint/tests, commits.
- **Persona**: Pure execution — builds through problems, ships working code.
- **Skills loaded**: `implement-task`, `review-code`
- **MCP servers**: github
- **Tools**: Read, Grep, Glob, Write, Edit, Bash, Terminal — full write access.
- **Restrictions**: None (has all write tools).

## Validator (`agents/validator.md`)

- **Role**: QA verification. Tests implementation against acceptance criteria, writes `qa-report.md`.
- **Persona**: Thorough skeptic — assumes nothing works until proven.
- **Skills loaded**: `test-plan`, `verify-implementation`
- **MCP servers**: github
- **Tools**: Read, Grep, Glob, Bash, Terminal
- **Restrictions**: No Write, Edit — read-only with test execution.

## Orchestrator (`agents/orchestrator.md`)

- **Role**: Pipeline manager. Coordinates Pathfinder, Forge, Validator. Tracks state in `pipeline-state.yaml`.
- **Persona**: Pure coordination — does no direct implementation.
- **Skills loaded**: none
- **MCP servers**: github
- **Tools**: Read, Grep, Glob — coordination only, no write or execution.
- **Restrictions**: No Write, Edit, Bash, Terminal.

## Keeper (`agents/keeper.md`)

- **Role**: GitHub version-control operator. Commits work, manages PRs and issues, reports on releases and tags.
- **Persona**: Extremely controlled operator — GitHub-only, never touches local code.
- **Skills loaded**: none
- **MCP servers**: github
- **Tools**: Read, Grep, Glob — read-only locally.
- **Restrictions**: No Write, Edit, Bash, Terminal. Denied MCP tools: `github_delete_file`, `github_fork_repository`, `github_create_repository`, `github_run_secret_scanning`, team/user reads. All mutations gated by MUTATION stop-and-confirm (see `keeper-rules`).

### Pipeline Flow

```
Pathfinder ── writes tasks.md ──► Forge ── writes code ──► Validator
                                       ▲                        │
                                       │    qa-report.md        │
                                       ◄────── (fail) ────────│
                                                                │ pass
                                                                ▼
                                                     Next task or done
```

# Skills

Six on-demand skills in `skills/`. Each has a `SKILL.md` with frontmatter and step-by-step instructions. Invoke by ID, not path.

| Skill ID | Description | When to Use |
|----------|-------------|-------------|
| `gather-requirements` | Elicit clear, structured requirements via questioning and codebase investigation. | Before writing any plan. Never skip this step. |
| `estimate-tasks` | Break requirements into discrete tasks with effort points (1/2/3/5/8). | After requirements are gathered, before writing `tasks.md`. |
| `implement-task` | Read a task from `tasks.md`, implement working code, verify, commit. | When Forge starts implementation of a task. |
| `review-code` | Self-review code before handoff: correctness, style, cleanliness, tests. | Before marking any task as complete. |
| `test-plan` | Create a test plan from implementation and acceptance criteria. | Before executing verification. |
| `verify-implementation` | Execute the test plan, check acceptance criteria, write `qa-report.md`. | After test plan is ready, to produce QA verdict. |

# Rules

Five behavioral constraint files in `rules/`. Rules are always-on constraints (unlike skills which are on-demand).

| File | Description | Key Constraints |
|------|-------------|-----------------|
| `rules/agent-rules/forge-rules.md` | Forge behavioral constraints — implementation discipline. | Follow spec exactly, run lint/tests, self-review, commit cleanly, flag blockers. No scope creep, no TODO/debug artifacts. |
| `rules/agent-rules/pathfinder-rules.md` | Pathfinder behavioral constraints — discovery and planning only. | Never write implementation code. Investigate codebase first. Output structured `tasks.md`. No commits, tests, or deployment. |
| `rules/agent-rules/validator-rules.md` | Validator behavioral constraints — thorough verification. | Assume nothing works. Test every criterion. Document failures with evidence. No approving without tests. |
| `rules/agent-rules/keeper-rules.md` | Keeper behavioral constraints — mutation-gated GitHub version control. | MUTATION gate on every state change. Never delete/fork/create repos, force-push, or merge without explicit confirmation. No native writes. |
| `rules/style-guides/clean-code.md` | Coding style guide. | Match existing style, no comments unless convention uses them, meaningful names, single-purpose functions, no dead code, clean imports. |

# Workflows

## spark-to-steel (`workflows/spark-to-steel.md`)

Full lifecycle pipeline: requirements gathering → implementation → QA verification.

1. **Pathfinder** — gathers requirements, breaks into tasks with effort estimates, writes `tasks.md`.
2. **Forge** — reads `tasks.md`, implements each task, runs lint/tests, commits code.
3. **Validator** — reads `tasks.md` and implementation, runs verification, writes `qa-report.md`.
4. **On failure** — Forge receives `qa-report.md`, fixes issues, loop to step 3.
5. **On pass** — report completion.

# MCP Configuration

## GitHub (`mcp/github.json`)

- **Type**: MCP server (remote)
- **Transport**: remote — `https://api.githubcopilot.com/mcp/`
- **Auth**: `Authorization: Bearer {env:GITHUB_PERSONAL_ACCESS_TOKEN}` with `oauth: false`
- **Tools**: all (`*`) — no allowlist restriction
- **Portability**: No absolute paths or secrets. Uses `{env:GITHUB_PERSONAL_ACCESS_TOKEN}` placeholder.
- **Prerequisite**: Set `GITHUB_PERSONAL_ACCESS_TOKEN` in your shell environment (a GitHub PAT with appropriate scopes).

# Harness Wiring

This repo is the SSOT (Single Source of Truth). Harness-specific directories are sync targets and must not be edited manually.

| Directory | Harness | Contents |
|-----------|---------|----------|
| `.opencode/` | OpenCode | Command definitions, skills, package.json for OpenCode plugin |
| `.agent/` | OpenSpec Agent | Skills and workflows for OpenSpec integration |

## OpenSpec Integration

The `.opencode/command/` and `.agent/workflows/` directories define four `opsx-*` commands that map to the OpenSpec change workflow:

| Command | Purpose |
|---------|---------|
| `opsx-explore` | Explore mode — investigate problems, clarify requirements |
| `opsx-propose` | Create a complete change proposal (design, spec, tasks) |
| `opsx-apply` | Implement tasks from an approved OpenSpec change |
| `opsx-archive` | Archive a completed change |

These are managed by OpenSpec. Do not modify them directly — use the OpenSpec CLI (`opsx-*` commands) to manage changes.

# Usage Guide

1. **Start with METADATA.md** — read it first for the authoritative repository structure guide and agent protocol.
2. **Understand the pack** — read `pack.json` for versioning info; read `README.md` for the project philosophy.
3. **Pick an agent** — for a new request, invoke Pathfinder (discovery). For implementation, invoke Forge. For verification, invoke Validator. For end-to-end orchestration, invoke Orchestrator.
4. **Reference skills by ID** — use skill IDs (`gather-requirements`, `implement-task`, etc.) when loading skills, not filesystem paths.
5. **Follow the pipeline** — the spark-to-steel workflow is the primary development loop. Let Orchestrator coordinate if available.
6. **Respect tool restrictions** — Pathfinder, Validator, and Keeper are read-only locally. Forge has full write access. Orchestrator is coordination-only.
7. **Check actual frontmatter** — agent skill/tool bindings in this file summarize frontmatter but may drift. Consult the actual agent `.md` files as SSOT.
8. **Use aipack sync** — after modifying pack content (agents, skills, rules, workflows), run `aipack sync` to propagate to harness locations. Do not manually edit files in `.opencode/` or `.agent/`.
9. **Do not edit generated artifacts** — files under `openspec/changes/`, `.opencode/`, and `.agent/` are managed by their respective tools. Edit the SSOT and let the tool pipeline propagate.
