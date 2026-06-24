# Antigravity Delegate

`antigravity-delegate` is a local Claude Code skill that orchestrates coding tasks by delegating the implementation to the Antigravity CLI (`agy`) running headless. It writes a self-contained brief for the task, lets `agy` perform the code edits, and then reviews the result. Claude loops feedback back to `agy` until it passes review, leaving the final review and commit to the human developer.

## Roles

* **Claude (orchestrator + reviewer):** Writes the brief, recommends a model, dispatches to `agy`, reviews the diff (code-review + clean-code), and writes feedback. Claude **never edits the implementer's code** and **never commits or pushes**.
* **`agy` (implementer):** Makes all code edits. Never commits.
* **You (human):** Choose the model, do the final review, and commit.

## Requirements

* **Antigravity CLI (`agy`)**: Installed and available on your PATH.
  * **Authentication**: Authenticate via `agy login` or by setting an API key (`ANTIGRAVITY_API_KEY` or `GEMINI_API_KEY`). If using Claude models, an Anthropic key (`ANTHROPIC_API_KEY`) is also required.
* **Git**: The workspace must be a git repository.

## Workflow

1. **Brief Formulation:** Claude confirms the scope of the task and writes a self-contained brief.
2. **Model Selection:** Claude recommends an implementation model and pauses. **You always pick the model** before dispatch.
3. **Dispatch to `agy`:** Claude dispatches the brief to `agy` running in a headless print mode (`agy -p "<prompt>" --model "<name>" --dangerously-skip-permissions`).
4. **Capture Diff:** Claude uses `git diff` and `git status --porcelain` to see exactly what `agy` changed.
5. **Review:** Claude runs two review passes on the diff: `code-review` and `clean-code`.
6. **Feedback Loop:** If there are blocking issues, Claude loops feedback back to `agy` using a **fresh** dispatch (`agy -p`, NOT `--continue`). This iteration is **capped at 3 cycles** to prevent infinite churn.
7. **Final Review & Commit:** Once the review passes (or the iteration cap is reached), Claude presents the diff to you. You review the final code and make the commit.

## Available Models

The human **always** picks the implementation model before dispatch. Available models (with effort tiers) are:

* `Gemini 3.5 Flash (Low)` (or `(Medium)` / `(High)`) - Trivial edits / formatting
* `Gemini 3.1 Pro (High)` (or `(Low)`) - Large scaffolding / new feature surface
* `Claude Sonnet 4.6 (Thinking)` - Tricky logic / refactor / correctness-critical
* `Claude Opus 4.6 (Thinking)` - Hardest reasoning
* `GPT-OSS 120B (Medium)` - Cost-sensitive / open-weights

## Install / Use

### Prerequisites
Before installing, make sure the Antigravity CLI (`agy`) is installed and authenticated first (see [Requirements](#requirements)).

### Installation
Install the skill using the Skills CLI:

```bash
npx skills add BBolaSamy/delegate-skills --skill antigravity-delegate --agent claude-code
```

### Usage
Once installed, ask Claude Code inside your project to delegate a task. For example:
* "delegate this to Antigravity"
* "have agy build [task]"

## Example

**User:**
> Add a new error logging module and delegate this to Antigravity.

**Claude:**
> I've written the brief to create the error logging module.
> I'd use **Gemini 3.1 Pro (High)** for this because it's a new feature surface. Want me to go with that, or pick another (Gemini 3.5 Flash, Claude Sonnet 4.6, Claude Opus 4.6, GPT-OSS 120B)?

**User:**
> Yes, Gemini 3.1 Pro (High) is fine.

**Claude:**
> Dispatching to `agy`...
>
> *[agy completes the implementation]*
>
> Reviewing the diff... I found a missing check for the log directory, looping feedback back to `agy`...
>
> *[agy completes iteration 2]*
>
> Review clean! Here is the final diff for your review. If it looks good, you can commit it.
