# Model selection

Claude uses this table to **recommend** a model. The human **always** makes the
final choice (SKILL.md step 3) — never dispatch before they confirm.

| Task shape | Recommend | Why |
|---|---|---|
| Large multi-file scaffolding / new feature surface | `Gemini 3.1 Pro (High)` | Speed + large context window |
| Tricky logic, refactors, error-handling, correctness-critical work | `Claude Sonnet 4.6 (Thinking)` (or `Claude Opus 4.6 (Thinking)` for the hardest) | Strongest reasoning |
| Trivial edits, formatting, tiny changes | `Gemini 3.5 Flash (Low)` | Fast + cheap |
| Cost-sensitive or fallback | `GPT-OSS 120B (Medium)` | Open-weights option |

The exact `--model` value strings (with effort tiers) are in `agy-commands.md`;
run `agy models` to confirm what's available.

## How to present the recommendation

> "I'd use **<model with its effort tier, e.g. `Gemini 3.5 Flash (Low)`>** for
> this because <one-line rationale>. Want me to go with that, or pick another
> (Gemini 3.1 Pro, Gemini 3.5 Flash, Claude Sonnet 4.6, Claude Opus 4.6,
> GPT-OSS 120B)?"

Always recommend a specific effort tier (the parenthesised suffix) — it is part
of the `--model` value. If the human picks a bare model name, ask which tier
before dispatching. Then stop and wait.
