# Google Antigravity CLI (`agy`) Cheat Sheet
### …plus legacy Gemini CLI

> ⚠️ **Heads up:** Google replaced **Gemini CLI** with **Antigravity CLI (`agy`)**. Since **18 June 2026**, Gemini CLI no longer serves requests for free, Google AI Pro and AI Ultra users. Enterprise / Google Cloud customers keep Gemini CLI access. If you're an individual user, `agy` is the one you want.
>
> Verified against the official Antigravity docs (antigravity.google/docs/cli/reference), October 2026.

---

## 📦 Install & Sign In

```bash
curl -fsSL https://antigravity.google/cli/install.sh | bash    # macOS / Linux
irm https://antigravity.google/cli/install.ps1 | iex           # Windows PowerShell

agy --version
agy                 # first run signs you in via the browser (OAuth)
```
Migrating? `agy plugin import gemini` converts Gemini CLI extensions to Antigravity plugins.

---

## 🚀 Starting & Resuming

| Command | What it does |
|---|---|
| `agy` | Start an interactive session |
| `agy -i "refactor auth"` | Run an opening prompt, then stay in the session |
| `agy -p "explain this repo"` | One-shot: print the answer and exit (headless) |
| `git diff \| agy -p "write a commit message"` | Pipe content in |
| `agy -c` | Continue the most recent conversation |
| `agy --conversation <id>` | Resume a specific conversation |
| `agy --add-dir ../backend` | Add another directory to the workspace (repeatable) |

---

## 🎛️ Flags

| Flag | Values / Use |
|---|---|
| `--model` | Pick the model (list with `agy models`) |
| `--effort` | `low` … `max` (depends on model) |
| `--mode` | `accept-edits` · `plan` · `auto` (preview, needs sandbox) |
| `--agent <name>` | Use a custom agent (list with `agy agents`) |
| `--sandbox` | Run terminal commands in an OS sandbox |
| `--dangerously-skip-permissions` | Auto-approve everything ⚠️ |
| `--project <id>` / `--new-project` | Choose or create a project |
| `--remote-control` | Allow driving the session from a browser |
| `--log-file <path>` | Override log location |

### Headless / scripting (`-p`)
| Flag | Use |
|---|---|
| `--output-format` | `text` · `json` · `stream-json` |
| `--input-format` | `text` · `stream-json` |
| `--json-schema` | Enforce structured output (string or file path) |
| `--print-timeout` | Time limit (default `0s` = wait for completion) |
| `--disable-slash-commands` | Treat `/…` prompts as plain text |

```bash
agy -p "Summarise the staged diff" --output-format json
```

---

## ⌨️ Input Prefixes
`/` slash commands · `@` file path autocomplete · `!` run a shell command · `?` help

---

## ⌨️ Keyboard Shortcuts

| Key | Action |
|---|---|
| `Esc` | Close panel / stop the agent / clear empty prompt |
| `Ctrl+C` | Exit (confirms if the agent is working) |
| `Ctrl+D` | Exit (empty prompt) |
| `Shift+Tab` | Cycle modes: default → accept-edits → auto → plan |
| `Shift+Enter` / `Ctrl+J` | New line |
| `Ctrl+O` | Expand/collapse tool details |
| `Ctrl+R` | Open Artifact Review panel |
| `Ctrl+G` | Compose prompt in `$EDITOR` |
| `Ctrl+V` | Paste images / clipboard |
| `Alt+J` | Jump to next subagent waiting for approval |
| `Ctrl+K` | Approve pending subagent action |
| `Ctrl+_` / `Ctrl+Shift+Z` | Undo / redo |
| `F5` | Voice dictation |
| `y` / `n` / `A` | Approve / reject / approve all (in review) |

---

## 💬 Slash Commands

| Command | What it does |
|---|---|
| `/help` | Commands & shortcuts |
| `/model` | Choose model (`/model <model> <prompt>` for a one-off) |
| `/effort` | Set reasoning effort |
| `/plan [task]` | Explore, clarify and draft an implementation plan |
| `/goal <objective>` | Work autonomously until the goal is verified |
| `/grill-me` | Agent interviews you before starting |
| `/boost <task>` | Multi-agent deep reasoning |
| `/browser <task>` | Hand off to the browser subagent |
| `/codesearch` (`/cs`) | Search code across the workspace |
| `/diff` | Interactive diff viewer |
| `/context` | Context usage panel |
| `/btw <question>` | Side question without interrupting |
| `/clear` (`/new`) | Reset the conversation |
| `/resume` (`/switch`) | Pick a previous conversation |
| `/rewind` (`/undo`) | Roll back to an earlier message |
| `/fork` · `/rename` | Branch / rename the session |
| `/permissions` | Tool permissions manager |
| `/config` (`/settings`) | Settings editor |
| `/mcp` · `/skills` · `/plugin` · `/hooks` · `/agents` | Manage extensions |
| `/learn` | Turn corrections into persistent rules/skills |
| `/schedule <when> <task>` | One-shot or recurring scheduled task |
| `/tasks` | Background shell tasks |
| `/usage` (`/quota`) · `/credits` | Quota & credits |
| `/copy` | Copy last response |
| `/exit` (`/quit`) | Quit |

---

## 📁 Key Files

| Path | Purpose |
|---|---|
| `~/.gemini/antigravity-cli/settings.json` | Settings (theme, `agentMode`, `toolPermission`, sandbox…) |
| `~/.gemini/antigravity-cli/keybindings.json` | Custom keybindings |
| `~/.gemini/antigravity-cli/log/` | Logs (first stop when debugging) |

Using a Gemini API key: set `"modelProvider": "gemini"` in settings and export `GEMINI_API_KEY`.

---
---

# 🗄️ Legacy: Gemini CLI (`gemini`)
*For enterprise / Google Cloud users who still have access.*

| Command | What it does |
|---|---|
| `gemini` | Start interactive session |
| `gemini -p "query"` | One-shot, non-interactive |
| `git diff \| gemini -p "write a commit message"` | Pipe content in |
| `gemini -m <model>` | Choose model |
| `gemini --include-directories a,b` | Add workspace directories |

| Slash command | What it does |
|---|---|
| `/help` (`/?`) | Help |
| `/memory list \| show \| refresh` | Manage `GEMINI.md` context |
| `/directory add <path>` (`/dir`) | Add workspace directories |
| `/compress` | Summarise chat to save tokens |
| `/commands list \| reload` | Custom TOML commands |
| `/copy` | Copy last output |
| `/about` | Version info |
| `/quit` (`/exit`) | Quit (`--delete` removes history) |

Prefixes: `@file` to include files, `!cmd` to run shell (lone `!` toggles shell mode).
Files: `~/.gemini/settings.json`, `GEMINI.md` (project context), `.gemini/commands/*.toml`.

---

## 🔗 Docs
- Antigravity CLI reference: https://antigravity.google/docs/cli/reference/
- Gemini CLI migration guide: https://antigravity.google/docs/cli/gcli-migration/
