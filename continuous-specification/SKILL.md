---
name: continuous-specification
description: Use whenever a production-code change alters executable behavior—features, fixes, integrations, migrations, scripts, performance work, or behavior-changing refactors. Establish behavior through small executable expectations and green checkpoints.
---

## Context marker

🔴🟢🌀

When the skill activates, begin the first commentary update with `🔴🟢🌀` and a concise `Using continuous-specification ...` announcement. Do not repeat the marker on later updates unless another skill activates.

# Continuous Specification

Use short feedback loops so design emerges from executable behavior rather than speculation:

```
Set → Meet → Minimize → Commit → Design
```

**REQUIRED SUB-SKILL:** Use `specification-structure` whenever adding or editing a behavior specification.

Keep user requirements authoritative throughout the loop. Treat executable expectations as the authority for implemented behavior and planned `[EXPECT]` items as the completeness ledger. A green suite proves neither that a new expectation is sensitive nor that every requested behavior is complete.

## Scope

Apply this workflow to every behavior-changing production edit, including a fix inside an otherwise non-behavioral task. Do not force it onto documentation, formatting, generated output, or a demonstrably behavior-preserving rename or move. For an exploratory spike, confirm that the code is disposable; start this workflow before turning spike code into maintained production code.

If the user explicitly asks to skip specifications, obey and state what verification will replace them. Never use "spike," "trivial," or "already covered" as an unspoken escape hatch.

Follow repository-native language, framework, naming, and file-layout conventions. Use Continuous Specification vocabulary in process updates, but do not rename established `test`, `assert`, or framework constructs merely to impose this vocabulary.

## Vocabulary

| CS term | Common test term |
|---|---|
| Expectation | Test |
| Specification | Test suite or file |
| Set, unmet | Red, failing |
| Meet | Green, passing |
| Design | Refactor |
| Establish, Execute, Expect | Arrange, Act, Assert |

## Core Rules

1. Set one independently meaningful behavior at a time.
2. State the expected failure before running the expectation.
3. Observe distinguishing red evidence before adding production behavior.
4. Add only enough production code to meet current executable expectations.
5. Minimize every behavior-changing production edit with targeted semantic mutants.
6. Commit each logical change as soon as the relevant expectations are green.
7. Design only while all relevant expectations are green.
8. Specify observable responses or state, not collaborator calls or private implementation.
9. Preserve required documentation, safety comments, licenses, and repository conventions. Prefer expressive code over redundant production comments.
10. Keep unrelated user changes out of test mutations, restoration, staging, and commits.
11. Push back when requirements are contradictory, unsafe, or too ambiguous to specify.

## Specification Planning

Before changing production behavior, write the complete ordered expectation list as single-line comments in the target specification file. Prefix every item exactly with `[EXPECT] `:

```text
[EXPECT] Zero plus a number equals that number
[EXPECT] Add two positive numbers
[EXPECT] Add two negative numbers
[EXPECT] Adding opposite numbers returns zero
```

If the target specification file is not yet known, draft the list in the working plan, then move it into the specification file before entering Set. Do not leave the only copy in transient commentary.

Keep each item to one independently meaningful behavior and order the list from the simplest case to more complex cases. Each requested behavior must map to at least one `[EXPECT]` item or be explicitly recorded as out of scope, deferred by the user, or blocked.

Walk through [ZOMBIES](references/zombies.md):

- Zero or empty cases
- One-item or first-transition cases
- Many-item cases
- Boundary transitions
- Interface clarity
- Exceptions and recovery

Identify two commands before implementation:

- The narrowest command that demonstrates the current expectation.
- The broader relevant suite that detects regressions in the affected behavior.

Use the narrow command during the short loop. Run the broader relevant suite before a green checkpoint and at task completion. If either command is unavailable, investigate before editing; if it remains unavailable, disclose the verification gap and do not claim executable proof or create a green-checkpoint commit.

Treat the remaining `[EXPECT]` comments as the completeness ledger. During Set, replace only the next comment with its executable expectation. Do not delete, reorder, or silently rewrite the remaining items to make the task appear complete.

## Keep an Evidence Ledger

Maintain a concise ledger during the work and reproduce its completed fields in the final report. Progress updates are transient and do not satisfy the final evidence requirement. Do not rely on phase labels or emojis as proof.

For each expectation record:

```
Expectation: <behavior>
Red: <command> → <distinguishing failure>
Green: <command> → <pass>
Mutants: <category: outcome; category: outcome; ...>
Broader suite: <command> → <result>
Commit: <hash and subject>
Requirements remaining: <items or none>
```

Record exact commands and meaningful outcomes; do not paste routine full logs. Mark an assertion `not run`, `not applicable`, or `deferred: <reason>` rather than implying it occurred. Never say red, green, mutation-complete, requirement-complete, or committed without the corresponding evidence.

Name the exact red or sensitivity command, even when it is identical to the green command. Classify every mutation catalogue category; group categories only when they share the same outcome and reason. State the narrow and broader commands separately, or state explicitly that one command served both roles. Always include `Requirements remaining: none` or list the unresolved items.

## Set an Unmet Expectation

1. Replace the next `[EXPECT]` item with one framework-native executable expectation.
2. Use the repository's established structure and naming. Use `specification-structure` for every behavior specification; when no convention exists, apply its given/when/then hierarchy alongside a clear Establish–Execute–Expect shape with one execution and one logical assertion.
3. Trace the expected value before writing the assertion.
4. Predict the distinguishing failure.
5. Run the narrow command and observe the expectation fail for the predicted reason.

Let a missing symbol produce a compilation failure when that arises naturally. Do not manufacture a compile failure in a dynamic language, for an existing interface, or when a behavioral failure is more direct. If compilation must be restored before the expectation can execute, add only the declaration or wiring needed to reach a behavioral failure; do not add the requested behavior.

### When a New Expectation Starts Green

Do not silently accept or skip a newly added expectation that starts green.

1. Check for a duplicate or tautological expectation, incorrect setup, or the wrong execution path.
2. If the behavior is genuinely pre-existing, perform a temporary sensitivity probe by changing the relevant production behavior in the smallest safe way.
3. Predict and observe the new expectation fail, then restore only the probe and rerun it green.
4. Record `Sensitivity` in place of `Red` in the evidence ledger.

Do not probe external systems, generated files, unrelated user edits, destructive paths, or behavior that cannot be safely restored. In those cases, explain why sensitivity could not be demonstrated and do not claim a red cycle. Keep a non-duplicative expectation only when it adds useful executable coverage.

## Meet the Expectation

1. Add the minimum production behavior needed by the executable expectations. Prefer a constant or special case when it is structurally simplest.
2. Do not implement future `[EXPECT]` items, but do not lose them: they remain required for task completeness.
3. Predict the narrow and broader results.
4. Run the narrow expectation green.
5. Enter Minimize even when the implementation appears minimal.

## Minimize with Semantic Mutants

Say `🧬 Starting semantic minimization`.

**REQUIRED FOLLOW-UP:** Use `targeted-mutation-testing` before the final green checkpoint when the changed production class or component has branching, lifecycle or asynchronous behavior, guards, error recovery, authentication, serialization, or an external-integration seam. Its scope is exactly that changed file and its dedicated specifications; never broaden it to a package or repository run without the user's request.

Challenge behavior-changing production edits made since the expectation was set. If production behavior did not change, record `Mutants: not applicable—no production behavior changed` and continue to the green checkpoint.

### Simplicity Hierarchy

Compare passing candidates lexicographically at the first level where they differ:

1. Meet the executable expectations.
2. Select fewer independent outcomes; count lookup entries and disguised special cases as branches.
3. Duplicate less knowledge.
4. Perform fewer state changes and side effects.
5. Use fewer operations and dependencies.
6. Provide less unspecified capability when otherwise structurally comparable.

A generalized implementation is justified when it wins this hierarchy. One addition example may justify a constant; enough examples may make one addition rule simpler than several hard-coded cases.

### Mutation Procedure

1. Inspect the owned production diff and classify every catalogue category as `applied`, `not applicable: <reason>`, or `deferred: <reason>`.
2. Apply one relevant less-capable mutant at a time. Never merely reason about an `applied` mutant.
3. Predict whether it should survive.
4. Run the narrow command.
5. Restore a killed mutant without overwriting unrelated or pre-existing changes.
6. If it survives, keep it only when it wins the simplicity hierarchy; otherwise name the decisive level.
7. Restart the applicability scan after keeping a mutant.
8. Run the broader relevant suite after the final candidate.

Use a fast narrow command for each mutant and the broader suite at the checkpoint; do not rerun an expensive full repository suite for every mutation unless risk or repository policy demands it. For a slow, flaky, destructive, or externally dependent narrow check, prioritize the mutants most likely to expose over-implementation and explicitly defer the rest. Never describe sampled or deferred mutation work as exhaustive.

Keep a concise category/outcome table in the evidence ledger. Do not retain temporary mutant files or probe changes.

### Mutation Catalogue

- **Deletion:** remove an added expression, statement, branch, helper, or alternative.
- **Hard-coding:** replace derived output with the observed expected value.
- **Input erasure:** ignore one or more inputs.
- **Case collapse:** make distinct branches or outcomes behave identically.
- **Cardinality collapse:** retain zero- or one-item behavior while removing many-item behavior.
- **Boundary removal:** eliminate behavior at a transition or limit.
- **State elision:** skip or simplify a state change.
- **Error removal:** remove a guard or failure path.
- **Alternative reduction:** remove regex alternatives, union members, switch arms, or accepted variants.
- **Dependency neutralization:** replace a collaborator result or side effect with a fixed value or no-op.

Do not add an expectation merely to preserve overeager implementation. Add it only when it represents intended behavior from the request or an explicit scope decision.

Say `🧬 Semantic minimization complete` only when every category is accounted for and no mutation is deferred. Otherwise say `🧬 Semantic minimization limited` and name the deferred coverage.

## Commit the Green Checkpoint

After minimization and the passing broader relevant suite, commit the expectation and its minimal production behavior together. Do not batch multiple expectations.

1. Inspect repository status.
2. Stage only the files or hunks owned by the current expectation.
3. Inspect the staged diff and remove unrelated user work from it.
4. Commit new behavior as `feat: <expectation description>` or a correction as `fix: <behavior corrected>`, unless repository conventions require another format.
5. Record the commit hash and subject in the evidence ledger.

Never commit red code, a failed broader suite, temporary mutants, sensitivity probes, or unrelated user changes. If Git is unavailable or the commit fails, preserve the green worktree, report the blocker, and do not pretend the checkpoint was committed.

## Design While Green

Say `🌀 Starting bounded design stage`.

Limit design to production and specification code touched by the current expectation plus the smallest supporting concepts needed to express it. Do not use a green checkpoint as authority to clean up unrelated code.

1. Identify an immediate design issue introduced, exposed, or obstructing the current behavior.
2. Make one behavior-preserving change at a time.
3. Run the narrow expectation and broader relevant suite after each change.
4. Minimize any design diff that changes behavior, calculations, parsing, state, dependencies, side effects, or accepted inputs.
5. Stage only the validated design change and commit it as `design: <change>`.
6. Record unrelated opportunities for the user instead of implementing them.

End Design when the current behavior is expressed clearly and no in-scope issue justifies another change. Say `🌀 Design complete` or `🌀 No in-scope design change needed`.

## Complete the Cycle

Verify:

- The expectation has red or sensitivity evidence, or the verification gap is explicit.
- The narrow expectation and broader relevant suite are green.
- Every mutation category is accounted for.
- Every surviving mutant that wins the hierarchy is implemented.
- The green behavior and each design change have their own commits.
- Design stayed within scope and introduced no unjustified behavior.

Return to the next `[EXPECT]` item.

## Know When to Stop

Distinguish these states:

- **Green:** currently executable expectations pass.
- **Behavior complete:** every requested `[EXPECT]` item is executable and green.
- **Task complete:** behavior is complete, broader verification passes, green changes are committed, and no required delivery work remains.

Stop as complete only in the third state. If the user stops the task while ledger items remain, stop working but report the exact incomplete, deferred, or blocked items; never summarize the feature as complete.

Before the normal completion summary, check that its evidence includes the expectation, exact red or sensitivity command and outcome, exact green command and outcome, every mutation category classification, broader-suite result, commit hash and subject, and requirements remaining. If any field is unavailable, disclose it instead of omitting it.

Run one bounded final evaluation over the requested behavior and touched files:

1. Reconcile every user requirement with the ledger.
2. Check ZOMBIES for a missed independently intended behavior.
3. Recheck mutation classifications for the feature diff.
4. Check one final time for an in-scope design problem.
5. Run the broader relevant suite.
6. Verify the worktree contains no uncommitted owned change.

Return to the appropriate phase for a concrete gap. Do not repeat open-ended design reviews after the bounded pass.

## Infrastructure and Independent Review

Avoid interaction-based mocking. Use [Nullable infrastructure wrappers](references/nullables.md) when external I/O requires a controllable seam, unless repository architecture or the user requires another behavioral testing technique.

Keep one agent responsible for the normal short loop. Consider independent review only when the user requests it, behavior is high-risk, requirements remain ambiguous, minimization cannot resolve a choice, or repeated cycles fail to stabilize. Treat review as deliberate escalation, not a substitute for executable evidence.
