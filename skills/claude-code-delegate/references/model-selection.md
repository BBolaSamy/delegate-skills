# Model selection

The orchestrator uses this table to **recommend** a model. The human **always**
makes the final choice (SKILL.md step 3) — never dispatch before they confirm.

| Task shape | Recommend | Why |
|---|---|---|
| Tricky logic, refactors, correctness-critical work | `opus` | Strongest reasoning |
| General implementation | `sonnet` | Balanced default |
| Trivial edits, formatting | `haiku` | Fast + cheap |

Aliases map to the latest model; a full ID (e.g. `claude-sonnet-4-6`) also works.

## How to present the recommendation

> "I'd use **<model>** for this because <one-line rationale>. Want me to go with
> that, or pick another (opus / sonnet / haiku)?"

Then stop and wait.
