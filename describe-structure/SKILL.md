---
name: describe-structure
description: "Use whenever editing a test or specification file, including *.test.* and *.spec.*. Use alongside Continuous Specification or TDD. In suites that already use describe, structure new or changed tests with readable when/given contexts and outcome-focused it captions; preserve flat suites without describe."
---

## Context marker

🔺

When the skill activates, begin the first commentary update with `🔺` and a concise `Using describe-structure ...` announcement. Do not repeat the marker on later updates unless another skill activates.

# Describe Structure

**REQUIRED:** Use this skill whenever coding with test-driven development or Continuous Specification.

When a test file already has a root `describe`, organize the new or changed specifications so the affected tree reads as complete behavior sentences. Preserve a flat test file with no `describe` structure.

```text
Component
  when the playback probe starts
    given a rejected play request
      reports unsupported playback
```

## Build the hierarchy

- Extract the action or triggering event from each existing `it` caption into a `describe` caption beginning with `when`.
- Extract each state, input, or precondition into a nested `describe` caption beginning with `given`.
- Write every `describe` and `it` caption for people, in plain-English domain language; try not to use a code function, method, or API name as the caption.
- Keep the observable outcome in the `it` caption.
- Put shared actions above their distinct givens. A `when` block should contain every applicable `given` branch.
- Read every root-to-leaf path aloud. Each must be grammatical without relying on test setup code.

Use `given` for a condition: `given a nullable probe with a configured result`.

Use `when` for an event: `when disposing the session`.

Write `when` captions in active voice. Avoid passive voice (`when the session is disposed`). When the action's subject is unknown, use a gerund phrase: `when saving changes`, not `when changes are saved`.

Name similar lifecycle events precisely. For example, distinguish `when the playback probe starts` from `when video playback begins`; the first initiates the probe, while the second confirms the player has started playing.

## Preserve the specification

- Limit restructuring to the new or changed tests unless the request explicitly includes broader test cleanup.
- Rearrange only the `describe` and `it` naming structure unless the requested work includes behavioral changes.
- Preserve assertions, test data, setup, and execution paths.
- Wrap tests without moving setup across hook boundaries; preserve each test's effective setup and execution scope.
- Run the focused specification after the restructuring and inspect its reported full test names.
