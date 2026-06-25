# Cursor Agent CLI (`cursor-agent`) commands

> `cursor-agent` runs headless via `-p` (print). It edits files in the current
> working directory and prints a response — capture changes with `git diff`.
> Confirm flags/models with `cursor-agent --help`.

## Auth (never put credentials in briefs)

- `cursor-agent login` (interactive), or `--api-key <key>` / `CURSOR_API_KEY`.

Check before dispatch:
```bash
command -v cursor-agent >/dev/null || { echo "cursor-agent not installed — see https://cursor.com/cli"; exit 1; }
```

## CURSOR_DISPATCH — initial headless run

```bash
cursor-agent -p "$(cat "$BRIEF")" --model "$MODEL" -f
```
- `-p` non-interactive; `-f`/`--force` allows tool actions in headless mode.
- `$MODEL` — e.g. `sonnet-4.5` or `gpt-5` (run `cursor-agent --help` to confirm).
- Run from the project directory so the workspace is the repo.

## CURSOR_FEEDBACK — re-dispatch with review comments

```bash
cursor-agent -p "$(cat "$FEEDBACK")" --model "$MODEL" -f
```
- A **fresh** run. The feedback file must name the files/lines to fix, since the
  current code is the implicit context.

## Model value names (confirm with `cursor-agent --help`)

| Intent | example `--model` |
|---|---|
| Strong reasoning | `sonnet-4.5` |
| Fast / cheap | `gpt-5` |
