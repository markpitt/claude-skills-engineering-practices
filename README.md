<h1>
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="docs/assets/banner-dark.svg">
    <img src="docs/assets/banner-light.svg" alt="Engineering practices" width="100%">
  </picture>
</h1>

Language-neutral craft: TDD, KISS, design patterns, twelve-factor, root cause analysis and decision interviews.

Craft that does not depend on a language. `gof-design-patterns` covers eight languages, which is why it sits here and not with the gurus. `grill-me` follows the `grill-me` skill popularised by Matt Pocock, written from the method rather than copied.

This is one of seven skill groups. [claude-skills](https://markpitt.dev/claude-skills/) lists them all and
holds the shared tooling: validation, packaging, the evaluation harness and the TUI.

## Skills

<!-- BEGIN GENERATED SKILL INDEX -->
| Skill | Bundled files | What it does |
|---|---|---|
| [audit-remediation-loop](skills/audit-remediation-loop/SKILL.md) | 4 files | Runs the full audit, remediation and review loop. |
| [gof-design-patterns](skills/gof-design-patterns/SKILL.md) | 6 files | Chooses and implements the 23 Gang of Four design patterns (creational, structural and behavioural) in C#… |
| [grill-me](skills/grill-me/SKILL.md) | none | Interview the user one decision at a time down a dependency-ordered decision tree, asking exactly one… |
| [kiss-principle](skills/kiss-principle/SKILL.md) | 3 files | Reviews code or a design for complexity it does not need, and says what to remove, what to keep and why. |
| [root-cause-analysis](skills/root-cause-analysis/SKILL.md) | 4 files | Structured root cause analysis using named methods: Toyota's 5 Whys, Ishikawa fishbone, Pareto analysis… |
| [tdd-guru](skills/tdd-guru/SKILL.md) | 3 files | Enforces current Test-Driven Development (TDD) best practices across programming languages and scripts… |
| [twelve-factor-app](skills/twelve-factor-app/SKILL.md) | 7 files | Applies the Twelve-Factor App methodology when designing, building, reviewing or auditing cloud-native and… |

7 skills.
<!-- END GENERATED SKILL INDEX -->

## Install

Copy a skill directory into either skills location, then start a new session:

```bash
cp -r skills/<skill-name> ~/.claude/skills/    # personal, all projects
cp -r skills/<skill-name> .claude/skills/      # this project only
```

For Claude Desktop, zip the skill directory's contents and upload the zip under Settings,
Capabilities, Skills:

```bash
cd skills/<skill-name> && zip -r ../../<skill-name>.zip .
```

## Licence

Apache License 2.0. See [LICENSE](LICENSE) and [NOTICE](NOTICE). Each skill directory carries its
own copy of the licence, so a skill copied out on its own stays covered.
