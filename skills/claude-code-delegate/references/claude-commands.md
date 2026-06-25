# Claude Code CLI (`claude`) commands

> Claude Code runs headless via `-p`/`--print`. It edits files in the current
> working directory and prints a response — capture changes with `git diff`.

## Auth (never put credentials in briefs)

- `claude` uses your logged-in Claude account, or set `ANTHROPIC_API_KEY`.

Check before dispatch:
```bash
command -v claude >/dev/null || { echo "claude not installed — see https://docs.claude.com/claude-code"; exit 1; }
```

## CLAUDE_DISPATCH — initial headless run

```bash
claude -p "$(cat "$BRIEF")" --model "$MODEL" --dangerously-skip-permissions
```
- `$MODEL` — `sonnet`, `opus`, `haiku`, or a full ID (e.g. `claude-sonnet-4-6`).
- `--dangerously-skip-permissions` auto-approves tool actions (trusted dir only).
- Run from the project directory so the workspace is the repo.

## CLAUDE_FEEDBACK — re-dispatch with review comments

```bash
claude -p "$(cat "$FEEDBACK")" --model "$MODEL" --dangerously-skip-permissions
```
- A **fresh** run. The feedback file must name the files/lines to fix, since the
  current code is the implicit context.

## Model value names

| Intent | `--model` |
|---|---|
| Hardest reasoning | `opus` |
| Balanced default | `sonnet` |
| Trivial / fast | `haiku` |
