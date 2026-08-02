  +===========================================================+
  |                                                           |
  |          *    *    *    *    *    *    *                  |
  |         / \  / \  / \  / \  / \  / \  / \                 |
  |        *    *    *    *    *    *    *                    |
  |         \ /  \ /  \ /  \ /  \ /  \ /  \ /                 |
  |          *    *    *    *    *    *    *                  |
  |                                                           |
  |                       ai-lib                              |
  |            Modular AI Workflow Library                    |
  |                                                           |
  +===========================================================+

A library of AI agent custom instructions. Some are half-baked, some are forged nicely. You may find something cool, or boring — like a library has.

Agent definitions, skills, behavioral rules, workflows, and MCP configs — all portable as an aipack.

  - * - * - * - * - * - * - * - * - * - * - * - * - * - * - *

## Inventory

| Layer | Count | What |
|-------|-------|------|
| **Agents** | 4 | Reusable personas with tool/skill bindings (Pathfinder, Forge, Validator, Orchestrator) |
| **Skills** | 6 | On-demand instructions — load when needed |
| **Rules** | 4 | Always-on behavioral constraints |
| **Workflows** | 1 | Multi-step pipelines from idea to shipped |
| **MCP** | 1 | Portable server configs |

  - * - * - * - * - * - * - * - * - * - * - * - * - * - * - *

## Quick Start

```bash
# Prerequisite: Set your GitHub Personal Access Token
export GITHUB_PERSONAL_ACCESS_TOKEN=ghp_...

aipack pack install --url https://github.com/<your-user>/ai-lib.git --name ai-lib
aipack pack add ai-lib
aipack sync --harness all

# Then in any supported coding agent:
#   /spark-to-steel   — run the full pipeline
```

  - * - * - * - * - * - * - * - * - * - * - * - * - * - * - *

## Philosophy

- **Machine-Readable** — optimized for AI agents, not humans browsing
- **Iteration-First** — prove it works, then formalize it
- **Portable** — one source of truth, sync to any harness
