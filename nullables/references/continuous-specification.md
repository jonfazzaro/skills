# Continuous Specification

Continuous Specification is a code design technique where each behavior is specified before it is implemented. Code is written in a tight cycle: set an expectation (red), meet it with minimal code (green), then design/refactor while staying green.

Nullables are the preferred isolation strategy within CS — they replace mocking frameworks and keep specifications state-based and sociable.

## Relationship to Nullables

- CS's Specification Design Rules prohibit mocking frameworks; Nullables are the alternative
- Infrastructure wrappers are built incrementally as specifications demand them — don't create wrappers speculatively
- The `nullables` skill is loaded from within CS when specifications involve infrastructure dependencies

## Skill

Load the `continuous-specification` skill for the full process.
