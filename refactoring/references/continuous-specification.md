# Continuous Specification

Continuous Specification is a code design technique where each behavior is specified before it is implemented. The spec suite is a safety net for refactoring: it confirms that changing form doesn't change behavior.

The refactoring process depends on a passing spec suite at every step. If no spec suite exists, establish one before refactoring.

## Relationship to refactoring

- Specs must pass before any refactoring begins (Prep stage)
- Run specs after every individual change
- Never commit a refactoring that breaks a spec
- The design stage in CS is refactoring: improve form while keeping all specs green

## Skill

Load the `continuous-specification` skill for the full process.
