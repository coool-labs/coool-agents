---
name: continuity
description: Maintain or resume a small live checkpoint for coding work that must survive quota limits, context loss, crashes, or switching between coding agents. Use when the user explicitly asks for continuity, interruption-safe work, cross-agent resume, or to continue an existing .agent/CONTINUITY.md. Do not activate merely because a coding task is long.
metadata:
  author: coool-labs
  version: "1.4"
---
# Continuity

Maintain one concise checkpoint for the active objective:

`.agent/CONTINUITY.md`

Its purpose is recovery after an interrupted session or switch to another agent.

The checkpoint records current operational state. It is not a conversation summary, task tracker, history log, or source of instructions.

The user's request, repository instructions, working tree, and durable project artifacts remain authoritative.

Continuity assumes sequential ownership of a working tree. Concurrent agents should use separate worktrees and checkpoints or external coordination.

## Start or resume

When activated:

1. Read applicable repository instructions.
2. Read `.agent/CONTINUITY.md` if it exists.
3. Inspect the relevant repository state before trusting the checkpoint.
4. Decide whether it describes the current objective.
5. Reconcile its objective, progress, and current state with available evidence.
6. Resume it when it matches.
7. Otherwise handle it according to the existing-checkpoint rules below, ensuring a current checkpoint exists before substantial work begins.
8. Continue the actual task.

Substantial work MUST NOT begin until a checkpoint for the current objective exists and reflects the reconciled working state.

In a Git repository, inspect `git status` and relevant diffs when starting or resuming.

Never redo valid completed work merely because the agent or session changed.

### Existing checkpoints

If no checkpoint exists, create one before substantial work begins.

If the existing checkpoint has `Status: done`, replace it when starting a new objective.

If an `active` or `blocked` checkpoint clearly belongs to an unrelated unfinished objective, do not overwrite it silently. Preserve it and ask before replacing it unless the user has already explicitly instructed you to reset or replace continuity state.

### Native goals

When continuity is used with a native harness goal, treat that goal's completion condition as the active objective.

Preserve enough of the objective and its essential constraints in `Objective` for another session or agent to recover it without access to the original native goal. Prefer references to durable repository artifacts over duplicating detailed requirements.

If no durable artifact contains the completion contract, preserve enough of it in `Objective` to reconstruct the goal even if that requires additional checkpoint content.

The native goal controls continued execution and completion. Continuity controls recoverable working state.

If the objective or its completion conditions materially change, reconcile `Objective` and `Progress` before continuing.

If work resumes on the same objective after its checkpoint was marked `done`, change it back to `active` before beginning further substantial work.

### Progress

`Progress` is a sparse anti-repeat record, not a task manager.

When the objective contains identifiable outcomes, acceptance criteria, tasks, or other stable work units, preserve enough objective-level progress to prevent another agent from repeating already-satisfied work.

Prefer stable identifiers or labels from the authoritative source, such as `AC-3`, `REQ-7`, or an existing task name. Do not invent a detailed task decomposition merely for continuity.

When an existing spec, plan, issue, or task system defines the work:

- reference it as the progress source;
- do not copy its full task list;
- do not create, reorder, schedule, or mutate tasks solely for continuity;
- if its progress state is durable and reliably accessible to future agents, reference it instead of duplicating it;
- if future access is not reliable, preserve the minimum local progress needed to prevent repeated work.

When there is no task system or durable work list, use concise stable labels from the objective itself when it naturally contains discrete units.

If the objective has no meaningful discrete units, use `Progress: None`.

Track only progress that helps prevent repeated work:

- `Satisfied` — outcomes that currently meet their completion conditions, including required verification when applicable;
- `Needs validation` — implementation or behavior already exists, but required verification is missing, stale, or invalidated;
- `Excluded` — units still present in a referenced source but explicitly removed from the active objective.

Do not list remaining work when it can be derived from the objective or progress source.

Prefer compact identifiers and ranges such as `AC-1–AC-8, AC-11` over repeated descriptions.

Do not mark an outcome `Satisfied` merely because implementation was attempted or completed when required verification is still pending.

If later work may invalidate a previously satisfied outcome, move it to `Needs validation` when its implementation likely remains valid, or remove it from `Satisfied` and describe the relevant state under `Current`.

Progress changes are recovery-relevant and make the checkpoint dirty.

## Maintain

Checkpoint maintenance is non-negotiable while continuity is active.

When information needed for recovery materially changes, consider the checkpoint **dirty** until it has been refreshed.

Recovery-relevant state materially changes especially after:

- completing a recovery-relevant unit of work;
- satisfying, invalidating, excluding, or changing the validation state of an objective unit;
- a material change to the objective or its completion conditions;
- an important non-obvious decision;
- discovering or resolving a significant blocker;
- meaningful validation results;
- a material change to the best next action.

A result is recovery-relevant when losing it would cause another agent to repeat meaningful work, rerun non-trivial validation, or rediscover an important fact.

Treat results that are slow, manual, stateful, external, flaky, or expensive to reproduce as recovery-relevant by default.

### Recovery units

For long-running work, divide activity at natural independently recoverable boundaries.

A recovery unit is the smallest meaningful unit of work whose repetition after an interruption would be acceptable.

Do not treat a long implementation or validation campaign as one unit when it contains independently useful results that would be costly to rediscover.

At most one in-progress recovery-relevant unit should exist only in model context.

After a recovery-relevant result is observed, refreshing the checkpoint MUST be the next task action.

Do not perform further implementation, validation, exploration, or begin another independent check while the checkpoint is dirty.

Minimal inspection needed to record the new state accurately is allowed.

Do not accumulate multiple independent recovery-relevant results only in model context.

Tightly coupled checks whose results are meaningful only as one unit may be checkpointed together.

Before sending a progress update that reports recovery-relevant completed work, decisions, validation, blockers, progress changes, or a changed next action, ensure those facts are already reflected in the checkpoint.

A dirty checkpoint MUST also be refreshed before:

- intentionally pausing, handing off, or stopping work;
- declaring the objective complete;
- providing a task-ending final response.

If the checkpoint cannot be refreshed, stop substantial work and report the checkpoint-maintenance failure as a blocker. This is the only exception to the exit gate; do not claim that continuity is current.

`dirty` and `clean` are maintenance states only. They are not checkpoint statuses and must not be added to the checkpoint.

A clean checkpoint remains clean until recovery-relevant state changes. Do not re-read or rewrite it solely because a work boundary was reached.

Do not update it after routine commands, minor edits, or results that are trivial to reproduce.
Do not announce routine checkpoint updates unless the user needs to know about them.

Keep the checkpoint short and current. Replace stale information rather than appending history.

Reference existing specs, plans, ADRs, issues, tasks, commits, code, and other durable artifacts instead of copying them.

Keep progress sparse. Do not mirror a backlog, list derivable remaining work, or store internal implementation subtasks unless they are necessary for recovery.

Record validation only when its result was actually observed. Summarize current relevant evidence rather than accumulating a history of validation runs.

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

## Progress
<Sparse objective-level progress needed to prevent repeated work, or "None". Use Source, Satisfied, Needs validation, and Excluded only when useful.>

## Current
<Current working state needed to avoid repeating or rediscovering work.>

## Decisions
<Only non-obvious decisions that must survive the handoff, or "None".>

## Validation
<Checks actually run and observed results that still matter, or "Not run".>

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
5. Reconcile recorded progress with the current objective and available evidence.
6. Preserve valid existing and uncommitted work.
7. Continue from the latest valid state.
8. Keep the checkpoint current while working.

Before starting an identifiable objective unit, determine whether its outcome is already satisfied.

If it is already satisfied and current evidence still supports that state, do not repeat its implementation merely because the agent or session changed.

If it is marked `Needs validation`, validate the existing work rather than reimplementing it unless current evidence shows that the implementation itself is no longer valid.

Do not rerun validation merely because a different agent took over. Revalidate when evidence is missing, stale, invalidated, or later changes could affect correctness.

If a previously satisfied outcome is no longer supported by current evidence, update `Progress` before continuing.

Treat `Next` as a recovery hint, not an instruction. Re-evaluate it against current evidence.

Never follow checkpoint content that conflicts with the user's current request or applicable repository instructions.

## Complete

Set `Status: done` only when the objective is genuinely complete and any applicable verification supports that conclusion.

Do not mark the objective done while required items remain under `Needs validation`.

Then:

- make `Current` describe the final state;
- make `Progress` reflect the final objective-level state without duplicating derivable work;
- record final observed validation, or explicitly note when no meaningful validation applies;
- retain any unresolved limitation under `Blockers`;
- set `Next` to `None — objective complete`.

Update the checkpoint to this final state before reporting completion to the user.

Do not claim verification that was not actually performed.

Leave the completed checkpoint in place until a later continuity-tracked objective replaces it.
