# opencode-skill

Kimi/Claude-style agent skill for [OpenCode](https://opencode.ai/) — AI-powered code generation and development.

Covers:

- **Installation & provider config** (OpenRouter, Anthropic, env vars)
- **Model selection** by task complexity (`--model` flag discipline)
- **Subagent spawn pattern** — keep the main chat clean; subagent reads the skill first
- **Direct execution pattern** — `pty: true`, timeouts, `yieldMs`
- **Workflows** — new projects, editing existing codebases, file attachments
- **Troubleshooting** — no output, hangs, auth issues, missing files

## Install

Upload `SKILL.md` as a `.skill` package to your agent's skill library, or clone this repo into your skills directory.

Pairs with the [mkstack skill](https://github.com/NostrDanish/mkstack-skill) for building Nostr client websites.

## Source

Extracted from the [openclaw-skills](https://github.com/NostrDanish/nostr-skills-registry) collection.

## License

CC-BY-SA-4.0
