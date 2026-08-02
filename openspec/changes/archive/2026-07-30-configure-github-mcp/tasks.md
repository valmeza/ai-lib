## 1. Update Pack SSOT

- [x] 1.1 Rewrite `mcp/github.json` to use remote server with `{env:GITHUB_PERSONAL_ACCESS_TOKEN}` and `oauth: false`
- [x] 1.2 Verify no absolute paths or secrets in the config

## 2. Update OpenCode Harness Config

- [x] 2.1 Update `~/.config/opencode/opencode.json` to use remote GitHub MCP server with env-var interpolation
- [ ] 2.2 Restart OpenCode session to pick up config changes (requires manual restart)

## 3. Sync and Validate

- [x] 3.1 Run `aipack sync --dry-run` to verify pack propagation
- [ ] 3.2 Run `opencode mcp list` to confirm server is discovered (requires session restart + GITHUB_PERSONAL_ACCESS_TOKEN set)
- [ ] 3.3 Run `opencode mcp debug github` to verify auth and connectivity (requires session restart + GITHUB_PERSONAL_ACCESS_TOKEN set)

## 4. Documentation

- [x] 4.1 Add `GITHUB_PERSONAL_ACCESS_TOKEN` env var requirement to pack documentation (README.md and AGENTS.md)
