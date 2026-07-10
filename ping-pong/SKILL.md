---
name: ping-pong
description: Adversarial ping-pong pair programming using two subagents. Use when doing Continuous Specification (aka TDD). Related to continuous-specification, refactoring, and micro-commits skills.
---

# Adversarial Ping-Pong Pairing with Subagents

STARTER_CHARACTER = 🏓

Two subagents take turns setting unmet expectations, meeting them, and designing — with the orchestrator acting as referee between each turn. The adversarial posture is the point: the design step strips the implementation to only what the expectation required, exposing over-engineering and forcing smaller steps.

When starting, announce: "🏓 Using Ping-Pong Pairing skill"

## The Cycle

```
A: set an unmet expectation
B: meet it
A: design
B: set an unmet expectation
A: meet it
B: design
...
```

The 3-step asymmetry means roles rotate — no one writes, passes, and refactors in the same position twice in a row.

## Steps

### 1. Set up context

Read the codebase to understand what is being built. Identify the implementation stub, the existing test structure and style, and any domain rules. Confirm domain rules with the user before starting.

### 2. Install skills

Ensure continuous-specification, refactoring, and micro-commits skills are available.

### 3. Person A sets an unmet expectation

Spawn a subagent as Person A with the current implementation, a summary of what has been specified so far, and the instruction to set the SMALLEST possible unmet expectation.

If the expectation is met trivially, keep it as a valid specification, skip the implementation turn, and continue to the next expectation candidate.

### 4. Person B meets the expectation

Spawn a subagent as Person B with the unmet expectation, the current implementation, and the instruction to write the MINIMUM code to meet the expectation — no more.

### 5. Person A designs

Spawn a subagent as Person A with the met expectations and the instruction to: (1) strip all code not required to meet the current expectations, then (2) clean the design of what remains.

The adversarial move: stripping unexercised guards and branches highlights how much smaller each step should be.

### 6. Referee between every handoff

Before passing to the next subagent, read the output and verify expectations are met or unmet as expected. Flag over-implementation (B added unexercised branches) or under-design (A left dead code). Adjust the next prompt if course correction is needed.

### 7. Commit

After each fully-met state: `feat:` commit. After each design pass: `design:` commit. Never commit with unmet expectations.

### 8. Rotate and repeat

Swap roles and continue the cycle until the behavior is fully specified.

### 9. Know when to stop

Stop when no remaining todos drive new unmet expectations, the next expectation requires a scope decision, or the user signals done. Summarize the commit history as the record of what was specified.
