# Specification Patterns for Nullables

Effective specifications with Nullables follow specific patterns that differ from mock-based specifying.

## Contents

- [Core Structure: Arrange-Act-Assert](#core-structure-arrange-act-assert)
- [Helper Functions (Signature Shielding)](#helper-functions-signature-shielding)
- [State-Based vs Interaction-Based](#state-based-vs-interaction-based)
- [Sociable Specifications](#sociable-specifications)
- [Overlapping Specifications](#overlapping-specifications)
- [Specifying Error Paths](#specifying-error-paths)
- [Specifying Sequences](#specifying-sequences)
- [Specifying Time-Dependent Code](#specifying-time-dependent-code)
- [Specifying Event-Driven Code](#specifying-event-driven-code)
- [Narrow Integration Specifications](#narrow-integration-specifications)
- [Assertion Patterns](#assertion-patterns)

## Core Structure: Arrange-Act-Assert

```javascript
it("processes payment successfully", async () => {
  // Arrange: Create Nullables with configured responses
  const gateway = PaymentGateway.createNull({ approved: true });
  const logger = Logger.createNull();
  const logOutput = logger.trackOutput();
  const service = new PaymentService(gateway, logger);

  // Act: Execute the code under test
  const result = await service.process({ amount: 100 });

  // Assert: Verify outcomes
  assert.equal(result.status, "approved");
  assert.deepEqual(logOutput.data[0], {
    level: "info",
    message: "Payment processed"
  });
});
```

## Helper Functions (Signature Shielding)

Encapsulate specification setup to protect against signature changes:

```javascript
describe("PaymentService", () => {
  it("approves valid payment", async () => {
    const { result, logOutput } = await processPayment({ amount: 100 });

    assert.equal(result.status, "approved");
    assert.equal(logOutput.data.length, 2);
  });

  it("rejects when gateway fails", async () => {
    const { result, logOutput } = await processPayment({
      amount: 100,
      gatewayResponse: { approved: false, reason: "insufficient funds" }
    });

    assert.equal(result.status, "rejected");
    assert.equal(result.reason, "insufficient funds");
  });

  // Helper encapsulates all setup
  async function processPayment({
    amount,
    gatewayResponse = { approved: true }
  } = {}) {
    const gateway = PaymentGateway.createNull(gatewayResponse);
    const logger = Logger.createNull();
    const logOutput = logger.trackOutput();

    const service = new PaymentService(gateway, logger);
    const result = await service.process({ amount });

    return { result, logOutput, gateway };
  }
});
```

When `PaymentService` constructor changes, only the helper updates. Specifications remain unchanged.

## State-Based vs Interaction-Based

Nullables enable state-based specifying. Verify outcomes, not method calls:

```javascript
// AVOID: Interaction-based (mock-style)
it("calls logger.info", () => {
  const logger = mock(Logger);
  service.process();
  verify(logger.info).calledWith("Processing");  // Brittle!
});

// PREFER: State-based
it("logs processing step", () => {
  const logger = Logger.createNull();
  const output = logger.trackOutput();

  service.process();

  assert.deepEqual(output.data[0].message, "Processing");
});
```

## Specifying Error Paths

Configure Nullables to return errors:

```javascript
it("handles gateway timeout", async () => {
  const { result, logOutput } = await processPayment({
    gatewayResponse: { error: "TIMEOUT" }
  });

  assert.equal(result.status, "error");
  assert.equal(result.message, "Payment gateway unavailable");
  assert.equal(logOutput.data[1].level, "error");
});

it("handles network failure", async () => {
  const { result } = await processPayment({
    gatewayResponse: { networkError: true }
  });

  assert.equal(result.status, "error");
  assert.equal(result.message, "Network error, please retry");
});
```

## Specifying Sequences

Use response arrays to specify multi-step flows:

```javascript
it("retries failed requests", async () => {
  const http = HttpClient.createNull([
    { status: 503 },  // First: service unavailable
    { status: 503 },  // Second: still unavailable
    { status: 200, body: "success" }  // Third: success
  ]);
  const requests = http.trackRequests();

  const result = await service.fetchWithRetry("/api/data");

  assert.equal(requests.data.length, 3);
  assert.equal(result, "success");
});

it("gives up after max retries", async () => {
  const http = HttpClient.createNull([
    { status: 503 },
    { status: 503 },
    { status: 503 }
  ]);

  await assert.rejects(
    () => service.fetchWithRetry("/api/data"),
    { message: "Max retries exceeded" }
  );
});
```

## Sociable Specifications

Specifications naturally become "sociable" - they exercise real code through the dependency chain:

```javascript
// Controller specification runs real logic, real validation
it("creates user with validated data", async () => {
  const { response, dbWrites } = await createUser({
    body: { email: "user@test.com", name: "Alice" }
  });

  assert.equal(response.status, 201);
  assert.deepEqual(dbWrites.data[0], {
    table: "users",
    data: { email: "user@test.com", name: "Alice" }
  });
});

// Only infrastructure is nulled
async function createUser({ body }) {
  const db = Database.createNull();
  const dbWrites = db.trackWrites();
  const logger = Logger.createNull();

  // Real controller, real validator, real business logic
  const controller = new UserController(db, logger);
  const response = await controller.create({ body });

  return { response, dbWrites };
}
```

## Overlapping Specifications

With sociable specifications, coverage naturally overlaps. A bug in a shared dependency causes multiple specification failures. This is a feature, not a bug:

- **Mocks hide bugs** - When each specification mocks its dependencies, a bug in real code might not surface until production.
- **Overlapping specifications surface bugs** - Multiple failures pinpoint the problem quickly: "All UserController specifications failed → check UserController."
- **Refactoring is safe** - Change implementation, run specifications, see what breaks.

If a change breaks many specifications, check the shared code they exercise. The failing specifications reveal your dependency graph.

## Specifying Time-Dependent Code

Use a nulled Clock:

```javascript
it("marks items as expired after TTL", () => {
  const clock = Clock.createNull("2020-01-01T00:00:00Z");
  const cache = new Cache(clock, { ttlMs: 60000 });

  cache.set("key", "value");

  // Advance time
  clock.advance(59000);
  assert.equal(cache.get("key"), "value");  // Still valid

  clock.advance(2000);
  assert.equal(cache.get("key"), undefined);  // Expired
});
```

Clock with advanceable time:

```javascript
class Clock {
  static createNull(initialTime = "2020-01-01T00:00:00Z") {
    return new Clock(new ControllableTime(initialTime));
  }

  constructor(timeSource) {
    this._time = timeSource;
  }

  now() {
    return this._time.now();
  }

  advance(ms) {
    this._time.advance(ms);
  }
}

class ControllableTime {
  constructor(isoString) {
    this._ms = new Date(isoString).getTime();
  }

  now() {
    return new Date(this._ms).toISOString();
  }

  advance(ms) {
    this._ms += ms;
  }
}
```

## Specifying Event-Driven Code

Use behavior simulation for events:

```javascript
it("broadcasts messages to other clients", () => {
  const network = Network.createNull();
  const sent = network.trackSentMessages();

  const server = new ChatServer(network);
  server.start();

  // Simulate external events
  network.simulateConnection("client-1");
  network.simulateConnection("client-2");
  network.simulateMessage("client-1", "Hello!");

  // Verify broadcast
  assert.deepEqual(sent.data, [
    { to: "client-2", message: "Hello!" }  // Sent to client-2, not client-1
  ]);
});
```

## Narrow Integration Specifications

Sociable specifications with Nullables provide most of your coverage without slow, flaky end-to-end specifications. But you still need confidence that your wrappers actually work with real infrastructure.

**Avoid broad integration specifications** that exercise entire flows through real systems—they're slow, flaky, and duplicate coverage you already have from sociable specifications. Instead, write narrow integration specifications that focus on one wrapper at a time.

Specify wrappers against real systems in isolation:

```javascript
// Separate file: _http_client_integration_spec.js
describe("HttpClient integration", () => {
  let server;

  before(async () => {
    server = await startTestServer();
  });

  after(async () => {
    await server.close();
  });

  it("makes real HTTP requests", async () => {
    const client = HttpClient.create();
    const response = await client.get(`http://localhost:${server.port}/test`);

    assert.equal(response.status, 200);
    assert.equal(response.body, "OK");
  });
});
```

These integration specifications verify the wrapper works with real infrastructure. Most specifications use Nullables; integration specifications provide confidence the real path works.

## Assertion Patterns

```javascript
// Exact match
assert.deepEqual(output.data, [expected]);

// Partial match (when timestamps/IDs vary)
assert.equal(output.data.length, 1);
assert.equal(output.data[0].message, "expected");

// Ignoring dynamic fields (portable approach)
const { timestamp, ...rest } = output.data[0];
assert.deepEqual(rest, { message: "expected" });
```

**Note:** `expect.any(Number)` is Jest-specific. For portable specifications, extract and ignore dynamic fields as shown above, or use a library-specific matcher.

---

For the complete pattern language, see [Testing Without Mocks](https://www.jamesshore.com/v2/projects/nullables/testing-without-mocks) by James Shore (long!) — the source material for these patterns.
