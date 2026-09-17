# Continuous Specification

Read this when a production-code change alters executable behavior. The
[Continuous Specification skill](../continuous-specification/SKILL.md) is the
source of truth for the complete workflow.

Establish one observable behavior at a time: set an unmet expectation, meet it
with the smallest behavior that satisfies it, minimize the implementation, make
a green checkpoint commit, then improve design while behavior remains green.
Plan the complete behavior list before implementation and keep unimplemented
items visible until they are completed or explicitly deferred.

Use [scoped verification](scoped-verification.md) for the focused and broader
checks, [observable specifications](observable-specifications.md) for assertion
design, and [safe green-checkpoint commits](safe-commits.md) for staging and
committing. Use [Nullable infrastructure wrappers](nullable-infrastructure.md)
when external I/O needs a controllable seam.

Typical checkpoints are one behavior commit at a time, followed by separate
behavior-preserving design commits. Do not commit an unmet expectation.
