---
name: opencode
description: "Run the OpenCode CLI (opencode.ai) to generate or edit a project from a prompt. Use when the user asks for OpenCode, `opencode run`, or an unattended coding pass on a directory. Do not use it for a one-line edit you can make directly."
license: CC-BY-SA-4.0
---

# OpenCode

OpenCode is a coding agent with a CLI. This skill is how to drive that CLI. It is not a general "write some code" skill: if you can edit the file yourself, do that.

Docs: https://opencode.ai/docs/cli/ (the command list there wins over this file when they differ).

## Install

```bash
command -v opencode || curl -fsSL https://opencode.ai/v2/install | bash
opencode --version
```

`npm install -g @opencode/cli` is the other documented install. Read the install script before piping it on a machine that holds keys.

## Credentials

- Human at a terminal: `opencode auth login`.
- Agent: the provider key is already in the environment (`ANTHROPIC_API_KEY`, `OPENROUTER_API_KEY`, or whichever provider `opencode models` shows). Do not ask the user to paste a key into the chat, do not echo it, and do not write it into `opencode.json`.
- If a config file must reference a key, use the documented substitution `{env:OPENROUTER_API_KEY}` and do not commit that file.

Config file, when you need one: `~/.config/opencode/opencode.json` with `"$schema": "https://opencode.ai/config.json"`.

## Model

Do not hard-code a model id from this skill. They go stale.

```bash
opencode models --refresh
opencode run --model <provider/model> "task"
```

The id looks like `provider/model`. A variant, when that model has one, is `provider/model#variant` (for example a reasoning effort the catalog lists). An unknown variant is an error. If the user named a model, use that. Otherwise pick the strongest coding model `opencode models` actually prints for a configured provider.

## Run it

From the project directory (create `<workspace>/projects/<name>` for a new project; not the home directory):

```bash
opencode run --model <provider/model> --auto --format json "what to build or change"
```

- `--auto` — apply edits without a permission prompt. Required when nothing is attached to a terminal, or the run stops and looks hung. Do not pass it for a task the user did not ask to have written to disk.
- `--format json` — one JSON event per line, so you can see which files changed.
- `--file path` (or `-f`) — attach a file. Repeat the flag for more than one.
- `--continue` / `--session` — keep going in an existing session instead of starting over.
- No `--model` means "whatever this install's default is". Set one when the choice matters.

After it exits, read the files it changed. Do not tell the user it worked because the process exited 0.

## When not to use it

- A single obvious edit. Do it yourself.
- Anything that needs a secret in the prompt. The prompt is sent to the model provider.
- Publishing, deploying, or pushing, unless the prompt the user approved says so. `--auto` does not grant that.

## If your harness is OpenClaw

Subagents do not inherit this skill. The spawn task must tell the subagent to read this file first. OpenCode's stream is invisible without a PTY, so the exec needs `pty: true`, and the wait needs to cover the whole run (on the order of 10 minutes for a new app). That harness detail does not change the CLI above.

## When it fails

| What you see | What to do |
|---|---|
| No output, process still running | You are not on a PTY, or it is waiting for a permission. Rerun with `--auto` only if writes were requested. |
| No provider / auth error | `opencode auth list` and `opencode models`. Fix the env, do not paste the key into the repo. |
| Exit 0 and no files | You were not in the project directory, or the prompt only asked for an explanation. Check `--format json`. |
| Unknown model | Run `opencode models --refresh` and use an id it prints. |
