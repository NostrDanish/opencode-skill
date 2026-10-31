# opencode-skill

Kimi/Claude-style agent skill for [OpenCode](https://opencode.ai/) — AI-powered code generation and development.

The procedure is the current CLI (`opencode run`), not a frozen model id.

- Install from https://opencode.ai/v2/install or `npm install -g @opencode/cli`
- Pick a model with `opencode models`. Do not reuse ids from old copies of this skill.
- From an agent, `--auto` is what stops the run from waiting on a permission prompt. Do not pass it unless the user asked for the files to be written.
- Keys stay in the environment or `opencode auth login`. Do not write them into the repo.

OpenClaw's `pty: true` / `sessions_spawn` notes are a harness appendix. Any other agent runs the CLI directly.

## Install

Upload `SKILL.md` as a `.skill` package to your agent's skill library, or clone this repo into your skills directory.

Pairs with the [mkstack skill](https://github.com/NostrDanish/mkstack-skill) for building Nostr client websites.

## Source

Extracted from the [openclaw-skills](https://github.com/NostrDanish/nostr-skills-registry) collection.

## License

CC-BY-SA-4.0
