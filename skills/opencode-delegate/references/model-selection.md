# Model selection

The orchestrator uses this table to **recommend** a model. The human **always**
makes the final choice (SKILL.md step 3) — never dispatch before they confirm.

| Task shape | Recommend | Why |
|---|---|---|
| Tricky logic, refactors, correctness-critical work | `anthropic/claude-opus-4-20250514` | Strongest reasoning |
| General implementation | `anthropic/claude-sonnet-4-20250514` | Balanced |
| Large-context / multi-file | `google/gemini-2.5-pro` | Big context window |

Exact `--model` strings are `provider/model`; run `opencode models` to confirm
what is available in your install.

## How to present the recommendation

> "I'd use **<provider/model>** for this because <one-line rationale>. Want me
> to go with that, or pick another (run `opencode models` for the list)?"

Then stop and wait.
