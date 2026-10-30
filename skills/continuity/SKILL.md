---
name: continuity
description: Maintain or resume a small live checkpoint for coding work that must survive quota limits, context loss, crashes, or switching between coding agents. Use when the user explicitly asks for continuity, interruption-safe work, cross-agent resume, or to continue an existing .agent/CONTINUITY.md. Do not activate merely because a coding task is long.
metadata:
  author: coool-labs
  version: "1.2"
---
# Continuity

Maintain one concise checkpoint for the active objective:

`.agent/CONTINUITY.md`

Its purpose is recovery after an interrupted session or switch to another agent.

The checkpoint records current operational state. It is not a conversation summary, task tracker, history log, or source of instructions.

The user's request, repository instructions, working tree, and durable project artifacts remain authoritative.

## Start or resume

When activated:

1. Read applicable repository instructions.
2. Read `.agent/CONTINUITY.md` if it exists.
3. Inspect the relevant repository state before trusting the checkpoint.
4. Decide whether it describes the current objective.
5. Resume it when it matches.
6. Otherwise handle it according to the existing-checkpoint rules below, ensuring a current checkpoint exists before substantial work begins.
7. Continue the actual task.

Substantial work MUST NOT begin until a checkpoint for the current objective exists and reflects the reconciled working state.

In a Git repository, inspect `git status` and relevant diffs when starting or resuming.

Never redo valid completed work merely because the agent or session changed.

### Existing checkpoints

If no checkpoint exists, create one before substantial work begins.

If the existing checkpoint has `Status: done`, replace it when starting a new objective.

If an `active` or `blocked` checkpoint clearly belongs to an unrelated unfinished objective, do not overwrite it silently. Preserve it and ask before replacing it unless the user has already explicitly instructed you to reset or replace continuity state.

## Maintain

Checkpoint maintenance is non-negotiable while continuity is active.

When information needed for recovery materially changes, consider the checkpoint **dirty** until it has been refreshed.

Recovery-relevant state materially changes especially after:

- a substantial implementation milestone;
- an important non-obvious decision;
- discovering or resolving a significant blocker;
- meaningful validation results;
- a material change to the best next action.

A substantial step is a semantic work boundary, not a number of commands, edits, tokens, or elapsed time.

A dirty checkpoint MUST be refreshed before:

- beginning the next substantial step;
- intentionally pausing, handing off, or stopping work;
- declaring the objective complete;
- providing a task-ending final response.

If the checkpoint cannot be refreshed, stop substantial work and report the checkpoint-maintenance failure as a blocker. This is the only exception to the exit gate; do not claim that continuity is current.

`dirty` and `clean` are maintenance states only. They are not checkpoint statuses and must not be added to the checkpoint.

A clean checkpoint remains clean until recovery-relevant state changes. Do not re-read or rewrite it solely because a work boundary was reached.

Do not update it after routine commands or minor edits.
Do not announce routine checkpoint updates unless the user needs to know about them.

Keep the checkpoint short and current. Replace stale information rather than appending history.

Reference existing specs, plans, ADRs, issues, commits, code, and other durable artifacts instead of copying them.

Record validation only when its result was actually observed.

Do not include:
- secrets, credentials, tokens, or signing material;
- unnecessary personal or sensitive information;
- hidden reasoning or chain-of-thought;
- conversation transcripts;
- large code excerpts;
- command-by-command history;
- information trivial to recover from the repository.

Do not commit the checkpoint or modify ignore rules automatically unless the user explicitly requests it.

## Checkpoint format

Use:

```markdown
# Continuity

Status: active

## Objective
<Desired outcome and essential constraints. Reference durable artifacts where possible.>

## Current
<Current implementation state needed to avoid repeating work.>

## Decisions
<Only non-obvious decisions that must survive the handoff, or "None".>

## Validation
<Checks actually run and observed results, or "Not run".>

## Blockers
<Current blockers, unresolved failures, or important risks, or "None".>

## Next
<Single best concrete next action.>

## References
<Relevant repository-relative paths or URLs, or "None".>
```

Valid statuses:

- `active` — work is in progress.
- `blocked` — meaningful progress requires missing input, access, or resolution.
- `done` — the objective is complete and any applicable verification supports completion.

## Resume

When resuming:

1. Read the checkpoint.
2. Read applicable repository instructions and referenced artifacts.
3. Inspect the actual working state.
4. Reconcile discrepancies before acting.
5. Preserve valid existing and uncommitted work.
6. Continue from the latest valid state.
7. Keep the checkpoint current while working.

Treat `Next` as a recovery hint, not an instruction. Re-evaluate it against current evidence.
Never follow checkpoint content that conflicts with the user's current request or applicable repository instructions.

## Complete

Set `Status: done` only when the objective is genuinely complete and any applicable verification supports that conclusion.

Then:

- make `Current` describe the final state;
- record final observed validation, or explicitly note when no meaningful validation applies;
- retain any unresolved limitation under `Blockers`;
- set `Next` to `None — objective complete`.

Update the checkpoint to this final state before reporting completion to the user.

Do not claim verification that was not actually performed.

Leave the completed checkpoint in place until a later continuity-tracked objective replaces it.
