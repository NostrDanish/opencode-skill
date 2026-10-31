# opencode-skill

Agent skill for the [OpenCode](https://opencode.ai/) CLI.

It runs `opencode run` when the user asked for OpenCode, or when the [mkstack skill](https://github.com/NostrDanish/mkstack-skill) is building a new Nostr client. It is not a general "write code" skill.

Auth stays in `opencode auth` or in environment variables the user sets. The skill does not take API keys in chat or write them into config. The model id comes from `opencode models` or from the user, not from a hardcoded name.
