## 1. Author keeper agent

- [ ] 1.1 Create `agents/keeper.md` with frontmatter: `name`, `description`, `mcp_servers: [github]`, `skills: []`, `tools: [Read, Grep, Glob]`, and `disallowed_tools` deny-list (delete/fork/create-repo, secret-scanning, team/user reads)
- [ ] 1.2 Write keeper body: scope (what it does/doesn't do), mutation gate, output contract (commit summary, PR description, issue update, status report)

## 2. Author keeper rules

- [ ] 2.1 Create `rules/agent-rules/keeper-rules.md` (<60 lines, trigger-action pairs) enforcing: MUTATION gate, no destructive ops, confirmation-before-merge, rollback planning, no force-push

## 3. Update documentation

- [ ] 3.1 Add keeper row to the agent inventory table in `AGENTS.md`
- [ ] 3.2 Add keeper to the directory/agent tables in `METADATA.md`

## 4. Sync and verify

- [ ] 4.1 Run `aipack sync --dry-run` and confirm the new agent + rule are in the diff
- [ ] 4.2 Run `aipack sync` to propagate keeper to the OpenCode harness
- [ ] 4.3 Run `opencode debug agent keeper` and confirm native tools are read-only and `disallowed_tools` is rendered
- [ ] 4.4 Restart OpenCode and smoke test keeper with a read-only GitHub call, then one gated write
