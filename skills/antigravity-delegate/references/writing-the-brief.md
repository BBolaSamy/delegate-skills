# Writing the brief

A brief is a **self-contained** task spec for `agy`. The implementer has no
memory of this conversation — everything it needs must be in the brief.

## Template

```markdown
# Task: <one-line goal>

## Context
<What this code/project is, and why this task matters. 2-5 sentences.>

## In scope
- <files / modules / behaviors the implementer SHOULD change>

## Out of scope — do NOT touch
- <files / behaviors that must stay unchanged>

## Acceptance criteria
- [ ] <observable, testable outcome 1>
- [ ] <observable, testable outcome 2>

## Constraints
- <conventions, libraries to use/avoid, perf or style rules>
- Do not commit or push. Do not add dependencies without noting them here.
```

## Rules

- Specific over vague: name files and functions, not "the relevant code".
- State what NOT to touch — implementers over-reach without an explicit fence.
- Acceptance criteria must be checkable from the diff alone.
- Never include secrets, tokens, or credentials.
