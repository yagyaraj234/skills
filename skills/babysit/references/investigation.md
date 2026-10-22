# Investigation playbook

Read this for questions about a particular codebase or design decision. Use the code anchor and original question to seed searches; expand using identifiers and dates found in the record.

## Discover sources

Use the host's tool inventory or connector discovery. Classify providers by their actual search/read capabilities. A tool's name alone does not establish access. Source control starts with local git when available; PR and issue access may require separate authentication. Record a missing repository, shallow history, missing CLI, or denied credentials as a coverage gap.

Account for all seven categories:

| Category | Search for | Follow through |
| --- | --- | --- |
| Source control | Target symbols, introducing changes, comments, tests, commit messages, PRs and reviews. | Read patches through renames and substantive discussion. Distinguish what changed from the stated reason. |
| Issue / ticket tracker | Linked IDs, feature names, symptoms, customer constraints, exact thresholds. | Read descriptions, comments, acceptance criteria, and parent or related tickets. Look for the forcing requirement. |
| Long-form documents | Design names, alternatives, architecture decisions, rollout plans, decision dates. | Use as supporting evidence of recorded intent. Identify proposed versus accepted decisions, and verify behavioral descriptions against the traced implementation. |
| Real-time team chat | Exact identifiers, PR or ticket links, error text, decision terms, relevant date windows. | Open full matching threads. Preserve proposals, objections, and the eventual resolution separately. |
| Infrastructure observability | Related incidents, metrics, logs, traces, monitors, and resource limits around the change. | Compare signals before and after where records permit. A timing correlation suggests a lead, not proven causation. |
| Error / exception tracking | Exact exception text, stack frames, affected releases, first and last occurrence. | Read linked fixes and incident discussions. Check that events concern the same failure path as the target. |
| Product analytics warehouse | Named events, experiments, flags, migration cohorts, numeric thresholds. | Inspect the available schema first; use bounded read-only queries, explicit time windows and aggregates. Record table names and query text. |

When a provider spans categories, state which evidence each search covers. When several providers serve one category, search each relevant provider; do not silently substitute one provider for the whole category. Report a category as partially covered if some providers could not be searched.

## Search process

1. Search the anchor's exact identifiers and linked records first. Broaden to synonyms, symptoms, predecessor names, and relevant dates if necessary.
2. Read substantive matching records and surrounding discussion. Titles and search snippets alone rarely establish a reason.
3. Follow cited records and decision revisions. Pass cross-provider leads to their assigned investigator; when working alone, follow them directly.
4. Record what supports the proposed explanation and what challenges it. Stop expanding a lead once it is exhausted or no longer concerns the target; state any remaining limits.

For defensive code—retries, null guards, timeouts, rate limits, circuit breakers, feature flags—also search the relevant failure signature, incident or postmortem, affected release, and linked fix within each available provider. Verify whether the historical condition still applies before recommending removal.

Use read/search operations. Keep database queries bounded and read-only, and summarize aggregates where raw user data is unnecessary. Do not post messages, mutate records, or change infrastructure during investigation.

## Investigator brief

Each delegated investigator gets:

- The user's original question and any proposed explanation to test.
- The code anchor: paths and line ranges, symbols, commit IDs, PRs, tickets, and relevant dates.
- One assigned provider and its category guidance from the table above.
- The defensive-code guidance when applicable.

Use a host-supported agent with access to that provider. Use the host's configured model and reasoning settings; this skill requires no vendor-specific model names or mode flags. If a child cannot access a provider, have the parent search it and record the limitation.

Ask each investigator to return:

1. **Coverage:** Provider, exact queries, date ranges, records opened, and access limits.
2. **Evidence:** Finding, precise citation, author/date when available, and whether the reason is explicitly stated or indirectly suggested.
3. **Contradictions:** Conflicting records with both citations.
4. **Gaps:** Successful searches with no matches, unavailable records, and questions left unanswered.
5. **Leads:** Cross-provider links or further searches for the parent to route.

Investigators collect evidence and label interpretations. The parent or synthesizer weighs the combined record. Do not mistake multiple copies of the same original claim for independent corroboration.

## Coverage record

For each category, report one of:

- **Searched:** Queries/scope, records reviewed, useful findings or no matching evidence.
- **Partial:** Searches completed plus providers, history, or records still inaccessible.
- **Unavailable:** The missing tool, repository, credentials, or access needed.
- **Skipped:** The concrete reason this category cannot bear on the question.

"Probably irrelevant" does not justify skipping an available category. "Unavailable" does not mean the evidence does not exist. Preserve each category's result in the final answer, even when every search was empty.
