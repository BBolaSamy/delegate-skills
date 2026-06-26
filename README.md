# Delegate Agents

**delegate-agents** is a family of skills that each delegate a coding task to a headless coding-agent CLI. Your orchestrating agent (Claude Code, OpenCode, Cursor, …) writes a self-contained brief, you pick the model, the implementer CLI does the edits, your agent reviews the diff and loops feedback back until it passes — and **you** commit. There is one skill per implementer:

| Skill | Delegates to | Implementer CLI |
|---|---|---|
| `antigravity-delegate` | Antigravity | `agy` |
| `opencode-delegate` | OpenCode | `opencode` |
| `cursor-delegate` | Cursor | `cursor-agent` |
| `claude-code-delegate` | Claude Code | `claude` |

## Roles

* **The orchestrating agent (orchestrator + reviewer):** writes the brief, recommends a model, dispatches to the implementer CLI, reviews the diff (correctness + clean-code), and writes feedback. It **never edits the implementer's code** and **never commits or pushes**.
* **The implementer CLI:** makes all code edits. Never commits.
* **You (human):** choose the model, do the final review, and commit.

## Requirements

* **The implementer CLI for the skill(s) you install** — `agy` (Antigravity), `opencode` (OpenCode), `cursor-agent` (Cursor), or `claude` (Claude Code) — installed, on PATH, and authenticated. Each skill's `references/<impl>-commands.md` documents the exact dispatch command, auth step, and model list.
* **Git:** the workspace must be a git repository.

## Workflow (same for every skill)

1. **Brief Formulation:** the agent confirms scope and writes a self-contained brief.
2. **Model Selection:** the agent recommends a model and pauses. **You always pick the model** before dispatch.
3. **Dispatch:** the agent runs the implementer CLI headless (e.g. `agy -p`, `opencode run`, `cursor-agent -p`, `claude -p`) with the brief + chosen model.
4. **Capture Diff:** the agent uses `git diff` and `git status --porcelain` to see exactly what changed.
5. **Review:** two passes on the diff (correctness + clean-code). On Claude Code it can use `/code-review` and `clean-code-guard`; on other agents it reviews directly.
6. **Feedback Loop:** blocking issues go back as a **fresh** re-dispatch, **capped at 3 cycles** to prevent infinite churn.
7. **Final Review & Commit:** the agent presents the diff; you review and commit.

## Install

Install whichever delegate skill(s) you want with the Skills CLI (add `--agent <your-agent>` to target a specific orchestrator, e.g. `claude-code`):

```bash
npx skills add BBolaSamy/delegate-agents --skill antigravity-delegate
npx skills add BBolaSamy/delegate-agents --skill opencode-delegate
npx skills add BBolaSamy/delegate-agents --skill cursor-delegate
npx skills add BBolaSamy/delegate-agents --skill claude-code-delegate
```

Each skill needs **its** implementer CLI installed and authenticated first.

## Usage

Ask your orchestrating agent to delegate a task to a specific implementer — name it in the request. For example:

* "delegate this to OpenCode"
* "have cursor-agent build [task]"

## Example

**User:**
> Add a new error logging module and delegate this to OpenCode.

**Agent:**
> I've written the brief to create the error logging module.
> I'd use **anthropic/claude-sonnet-4** for this because it's a new feature surface. Want me to go with that, or pick another (run `opencode models` for the list)?

**User:**
> Yes, that's fine.

**Agent:**
> Dispatching to `opencode`...
>
> *[opencode completes the implementation]*
>
> Reviewing the diff... I found a missing check for the log directory, looping feedback back to `opencode`...
>
> *[opencode completes iteration 2]*
>
> Review clean! Here is the final diff for your review. If it looks good, you can commit it.
