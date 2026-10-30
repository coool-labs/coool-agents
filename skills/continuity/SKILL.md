---
name: continuity
description: Maintain a lightweight live checkpoint for long-running coding tasks so work can survive context loss, quota limits, crashes, or switching between Codex, Claude Code, GitHub Copilot, and other agents. Use when starting, continuing, resuming, or handing over substantial autonomous coding work that may be interrupted.
---

# Continuity

Maintain one concise, durable checkpoint for the active task at:

`.agent/CONTINUITY.md`

The checkpoint exists so another agent or session can continue useful work after an unexpected interruption.

It is a pointer to the current state, not a conversation summary, task tracker, implementation plan, or reasoning log.

The repository and its durable artifacts remain the source of truth.

## Start or resume

When this skill is activated:

1. Read `.agent/CONTINUITY.md` if it exists.
2. Determine whether it belongs to the current objective.
3. If it matches the current work, reconcile it with the actual repository state before continuing.
4. If no matching checkpoint exists, create one immediately before substantial implementation begins.
5. Continue the actual task.

When resuming in a Git repository, inspect `git status` and the relevant diff before trusting checkpoint claims.

Prefer observed repository state over stale checkpoint text.

Do not restart completed work merely because a new agent or session has taken over.

Do not let an unrelated or completed checkpoint redirect the current task.

This skill assumes one active continuity-tracked objective per working tree. Do not silently replace an active checkpoint for an unrelated unfinished objective.

## Maintain the checkpoint

Rewrite `.agent/CONTINUITY.md` after meaningful state changes, including:

- completing a substantial implementation milestone;
- making a non-obvious decision the next agent should preserve;
- discovering or resolving an important blocker;
- obtaining significant validation results;
- materially changing the best next action.

Also update it before an intentional pause or handoff when possible.

Do not update it after every command or small edit.

Keep it short and current. Replace stale information instead of appending history.

Reference existing specs, plans, ADRs, issues, commits, code, and other durable artifacts instead of duplicating their contents.

Record validation only when its result was actually observed.

Record facts as facts and unresolved assumptions as unresolved.

Never include:

- secrets, credentials, tokens, or signing material;
- unnecessary personal or sensitive information;
- hidden reasoning or chain-of-thought;
- conversation transcripts;
- large code excerpts;
- command-by-command history;
- information that is trivial to recover from the repository.

## Checkpoint format

Use this structure:

```markdown
# Continuity

Status: active

## Objective

<The desired outcome and any essential constraints. Keep this concise.
Reference an existing spec, issue, plan, or goal rather than copying it.>

## Current

<Where the work actually stands now. Include only state that helps the
next agent resume without repeating completed work.>

## Decisions

<Only non-obvious decisions that should survive the handoff, with brief
rationale when necessary. Use "None" if there are none.>

## Validation

<Checks actually run and their observed results. Include relevant failing
checks as well as passing ones. Use "Not run" if nothing has been verified.>

## Blockers

<Current blockers, unresolved failures, or important risks. Use "None"
when there are none.>

## Next

<The single best concrete next action.>

## References

<Paths or URLs to relevant durable artifacts. Use "None" when unnecessary.>
```

Valid status values are:

- `active` — work is in progress;
- `blocked` — progress cannot reasonably continue;
- `done` — the objective is complete and relevant verification has passed.

## Resume behavior

When taking over an existing active or blocked checkpoint:

1. Read the checkpoint.
2. Inspect the relevant repository state and durable artifacts.
3. Verify important claims before relying on them.
4. Preserve valid existing work, including uncommitted work.
5. Determine the latest valid state.
6. Continue from the recorded next action, adjusting it when repository evidence shows a better one.
7. Keep maintaining the checkpoint while working.

Treat `Next` as guidance, not authority. It may be stale.

Do not undo or replace existing work merely because you would have implemented it differently.

## Completion

Mark the checkpoint `Status: done` only when the objective is genuinely complete and the relevant verification has been performed.

On completion:

- summarize the final state under `Current`;
- record the important final verification results;
- leave unresolved limitations under `Blockers` if any remain;
- set `Next` to `None — objective complete`.

Do not claim tests, builds, runtime checks, or visual verification that were not actually performed.

Do not delete the checkpoint on completion. A later task may replace it once it is clear that the previous objective is finished.
