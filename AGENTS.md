# AGENTS.md

## Project

Laravel + PostgreSQL web application

## Required read order (start every task with these)

1. `docs/ai-context/project-summary.md`
2. `docs/ai-context/common-commands.md`
3. Relevant file(s) below depending on the task

## Task-based reading

| Task | Read |
|---|---|
| Auth changes | `docs/architecture/authz-authn.md` |
| DB changes | `docs/architecture/data-model.md` and `docs/adr/` |
| Test changes | `docs/development/testing-strategy.md` |
| Release changes | `docs/operations/deployment.md` |
| Architecture changes | `docs/adr/` (all relevant ADRs) |

## Rules

- Do **not** read the entire docs tree unless explicitly needed
- Read only files relevant to the current task
- Before any architecture change, check `docs/adr/`
- Do not change env / secret handling casually
- Do not introduce schema-breaking migration without a migration plan
- Do not bypass authorization layer (Policy / Gate)
- Do not edit unrelated files

## Output expectations

For every non-trivial change:
- Summarize changed files
- Mention risks
- Propose tests
- Reference related ADR / REQ IDs when available

## Prohibited actions

- Committing secrets or credentials
- Bypassing Gate / Policy for authorization
- Schema-breaking migrations without migration plan
- Force-pushing to main/master
