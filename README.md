# Skills

A collection of skills to help agents make code cheaper and safer to change.

| Skill | Description |
|-------|-------------|
| [continuous-specification](continuous-specification/SKILL.md) | Specify behavior-changing production work through small executable expectations, green checkpoints, targeted mutation checks, and in-scope design. |
| [describe-structure](describe-structure/SKILL.md) | Structure changed specifications with readable `when` and `given` contexts while preserving a suite's existing test style and behavior. |
| [targeted-mutation-testing](targeted-mutation-testing/SKILL.md) | Verify changed, behaviorally meaningful production code with file-scoped mutation testing and observable, state-based specifications. |
| [nullables](nullables/SKILL.md) | Specify code with external I/O (HTTP, files, databases, clocks, or randomness) without mocks, using production-ready null infrastructure. |
| [refactoring](refactoring/SKILL.md) | Improve in-scope code form without changing behavior, keeping specifications green and committing each bounded design step. |
| [micro-commits](micro-commits/SKILL.md) | Commit each green checkpoint or isolated logical change so history stays readable, recovery stays cheap, and collaboration stays safe. |
| [ensemble-review](ensemble-review/SKILL.md) | Run three isolated, independent reviews and synthesize their evidence into a prioritized revision plan. |

## Installation

To install all of them:

```sh
npx skills add jonfazzaro/skills
```

To install a single skill (at the project or global level):

```sh
npx skills add https://github.com/jonfazzaro/skills --skill <skill-name>
```

For example:

```sh
npx skills add https://github.com/jonfazzaro/skills --skill continuous-specification
```

## Usage

Once installed, skills are available as slash commands or referenced automatically by your agent based on context. Each skill's `SKILL.md` file contains the full process and reference material the agent uses when the skill is active.
