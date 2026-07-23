---
name: continuous-specification
description: Continuous Specification process for writing code by defining specifications before implementing, meeting one expectation at a time, minimizing production code with targeted semantic mutants, and designing only while green. Use whenever adding new code unless the user explicitly asks to skip specifications or the code is exploratory/spike. Also known as TDD, but framed around specification rather than testing.
---

# Continuous Specification

Continuous Specification is a code design technique. Design emerges from usage, not speculation. Short feedback loops let you course-correct immediately. The resulting architecture is specifiable by design, not retrofitted. We are not trying to rush towards feature completion — it's important that the code is correct and well-designed. Be thorough and only add what specifications demand.

When starting, announce: "🏓 Using Continuous Specification skill"

STARTER_CHARACTER = 🏓, ❗️ when setting an expectation, ✅ when met (green), 🧬 when minimizing, 🌀 when designing, always followed by a space

## Vocabulary Bridge

CS terminology is the language of this skill. In codebases where "testing" language is established and not being changed, interpret existing terms using this mapping:

| CS Term    | Test Term                 |
|------------|---------------------------|
| Expectation | Test                      |
| Specification | Test Suite, file          |
| Set, unmet | Red, Fail, make it fail   |
| Meet       | Green, Pass, make it pass |
| Design     | Refactor                  |
| Establish  | Arrange                   |
| Execute    | Act                       |
| Expect     | Assert                    |

**When reading existing code:** translate test language into CS terms mentally — a failing test is a Set expectation (that's still unmet), a passing test suite is a Met Specification, etc.

**When writing new code:** always use CS language regardless of what surrounds it. New expectations, new specifications, and new comments use CS vocabulary.

## Core Rules

1. ALL code changes follow Continuous Specification — feature requests mid-stream are NOT exceptions. Set the expectation first, then code.
2. Set only one expectation at a time — focus on the simplest, lowest-hanging fruit behavior.
    + Only write enough of an expectation to be unmet--do not write more than is needed to fail given the current state of the code.
    + Be a stickler on this one! Expecting too much at once can lead to gaps in the specification.
3. Predict failures — state what we expect to fail before running the spec.
4. Two-step red phase:
   - First: make it fail to compile (class/method doesn't exist)
   - Second: make it compile but fail the expectation (return wrong value)
5. Minimal code to meet the expectation — just enough to make it green.
   + If no expectation requires it, don't write it.
   + Hard-code or otherwise implement *only* what the executable expectations require. Keep the hard-coded version until enough expectations make a generalized version structurally simpler.
   + Future `[EXPECT]` comments provide no implementation authority.
6. No comments in production code — keep it clean unless specifically asked.
7. Run all expectations in the specification every time — not just the one(s) you're working on.
8. Minimize every behavior-changing production edit with targeted semantic mutants before leaving green.
9. Design at the first opportunity when all expectations are met.
    + Do *not* design/refactor while there are any unmet expectations.
    + Do *not* wait until the end of the implementation to design/refactor. Look for a chance every time expectations are met.
10. Specify behavior, not implementation — check responses or state, not method calls.
11. Push back when something seems wrong or unclear.

## Specification Types

- **Code Specifications**: specify isolated components and units. Written in Establish-Execute-Expect structure.
- **User Specifications**: specify critical paths across the whole system. Validate entire system behavior end to end.

Separate these concerns. Keep Code Specifications focused on a single component's public interface. Write User Specifications for critical user journeys.

## Specification Planning

1. Think about what the code you want to write should do.
2. Plan each expectation as a single-line `[EXPECT]` comment.

    Example:

    ```
    [EXPECT] Zero plus a number is equal to that number
    [EXPECT] Add two positive numbers
    [EXPECT] Add two negative numbers
    [EXPECT] Adds a negative and a positive number
    [EXPECT] Division by zero is not allowed
    ...
    ```

3. Check completeness — walk through [ZOMBIES](references/zombies.md) explicitly:
   - Zero/empty cases covered?
   - One item cases covered?
   - Many items cases covered?
   - Boundary transitions covered?
   - Interface clarity verified?
   - Exceptions/errors covered?

## Implementation Phase

One agent owns the complete short feedback loop:

```
Set → Meet → Minimize → Design
```

Do not spawn subagents for the normal cycle. Preserve rigor through executable evidence: observe the expectation fail, meet it minimally, apply semantic mutants, and keep every simplifying mutant that the specification permits.

### Set an Unmet Expectation

1. Replace the next `[EXPECT]` comment directly with a failing expectation. No intermediate markers.
2. Structure in Establish-Execute-Expect with empty lines separating each section (no section label comments).
3. Name expectations descriptively in natural language. Avoid underscores unless the target language requires them.
4. Use a `describe`/`it` nested structure:
   - `Given` blocks describe preconditions or data state.
   - `When` blocks describe the event or user action.
   - Nest `Given` inside `When` to express the same action under different conditions.
   - Start expectation names with `it should` (or language equivalent).
   - Names should read as complete sentences when combined with their describe blocks.
5. Think through the expected value BEFORE writing. Trace the logic step by step.
6. Predict what will fail.
7. Run specifications — see compilation error (if targeting something new).
8. Add minimal code to compile.
9. Predict the expectation failure.
10. Run specifications — see expectation failure.

If existing code already meets the expectation, keep it as a valid specification and skip to the next `[EXPECT]`.

### Meet the Expectation

1. Add the MINIMUM code to meet the expectation — no more. Prefer a constant or special case when that is the simplest implementation of the executable expectations.
2. Predict whether all expectations will be met and why.
3. Run specifications — see green.
4. Enter Minimize even when the implementation already appears minimal.

### Minimize with Semantic Mutants

Say `🧬 Starting semantic minimization`.

Challenge every behavior-changing production edit made since the expectation was set. If production code did not change, report `🧬 No production change; no mutation needed` and continue to Design.

#### Simplicity Hierarchy

Compare implementations lexicographically at the first level where they differ:

1. **Executable expectations are met.** Disqualify any candidate that fails.
2. **Fewer independently selected outcomes.** Prefer no branching over conditionals, lookup tables, pattern arms, or disguised special cases.
3. **Less duplicated knowledge.** Prefer one rule over repeated constants, expressions, or case-specific facts.
4. **Fewer state changes and side effects.** Prefer values and transformations over mutable state, ordering constraints, or extra I/O.
5. **Fewer operations and dependencies.** Prefer a constant over a calculation, a direct calculation over a helper network, and existing concepts over new machinery.
6. **Less unspecified capability.** When structurally comparable, prefer the implementation that does less beyond the executable expectations.

Count semantic cases rather than syntax. A dictionary containing five expected answers has five independently selected outcomes even though it contains no `if`.

A generalized implementation is justified only when it wins this hierarchy. For example, one addition example justifies `return 4`; enough examples may make `return left + right` simpler than an expanding set of hard-coded cases.

#### Mutation Procedure

1. Inspect the changed production code and identify every applicable mutation category below.
2. Apply one less-capable semantic mutant at a time. Do not merely reason about it.
3. Predict whether the mutant should survive and why.
4. Run all expectations in the specification.
5. Reject and restore a mutant if an expectation fails.
6. If all expectations pass, keep the mutant only when it wins the simplicity hierarchy. When rejecting a passing mutant, name the decisive hierarchy level.
7. Restart the applicability scan after keeping a mutant.
8. Continue until no candidate wins. Say `🧬 Semantic minimization complete`.

Do not add an expectation merely to preserve an overeager implementation. Add an expectation only when it represents independently intended behavior. Keep only a concise report of attempted mutants; do not retain mutant files or fixtures.

After minimization, verify all expectations are met and commit the behavior as `feat: <expectation description>`.

#### Mutation Catalogue

Consider each category and apply every relevant one:

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

Treat each alternative in a regex, union type, conditional, lookup, or switch as its own behavior. If only `.` is expected, use `[^.]+\.` rather than `[^.?!]+[.?!]`.

### Design

1. Reflect on the domain: is there a missing concept that would make the code more expressive? An object waiting to be extracted? A better way to model the problem?
2. Introduce domain concepts and improve structure without deliberately adding behavior.
3. Say `🌀 Starting design stage` and list planned changes.
4. Implement one change at a time and run all expectations after each.
5. If a design change alters production behavior, calculations, conditions, parsing, state, dependencies, or accepted inputs, run Minimize against that design diff before continuing. Pure renames, moves, and behavior-preserving extractions require only a specification run.
6. Commit each validated design change as `design: <what changed>`.
7. Say `🌀 Design complete` when done or when no design is needed.

The hierarchy permits a generalized design when it is structurally simpler than narrower passing mutants. It does not permit speculative capability that has no structural justification.

### Complete the Cycle

Verify the following exit criteria:

- The new expectation was observed unmet before production behavior was added.
- All expectations are met.
- Every applicable semantic mutation category was exercised against behavior-changing production edits.
- Every surviving mutant that wins the hierarchy is now the implementation.
- Design introduced no unjustified capability.

Never commit with unmet expectations.

### Know When to Stop

Stop when no remaining `[EXPECT]` comments drive new unmet expectations, the next expectation requires a scope decision, or the user signals done. Summarize the commit history as the record of what was specified.

## Optional Independent Review

Do not use subagents by default, including during Final Evaluation. Consider an independent review only when the user requests one or when requirements are ambiguous, behavior is high-risk, the hierarchy cannot resolve a design choice, or the cycle repeatedly fails to reach a stable minimum. Treat this as deliberate escalation, not part of the normal workflow.

## Specification Design Rules

- Never use mocking frameworks. Use [Nullable infrastructure wrappers](references/nullables.md) to isolate dependencies.
- One execution (act) per expectation.
- One logical assertion per expectation to clarify failures.
- Keep specification setup DRY but explicit.
- Create builders for complex object establishment.
- Write failing expectations before fixing bugs to prevent regression.
- Specify unhappy paths and edge cases, not just the happy path.
- Apply FIRST principles: Fast, Isolated, Repeatable, Self-verifying, Timely.

## Final Evaluation

1. Analyze the code written and think about expectations that might have been missed.
2. If there are any gaps, start the process for the missing expectations from the beginning — starting from `[EXPECT]` comments then following the process until done.
3. Re-run the mutation applicability scan over the feature's behavior-changing production edits. Return to Minimize for any category that was missed.
4. Keep hard-coding when it remains simplest under the hierarchy. Generalize only when the executable expectations make the generalized form structurally simpler.
5. Analyze code expressiveness and quality. If anything can improve, return to Design and minimize any resulting behavior-changing diff.
