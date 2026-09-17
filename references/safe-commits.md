# Safe green-checkpoint commits

Read this immediately before staging or committing a bounded logical change.

Commit only a green, working change. Verify it with the focused check and the
broader relevant suite when both apply. Keep each commit to one coherent unit
of work.

Stage only the files or hunks owned by that change, using partial staging when
needed. Inspect the staged diff before committing and remove unrelated user
work. Use the repository's commit-message convention. Leave incomplete or red
work unstaged; never commit temporary probes, generated mutation artifacts, or
unrelated changes.
