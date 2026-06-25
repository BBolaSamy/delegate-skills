---
name: antigravity-delegate
description: Use when you want to delegate a coding task to the Antigravity CLI (agy) as a headless implementer while your coding agent orchestrates. The agent writes a self-contained brief, you pick the implementation model, Antigravity does the edits, the agent reviews the diff (code-review + clean-code) and loops feedback back to Antigravity until it passes, then you review and commit. Trigger phrases include "delegate this to Antigravity", "have agy build", and "antigravity-delegate".
---

# Antigravity Delegate

Delegate a coding task to the Antigravity CLI (`agy`) running headless. Your
coding agent (Claude Code, OpenCode, Cursor, …) orchestrates and reviews; `agy`
implements; **you** commit.

## Roles (do not blur these)

- **The orchestrating agent (orchestrator + reviewer)** — Claude Code, OpenCode,
  Cursor, or similar: writes the brief, recommends a model, dispatches to `agy`,
  reviews the diff, writes feedback. It **never edits the implementer's code**
  and **never commits or pushes**.
- **`agy` (implementer):** makes all code edits. Never commits.
- **You (human):** choose the model, do the final review, and commit.

## Hard rules

1. Always **pause for the human's explicit model choice** before dispatching.
2. The orchestrator never edits the produced code — every fix goes back to `agy` as feedback.
3. Never commit, never push — present the diff and let the human land it.
4. Never put secrets in briefs; `agy` auth comes from environment variables.
5. Review loop is capped at **3 iterations**, then escalate to the human.

## Workflow

1. **Intake.** Confirm scope. If the request is thin, ask: which files are in
   scope, what does "done" look like, what must NOT be touched.

2. **Write the brief.** Follow `references/writing-the-brief.md`. Save to
   `.antigravity-delegate/brief-<timestamp>.md`.

3. **Recommend + confirm model.** Use `references/model-selection.md` to pick a
   recommendation, state it with a one-line rationale, then **STOP and ask the
   human to confirm or choose another**. Do not dispatch until they answer.

4. **Pre-flight.** Run the checks in `references/agy-commands.md`:
   - `agy` is on PATH (else refuse with the setup guidance in agy-commands.md).
     There is no auth-status command, so pre-flight only checks presence; if a
     dispatch later fails with an auth error, surface the same guidance (run
     `agy login`, or set `ANTIGRAVITY_API_KEY`/`GEMINI_API_KEY`, plus
     `ANTHROPIC_API_KEY` for Claude models).
   - Working dir is a git repo; record baseline: `BASE=$(git rev-parse HEAD)`.
   - If `git status --porcelain` is non-empty, offer to `git stash` unrelated
     changes so the captured diff is solely Antigravity's work.

5. **Dispatch.** Run `AGY_DISPATCH` from `references/agy-commands.md` with the
   chosen model and brief. Capture stdout, stderr, and exit code.

6. **Capture the diff.** `git diff` plus untracked files
   (`git status --porcelain`) to see exactly what `agy` changed.

7. **Review.** Run two passes on the diff (see `references/review-loop.md`):
   - **correctness** — bugs, security, broken acceptance criteria.
   - **clean-code** — maintainability (Clean Code / SOLID / DRY / KISS / YAGNI).
   If your agent provides dedicated review tooling (e.g. Claude Code's
   `/code-review` command and the `clean-code-guard` skill), use it; otherwise
   perform the review directly.

8. **Decide.** Apply the severity bar in `references/review-loop.md`:
   - Both passes clean (only cosmetic nits, if any) → go to step 11.
   - Any substantive (correctness / security / real maintainability) issue → step 9.

9. **Feedback + re-dispatch.** Write a feedback file per
   `references/review-loop.md` to `.antigravity-delegate/feedback-<n>.md`, then
   run `AGY_FEEDBACK`. Increment the iteration counter and loop to step 5.
   The model stays the same unless the human re-chooses.

10. **Escalate.** If iteration count reaches 3 with issues still open, stop.
    Present the current diff and the outstanding findings; let the human decide.

11. **Present & land.** Show the human the final diff and a summary: model used,
    iterations run, and review results. They review and commit. The skill is done.

## On `agy` failure

If `agy` exits non-zero, surface its stderr to the human and stop — do not loop
blindly on a failed run.
