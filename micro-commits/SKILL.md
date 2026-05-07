---
name: micro-commits
description: Micro-commits process for committing early and often as code is written. Use whenever writing or changing code, especially alongside Continuous Specification or TDD — each green spec run is a commit opportunity.
---

# Micro-commits

STARTER_CHARACTER = 📍

Commit early and often to move forward quickly and safely. Each commit is a safety checkpoint: a known-good state you can return to. Small, focused commits make history readable, reversals cheap, and collaboration smooth.

When starting, announce: "📍 Using Micro-commits skill"

## When to Commit

Commit at every natural green checkpoint — do not batch up multiple logical changes into one commit:

- **After meeting an expectation (green)**: the new expectation + the minimal code to meet it
- **After each design/refactor step**: one design change per commit
- **After any isolated logical change**: a rename, a moved file, a configuration update

If you are not using specs, commit after any coherent unit of work that leaves the code in a working state.

## Commit Message Format

Use short, lowercase messages that describe *what changed*, not *why*:

| Phase | Prefix    | Example                                      |
|-------|-----------|----------------------------------------------|
| New behavior (green spec) | `feat: `  | `feat: zero plus a number equals that number` |
| Design / refactor | `design: ` | `design: extract payment calculator`         |
| Fix | `fix: `   | `fix: off-by-one in pagination`              |
| Other (config, docs, tooling) | no prefix | `add eslint config`                          |

Keep messages under 72 characters. No periods. No past tense ("added") — use present tense ("add") or noun phrases ("zero plus a number equals that number").

## Process

1. Verify the code is in a green / working state before committing.
2. Stage only the files relevant to this commit (`git add -p` for partial staging when needed).
3. Write the commit message using the format above.
4. Commit.

Never commit broken or red code. If something is partially done, stash or leave it unstaged.

## Integration with Continuous Specification

In the CS cycle, commit at these moments:

- After **step 12** (all expectations met, green): commit the new expectation feat: minimal code together.
- After **each design change** in step 14 (design stage): one commit per change, using `- r` prefix.
- Do NOT commit during the red phase (unmet expectation). Wait for green.

Example sequence:

```
feat: zero plus a number equals that number
feat: add two positive numbers
design: extract addition into calculator class
feat: add two negative numbers
design: rename add to sum
```

## Reverting

If something goes wrong, micro-commits make recovery precise:

- `git revert HEAD` — undo the last commit safely
- `git reset --soft HEAD~1` — undo last commit, keep changes staged
- `git log --oneline` — scan the history to find the last good state
