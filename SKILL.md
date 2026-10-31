---
name: opencode
description: "Run the OpenCode CLI to generate or edit a project from a prompt. Use for a new app, a multi-file feature, or a refactor that would take many edits. Do not use it for a one-file change you can make directly. Triggers on opencode, opencode run, and 'have OpenCode build this'."
license: CC-BY-SA-4.0
---

# OpenCode

OpenCode is a terminal coding agent. You drive it with `opencode run`. You do not reimplement it with your own edits unless it failed.

## Install and providers

```bash
command -v opencode || curl -fsSL https://opencode.ai/install | bash
opencode --version
```

The installer is the upstream script at `opencode.ai`. Do not run a copy from anywhere else.

API keys stay in the environment. The config file references them. It does not contain them.

```bash
mkdir -p ~/.config/opencode
```

`~/.config/opencode/opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "openrouter": {
      "options": { "apiKey": "{env:OPENROUTER_API_KEY}" }
    }
  }
}
```

`{env:NAME}` and `{file:path}` are the substitutions OpenCode documents. Export `OPENROUTER_API_KEY` or `ANTHROPIC_API_KEY` in the environment. Never write a key into the json, never echo it, never commit it.

Check that the provider actually loaded:

```bash
opencode auth list
opencode models <provider>
```

## Model

Pass `--model` every time. If the user named one, use that. If they did not, run `opencode models` and pick a current Claude Sonnet (or whatever they already use for hard edits) from that list. Do not invent a model id. Model ids in old notes, including `claude-sonnet-4.5`, go stale.

## Run

From the project directory, in a PTY, with a long wait. A multi-file app often needs several minutes. Ten minutes is a reasonable default; raise it if the prompt is large.

```bash
cd <workspace>/projects/<project-name>
opencode run --model <provider/model> "<task>"
```

Create the directory first (`mkdir -p <workspace>/projects`). Do not use the home directory.

OpenCode needs a PTY to stream its progress. In Cursor, run it in the terminal. On OpenClaw or Hermes `exec`, set `pty: true`, `timeout: 600`, and `yieldMs: 600000`. Without a PTY it may still write files and show you nothing.

A subagent is optional. If you use one, the subagent must read this skill first. `sessions_spawn` exists only on OpenClaw-style runtimes.

Attach a file with `--file` when the prompt depends on one image or spec:

```bash
opencode run --model <provider/model> --file design.png "Match this layout"
```

## After it exits

Look at the files, do not trust the summary alone. Run the project's test or dev command. Report which files changed and where the project is.

If it exits with no files: you are probably in the wrong directory, the key is missing (`opencode auth list`), or the model id is not in `opencode models`. Retry once with a shorter prompt before changing the project by hand.

## When not to use it

A single file edit, a rename, or reading code. Do those yourself. OpenCode is for work that would otherwise be a long series of edits.

Docs: https://opencode.ai/docs and https://opencode.ai/docs/cli/
