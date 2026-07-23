---
name: refactoring
description: Refactoring process for improving code form without changing behavior. Use when the user asks to refactor or when an in-scope readability or design problem obstructs requested work. Keep changes bounded to the requested or touched scope, preserve executable behavior, and commit each green design change.
---

# Refactoring: Designing Your Code

STARTER_CHARACTER = 🟣

When starting, announce: "🟣 Using REFACTORING skill".

Refactoring is how you design your code. It is not cleanup after the fact — it is an active design activity that shapes the code into a clear expression of its intent.

Work autonomously as much as possible. Start with the simplest thing or file and proceed to the more complex ones.

## Stages

1. Prep
2. Main Refactoring
3. Final Evaluation
4. Summary

## Specification Code Policy

Do not change specification code during refactoring, except:
- Renames that follow production code renames (imports, function calls)
- Import path updates if something moved

Never change specification assertions, specification data, or specification logic.

## 1. Prep

- Determine scope: use specified files, or identify related files (imports, shared functionality), or ask user
- Add files in scope to todo list
- Find or create ./spec.sh, verify all specs pass
- Review comments in scope; preserve required documentation, safety notes, licenses, and useful rationale, and remove only comments made redundant by clearer code

## 2. Main Refactoring

### Code Style

Prefer self-explanatory, readable code over comments.

- Use functional helper methods for clarity
- Remove dead code
- Extract paragraphs into methods
- Use better variable names
- Remove unused imports
- Remove unhelpful local variables
- Look for opportunities to simplify
- Use domain language - name things for what they ARE, not how they're implemented
- Keep consistent abstraction levels within methods

### Process

For each refactor:
1. Ensure all specs pass
2. Choose and perform the simplest possible refactoring (one at a time)
3. Ensure all specs pass after the change
4. Commit each successful refactor as `design: <refactoring>` unless repository conventions require another format.
   Prefer small granular commits. If applying the same refactoring pattern to multiple locations, change one location at a time and commit each separately.
5. Provide a status update after each refactor

## 3. Final Evaluation

When you see no more in-scope refactoring opportunities, say "🔍 Entering final evaluation."

Shift focus from implementation to one bounded critical pass over the requested and touched scope.

Re-read Code Style guidelines. Look at each file in scope. Consider blind spots - what improvements haven't we even considered that would make the code better, easier, more maintainable?

For each file, look for one concrete in-scope issue introduced, exposed, or obstructing the requested work. If you find one:
1. Fix it using the same refactoring process (test, change, test, commit)
2. Look again; fixing one thing often reveals the next

Stop after the bounded pass finds no concrete in-scope issue. Report unrelated opportunities instead of expanding the task.

## 4. Summary

Provide a high-level summary of the refactoring:
- List each file that was touched
- Describe the key improvements made in each file

## Language-Specific

For Java: See [references/java.md](references/java.md)
