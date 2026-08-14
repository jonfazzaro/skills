---
name: targeted-mutation-testing
description: Use when a changed production class or component has branching, lifecycle or asynchronous behavior, guards, error recovery, authentication, serialization, or an external-integration seam and needs file-scoped mutation testing before completion.
---

## Context marker

🧬

When the skill activates, begin the first commentary update with `🧬` and a concise `Using targeted-mutation-testing ...` announcement. Do not repeat the marker on later updates unless another skill activates.

# Targeted Mutation Testing

Run mutation testing for one changed production class or component and its dedicated specifications. This is a completion check for meaningful behavior, not a package- or repository-wide quality metric.

## Scope Contract

Require one explicit production target and its dedicated specification files. Mutate only that file. Run only its dedicated specifications.

Do not use directory globs, whole-project mutation commands, unrelated tests, or a coverage percentage as a goal. Do not add mock-call assertions simply to kill a mutant.

Skip only a pure rename, formatting-only change, generated code, or a user-approved exception. Record the reason.

## Workflow

1. Run the dedicated specifications first.
2. Run Stryker against exactly the target and its specs. For example:

```bash
npx stryker run \
  --mutate src/path/ChangedThing.ts \
  --testFiles src/path/ChangedThing.test.unit.ts \
  --testRunner vitest \
  --coverageAnalysis off
```

3. If the target run is too slow or unreliable, divide only that file into explicit mutation ranges. Preserve the complete range accounting; never describe a sampled range as a full run.
4. Classify every result:
   - **Killed**: record it.
   - **Actionable survivor**: add one state- or output-based specification for intended behavior, using Nullables for I/O.
   - **No coverage**: add a specification only when the uncovered behavior is part of the target's intended contract.
   - **Static/tool limitation, timeout, or intentional survivor**: record the exact mutant and reason; ask Jon when the disposition changes scope or contract.
5. For each new specification that starts green, perform a safe sensitivity probe and restore it before continuing.
6. Re-run the affected mutation range and the dedicated specifications. Run the broader relevant suite before committing.

## Completion Record

Report the target, exact commands, totals for killed/survived/no-coverage/timeouts, and the disposition of every non-killed mutant. State explicitly whether the target was fully mutation-complete or limited.

## Common Mistakes

- Broadening a target run to a module or project.
- Treating all surviving mutants as bugs.
- Testing private calls or mocks rather than observable behavior.
- Hiding incomplete ranges behind an aggregate score.
