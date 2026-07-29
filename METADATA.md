# Repository Schema Definition
This repository is a modular AI workflow library.

## Directory Structure
- /pack.json: aipack manifest (portable pack definition)
- /agents: Agent definitions with YAML frontmatter (Pathfinder, Forge, Validator, Orchestrator)
- /skills: On-demand skill instructions (one SKILL.md per skill directory)
- /rules: Behavioral constraints and style guides
- /workflows: Repeatable multi-step processes (markdown + frontmatter)
- /mcp: MCP server configurations
- /openspec: OpenSpec schema and changes

## Agent Protocol
When modifying this repo:
1. READ this METADATA.md first to understand the context.
2. MAINTAIN modularity: Do not hardcode skills inside agents.
3. USE skill IDs (not paths) when referencing skills in agent frontmatter.
4. RETURN the full file content for all modifications.
