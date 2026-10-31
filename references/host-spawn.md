# OpenClaw-style OpenCode spawn

Only if the host actually has `sessions_spawn` / `exec` with `pty` and
`yieldMs`. Cursor Cloud agents should use a shell + tmux instead (see SKILL.md).

```
sessions_spawn:
  task: "Read the opencode skill, then use OpenCode to <task> at <workspace>/projects/<name>"
  label: "opencode-<name>"
  runTimeoutSeconds: 660
```

Tell the subagent to read this skill first. Include the model in the task
text. Projects go in `<workspace>/projects/`, never `$HOME`.

Direct exec shape:

```
exec:
  command: cd /path/to/project && opencode run --model <model> "Task"
  pty: true
  timeout: 600
  yieldMs: 600000
```

`pty: true` is required. `yieldMs` must cover `timeout`.
