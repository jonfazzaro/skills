---
name: ensemble-review
description: Runs up to three isolated, concurrent reviews, then synthesizes their findings into a prioritized revision plan. Use when the user explicitly asks for an ensemble review, independent multi-model review, or cross-check of a document, proposal, specification, plan, or runbook for internal consistency, narrative flow, and end-to-end coherence.
---

# Ensemble Review

Review one target independently with up to three available reviewers, have each
reviewer apply all three lenses, preserve each review as a separate artifact,
and reconcile them into one actionable synthesis.

## Prepare

1. Resolve the review target from the user's request. If it is missing or
   ambiguous, ask for it.
2. Verify that the target exists and is readable.
3. Do not modify the target unless the user separately requests edits.
4. Place reports:
   - For a file target, in the file's containing directory.
   - For a directory target, inside that directory.
5. Use these exact report names:
   - `REVIEW-1-CONSISTENCY.md`
   - `REVIEW-2-FLOW.md`
   - `REVIEW-3-COHERENCE.md`
   The names identify report slots and do not assign lenses.
6. Overwrite any active report path that already exists by default. Use an
   alternate output location only when the user requests one.

## Check Capabilities

Use up to three isolated subagents concurrently. If fewer than three reviewing
agents are configured, use every available reviewing agent. Stop only when no
reviewing agent is available.

Select the strongest distinct models exposed by the current session when
possible. Use explicit model overrides when they are available. Use the
inherited primary model as a fallback by omitting the model override. Model
diversity is preferred but must not reduce the number of available reviewers.

## Delegate Independent Reviews

Start every available reviewer, up to three, concurrently. Assign a selected
model and report path to each reviewer. Set explicit model overrides where
supported and use inheritance for the primary-model fallback. Give them the
same target and shared review contract. Do not expose one reviewer's prompt,
work, or report to another reviewer before all reviewers finish.

Use this shared contract:

- Read the complete target relevant to the requested scope.
- Assess the target through all three review lenses listed below.
- Cite exact locations, headings, or wording for every finding.
- Classify severity as `high`, `medium`, or `low`.
- Explain the reader or execution impact.
- Propose a concrete revision.
- Include separate sections for `Findings`, `Strengths`, and `Open questions`.
- Write the complete review only to the assigned report path.
- Do not edit the target or any sibling report.

Require every reviewer to apply these lenses:

1. **Internal consistency** — contradictions, terminology, assumptions, scope,
   and agreement between sections.
2. **Narrative flow** — ordering, transitions, clarity, repetition, and whether
   a reader can safely follow or execute the document.
3. **End-to-end coherence** — whether the proposed steps produce the stated
   outcome, including dependencies, edge cases, failure handling, and rollout
   gaps.

## Verify the Reports

Wait for all reviewers to finish. Verify that every active report exists, is
readable, is non-empty, and contains `Findings`, `Strengths`, and
`Open questions`.

Do not claim the ensemble is complete or begin synthesis unless every active
report passes verification. If a reviewer fails, report which artifact is
missing or incomplete and preserve completed reports for diagnosis.

## Synthesize

Read every active report yourself and produce a reconciled synthesis for the
user:

1. Lead with the highest-confidence, highest-impact findings.
2. Merge duplicate findings instead of repeating them.
3. Distinguish consensus findings from single-reviewer observations.
4. Explain meaningful reviewer disagreements and the evidence supporting each
   side.
5. Preserve strengths that should survive revision.
6. End with a prioritized revision sequence.

In the final response, link every generated report, identify the target
reviewed, state that the target was not changed, and state that every generated
report was successfully read. Do not imply that findings were implemented
unless the user separately requested edits.
