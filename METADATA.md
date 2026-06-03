# Repository Schema Definition
This repository is a modular AI workflow library.

## Directory Structure
- /agents: Persona, system prompts, and core behavior definitions.
- /skills: Modular prompt snippets (task-specific instructions).
- /rules: Global or situational constraints (e.g., coding standards).
- /mcp: Tool configurations for agentic access.
- /workflows: Orchestrator manifests that link the above components.

## Agent Protocol
When modifying this repo:
1. READ this METADATA.md first to understand the context.
2. MAINTAIN modularity: Do not hardcode skills inside agents.
3. USE the manifest.yaml in /workflows/ to link components.
4. RETURN the full file content for all modifications.
