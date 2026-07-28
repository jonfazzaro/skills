# Skills

A collection of skills to help agents make code cheaper and safer to change.

| Skill | Description |
|-------|-------------|
| [continuous-specification](continuous-specification/SKILL.md) | TDD framed around specification — one agent sets one behavior, meets and minimizes it, commits the green checkpoint, and designs within scope. |
| [nullables](nullables/SKILL.md) | Specify code with external I/O (HTTP, files, databases, clocks) without mocks. Infrastructure wrappers with `create()`/`createNull()` factory methods enable fast, state-based, sociable specifications. |
| [refactoring](refactoring/SKILL.md) | Bounded design process — improve one in-scope behavior-preserving step at a time, keep specifications green, and commit each change. |
| [micro-commits](micro-commits/SKILL.md) | Commit at every green checkpoint. Small, focused commits make history readable, reversals cheap, and progress safe. |
| [ensemble-review](ensemble-review/SKILL.md) | Run three isolated, concurrent reviews with distinct models and synthesize their findings into a prioritized revision plan. |

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
