# Skills

Agent skills for Claude Code, Codex, and any agent that supports the skills format. Install them with one command and use them in any project.

## Install

Install with the [skills CLI](https://skills.sh). No clone needed.

```bash
# all skills
npx skills add yagyaraj234/skills

# or just one
npx skills add yagyaraj234/skills --skill check-readiness
```

The CLI detects your agent (Claude Code, Codex, Cursor, and others) and puts the skills in the right place. Restart your agent, then try `/check-readiness diff` (or `$check-readiness diff` in Codex).

## Skills

| Skill | Invoke | Use when |
| --- | --- | --- |
| [`check-readiness`](skills/check-readiness/SKILL.md) | `/check-readiness [mode]` | You want a readiness review of a diff, PR, release, or the whole repo. It runs the backend and frontend skills as needed and ends with `READY`, `NOT READY`, or `UNVERIFIED`. |
| [`backend-readiness`](skills/backend-readiness/SKILL.md) | `/backend-readiness [mode]` | You are reviewing APIs, workers, webhooks, databases, or services: validation, authn/authz, payments, migrations, secrets, tests. |
| [`frontend-readiness`](skills/frontend-readiness/SKILL.md) | `/frontend-readiness [mode]` | You are reviewing app UI: routes, forms, state, empty/loading/error states, accessibility, performance, unsafe rendering. |
| [`bro`](skills/bro/SKILL.md) | `/bro` | An answer is too jargon-heavy. Rewrites the last message in plain, short human language. Manual only. |
| [`babysit`](skills/babysit/SKILL.md) | `/babysit "why did we choose this?"` | You want the reasons and tradeoffs behind code or a decision. Uses `walkthrough` internally, then investigates history and available connected sources. Manual only. |
| [`walkthrough`](skills/walkthrough/SKILL.md) | `/walkthrough "how does this work?"` | You want a patient, step-by-step explanation of a topic or your app's execution and data flow, grounded in current code. Also used internally by `babysit`. |
| [`ship`](skills/ship/SKILL.md) | `/ship` | Runs a readiness check, then opens or updates a PR with a clear description. Manual only. |

Install `babysit` and `walkthrough` together. `babysit` uses `walkthrough` to trace the current code before researching the reasons behind it.

Both skills read the implementation and cite the source behind their explanations. Project documentation is optional supporting material; missing docs do not prevent a source-based explanation, and a documentation summary alone does not complete one.

### Readiness modes

Used by `check-readiness`, `backend-readiness`, and `frontend-readiness`. The mode is optional: a PR URL picks `pr`, local changes pick `diff`, and otherwise it runs `full`.

| Mode | Target |
| --- | --- |
| `diff` | Staged, unstaged, and relevant untracked changes plus affected callers and tests (default for changes). |
| `pr <URL>` | The linked PR's diff, checks, and discussion. |
| `release` | The repo and its deployment target; every critical flow and release dependency. |
| `full` | The whole repo across all applicable backend and frontend surfaces. |

Examples:

```
$check-readiness diff
$check-readiness pr https://github.com/org/repo/pull/123
$backend-readiness release
$frontend-readiness full
```

Behavior to know:

- **Read-only by default.** It reports findings and fixes; it edits code only if you explicitly ask it to fix them.
- Each check is `PASS` (with evidence), `FAIL` (blocking or advisory), `BLOCKED` (missing credentials/env), or `N/A` (with reason).
- Blocking failures (security, auth, data loss, broken critical flow, leaked secrets, failing build/tests) mean `NOT READY`. Advisory ones don't.
- `backend-readiness` and `frontend-readiness` reuse the rules in `check-readiness`, so install all three together.
