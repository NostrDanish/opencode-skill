---
name: opencode
description: "Run the OpenCode CLI (opencode run) when the user explicitly asks for OpenCode, or when the mkstack skill is building a Nostr client with it. Do not use for ordinary coding the host agent can do itself, or for tasks that are not code."
license: CC-BY-SA-4.0
---

# OpenCode

OpenCode is a separate coding CLI (https://opencode.ai). This skill drives that CLI. It is not a reason to hand off work the current agent can edit directly.

## When

Use it only if:

- the user asked for OpenCode by name, or
- the mkstack skill has just scaffolded a project and its next step is OpenCode.

Skip it for a one-file change, a read, or a review.

## Install and auth

Check `opencode --version` first.

```bash
npm install -g @opencode/cli
```

Upstream's script is `curl -fsSL https://opencode.ai/v2/install | bash`. Prefer npm when it is available. Docs: https://opencode.ai/docs/cli/

Do not ask the user to paste an API key into the chat, and do not write a key into `opencode.json`. Ask them to run `opencode auth` themselves, or to export the provider variable (`OPENROUTER_API_KEY`, `ANTHROPIC_API_KEY`, …) in their own shell. A config file may reference `{env:OPENROUTER_API_KEY}` and must not contain the key.

If `opencode models` fails for lack of a provider, stop and say auth is missing. Do not invent a key.

## Model

If the user named a `provider/model` id, use it. Otherwise:

```bash
opencode models
```

Pick a current coding model from that list and pass it as `--model provider/model`. Do not reuse a model id from memory, including older Claude ids that this skill used to hardcode.

## Run

Run from the project directory so OpenCode sees the repo:

```bash
cd <project>
opencode run --model <provider/model> --auto "<task>"
```

- `--auto` approves permissions that are not explicitly denied. Use it for a scaffold the user already asked for. Omit it if the user wanted to confirm each command.
- `--file <path>` attaches a file.
- `--format json` is for when the caller must parse events. The default format is for reading progress.
- Give it several minutes. A new app can take longer than a small edit.

OpenCode wants a TTY to stream progress. If the host's shell has no TTY, still run the command; then read the project tree to see what changed. Missing progress text does not mean it failed.

If this host has subagents, the run can happen there. Tell that agent to read this skill first and include the model id in the task. The commands above are the same either way.

## After

List the files that changed and say what they do. If the run wrote nothing, check the output for an auth or model error before running it again.

## Config

Optional default, without secrets, in `~/.config/opencode/opencode.jsonc` or the project's `opencode.jsonc`:

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "model": "provider/model-from-opencode-models"
}
```

Full field list: https://opencode.ai/docs/config/ and https://opencode.ai/config.json
