---
name: opencode
description: Use the OpenCode CLI for multi-iteration AI code generation — new projects, edits, prototypes. Triggers on "opencode run", "OpenCode CLI", or when a host wants an external coding agent instead of editing files itself. Do not use for simple one-file edits the host can do directly.
license: CC-BY-SA-4.0
---

# OpenCode

External coding CLI. The host agent installs it, picks a model, and runs
`opencode run --model … "prompt"` **from the project directory** with a
pseudo-terminal so output streams.

## When to use

- Multi-iteration project create/edit that would burn the host session
- Scaffolding a new app in `<workspace>/projects/<name>/`

**Do not use** for a single file write, a rename, or read-only analysis.

## Install

```bash
which opencode || curl -fsSL https://opencode.ai/install | bash
ln -sf ~/.opencode/bin/opencode ~/.local/bin/opencode
opencode --version
```

## Config (never commit keys)

Prefer env vars. If you write `~/.config/opencode/opencode.json`, use
`{env:OPENROUTER_API_KEY}` — never a literal `sk-…` in a file that might
be copied or committed.

```bash
export OPENROUTER_API_KEY="…"   # ask the user; do not echo it
opencode models openrouter      # refresh names; they change
```

Ask which model to use. Suggested starting points (re-check with
`opencode models`): complex → `openrouter/anthropic/claude-sonnet-4.5`;
simple → a Haiku-class model.

Full config + troubleshooting: [references/config-and-troubleshooting.md](references/config-and-troubleshooting.md).

## How to run (host-agnostic)

Always:

1. `mkdir -p <workspace>/projects/<name> && cd <workspace>/projects/<name>`
   (or `cd` into an existing project). Never `~/` as the project root.
2. Run with a **PTY**. Without a PTY, OpenCode looks hung and you see no output.
3. Block until it finishes (10 minutes default; 15 for large scaffolds).
4. Then `ls` and read the files it created. Report paths and next steps.

### Cursor / generic shell

Use the host's shell tool with a PTY/tmux session and a long block time
(600000 ms ≈ `yieldMs` in OpenClaw docs). Example:

```bash
cd <workspace>/projects/<name> && opencode run --model <model> "<prompt>"
```

### OpenClaw-style spawn

If the host has `sessions_spawn` / `exec` with `pty`, `timeout`, `yieldMs`,
use those. Subagents do **not** inherit this skill — tell them to read it
first. See [references/host-spawn.md](references/host-spawn.md).

## After it finishes

```bash
ls -la <workspace>/projects/<name>
# read the entry files; mention what was created and where
```

Docs: https://opencode.ai/docs · CLI: https://opencode.ai/docs/cli/
