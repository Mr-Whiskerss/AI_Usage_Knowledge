# OpenAI CLI (`openai`) Cheat Sheet

> The official OpenAI command-line tool for calling the OpenAI API (the models behind ChatGPT) straight from your terminal. Verified against OpenAI's docs, October 2026.
>
> ⚠️ This uses **API billing** with an API key, not your ChatGPT subscription. For a ChatGPT-plan coding agent in the terminal, use **Codex CLI** (separate cheat sheet).

---

## 📦 Install & Auth

```bash
brew install openai/tools/openai                              # Homebrew
go install 'github.com/openai/openai-cli/cmd/openai@latest'   # Go 1.25+

export OPENAI_API_KEY="sk-..."        # regular API calls
export OPENAI_ADMIN_KEY="..."         # Admin API commands
export OPENAI_BASE_URL="https://..."  # point at another compatible endpoint
```
> Old versions of the Python SDK installed a legacy `openai` command. If commands don't match this sheet, your shell may be finding the old binary.

---

## 🎛️ Global Flags

| Flag | Use |
|---|---|
| `--format` | `auto` · `json` · `jsonl` · `pretty` · `raw` · `yaml` · `explore` |
| `--transform` | Pull out / reshape data with a GJSON path |
| `--debug` | Print request & response details to stderr (auth redacted) |

**Most useful transform** (grab just the assistant's text):
```bash
--transform 'output.#(type=="message").content.0.text'
```

---

## 💬 Text (Responses API)

```bash
# Simple prompt
openai responses create --model gpt-6-astra --input "Say hello in one sentence."

# Just the text, not the full JSON
openai responses create --model gpt-6-astra --input "Explain DNS briefly" \
  --format yaml --transform 'output.#(type=="message").content.0.text'

# Include a local file in the prompt
openai responses create --model gpt-6-astra \
  --input "Summarise this: $(cat ./note.md)" \
  --format yaml --transform 'output.#(type=="message").content.0.text'
```

### Multiline / complex requests → YAML here-doc
```bash
openai responses create --format yaml \
  --transform 'output.#(type=="message").content.0.text' <<'YAML'
model: gpt-5.5
instructions: Return exactly one sentence.
input: |
  Summarise this release note.
YAML
```
> 💡 Prompts containing `:` or `{}` can be misread as YAML when passed as flags, so put them under `input: |` instead.

### Structured JSON output
```bash
openai responses create --model gpt-6-astra \
  --instructions "Extract the person and topic." \
  --input "Ada Lovelace wrote notes about the Analytical Engine." \
  --text.format "$(cat ./schema.json)" \
  --format yaml --transform 'output.#(type=="message").content.0.text'
```

### Web search tool
```bash
openai responses create --model gpt-6-astra <<'YAML'
tools:
  - type: web_search
input: Latest news on X, three bullets with sources.
YAML
```

### Files (PDFs etc.)
```bash
FILE_ID=$(openai files create --file ./brief.pdf --purpose user_data \
  --format yaml --transform id)
# then reference it as input_file.file_id in a YAML body
```

---

## 🖼️ Images

```bash
# Generate (decode the base64 yourself — no --output yet)
openai images generate --model gpt-image-2 --prompt "A green cube" \
  --format yaml --transform 'data.0.b64_json' | base64 --decode > cube.png

# Edit
openai images edit --model gpt-image-2 --image ./cube.png \
  --prompt "Make it blue" \
  --format yaml --transform 'data.0.b64_json' | base64 --decode > cube2.png
```
Transparent background: `--background transparent` (png or webp).

---

## 🔊 Audio

```bash
# Text to speech (note: gpt-4o-mini-tts is deprecated, shutting down 6 Jan 2027)
openai audio:speech create --model gpt-4o-mini-tts --voice marin \
  --input "Hello from the terminal" --output hello.mp3

# Transcription → plain text
openai audio:transcriptions create --model gpt-transcribe \
  --file ./speech.mp3 --transform text --raw-output
```

---

## 🏢 Admin API (needs `OPENAI_ADMIN_KEY`)

```bash
openai admin:organization:projects create --name "automation project" --format json
openai admin:organization:projects:service-accounts create \
  --project-id "$PROJECT_ID" --name "automation bot" --format json
```
⚠️ Responses contain live API keys, so keep the output files out of git.

---

## 🔗 Docs
- OpenAI CLI: https://developers.openai.com/api/docs/libraries/openai-cli
- API reference: https://developers.openai.com/api/reference
