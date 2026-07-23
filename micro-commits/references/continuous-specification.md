# Continuous Specification

Continuous Specification is a code design technique where each behavior is specified before it is implemented. Code is written in a tight cycle: set an expectation (red), meet it with minimal code (green), then design/refactor while staying green.

Micro-commits are part of this cycle: commit each expectation after semantic minimization and the broader relevant suite are green, then commit each validated design step separately.

## Integration points

- Commit each new behavior at its green checkpoint: `feat: <expectation description>`
- Commit each corrected behavior at its green checkpoint: `fix: <behavior corrected>`
- Commit each design change separately: `design: <what changed>`
- Never commit during the red phase (unmet expectation)
- Stage only owned changes and inspect the staged diff before committing

## Skill

Load the `continuous-specification` skill for the full process.
