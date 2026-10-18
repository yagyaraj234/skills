---
name: ship
description: Open a PR (or update an open one) with a readiness check and a clear description. Use when asked to create a PR, open a PR, or write a PR description.
disable-model-invocation: true
---

1. Check readiness first. Run `check-readiness diff` on the changes. If the PR is already open, run `check-readiness pr <URL>` instead. Fix or flag anything serious before going on.
2. Look for a PR template (`.github/pull_request_template.md`, `PULL_REQUEST_TEMPLATE.md`, or a `PULL_REQUEST_TEMPLATE/` folder). If there is one, fill it in. Don't drop or rename its sections.
3. No template? Write the description yourself, like one person telling another what changed: what it does, why, and anything to watch out for. Plain words, no jargon, short.
4. Open the PR with `gh pr create`. If one is already open, update it with `gh pr edit`.
5. Reply with the PR link and a one-line readiness summary.
