# Skill context markers

## Purpose

Make skill activation visible without adding noise to later progress updates. Each skill owns a distinct, meaningful emoji marker that tells Jon which workflow has just begun.

## Activation convention

Each skill declares its marker in a `Context marker` section near the beginning of its `SKILL.md`.

When the skill activates, the agent begins its first commentary update with that marker and a concise `Using <skill> ...` announcement. The marker is not repeated on subsequent commentary updates unless another skill activates.

## Marker registry

- `ensemble-review`: `🧠🧠🧠`
- `refactoring`: `📐`
- `targeted-mutation-testing`: `🧬`
- `nullables`: `🔌`
- `specification-structure`: `🔺`
- `continuous-specification`: `📋`
- `micro-commits`: `🔹`

## Scope

Apply the convention to the seven repository skills only. Do not change skill names, trigger descriptions, or their workflows.

## Verification

Review every `SKILL.md` to confirm it has exactly one `Context marker` section, that its marker matches the registry, and that its activation-only behavior is stated.
