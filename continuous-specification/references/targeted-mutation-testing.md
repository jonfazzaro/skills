# Targeted Mutation Testing

Targeted mutation testing checks whether a changed production component's dedicated specifications detect meaningful behavior changes.

The `targeted-mutation-testing` skill provides the full workflow. Load it before the final green checkpoint when the changed production component has branching, lifecycle or asynchronous behavior, guards, error recovery, authentication, serialization, or an external-integration seam.

## Scope

Mutate only the changed production file and run only its dedicated specifications. Treat every non-killed mutant as a disposition to record, not as a reason to broaden scope or add implementation-coupled assertions.

## Keep specifications behavioral

- Add state- or output-based specifications for actionable survivors.
- Use Nullable infrastructure wrappers for external I/O seams.
- Record intentional survivors, tool limitations, and incomplete ranges with their exact reason.
