# OpenCode CLI (`opencode`) commands

> OpenCode runs headless via `opencode run`. It edits files in the current
> working directory and prints a text response — capture changes with
> `git diff`, not stdout. Confirm flags with `opencode --help`; in `run` mode
> tool-permission behavior is governed by your OpenCode permission config.

## Auth (never put credentials in briefs)

- `opencode auth login` (interactive), or set the provider key in the
  environment (e.g. `ANTHROPIC_API_KEY`, `OPENAI_API_KEY`, `GEMINI_API_KEY`).

Check before dispatch:
```bash
command -v opencode >/dev/null || { echo "opencode not installed — see https://opencode.ai/docs"; exit 1; }
```

## OPENCODE_DISPATCH — initial headless run

```bash
opencode run --model "$MODEL" "$(cat "$BRIEF")"
```
- `$MODEL` — `provider/model`, e.g. `anthropic/claude-sonnet-4-20250514`.
- Run from the project directory so the workspace is the repo.
- Ensure OpenCode is allowed to edit non-interactively (permission config).

## OPENCODE_FEEDBACK — re-dispatch with review comments

```bash
opencode run --model "$MODEL" "$(cat "$FEEDBACK")"
```
- A **fresh** run. The feedback file must name the files/lines to fix, since
  the current code is the implicit context.

## Model value names (run `opencode models` to confirm)

Form is `provider/model`:

| Intent | example `--model` |
|---|---|
| Hardest reasoning | `anthropic/claude-opus-4-20250514` |
| Balanced default | `anthropic/claude-sonnet-4-20250514` |
| Large context | `google/gemini-2.5-pro` |
