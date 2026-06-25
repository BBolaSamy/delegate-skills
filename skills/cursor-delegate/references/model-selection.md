# Model selection

The orchestrator uses this table to **recommend** a model. The human **always**
makes the final choice (SKILL.md step 3) — never dispatch before they confirm.

| Task shape | Recommend | Why |
|---|---|---|
| Tricky logic, refactors, correctness-critical work | `sonnet-4.5` | Strong reasoning |
| Fast / cheap / trivial edits | `gpt-5` | Speed |

Confirm the available `--model` values with `cursor-agent --help` or the Cursor
docs before first use.

## How to present the recommendation

> "I'd use **<model>** for this because <one-line rationale>. Want me to go with
> that, or pick another (see `cursor-agent --help`)?"

Then stop and wait.
