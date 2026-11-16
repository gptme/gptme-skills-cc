# gptme-skills — Claude Code Plugin

Battle-tested AI agent skills from [gptme](https://gptme.org), packaged as a Claude Code plugin.

## Install

From inside Claude Code:

```
/plugin marketplace add gptme/gptme-skills-cc
/plugin install gptme-skills@gptme-skills-cc
```

or from a shell:

```bash
claude plugin marketplace add gptme/gptme-skills-cc
claude plugin install gptme-skills@gptme-skills-cc
```

`claude plugin details gptme-skills` should list four skills. Skills are namespaced by the
plugin name, so they are invoked as `/gptme-skills:code-review` and so on.

The older `/plugin install github:gptme/gptme-skills-cc` form no longer resolves: Claude Code
looks the plugin up in a configured marketplace, and this repo is its own marketplace only
once it is added as above.

From the Claude Code marketplace (coming soon):

```
/plugin install gptme-skills@claude-plugins-official
```

## Skills

| Skill | Description |
|-------|-------------|
| `/gptme-skills:code-review` | Systematic PR and code audit workflow with bundled analysis utilities |
| `/gptme-skills:persistent-learning` | Capture insights to durable files so future sessions inherit the knowledge |
| `/gptme-skills:progressive-disclosure` | Restructure large documentation into token-efficient slim indexes |
| `/gptme-skills:git-workflow` | Conventional commits, worktree branches, CI gate — reliable git for AI agents |

## Why gptme Skills?

These patterns come from running autonomous AI agents continuously — not from docs or theory. They address recurring failure modes:

- **Code review**: Agents produce inconsistent, shallow reviews. `/code-review` gives a systematic multi-dimension workflow and automated pattern detection.
- **Persistent learning**: Claude sessions are stateless. `/persistent-learning` teaches the write-first habit that makes knowledge survive session boundaries.
- **Progressive disclosure**: Large documentation files waste context budget. `/progressive-disclosure` gives a 40-60% reduction pattern that keeps content accessible.
- **Git workflow**: Agents commit sloppily, skip CI, and cause merge accidents. `/git-workflow` establishes conventional commits, feature branches, and CI gate as defaults.

## Comparison with superpowers

[obra/superpowers](https://github.com/obra/superpowers) is an excellent general-purpose skill pack. gptme-skills is complementary — it covers different ground:

| Topic | superpowers | gptme-skills |
|-------|-------------|-------------|
| TDD / test-first | ✅ | — |
| Systematic debugging | ✅ | — |
| Code review | — | ✅ (with bundled analysis utils) |
| Persistent cross-session learning | — | ✅ |
| Documentation restructuring | — | ✅ |
| Git / commit workflow | — | ✅ |

Install both. They don't overlap.

## Cross-Harness Compatibility

These skills also work in [gptme](https://gptme.org) — the same SKILL.md format is used by both runtimes. Skills written for gptme-skills-cc work in gptme without modification (gptme uses additional frontmatter fields for keyword matching, which Claude Code safely ignores).

This plugin is generated from [gptme-contrib](https://github.com/gptme/gptme-contrib), the shared skill library for the gptme ecosystem.

## License

MIT — see [LICENSE](LICENSE).

## Related

- [gptme](https://gptme.org) — the open-source AI agent framework these patterns come from
- [gptme-contrib](https://github.com/gptme/gptme-contrib) — gptme's shared skill library (source of truth)
- [obra/superpowers](https://github.com/obra/superpowers) — complementary general-purpose skill pack
- [Claude Code Plugin Reference](https://code.claude.com/docs/en/plugins-reference) — plugin format docs
