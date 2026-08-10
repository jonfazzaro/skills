# Specification Structure

Use the `specification-structure` skill whenever adding or editing a behavior specification.

Structure behavior specifications so every root-to-leaf path reads as a complete sentence:

```text
Component
  when the playback probe starts
    given a rejected play request
      reports unsupported playback
```

- Put the triggering event in a `when` block.
- Put state, input, or preconditions in a nested `given` block.
- Keep the observable outcome in the leaf expectation.
- Group applicable `given` branches below their shared `when` block.
- Read every root-to-leaf path aloud and make it grammatical without relying on setup code.

## Preserve execution scope

Wrap tests without moving setup across hook boundaries. Preserve each test's effective setup and execution scope, plus its assertions, test data, and execution path.

For the reusable workflow, use the `specification-structure` skill.
