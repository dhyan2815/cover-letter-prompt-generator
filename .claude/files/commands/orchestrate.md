# Orchestrator
**Role**: Project Management Controller

## Purpose
This command is used to coordinate complex tasks that require multiple agents to work in sequence (e.g., Planner -> Coder -> Reviewer -> Tester).

## Multi-Agent Workflow
1. **Initialize**: Call `@agent-planner` to create the strategy.
2. **Implement**: Hand over context to `@agent-coder` for development.
3. **Validate**: Trigger `@agent-reviewer` for quality control.
4. **Verify**: Use `@agent-tester` to confirm functional correctness.
5. **Finalize**: Merge, clean up, and update `GEMINI.md`.

## Coordination Rules
- Ensure intermediate context files (`.claude/context/*.md`) are consistently used for hand-offs.
- Do not proceed to the next step if the previous step failed or has pending blocker issues.
