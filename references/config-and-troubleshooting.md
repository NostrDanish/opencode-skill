# OpenCode config and troubleshooting

## Config file

`~/.config/opencode/opencode.json` — never commit it; it may hold keys.

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "openrouter/anthropic/claude-sonnet-4.5",
  "provider": {
    "openrouter": {
      "options": {
        "apiKey": "{env:OPENROUTER_API_KEY}"
      }
    }
  },
  "tools": { "write": true, "bash": true },
  "permission": { "edit": "auto", "bash": "ask" }
}
```

`{env:VAR_NAME}` loads from the environment. `{file:path}` loads from a file.
Do not paste `sk-or-…` or `sk-ant-…` into the JSON.

Env alternative: `OPENROUTER_API_KEY`, `ANTHROPIC_API_KEY`.

Refresh models: `opencode models` / `opencode models openrouter`.
Auth check: `opencode auth list`.

## Troubleshooting

| Symptom | Fix |
|---|---|
| No output, looks hung | PTY is missing. Re-run in a real terminal / `pty: true` / tmux. |
| 10+ minutes, no files | Key or model. `opencode models <provider>`, then a 1-file test prompt. |
| "No provider configured" | Config file missing or env unset. `opencode auth list`. |
| Completes, no files | Wrong `cwd`. Run from the project directory. `--format json` for a structured log. |

## Prompts

Be specific (stack, styling, files to touch). Start with a small task to
prove the key works before a 10-minute scaffold.
