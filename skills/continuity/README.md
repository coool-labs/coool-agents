# continuity

**Live cross-agent continuity for long-running coding work.**

`continuity` maintains a small repository-local checkpoint so work can survive quota limits, context loss, crashes, or switching between coding agents.

It is designed for Agent Skills-compatible tools such as Codex, Claude Code, OpenCode, and GitHub Copilot.

## Why?

Long-running agent sessions often end without a clean handoff:

```text
work → work → work → quota / context / crash
```

Continuity keeps a live checkpoint instead:

```text
.agent/CONTINUITY.md
```

The checkpoint contains only the state another capable agent needs to resume without repeating or rediscovering meaningful work.

The repository remains the source of truth.

## Install

```bash
npx skills add coool-labs/coool-agents --skill continuity
```

For specific agents:

```bash
npx skills add coool-labs/coool-agents \
  --skill continuity \
  -a codex \
  -a claude-code \
  -a opencode \
  -a github-copilot
```

## Update

```bash
npx skills update continuity
```

Or update all installed skills:

```bash
npx skills update
```

## Usage

Continuity is opt-in.

When your coding agent supports native goals, the recommended pattern is:

```text
/goal Use the continuity skill while working toward this objective: <your goal>
```

For example:

```text
/goal Use the continuity skill while working toward this objective: implement the authentication redesign in docs/auth-redesign.md, satisfy every acceptance criterion, preserve existing behavior, and add appropriate automated coverage.
```

The native goal defines **what must be achieved**.

Continuity records **where the work stands and what must not be repeated**.

## Resume interrupted work

Open the same working tree in another compatible agent and use:

```text
/goal Use the continuity skill. Resume the active objective from .agent/CONTINUITY.md and continue until it is complete and verified, or genuinely blocked.
```

The incoming agent reconciles the checkpoint with the actual repository state before continuing.

No conversation export or access to the previous session is required.

## What the checkpoint contains

Continuity keeps these concerns separate:

```text
Objective   → what must ultimately be true
Progress    → which objective-level outcomes are already satisfied
Current     → where the work currently stands
Validation  → evidence already observed
Blockers    → what prevents progress
Next        → the next concrete action
```

`Progress` is deliberately sparse. It exists to prevent repeated work, not to replace a task manager.

For example:

```markdown
## Progress
Source: `docs/release-checklist.md`
Satisfied: AC-1–AC-8, AC-11
Needs validation: AC-9
Excluded: AC-10
```

Continuity should not copy the full task list or list remaining work when that can be derived from the referenced source.

If an existing spec, plan, issue, or task system already stores durable progress that future agents can access, Continuity references it instead of duplicating it.

If there is no task system, Continuity can use stable labels from the goal itself.

## Long-running work

Long-running work is divided at natural recovery boundaries.

When a meaningful unit of work completes:

```text
recovery-relevant result
        ↓
checkpoint becomes dirty
        ↓
update CONTINUITY.md
        ↓
continue with the next independent unit
```

The checkpoint update must be the next task action after a recovery-relevant result.

This prevents an agent from accumulating significant completed work only in model context and then losing it when the session ends.

Routine commands, minor edits, and trivial results do not require checkpoint updates.

A clean checkpoint should not be repeatedly read or rewritten without reason.

## Avoiding repeated work

When another agent takes over, it should reconcile recorded progress with the repository and available evidence.

If an outcome is already satisfied, do not repeat its implementation merely because the agent or session changed.

If existing work only needs verification, Continuity records it as `Needs validation` so the next agent validates it instead of rebuilding it.

Previously satisfied work can become unsatisfied or require revalidation when later changes invalidate the earlier evidence.

## Principles

- **Repository first.** Code, tests, and durable project artifacts outrank the checkpoint.
- **Reference, don't duplicate.** Point to existing specs, plans, issues, ADRs, and tasks.
- **Current state, not history.** Replace stale information instead of accumulating a log.
- **Sparse progress.** Store only enough objective-level progress to prevent repeated work.
- **Observed validation only.** Record checks only when their results were actually observed.
- **Low overhead.** Update only when recovery-relevant state changes.
- **One active objective.** Do not silently overwrite an unrelated unfinished checkpoint.

## What continuity is not

Continuity is not:

- a task manager;
- a project planner;
- a session transcript;
- an implementation history;
- an agent orchestrator;
- a chain-of-thought store.

It deliberately stays small.

## Status

Current version: **1.4**

Continuity is currently an instruction-only skill. It has no hooks, daemons, background processes, helper scripts, or external services.

The checkpoint rules are behavioral instructions to the coding agent rather than mechanical enforcement by the harness.

More machinery should only be added if real-world usage shows that it is necessary.

## License

See the repository license.
