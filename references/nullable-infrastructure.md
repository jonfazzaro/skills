# Nullable infrastructure wrappers

Read this when a specification needs predictable behavior from external I/O,
such as HTTP, files, databases, clocks, or randomness.

Wrap the infrastructure behind a production-ready abstraction with a real
creation path and a configurable null creation path. The null path neutralizes
I/O while the production code still exercises real application behavior. Keep
the wrapper's configuration at the caller's domain level and observe its
outputs rather than mocking method calls.

For the complete construction, composition, migration, and anti-pattern
guidance, read the [Nullables skill](../nullables/SKILL.md).
