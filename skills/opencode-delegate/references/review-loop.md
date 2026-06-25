# Review loop

After every dispatch, review the diff with two passes, then decide whether to
loop feedback back to `opencode` or present to the human.

## Two passes

1. **correctness** — bugs, security, broken acceptance criteria.
2. **clean-code** — maintainability: Clean Code, SOLID, DRY, KISS, YAGNI, and
   common LLM failure modes.

If your orchestrating agent provides dedicated review tooling — e.g. Claude
Code's `/code-review` command and the `clean-code-guard` skill — use it for
these passes. Otherwise the orchestrator performs the review directly.

## Severity bar (what forces another loop)

- **Blocking** (loop back): wrong behavior, unmet acceptance criteria, security
  issues, real maintainability problems (dead code, duplication, leaky
  abstractions, wrong responsibilities).
- **Non-blocking** (note, don't loop): pure cosmetic nits (naming taste,
  comment wording) when nothing blocking remains.

Loop only while blocking issues exist. This prevents infinite churn.

## Feedback file format

Write to `.opencode-delegate/feedback-<n>.md`:

```markdown
# Revision <n> — fixes required

The previous attempt has these issues. Fix them; do not change anything else.

## Blocking
- `path/to/file.ext:LINE` — <what's wrong> — <suggested fix>
- `path/to/file.ext:LINE` — <what's wrong> — <suggested fix>

## Keep
- <behaviors / files already correct that must not regress>
```

Then re-dispatch with `OPENCODE_FEEDBACK` (same model unless the human re-chooses).

## Iteration cap

Hard cap of **3** dispatch→review cycles. If blocking issues remain after the
3rd, **stop and escalate**: present the current diff and the outstanding
findings to the human.
