# Scoped verification

Read this when deciding which checks to run for a bounded code or specification
change.

Use two layers of evidence:

- Run the narrowest focused check after each small change. It gives fast,
  directly relevant feedback.
- Run the broader relevant suite at a green checkpoint and before completion.
  It guards the affected behavior against regressions outside the focused file.

Identify both commands before editing when the work changes behavior. Record
the exact commands and meaningful outcomes. If a required check is unavailable,
investigate first; if it remains unavailable, disclose the verification gap
instead of claiming green evidence.
