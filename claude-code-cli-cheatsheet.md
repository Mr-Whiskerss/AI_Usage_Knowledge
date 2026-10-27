# Claude Code CLI Cheat Sheet

> Verified against the official docs (code.claude.com/docs), October 2026. Some features need recent versions, so run `claude update` if something's missing. `claude --help` doesn't list every flag.

---

## 🚀 Starting & Resuming

| Command | What it does |
|---|---|
| `claude` | Start an interactive session |
| `claude "explain this project"` | Start interactive session with an initial prompt |
| `claude -p "query"` | One-shot: print the answer and exit (headless) |
| `cat logs.txt \| claude -p "explain"` | Pipe content in |
| `claude -c` | Continue the most recent conversation in this directory |
| `claude -c -p "check for type errors"` | Continue, non-interactively |
| `claude -r` | Open the session picker |
| `claude -r "auth-refactor" "finish this PR"` | Resume a session by ID or name |
| `claude -n "my-feature"` | Give the session a name (resume it later by name) |
| `claude --resume abc123 --fork-session` | Resume as a new branch of the session |
| `claude --from-pr 123` | Pick from sessions linked to a PR |
| `claude -w feature-auth` | Start in an isolated git worktree |

---

## 🛠️ Subcommands

| Command | What it does |
|---|---|
| `claude update` | Update to the latest version |
| `claude install stable` | Install/reinstall a specific version (`stable`, `latest`, or `2.1.x`) |
| `claude doctor` | Installation & settings diagnostics |
| `claude auth login` / `logout` / `status` | Manage sign-in (`--console` for API billing, `--sso` for SSO) |
| `claude setup-token` | Generate a long-lived OAuth token for CI/scripts |
| `claude mcp` | Configure MCP servers |
| `claude mcp login <name>` | Run an MCP server's OAuth flow from the shell |
| `claude plugin install <name>@<marketplace>` | Manage plugins |
| `claude agents` | Agent view: monitor/dispatch background sessions (`--json` to script) |
| `claude attach <id>` / `logs <id>` / `stop <id>` / `rm <id>` | Manage background sessions |
| `claude purge [path] --dry-run` | Delete local Claude Code state for a project |
| `claude remote-control` | Control Claude Code from claude.ai or the Claude app |
| `claude ultrareview 1234 --json` | Run ultrareview non-interactively |

---

## 🎛️ Most Useful Flags

### Model & behaviour
| Flag | Example |
|---|---|
| `--model` | `claude --model opus` (aliases: `sonnet`, `opus`, `haiku`, `fable`) |
| `--fallback-model` | `claude --fallback-model sonnet,haiku` |
| `--effort` | `claude --effort high` (`low`, `medium`, `high`, `xhigh`, `max`, `ultracode`) |
| `--permission-mode` | `claude --permission-mode plan` (`default`, `acceptEdits`, `plan`, `auto`, `dontAsk`, `bypassPermissions`) |
| `--dangerously-skip-permissions` | Skip all permission prompts ⚠️ use only in sandboxes |
| `--add-dir` | `claude --add-dir ../apps ../lib` |
| `--agent` | `claude --agent my-custom-agent` |
| `--bg` | `claude --bg "investigate the flaky test"` (background session) |

### Tools & permissions
| Flag | Example |
|---|---|
| `--tools` | `claude --tools "Bash,Edit,Read"` (restrict available tools) |
| `--allowedTools` | `claude --allowedTools "Bash(git log *)" "Read"` (no prompt for these) |
| `--disallowedTools` | `claude --disallowedTools "Bash(rm *)" "Edit"` |
| `--mcp-config` | `claude --mcp-config ./mcp.json` |
| `--strict-mcp-config` | Only use servers from `--mcp-config` |

### System prompt
| Flag | Behaviour |
|---|---|
| `--append-system-prompt "..."` | Add rules on top of the default prompt (usually what you want) |
| `--append-system-prompt-file ./rules.txt` | Same, from a file |
| `--system-prompt "..."` | **Replace** the whole default prompt |
| `--system-prompt-file ./prompt.txt` | Replace, from a file |

### Troubleshooting
| Flag | Use |
|---|---|
| `--safe-mode` | Start with all customisations disabled (CLAUDE.md, hooks, MCP, plugins...) |
| `--debug='mcp,startup'` | Debug mode with category filter |
| `--verbose` | Full turn-by-turn output |
| `-v` / `--version` | Show version |

---

## 🤖 Scripting / CI (print mode)

```bash
# Basic one-shot
claude -p "summarise README.md"

# JSON output for parsing
git diff | claude -p "list breaking changes" --output-format json

# Streaming JSON (needs --verbose)
claude -p --output-format stream-json --verbose "query"

# Guardrails
claude -p --max-turns 3 --max-budget-usd 5.00 "query"

# Validated structured output
claude -p --json-schema '{"type":"object","properties":{...}}' "query"

# Fast, minimal startup (skips hooks, skills, plugins, MCP, CLAUDE.md)
claude --bare -p "query"

# Deny any permission prompt nobody can answer
claude -p --permission-prompts none "query"

# Don't save the session to disk
claude -p --no-session-persistence "query"
```

Print-mode-only flags: `--output-format` (`text` | `json` | `stream-json`), `--input-format`, `--max-turns`, `--max-budget-usd`, `--json-schema`, `--no-session-persistence`.

---

## ⌨️ Input Prefixes

| Type | Does |
|---|---|
| `/` | Commands & skills (type `/` to see them all, keep typing to filter) |
| `!` | Shell mode: run a command directly, output goes into context (`! npm test`) |
| `@` | File path autocomplete / mention |
| `?` (empty input) | Toggle shortcut help panel |

---

## ⌨️ Keyboard Shortcuts

### Control
| Key | Action |
|---|---|
| `Esc` | Interrupt Claude (keeps work so far) / close dialog |
| `Esc` `Esc` | Clear draft, or (empty input) open **rewind** menu |
| `Ctrl+C` | Interrupt; on idle, 1st press clears input, 2nd exits |
| `Ctrl+D` | Exit (press twice) |
| `Shift+Tab` | Cycle permission modes (Manual → acceptEdits → plan → ...) |
| `Option/Alt+P` | Switch model |
| `Option/Alt+T` | Toggle extended thinking (where supported) |
| `Option/Alt+O` | Toggle fast mode |
| `Ctrl+O` | Toggle transcript viewer |
| `Ctrl+B` | Background a running task (tmux: press twice) |
| `Ctrl+T` | Toggle Claude's task checklist |
| `Ctrl+R` | Reverse search prompt history |
| `Ctrl+S` | Stash / restore current prompt |
| `Ctrl+G` | Edit prompt in your `$EDITOR` |
| `Ctrl+V` (`Alt+V` on Windows) | Paste image from clipboard |
| `Ctrl+L` | Redraw screen |
| `Ctrl+Enter` | Send queued messages now |
| `Ctrl+X Ctrl+K` | Stop all background subagents (press twice) |

### Multiline input
`\` + `Enter` (works everywhere) · `Shift+Enter` (most modern terminals) · `Ctrl+J` · `Option+Enter` (macOS, Option-as-Meta)

### Text editing (readline-style)
`Ctrl+A` / `Ctrl+E` line start/end · `Ctrl+K` delete to end · `Ctrl+U` delete to start · `Ctrl+W` delete back to whitespace · `Ctrl+Y` paste deleted · `Alt+B` / `Alt+F` word back/forward · `Ctrl+_` undo

> 💡 macOS: `Alt` shortcuts need "Option as Meta" enabled in your terminal.

---

## 💬 Handy Slash Commands

| Command | What it does |
|---|---|
| `/help` | List help & commands |
| `/init` | Generate a `CLAUDE.md` for the project |
| `/memory` | Edit memory / `CLAUDE.md` files |
| `/clear` | Start a fresh conversation (old one stays resumable) |
| `/compact` | Summarise the conversation to free context |
| `/context` | See what's using your context window |
| `/model` | Switch model |
| `/effort` | Change effort level |
| `/fast` | Toggle fast mode |
| `/config` | Settings (vim mode, theme, prompt suggestions...) |
| `/permissions` | View/edit allow & deny rules |
| `/mcp` | Manage MCP servers |
| `/agents` | Manage subagents |
| `/plugin` | Browse/install/enable plugins |
| `/resume` | Pick a previous session |
| `/rename` | Rename the current session |
| `/rewind` | Restore code/conversation to an earlier point |
| `/diff` | Review working-tree changes and per-turn edits |
| `/btw <question>` | Side question that doesn't enter history |
| `/tasks` | Running background shells & subagents |
| `/recap` | One-line summary of the session so far |
| `/add-dir` | Add another working directory |
| `/status` · `/cost` | Session info / token usage |
| `/doctor` | Setup checkup (can apply fixes) |
| `/login` · `/logout` | Switch accounts |
| `/exit` | Quit |

---

## 📁 Key Files

| Path | Purpose |
|---|---|
| `CLAUDE.md` (project root) | Project instructions loaded every session |
| `~/.claude/CLAUDE.md` | Your personal global instructions |
| `~/.claude/settings.json` | User settings |
| `.claude/settings.json` | Project settings (commit to repo) |
| `.claude/settings.local.json` | Personal project settings (git-ignored) |
| `.claude/worktrees/<name>` | Worktrees created by `-w` |

---

## 🔗 Docs

- CLI reference: https://code.claude.com/docs/en/cli-reference
- Interactive mode & shortcuts: https://code.claude.com/docs/en/interactive-mode
- Commands: https://code.claude.com/docs/en/commands
