---
name: ping-pong-pairing
description: Adversarial ping-pong pair programming using two subagents. Use when doing TDD with two agents taking turns writing failing tests, making them pass, and refactoring. Related to continuous-specification and micro-commits skills.
---

# Adversarial Ping-Pong Pairing with Subagents

STARTER_CHARACTER = 🏓

Two subagents take turns writing failing tests, making them pass, and refactoring — with the orchestrator acting as referee between each turn. The adversarial posture is the point: the refactoring step strips the implementation to only what the test required, exposing over-engineering and forcing smaller steps.

When starting, announce: "🏓 Using Ping-Pong Pairing skill"

## The Cycle

```
A: write failing test
B: make it pass
A: refactor
B: write failing test
A: make it pass
B: refactor
...
```

The 3-step asymmetry means roles rotate — no one writes, passes, and refactors in the same position twice in a row.

## Steps

### 1. Set up context

Read the codebase to understand what is being built. Identify the implementation stub, the existing test structure and style, and any domain rules. Confirm domain rules with the user before starting.

### 2. Install skills

Ensure micro-commits and continuous-specification skills are available.

### 3. Person A writes a failing test

Spawn a subagent as Person A with the current implementation, a summary of what has been proven so far, and the instruction to write the SMALLEST possible failing test.

If the test passes trivially, keep it as a valid specification, skip the implementation turn, and continue to the next failing test candidate.

### 4. Person B makes it pass

Spawn a subagent as Person B with the failing test, the current implementation, and the instruction to write the MINIMUM code to make the test pass — no more.

### 5. Person A refactors

Spawn a subagent as Person A with the passing tests and the instruction to: (1) strip all code not required to pass the current tests, then (2) clean the design of what remains.

The adversarial move: stripping unexercised guards and branches highlights how much smaller each step should be.

### 6. Referee between every handoff

Before passing to the next subagent, read the output and verify tests pass or fail as expected. Flag over-implementation (B added unexercised branches) or under-refactoring (A left dead code). Adjust the next prompt if course correction is needed.

### 7. Commit

After each green state: `feat:` commit. After each design refactor: `design:` commit. Never commit red code.

### 8. Rotate and repeat

Swap roles and continue the cycle until the behavior is fully specified.

### 9. Know when to stop

Stop when no remaining todos drive new failing tests, the next test requires a scope decision, or the user signals done. Summarize the commit history as the record of what was specified.
