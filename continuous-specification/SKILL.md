---
name: continuous-specification
description: Continuous Specification process for writing code by defining specifications before implementing. Use whenever adding any new code, unless the user explicitly asks to skip specs or the code is exploratory/spike. Also known as TDD, but framed around specification rather than testing. Supports ping-pong mode for adversarial pairing with two subagents alternating set/meet/design turns.
---

# Continuous Specification

Continuous Specification is a code design technique. Design emerges from usage, not speculation. Short feedback loops let you course-correct immediately. The resulting architecture is specifiable by design, not retrofitted. We are not trying to rush towards feature completion — it's important that the code is correct and well-designed. Be thorough and only add what specifications demand.

When starting, announce: "Using Continuous Specification skill in mode: [auto|human|ping-pong]"

MODE (user specifies, default: auto)

- auto: DO NOT ask for confirmation or approval. Proceed through all steps without stopping.
- human: wait for confirmation at key points
- ping-pong: run the adversarial ping-pong pairing process using two subagents (see Ping-Pong Mode section)

STARTER_CHARACTER = ❗️ when setting an expectation, ✅ when met (green), 🌀 when designing, always followed by a space

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
   + Be a stickler on this one! Try to "thwart" the expectation by hard-coding or otherwise implementing *only* what's being expected, not the stated intent of the expectation. This should then cause the expectation to be written better, to be more explicit, and cover more cases.
6. No comments in production code — keep it clean unless specifically asked.
7. Run all expectations in the specification every time — not just the one(s) you're working on.
8. Design at the first opportunity when all expectations are met.
    + Do *not* design/refactor while there are any unmet expectations.
    + Do *not* wait until the end of the implementation to design/refactor. Look for a chance every time expectations are met.
9. Specify behavior, not implementation — check responses or state, not method calls.
10. Push back when something seems wrong or unclear.

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
4. If MODE is human, wait for confirmation after planning the specification.

## Implementation Phase

1. Replace the next `[EXPECT]` comment directly with a failing expectation. No intermediate markers.
2. Structure expectations in Establish-Execute-Expect with empty lines separating each section (do not add section labels as comments).
3. Name expectations descriptively in natural language. Avoid underscores unless the target language requires them.
4. Use a `describe`/`it` nested structure:
   - `Given` blocks describe preconditions or data state.
   - `When` blocks describe the event or user action.
   - Nest `Given` blocks inside `When` blocks to express the same action under different conditions.
   - Start expectation names with `it should` (or language equivalent).
   - Expectation names should read as complete sentences when combined with their describe blocks.
5. Think through the expected value BEFORE writing the expectation. Trace the logic step by step.
6. Predict what will fail.
7. Run specifications, see compilation error (if the expectation targets something new).
8. Add minimal code to compile.
9. Predict the expectation failure.
10. Run specifications, see expectation failure.
11. Add minimal code to meet the expectation.
12. Predict whether all expectations will be met and why. Run specifications, see green.
13. Simplify. For each line/expression just added, ask: "Does a failing expectation require this?"
    - If no expectation requires it, delete it — or if it's necessary, add an `[EXPECT]` comment for it.
    - Run specifications after each simplification.
    - Repeat until every line is justified by an expectation.
14. Design.
    - Reflect on the domain: is there a missing concept that would make the code more expressive? An object waiting to be extracted? A better way to model the problem?
    - Introduce domain concepts (new abstractions) only — add NO new behavior. All expectations must still be met, and no new code should be added that doesn't have an expectation.
    - Think about improvements to expressiveness, clarity, simplicity.
    - Say `🌀 Starting design stage` and list planned changes.
    - Implement one at a time, run specifications after each.
    - When done (or if none needed), say "🌀 Design complete".
15. Go to step 1 for the next `[EXPECT]` comment. Repeat until all planned expectations are met.

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
3. Is anything still hardcoded in the code that shouldn't be? Fix it, analyze gaps, and go back to earlier stages if needed.
4. Analyze code expressiveness and quality. If there's anything to improve, go to design phase.

## Ping-Pong Mode

STARTER_CHARACTER = 🏓

Two subagents take turns setting unmet expectations, meeting them, and designing — with the orchestrator acting as referee between each turn. The adversarial posture is the point: the design step strips the implementation to only what the expectation required, exposing over-engineering and forcing smaller steps.

When starting, announce: "🏓 Using Ping-Pong Pairing"

### The Cycle

```
A: set an unmet expectation
B: meet it
A: design
B: set an unmet expectation
A: meet it
B: design
...
```

The 3-step asymmetry means roles rotate — no one writes, passes, and designs in the same position twice in a row.

### Setup

Read the codebase to understand what is being built. Identify the implementation stub, the existing test structure and style, and any domain rules. Confirm domain rules with the user before starting.

### Person A sets an unmet expectation

Spawn a subagent as Person A with the current implementation, a summary of what has been specified so far, and the instruction to set the SMALLEST possible unmet expectation.

If the expectation is met trivially, keep it as a valid specification, skip the implementation turn, and continue to the next expectation candidate.

### Person B meets the expectation

Spawn a subagent as Person B with the unmet expectation, the current implementation, and the instruction to write the MINIMUM code to meet the expectation — no more.

### Person A designs

Spawn a subagent as Person A with the met expectations and the instruction to: (1) strip all code not required to meet the current expectations, then (2) clean the design of what remains.

The adversarial move: stripping unexercised guards and branches highlights how much smaller each step should be.

### Referee between every handoff

Before passing to the next subagent, read the output and verify expectations are met or unmet as expected. Flag over-implementation (B added unexercised branches) or under-design (A left dead code). Adjust the next prompt if course correction is needed.

### Commit

After each fully-met state: `feat:` commit. After each design pass: `design:` commit. Never commit with unmet expectations.

### Rotate and repeat

Swap roles and continue the cycle until the behavior is fully specified.

### Know when to stop

Stop when no remaining todos drive new unmet expectations, the next expectation requires a scope decision, or the user signals done. Summarize the commit history as the record of what was specified.
