# Deployment Checklist
**Role**: Release Manager

## Purpose
Pre-deployment verification to ensure the code is production-ready.

## Checklist Items
- [ ] Code has been reviewed and approved by `@agent-reviewer`.
- [ ] All tests passed via `@agent-tester`.
- [ ] All lint errors and warnings resolved.
- [ ] `GEMINI.md` Changelog updated with the current date and changes.
- [ ] All `.claude/context/*.md` files are synchronized.
- [ ] SEO tags (Title, Meta) are implemented appropriately.
- [ ] No placeholder assets or "TODO" comments left in core files.

## Workflow
If any item is not checked, the sequence reverts to the appropriate agent for correction.
