---
name: nullables
description: Specifies behavior without mocks using Nullables. Use when writing specifications, especially specifying code with external I/O (HTTP, files, databases, clocks, random numbers), designing infrastructure wrappers or replacing mocking libraries.
---

# Nullables: Specifying Without Mocks

STARTER_CHARACTER = ⭕️

## The Problem

External I/O is slow and flaky. Specifications hitting real databases, APIs, or file systems run slow and fail randomly. We want specifications that run in milliseconds and never fail due to network issues.

Mocking libraries solve speed but introduce a new problem: they couple specifications to implementation by verifying specific method calls. Specification code using mocking libraries is brittle—it breaks when code is refactored, even when behavior is unchanged.

## The Solution

Nullables are production code with an "off switch" for infrastructure—not test doubles, but real code you can ship (dry-run modes, cache warming, offline operation). They enable **narrow, sociable, state-based specifications**:

- **Narrow**: Each expectation focuses on one class/module, not broad end-to-end flows
- **Sociable**: Specifications use real dependencies—only infrastructure I/O is neutralized. (Contrast with "solitary" tests that mock everything, isolating the class under test.)
- **State-based**: Assert on outputs and state, not on which methods were called

## When to Use

**Use Nullables for:**
- Code that talks to external systems (HTTP, files, databases, clocks, random)
- Third-party libraries you don't control
- Non-deterministic operations

**Don't use Nullables for:**
- Pure logic — specify directly, no wrapper needed
- Your own classes — make them Nullable directly, or null their dependencies

**Greenfield**: Add wrappers incrementally as specifications demand—don't over-engineer upfront.

**Existing codebase**: See [migration.md](references/migration.md) for incremental conversion strategies.

## The Foundation: A-Frame Architecture

A-Frame is the architectural insight that makes Nullables work especially well.
Traditional layered architecture stacks Logic on top of Infrastructure, making Logic depend on slow, brittle I/O.
A-Frame makes them **peers** instead:

```
        Application (coordinates)
            ↓              ↓
Logic (pure, specified)    Infrastructure (Nullables)
```

**Key rule:** Logic never imports Infrastructure directly. Application coordinates between them via [Logic Sandwich](references/architecture/logic-sandwich.md): read → process → write.

- **Logic** — pure functions, no I/O
- **Infrastructure** — wrapped with `create()`/`createNull()`
- **Application** — thin coordination layer

This separation lets you swap real infrastructure for nulled versions without touching Logic. For full details, see [a-frame.md](references/architecture/a-frame.md). For event-driven code, see [event-driven.md](references/architecture/event-driven.md).

## Core Pattern: Two Factory Methods

Every infrastructure wrapper has two creation paths:

```javascript
class Clock {
  static create() {
    return new Clock(Date);  // Real system clock
  }

  static createNull(now = "2020-01-01T00:00:00Z") {
    return new Clock(new StubbedDate(now));  // Controlled clock
  }

  constructor(dateClass) {
    this._dateClass = dateClass;
  }

  now() {
    return new this._dateClass().toISOString();
  }
}

// Embedded stub - lives in production code, not specification files
class StubbedDate {
  constructor(isoString) {
    this._time = new Date(isoString).getTime();
  }
  toISOString() {
    return new Date(this._time).toISOString();
  }
}
```

**Key principles:**
- `createNull()` parameters match the caller's abstraction level (ISO strings, not milliseconds)
- Embedded stubs live alongside the wrapper, implementing only what's actually used
- Add [Output Tracking](references/building/output-tracking.md) to observe what was written

For complete construction details, see [infrastructure-wrappers.md](references/building/infrastructure-wrappers.md).

## Specifying with Nullables

Every wrapper follows the same pattern. Here's how you specify code that uses one:

```javascript
describe("App", () => {
  it("transforms input and writes result", () => {
    const { output } = run({ args: ["hello"] });
    assert.deepEqual(output.data, ["uryyb\n"]);  // ROT-13
  });

  function run({ args = [] } = {}) {
    const commandLine = CommandLine.createNull({ args });
    const output = commandLine.trackOutput();
    new App(commandLine).run();
    return { output };
  }
});
```

Specifications exercise real `App` code. Only infrastructure I/O is neutralized. The `run()` helper protects specifications from constructor changes ([Signature Shielding](references/test-patterns.md#helper-functions-signature-shielding)).

### Specification Philosophy

- **State-based, not interaction-based** — verify what was produced, not which methods were called
- **Sociable, not solitary** — specifications use real dependencies; only infrastructure is nulled. Bugs cause multiple specification failures, pinpointing the problem
- **Paranoic Telemetry** — assume everything fails. Specify error paths, timeouts, and failures as thoroughly as happy paths
- **Collaborator-Based Isolation** — use dependencies' own methods in assertions rather than hardcoding expectations:
  ```javascript
  // BAD: Breaks if format changes (also leaks implementation details into your clients, creates bad coupling)
  assert.deepEqual(output.data, [{ level: "info", message: "Done", ts: 123 }]);
  // GOOD: Uses dependency's format
  assert.deepEqual(output.data, [logger.formatEntry("info", "Done")]);
  ```
- **Narrow Integration Specifications** — sociable specifications verify logic; add a few specifications per wrapper that hit real systems to catch stub drift

For specification techniques (sequences, time, events, errors), see [test-patterns.md](references/test-patterns.md).

## Building Patterns

These patterns work together:

- **[Output Tracking](references/building/output-tracking.md)** — Observe what was produced, not which methods called
- **[Configurable Responses](references/building/configurable-responses.md)** — Control what Nullables return at your abstraction level
- **[Embedded Stubs](references/building/embedded-stubs.md)** — Stubs live in production code, maintained with wrapper
- **[Wrapper Composition](references/building/infrastructure-wrappers.md#wrapper-composition-fake-it-once-you-make-it)** — High-level code composes from lower-level Nullables; only leaves have stubs

## Anti-Patterns

**Using mock libraries** — Couples specifications to implementation. Don't import sinon, jest.mock, etc. Nullables replace them.

**Constructor connects to infrastructure** — Constructors should perform no work. Defer connections to explicit methods. See [Zero-Impact Instantiation](references/building/infrastructure-wrappers.md#zero-impact-instantiation).

**Parameters at wrong abstraction level** — `createNull()` should accept domain concepts, not implementation details:

```javascript
// BAD: Leaking HTTP details
LoginClient.createNull({ httpResponse: { status: 200, body: '{"email":"x"}' } });

// GOOD: Domain level
LoginClient.createNull({ email: "user@example.com", verified: true });
```

**Stubs in specification files** — Stubs belong in production code alongside the wrapper. See [embedded-stubs.md](references/building/embedded-stubs.md).

**Stub as complex as the real thing** — If your stub needs significant logic, reconsider the abstraction.

**Specifying the null** — Specifications of a class must call `create()` on the class under test, not `createNull()`. `createNull()` exists for the benefit of *other* classes' specifications — a service's specifications would use `Dependency.createNull()` so they don't have to wire up real infrastructure. But a class's own specifications should call `create()` with null deps injected, so its real code runs.

```javascript
// BAD: MyService's own specifications calling createNull() on itself
const service = MyService.createNull();  // skips real create() logic

// GOOD: MyService's own specifications call create() with nulled deps
const service = MyService.create(MyRepository.createNull(), MyHttpClient.createNull());

// ALSO GOOD: a higher-level class's specifications use MyService.createNull()
const handler = Handler.create(MyService.createNull());
```

**Asynchronous create** — `create()` and constructors must be synchronous. Any async initialization (config lookups, network calls) must happen in the caller *before* `create()` is invoked.

```javascript
// BAD: async work inside create()
static async create() {
  const config = await loadConfig();  // ❌
  return new MyService(config);
}

// GOOD: caller resolves async deps, passes them in
const config = await loadConfig();
const service = MyService.create(config);
```
