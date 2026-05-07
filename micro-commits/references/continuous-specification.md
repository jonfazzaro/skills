# Continuous Specification

Continuous Specification is a code design technique where each behavior is specified before it is implemented. Code is written in a tight cycle: set an expectation (red), meet it with minimal code (green), then design/refactor while staying green.

Micro-commits integrate naturally with this cycle — every green run is a commit opportunity, and every design step is its own commit.

## Integration points

- Commit after **step 12** (all expectations met, green): `feat: <expectation description>`
- Commit after **each design change** in step 14: `design: <what changed>`
- Never commit during the red phase (unmet expectation)

## Skill

Load the `continuous-specification` skill for the full process.
