---
name: ensemble-review
description: Runs three isolated, concurrent reviews with distinct available models, then synthesizes their findings into a prioritized revision plan. Use when the user explicitly asks for an ensemble review, independent multi-model review, or cross-check of a document, proposal, specification, plan, or runbook for internal consistency, narrative flow, and end-to-end coherence.
---

# Ensemble Review

Review one target through three independent lenses, preserve each review as a
separate artifact, and reconcile them into one actionable synthesis.

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
6. Check whether any report path already exists. Do not overwrite an existing
   report without the user's explicit approval; ask for replacement approval or
   an alternate output location.

## Check Capabilities

Require all of the following before delegating:

- Three isolated subagents.
- Concurrent execution.
- Three distinct available model selections.

Select the strongest three distinct models exposed by the current session. Use
explicit model overrides when they are available. If a preferred model is not
available, choose the next strongest available distinct model. If fewer than
three explicit overrides are available, use the inherited primary model as the
next fallback when it is distinct from the selected overrides. Omit the model
override for that reviewer so it inherits the primary model.

Stop and name the missing capability only when these fallbacks still cannot
produce three distinct model selections. Do not silently reduce the reviewer
count, reuse a model, run sequentially, or create partial report files.

## Delegate Independent Reviews

Start all three reviewers concurrently. Assign a distinct selected model and
report path to each reviewer. Set explicit model overrides where supported and
use inheritance only for the primary-model fallback. Give them the same target
and shared review contract, followed by one complementary lens. Do not expose
one reviewer's prompt, work, or report to another reviewer before all three
finish.

Use this shared contract:

- Read the complete target relevant to the requested scope.
- Assess internal consistency and reader flow while emphasizing the assigned
  lens.
- Cite exact locations, headings, or wording for every finding.
- Classify severity as `high`, `medium`, or `low`.
- Explain the reader or execution impact.
- Propose a concrete revision.
- Include separate sections for `Findings`, `Strengths`, and `Open questions`.
- Write the complete review only to the assigned report path.
- Do not edit the target or either sibling report.

Assign these lenses:

1. **Internal consistency** — contradictions, terminology, assumptions, scope,
   and agreement between sections.
2. **Narrative flow** — ordering, transitions, clarity, repetition, and whether
   a reader can safely follow or execute the document.
3. **End-to-end coherence** — whether the proposed steps produce the stated
   outcome, including dependencies, edge cases, failure handling, and rollout
   gaps.

## Verify the Reports

Wait for all three reviewers to finish. Verify that every report exists, is
readable, is non-empty, and contains `Findings`, `Strengths`, and
`Open questions`.

Do not claim the ensemble is complete or begin synthesis unless all three
reports pass verification. If a reviewer fails, report which artifact is
missing or incomplete and preserve completed reports for diagnosis.

## Synthesize

Read all three reports yourself and produce a reconciled synthesis for the
user:

1. Lead with the highest-confidence, highest-impact findings.
2. Merge duplicate findings instead of repeating them.
3. Distinguish consensus findings from single-reviewer observations.
4. Explain meaningful reviewer disagreements and the evidence supporting each
   side.
5. Preserve strengths that should survive revision.
6. End with a prioritized revision sequence.

In the final response, link all three reports, identify the target reviewed,
state that the target was not changed, and state that all three reports were
successfully read. Do not imply that findings were implemented unless the user
separately requested edits.
