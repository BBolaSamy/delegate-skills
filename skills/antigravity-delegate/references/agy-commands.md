# Antigravity CLI (`agy`) commands

> Verified against `agy --version` 1.0.10 / `agy --help` on 2026-06-24.
> `agy` runs headless via `-p` (print mode): it edits files in the current
> working directory (the workspace) and prints a text response. There is **no**
> `run` subcommand, **no** `--prompt-file`, and **no** JSON output — capture
> what changed with `git diff`, not from stdout. Re-run `agy --help` if a flag
> errors; the CLI is young.

## Auth (never put credentials in briefs)

- Authenticate once with `agy login` (shares your Antigravity account), or for
  unattended use export an API key: `ANTIGRAVITY_API_KEY` or `GEMINI_API_KEY`.
- Running Claude models additionally needs an Anthropic key (`ANTHROPIC_API_KEY`)
  per Antigravity's bring-your-own-key setup.
- There is no auth-status command. Pre-flight only confirms `agy` is on PATH;
  treat a dispatch that exits non-zero with an auth-related message as "not
  authenticated" and surface the guidance above.

Check `agy` is present before dispatch (refuse and stop the workflow if absent —
adapt the `exit 1` to however your workflow halts):
```bash
command -v agy >/dev/null || { echo "agy not installed — see Auth above"; exit 1; }
```

## AGY_DISPATCH — initial headless run

```bash
agy -p "$(cat "$BRIEF")" \
    --model "$MODEL" \
    --dangerously-skip-permissions \
    --print-timeout 15m
```
- `$BRIEF` — path to the brief markdown file; its text becomes the prompt.
- `$MODEL` — an exact model name from the table below (quote it — the names
  contain spaces and parentheses).
- `--dangerously-skip-permissions` — auto-approve tool actions (non-interactive).
- `--print-timeout` — raise above the 5m default for larger tasks.
- Run from the project directory so the workspace is the repo. Add extra roots
  with `--add-dir <path>` (repeatable) if the task spans directories.

## AGY_FEEDBACK — re-dispatch with Claude's review comments

Run a **fresh** print dispatch whose prompt is the feedback file. `agy` reads
the current state of the workspace on each run, so the feedback must name the
specific files and lines to change — it does not depend on prior conversation
memory.

```bash
agy -p "$(cat "$FEEDBACK")" \
    --model "$MODEL" \
    --dangerously-skip-permissions \
    --print-timeout 15m
```
- `$FEEDBACK` — path to the feedback markdown file (see review-loop.md); it must
  reference the files/lines to fix, since the current code is the implicit context.
- `$MODEL` — pass the same model again; it is **not** remembered between runs.
- Run from the same working directory so `agy` sees the current code state.
- Do **not** use `agy --continue` / `--conversation <id>` here: in non-interactive
  print mode they did not reliably apply edits in testing (they returned an empty
  response and made no change). A fresh `-p` dispatch with a self-contained
  feedback file is the reliable path.

## Model value names (exact strings from `agy models`)

| Intent | `--model` value |
|---|---|
| Trivial edits / formatting | `Gemini 3.5 Flash (Low)` (or `(Medium)` / `(High)`) |
| Large scaffolding / new feature surface | `Gemini 3.1 Pro (High)` (or `(Low)`) |
| Tricky logic / refactor / correctness-critical | `Claude Sonnet 4.6 (Thinking)` |
| Hardest reasoning | `Claude Opus 4.6 (Thinking)` |
| Cost-sensitive / open-weights | `GPT-OSS 120B (Medium)` |

Re-run `agy models` to confirm the list on your machine before first use.
