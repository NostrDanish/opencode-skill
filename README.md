# opencode-skill

Agent skill for the [OpenCode](https://opencode.ai/) CLI: install, provider config, and `opencode run`.

API keys stay in the environment (`{env:OPENROUTER_API_KEY}` in the config). The model id comes from `opencode models`, not from a hardcoded name. OpenCode needs a PTY.

Pairs with the [mkstack skill](https://github.com/NostrDanish/mkstack-skill) when the project is a Nostr client.

## Install

Copy or symlink the directory that contains `SKILL.md` into the agent's skills path. Install the CLI with the script at `https://opencode.ai/install`.

## License

CC-BY-SA-4.0
