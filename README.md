# AI CLI Master Cheat Sheet

> The one-page overview for **AI_Usage_Knowledge**. Use this to find the equivalent command across tools, then open the tool's own sheet for the details. Last checked October 2026.

##  What's in this repo

| File | Tool | Command | Signs in with |
|---|---|---|---|
| [claude-code-cli-cheatsheet.md](claude-code-cli-cheatsheet.md) | Claude Code (Anthropic) | `claude` | Claude subscription or API key |
| [codex-cli-cheatsheet.md](codex-cli-cheatsheet.md) | Codex CLI (OpenAI) | `codex` | ChatGPT plan or API key |
| [google-antigravity-gemini-cli-cheatsheet.md](google-antigravity-gemini-cli-cheatsheet.md) | Antigravity CLI (Google), plus legacy Gemini CLI | `agy` / `gemini` | Google account (OAuth) |
| [openai-chatgpt-cli-cheatsheet.md](openai-chatgpt-cli-cheatsheet.md) | OpenAI CLI | `openai` | API key only (pay-as-you-go) |

>  **Gemini CLI** stopped working for free, AI Pro and AI Ultra users on 18 June 2026. Use **`agy`** instead.
>  **OpenAI CLI** isn't a coding agent. It sends raw API calls (text, images, audio). For ChatGPT-style agent work in a terminal, use **Codex**.

---

## Install

| Tool | Command |
|---|---|
| Claude Code | See docs, then `claude update` to stay current |
| Codex | `curl -fsSL https://chatgpt.com/codex/install.sh \| sh` or `npm i -g @openai/codex` |
| Antigravity | `curl -fsSL https://antigravity.google/cli/install.sh \| bash` |
| OpenAI CLI | `brew install openai/tools/openai` |

---

## Rosetta Stone: same task, three agents

### Sessions
| Task | Claude Code | Codex | Antigravity |
|---|---|---|---|
| Start interactive | `claude` | `codex` | `agy` |
| Start with a prompt | `claude "task"` | `codex "task"` | `agy -i "task"` |
| One-shot, print & exit | `claude -p "task"` | `codex exec "task"` | `agy -p "task"` |
| Pipe input | `cat f \| claude -p "…"` | `cat f \| codex exec -` | `cat f \| agy -p "…"` |
| Continue last session | `claude -c` | `codex resume --last` | `agy -c` |
| Pick a past session | `claude -r` | `codex resume` | `/resume` |
| Resume a specific one | `claude -r <id/name>` | `codex resume <id>` | `agy --conversation <id>` |
| Branch a session | `--fork-session` | `codex fork <id>` | `/fork` |

### Model & behaviour
| Task | Claude Code | Codex | Antigravity |
|---|---|---|---|
| Choose model | `--model opus` | `-m <model>` | `--model <model>` |
| Reasoning effort | `--effort high` | `/model` (model + effort) | `--effort high` |
| Plan before acting | `--permission-mode plan` | `/plan` | `--mode plan` |
| Auto-accept file edits | `--permission-mode acceptEdits` | `-s workspace-write -a on-request` | `--mode accept-edits` |
| Skip ALL approvals ⚠️ | `--dangerously-skip-permissions` | `--yolo` | `--dangerously-skip-permissions` |
| Sandbox commands | (settings) | `-s read-only \| workspace-write` | `--sandbox` |
| Extra directories | `--add-dir ../x` | `--add-dir ../x` | `--add-dir ../x` |
| Git worktree | `-w name` | `--worktree` | — |
| Attach image | `Ctrl+V` | `-i file.png` | `Ctrl+V` |

### Scripting / CI
| Task | Claude Code | Codex | Antigravity |
|---|---|---|---|
| JSON output | `--output-format json` | `exec --json` | `--output-format json` |
| Streaming JSON | `--output-format stream-json` | `exec --json` (JSONL) | `--output-format stream-json` |
| Enforce a schema | `--json-schema '…'` | `--output-schema file.json` | `--json-schema …` |
| Don't save session | `--no-session-persistence` | `--ephemeral` | — |
| Cap turns / spend | `--max-turns` · `--max-budget-usd` | — | `--print-timeout` |

### Housekeeping
| Task | Claude Code | Codex | Antigravity |
|---|---|---|---|
| Update | `claude update` | `codex update` | `agy update` |
| Diagnose problems | `claude doctor` | `codex doctor` | check `~/.gemini/antigravity-cli/log/` |
| MCP servers | `claude mcp` | `codex mcp` | `agy mcp` / `/mcp` |
| Plugins | `claude plugin` | `codex plugin` | `agy plugin` / `/plugin` |
| Sign in / out | `claude auth login` / `logout` | `codex login` / `logout` | browser on first run / `/logout` |

---

## Inside a session

| Task | Claude Code | Codex | Antigravity |
|---|---|---|---|
| Switch model | `/model` | `/model` | `/model` |
| Permissions | `/permissions` | `/permissions` | `/permissions` |
| Create project instructions file | `/init` | `/init` | — |
| Review changes | `/diff` | `/review` | `/diff` |
| Undo / rewind | `Esc Esc` or `/rewind` | — | `/rewind` |
| Side question | `/btw` | — | `/btw` |
| Fresh conversation | `/clear` | — | `/clear` |
| Cycle modes | `Shift+Tab` | — | `Shift+Tab` |
| Run a shell command | `!cmd` | — | `!cmd` |
| Mention a file | `@path` | — | `@path` |
| Write prompt in editor | `Ctrl+G` | — | `Ctrl+G` |
| Show shortcuts | `?` | `?` | `?` |
| Quit | `/exit` | — | `/exit` |

"—" means not covered in that tool's sheet, not necessarily missing.

---

## Where config & instructions live

| | Claude Code | Codex | Antigravity |
|---|---|---|---|
| Project instructions | `CLAUDE.md` | `AGENTS.md` | Rules & Skills |
| User settings | `~/.claude/settings.json` | `~/.codex/config.toml` | `~/.gemini/antigravity-cli/settings.json` |
| Project settings | `.claude/settings.json` | `.codex/config.toml` | — |

---

## Safety habits (all tools)

1. **Commit before you start.** Git is your real undo button.
2. **Default to asking.** Only use skip-permissions / `--yolo` in a throwaway container or VM.
3. **Prefer plan mode** for big changes, and read the plan before letting it run.
4. **Sandbox in CI**, e.g. `codex exec -s workspace-write`, or `agy -p` with `--sandbox`.
5. **Never commit API keys.** Keep them in env vars or a git-ignored `.env`.
6. **Keep tools updated.** All of these change monthly, so flags here can drift.

---

## Official docs
- Claude Code: https://code.claude.com/docs/en/cli-reference
- Codex: https://learn.chatgpt.com/docs/developer-commands
- Antigravity: https://antigravity.google/docs/cli/reference/
- OpenAI CLI: https://developers.openai.com/api/docs/libraries/openai-cli
