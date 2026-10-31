---
name: opencode
description: Drive the OpenCode CLI for multi-step code generation in workspace projects. Use when creating or editing apps that need an iterative coding agent, not for simple file edits.
license: CC-BY-SA-4.0
---

# OpenCode

Use [OpenCode](https://opencode.ai/) for multi-step code generation. Pair with `mkstack` when scaffolding a Nostr client.

## Host compatibility

| OpenClaw | Cursor / generic agent |
|---|---|
| `sessions_spawn` / `exec` blocks below | Run the same `opencode run …` in a terminal tool |
| `pty: true`, `yieldMs`, `timeout` | Interactive terminal, 10 minute budget, do not background |
| `workdir:` | `cd` or `opencode run --dir <path>` |

Subagents do **not** inherit this skill. Tell them to read it first.

## When to use

Use for creating projects, editing existing codebases, and multi-iteration coding.

Do **not** use for simple file writes, non-coding tasks, or read-only analysis.

## Install

```bash
which opencode || curl -fsSL https://opencode.ai/install | bash -s -- --version latest
mkdir -p ~/.local/bin
ln -sf ~/.opencode/bin/opencode ~/.local/bin/opencode
opencode --version
```

The installer is a remote script — prefer `--version <tag>` once you know a good tag. Review the script if you are in a high-assurance environment.

## Auth (never write live keys into JSON)

**Preferred:** `opencode auth login` (stores credentials in the OpenCode auth file, not in a committed config).

**Or environment variables** (do not paste keys into chat logs):

```bash
export OPENROUTER_API_KEY
export ANTHROPIC_API_KEY
```

If you must write `~/.config/opencode/opencode.json`, reference env vars only:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "openrouter": {
      "options": {
        "apiKey": "{env:OPENROUTER_API_KEY}"
      }
    }
  }
}
```

Never write `sk-or-…` / `sk-ant-…` literals into that file. `{env:VAR}` and `{file:path}` are the supported substitutions.

Verify: `opencode models` (or `opencode models <provider>`). Primary login flow is `opencode auth login`.

## Model selection

Do not hardcode model ids — they rot. Before a run:

```bash
opencode models
opencode models openrouter
```

Pick from that list. Always pass `--model <provider>/<id>`. Ask the user if they have a preference.

## Usage

### Subagent (recommended)

```
sessions_spawn:
  task: "Read the opencode skill, then use OpenCode to create a landing page with contact form at <workspace>/projects/coffee-shop. Use --auto and the model <provider>/<id>."
  label: "opencode-landing-page"
  runTimeoutSeconds: 660
```

Projects go in `<workspace>/projects/<name>` — never `$HOME`. Create the directory first.

For edits: same spawn, point at the existing project path.

### Direct run

```bash
mkdir -p <workspace>/projects/my-project
opencode run --auto --dir <workspace>/projects/my-project \
  --model <provider>/<id> \
  "Create a landing page for a coffee shop with menu, about, contact. Mobile-responsive."
```

`--auto` is required when the parent agent cannot answer permission prompts. Pair it with a tight `permission` block (deny destructive bash) so unattended runs cannot wander.

OpenClaw exec extras: `pty: true`, `timeout: 600`, `yieldMs: 600000`. Without a PTY you will see no streaming output.

Attachments:

```bash
opencode run --auto --model <provider>/<id> --file design.png "Match this design"
```

After the run: list files, read the important ones, report path + what changed.

## Config notes

See [OpenCode config](https://opencode.ai/docs/config/) and [permissions](https://opencode.ai/docs/permissions/). A safe unattended shape:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "allow",
    "bash": "ask"
  }
}
```

With `--auto`, `bash: ask` will be auto-approved — keep the task prompt narrow.

## Troubleshooting

| Symptom | Fix |
|---|---|
| No output | Need a PTY / interactive terminal |
| Hangs, no files | Check auth (`opencode auth login`), `opencode models`, try a one-file task |
| "No provider configured" | Login or env vars; do not assume a JSON key you just invented |
| Completes, empty dir | Confirm `--dir` / cwd; re-run with `--format json` if available |

## Tips

1. Be specific (stack, styling, files).
2. Test the install with a tiny task first.
3. Run from the project directory (or `--dir`).
4. Complex tasks may need 10–15 minutes.

## Reference

- Docs: https://opencode.ai/docs
- CLI: https://opencode.ai/docs/cli/
- Schema: https://opencode.ai/config.json
