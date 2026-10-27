# OpenAI Codex CLI Cheat Sheet

> Verified against OpenAI's official Codex docs (learn.chatgpt.com / developers.openai.com), October 2026. Codex signs in with your **ChatGPT** account (or an API key). Run `codex update` if something's missing.

---

## 📦 Install & Sign In

```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh   # macOS / Linux
irm https://chatgpt.com/codex/install.ps1 | iex        # Windows PowerShell
npm install -g @openai/codex                           # npm
brew install --cask codex                              # Homebrew

codex                    # first run opens browser sign-in
codex login              # sign in again
codex logout             # remove stored credentials
printenv OPENAI_API_KEY | codex login --with-api-key   # API key (scripts/CI)
```

---

## 🚀 Sessions

| Command | What it does |
|---|---|
| `codex` | Start the interactive terminal UI (TUI) |
| `codex "explain this repo"` | Start with an initial prompt |
| `codex -i screenshot.png "fix this layout"` | Attach image(s) to the first prompt |
| `codex resume` | Pick a saved chat to reopen |
| `codex resume --last` | Reopen the most recent chat (`--all` = any directory) |
| `codex fork <SESSION>` | Branch a saved chat into a new one |
| `codex archive <SESSION>` / `unarchive` | Hide / restore a chat in the picker |
| `codex delete <SESSION>` | Permanently delete a chat ⚠️ |
| `codex queue --thread <ID> --message "..."` | Queue a message for an existing session |
| `codex agents` | Open the agent command center |

---

## 🎛️ Global Flags

| Flag | Example / Values |
|---|---|
| `-m, --model` | `codex -m gpt-6.1-sol` |
| `-s, --sandbox` | `read-only` · `workspace-write` · `danger-full-access` |
| `-a, --ask-for-approval` | `on-request` · `never` |
| `--approve-for-me` | Auto-review approvals + `workspace-write` sandbox |
| `--yolo` | No approvals, no sandbox ⚠️ hardened environments only |
| `-C, --cd <path>` | Set the working directory |
| `--add-dir <path>` | Grant write access to another directory (repeatable) |
| `-c key=value` | One-off config override (parsed as TOML) |
| `-p, --profile <name>` | Layer a named config profile on top of your config |
| `--search` | Enable **live** web search (default is cached) |
| `--worktree` | Start in a new managed Git worktree |
| `--oss` | Use a local open-source model (LM Studio / Ollama) |
| `--enable` / `--disable <feature>` | Toggle a feature flag for this run |

> 💡 Recommended low-friction local setup: `codex --sandbox workspace-write --ask-for-approval on-request`

---

## 🤖 Scripting / CI with `codex exec` (alias `codex e`)

```bash
# Basic one-shot
codex exec "update the changelog for this diff"

# Pipe the prompt from stdin
cat task.md | codex exec -

# Sandboxed, no prompts (good default for CI)
codex exec -s workspace-write "run tests and fix failures"

# JSON events (JSONL) instead of formatted text
codex exec --json "summarise the repo"

# Save the final message to a file
codex exec -o result.txt "write release notes"

# Validate the final answer against a JSON Schema
codex exec --output-schema schema.json "extract the API endpoints"

# Don't persist the session to disk
codex exec --ephemeral "quick question"

# Run outside a git repo
codex exec --skip-git-repo-check "tidy these files"

# Resume / fork exec sessions
codex exec resume --last "now add tests"
codex exec fork <SESSION_ID> "try a different approach"
```

---

## 🔍 Code Review

```bash
codex review --uncommitted          # staged, unstaged & untracked changes
codex review --base main            # everything vs a base branch
codex review --commit abc123        # a single commit
codex review "focus on security"    # custom instructions
```
Pick **one** target per run. Reviews don't modify your working tree.

---

## 🛠️ Other Subcommands

| Command | What it does |
|---|---|
| `codex update` | Update the CLI |
| `codex doctor` | Diagnostic report (`--json`, `--summary`) |
| `codex mcp list` / `add` / `remove` | Manage MCP servers |
| `codex mcp login <name>` | Authenticate an MCP server |
| `codex plugin` | Install / list / remove plugins |
| `codex features list` / `enable` / `disable` | Manage feature flags persistently |
| `codex cloud` | Browse or run Codex cloud chats |
| `codex cloud exec --env <ENV_ID> "task"` | Submit a cloud task (`--attempts 1-4`) |
| `codex apply <TASK_ID>` | Apply a cloud chat's diff locally (alias `codex a`) |
| `codex sandbox` | Run arbitrary commands inside Codex's sandbox |
| `codex completion zsh` | Shell completions (bash, zsh, fish, power-shell, elvish) |
| `codex app` | Launch the ChatGPT desktop app |

---

## 💬 Slash Commands (inside the TUI)

| Command | What it does |
|---|---|
| `/init` | Create an `AGENTS.md` with instructions for Codex |
| `/status` | Show current session configuration |
| `/model` | Choose model & reasoning effort |
| `/permissions` | Choose what Codex can do without asking |
| `/review` | Review changes and find issues |
| `/plan` | Plan before acting |
| `/goal` | Set a goal to work toward |
| `/mcp` | Inspect MCP servers & tools |
| `/feedback` | Send feedback |

Press `?` in the TUI to see keyboard shortcuts.

---

## 📁 Key Files

| Path | Purpose |
|---|---|
| `~/.codex/config.toml` | Main config (shared by CLI, desktop app & IDE extension) |
| `.codex/config.toml` | Project-level config |
| `AGENTS.md` | Project instructions Codex reads every session |
| `$CODEX_HOME/<profile>.config.toml` | Named profile used with `-p` |

Precedence: CLI flags & `-c` overrides beat config files.

---

## 🔗 Docs
- Codex CLI: https://learn.chatgpt.com/docs/codex/cli
- Command reference: https://learn.chatgpt.com/docs/developer-commands
