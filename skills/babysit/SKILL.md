---
name: babysit
description: Explain code's reasons and tradeoffs from its implementation, with historical intent distinguished from inference.
disable-model-invocation: true
---

# Babysit

Investigate **why** something took its current shape, then explain it like two best friends talking it through. Be patient and direct; treat the user as capable but unfamiliar with this decision. Use full sentences, explain technical terms when they first matter, and connect abstract tradeoffs to a concrete example.

`/babysit "why did we choose this state management approach?"`

Use [walkthrough](../walkthrough/SKILL.md) internally to establish **how** the code works before investigating **why** it took that shape. Distinguish the technical effects it establishes from the historical reasons someone chose them.

## 1. Pin down the question

Use the question after `/babysit` or `$babysit`, then conversation context. When the target is ambiguous but a likely referent exists, state your interpretation briefly and proceed. If no target can be identified, ask one focused question and wait.

For a general conceptual question, go directly to step 5: explain the technical reason using reliable primary sources where verification is needed. For a question about the user's application or a particular decision, follow steps 2–4 first.

**Done when:** The question has a concrete target, or you have asked for the missing target and paused.

## 2. Establish the code anchor

Run [walkthrough](../walkthrough/SKILL.md) for the identified target. Pass the original question, repository, and relevant conversation context, and request its internal handoff. Wait for its implementation-backed flow before continuing; use the cited code anchor and evidence gaps as the starting point for the history investigation. A handoff based only on documentation needs a source trace before it can satisfy this step. If walkthrough is unavailable, report the missing dependency rather than substituting a guessed code anchor.

Use blame and history on that anchor to find the introducing change and substantive revisions, including renamed files. Read commit messages, linked PR bodies, review discussions, and ticket references when accessible.

Start with:

```sh
git blame -L <start>,<end> -- <file>
git log --oneline -20 -- <file>
git log --follow -p -- <file>
git show -s --format=%B <commit>
```

When GitHub access is available, read relevant PRs with `gh pr view <number> --json title,body,createdAt,mergedAt,closingIssuesReferences,comments,reviews`, or the equivalent connector. Extract PR and ticket identifiers from the actual history. A recent edit may preserve a much older decision; trace the reason back rather than treating the last author as its origin.

**Done when:** Walkthrough has returned its code anchor and flow, and you have identified relevant changes, linked records, and any unavailable history. If the dependency or repository is missing, report the blocker and pause this investigation.

## 3. Investigate the available record

Read [the investigation playbook](references/investigation.md) before searching or delegating. It defines the seven evidence categories, category searches, and investigator brief.

Discover the host's available connectors, MCP tools, and local tools. Search every available evidence category, including sources that may return nothing. Use these records to investigate motivation after tracing the code; documents are supporting historical evidence, not a substitute for the implementation. Missing docs do not block an explanation of the code's effects and tradeoffs. Record unavailable sources and failed access separately from successful searches with no matches. Skip an available category only when it is provably irrelevant, and record the reason.

When delegation is supported, assign independent source searches to investigators in parallel, within the host's concurrency limits. Give each investigator one provider, its category guidance, the code anchor, and the original question. When delegation is unavailable, perform the same searches yourself; coverage stays the same. Keep code, connected services, and external state read-only.

Follow relevant cross-source leads with the investigator responsible for that provider, or yourself when working alone. Search failures are gaps to report, not findings about the decision.

**Done when:** All seven categories have a recorded outcome and relevant leads have been followed or explicitly left open.

## 4. Separate evidence from interpretation

Combine the findings, remove duplicate citations, and preserve contradictions. Verify that cited records exist and support the associated claim. If investigators were used, hand the anchor, original question, all findings, and this confidence framework to a synthesizer when supported; otherwise synthesize them yourself.

| Confidence | What qualifies | How to say it |
| --- | --- | --- |
| Direct | A contemporary comment, PR, ticket, document, or message explicitly states the reason. | State the reason with the citation beside it. |
| Supported | Multiple independent clues converge without explicitly stating the reason. | "The evidence points toward…" Explain what each clue contributes. |
| Inferred | A plausible interpretation of limited evidence. | "This may have been…" Show the reasoning and its limits. |
| Speculative | Several explanations fit and evidence cannot distinguish them. | "One possibility is…" Include alternatives and what would resolve them. |
| Unknown | The record searched does not answer the question. | Name the unanswered question and the search gap. |

Derive practical effects and tradeoffs from the traced mechanism: what it enables, what failure it prevents, and what it costs. Code establishes those mechanics; a plausible benefit alone does not establish author intent. When historical motivation is undocumented, explain the code-backed effects and label the proposed reason as inferred or unknown. Treat the user's suggested reason as a hypothesis to check. Keep original motivation separate from whether the choice still makes sense today. Contradictory sources stay visible, with both citations.

**Done when:** Every rationale claim has a confidence level, factual claims have supporting citations, and conflicting or missing evidence remains explicit.

## 5. Explain it like a friend

Lead with the clearest supported answer in plain words, grounded in walkthrough's source trace. Walk through one concrete example showing the mechanism, its practical effects, and its tradeoffs; connect historical pressures or constraints only as far as the evidence supports them. Explain uncertain parts as uncertain in the same friendly voice.

Keep these distinctions visible, using short paragraphs or headings as the answer needs:

- **What we know:** The question, code anchor, and evidence-backed explanation with citations.
- **What seems likely:** Inferences and competing explanations, only when useful.
- **What remains unknown:** Specific gaps and contradictions that affect the answer.
- **Sources checked:** One compact entry per evidence category, including null results, access failures, and justified skips. State the actual queries, scope, and records reviewed; let citations identify individual records.
- **Confidence:** A sentence explaining which parts are established and which remain uncertain.

If the question prepares a code change, finish with **Preserve / Change / Avoid / Risk** constraints derived from the findings. Recommendations remain recommendations; historical investigation does not authorize edits.

For a general conceptual question, use the same patient explanation and one concrete example, with source citations where needed; omit the project investigation coverage report.

**Done when:** The user has an understandable answer, can follow its evidence, and can tell established reasons from plausible explanations. Answer follow-ups at the point of confusion, using a smaller step or a different example when needed.
