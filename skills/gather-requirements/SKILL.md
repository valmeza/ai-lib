---
name: gather-requirements
description: Elicit clear, structured requirements from the user or context.
---

When gathering requirements:

1. **Understand the request** — read the user's input carefully. Identify what they're asking for vs. what problem they actually need solved.
2. **Ask clarifying questions** if anything is ambiguous:
   - What is the goal?
   - Who is the user?
   - What does success look like?
   - Are there constraints (time, tech, resources)?
   - Is there an existing pattern in the codebase to follow?
3. **Investigate the codebase** — use file search and grep to find existing relevant code, patterns, and conventions.
4. **Summarize** the requirements in a structured format:
   - Goal
   - Scope (in scope / out of scope)
   - Key files or areas affected
   - Dependencies
   - Success criteria
5. **Do NOT implement anything.** Stop at requirements.

Output your findings as structured notes for use by the estimate-tasks skill.
