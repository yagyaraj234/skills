---
name: check-readiness
description: Check readiness from a working diff, PR URL, release target, or full repository audit. Use when asked to review changes, assess release readiness, or find backend and frontend issues with evidence.
---

# Check readiness

Invoke as `$check-readiness diff`, `$check-readiness pr <URL>`, `$check-readiness release`, or `$check-readiness full`.

Read-only by default. Report issues and concrete resolutions. Edit only when the user explicitly asks to fix or apply findings; then verify affected behavior.

## 1. Choose target

| Mode | Target | Completion criterion |
| --- | --- | --- |
| `diff` | Current repository's staged, unstaged, and relevant untracked changes, plus affected callers and tests. Default when changes are the target. | Every changed behavior and affected boundary accounted for. |
| `pr <URL>` | Linked PR diff, base, conversation, checks, and affected context. | Every changed behavior accounted for; missing local or remote evidence named. |
| `release` | Current repository and named deployment target, or the target found in repo configuration. | Every critical flow and release dependency has a state. |
| `full` | Current repository across applicable backend and frontend surfaces. | Every major route, operation, trust boundary, data store, and lifecycle path inventoried and assessed. |

Use the mode the user names. If none is named, infer from a PR URL, current changes, or a stated release; otherwise use `full`. Record exact target, revision or diff, environment, and available validation paths before checking. If a PR URL cannot be accessed, report what is blocked; do not substitute an unrelated local diff.

### Severity

Every `FAIL` is **blocking** or **advisory**. Blocking: security, authn/authz, data loss or corruption, broken critical flow, unverified payment or webhook handling, leaked secrets, failing build or tests. Everything else is advisory and does not by itself prevent `READY`. Each readiness skill lists its own blocking areas.

### Depth by mode

`diff` and `pr`: check only behavior the change touches; mark the rest `N/A (not touched)` in one line, not per check. Release-gate checks (Lighthouse, deployed-environment smoke tests, share previews) apply to `release` and `full` only, unless the change affects them.

## 2. Gather evidence

For `diff`, inspect `git status --short --branch`, staged and unstaged diffs, and relevant untracked files. For `pr`, read the linked PR and its diff, checks, and discussion; use a matching local checkout for tests only when its revision is verified. For `release` and `full`, map the in-scope application before testing. Trace callers, data effects, configuration, tests, and deployment dependencies for each affected area.

Run [backend-readiness](../backend-readiness/SKILL.md) when backend code or behavior is in scope. Run [frontend-readiness](../frontend-readiness/SKILL.md) when frontend code or behavior is in scope. Pass the selected mode and target to each; if both apply, combine their findings. For a change review, apply checks to affected behavior. For a release, apply checks across critical flows and the target environment. For `full`, assess all applicable checks across the repository. Mark each applicable check `PASS`, `FAIL`, `BLOCKED`, or `N/A`; attach current evidence to every `PASS` and a reason to every `N/A`.

Use source, automated tests, local runtime, provider sandbox, and deployed environment as distinct evidence layers. A build proves a build. Record unavailable credentials, infrastructure, or environments as `BLOCKED`, with the exact proof still needed. Run focused checks that can verify the target; do not claim runtime or release behavior from source alone.

Complete when applicable checks have states, failures have a cause and resolution, and each readiness claim matches its evidence layer.

## 3. Report and act

Report mode and target, then findings ordered by security, data loss, correctness, and user impact. Each finding names location or flow, evidence, consequence, and smallest concrete resolution. Include checks run with exact results, plus blocked or unverified items. End with `READY`, `NOT READY`, or `UNVERIFIED` for the chosen target: `READY` requires no known blocking failures and evidence for its applicable gate; `UNVERIFIED` means missing evidence prevents that claim.

Use this template:

```
Mode / target: <mode, revision or diff, environment>

Findings
| Severity | Location or flow | Evidence | Consequence | Resolution |

Checks run
| Command or test | Result |

Blocked / unverified
- <item>: <proof still needed>

Verdict: READY | NOT READY | UNVERIFIED
```

Keep review runs read-only. If the user asks to fix findings, apply the requested fixes in affected code, run focused verification, and report remaining risks. Do not infer edit authorization from a request to check readiness.

Complete when the report distinguishes verified behavior from gaps, or requested fixes have been applied and verified.
