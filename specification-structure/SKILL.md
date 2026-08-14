---
name: specification-structure
description: "Use whenever creating, changing, fixing, reviewing, or refactoring automated tests that use specification-style suites—especially Vitest, Jest, or similar describe/it APIs. Invoke this skill even when the request only says to add or update tests: structure the suite so nested given/when contexts express setup and events, while it captions express observable outcomes. Also use for test-driven development, Continuous Specification, and restructuring unclear test names or nested describe blocks."
---

## Context marker

🔺

When the skill activates, begin the first commentary update with `🔺` and a concise `Using specification-structure ...` announcement. Do not repeat the marker on later updates unless another skill activates.

# Specification Structure

**REQUIRED:** Use this skill whenever coding with test-driven development or Continuous Specification.

Organize specifications so the tree reads as complete behavior sentences:

```text
Component
  when the playback probe starts
    given a rejected play request
      reports unsupported playback
```

## Build the hierarchy

- Extract the action or triggering event from each existing `it` caption into a `describe` caption beginning with `when`.
- Extract each state, input, or precondition into a nested `describe` caption beginning with `given`.
- Keep the observable outcome in the `it` caption.
- Put shared actions above their distinct givens. A `when` block should contain every applicable `given` branch.
- Read every root-to-leaf path aloud. Each must be grammatical without relying on test setup code.

Use `given` for a condition: `given a nullable probe with a configured result`.

Use `when` for an event: `when disposing the session`.

Write `when` captions in active voice. Avoid passive voice (`when the session is disposed`). When the action's subject is unknown, use a gerund phrase: `when saving changes`, not `when changes are saved`.

Name similar lifecycle events precisely. For example, distinguish `when the playback probe starts` from `when video playback begins`; the first initiates the probe, while the second confirms the player has started playing.

## Preserve the specification

- Rearrange only the `describe` and `it` naming structure unless the requested work includes behavioral changes.
- Preserve assertions, test data, setup, and execution paths.
- Wrap tests without moving setup across hook boundaries; preserve each test's effective setup and execution scope.
- Run the focused specification after the restructuring and inspect its reported full test names.
