# continuity

**Live cross-agent continuity for long-running coding tasks.**

`continuity` maintains a small repository-local checkpoint while an agent works, so another session or coding agent can recover after quota limits, context loss, crashes, or agent switches.

It is designed to work across Agent Skills-compatible coding tools such as Codex, Claude Code, OpenCode, and GitHub Copilot.

## Why?

Normal handoffs assume you know when a session is about to end:

```text
work → prepare handoff → switch agent
```

Autonomous sessions often end unexpectedly:

```text
work → work → work → quota / context / crash
```

Continuity keeps a live recovery checkpoint during the work instead:

```text
.agent/CONTINUITY.md
```

The checkpoint contains only what another capable agent needs to resume effectively:

- objective;
- current implementation state;
- important decisions;
- observed validation;
- blockers;
- best next action;
- references to durable project artifacts.

It is not a conversation log, task manager, history log, or chain-of-thought record.

The repository remains the source of truth.

## Install

```bash
npx skills add coool-labs/coool-agents --skill continuity
```

Or install it for specific agents:

```bash
npx skills add coool-labs/coool-agents \
  --skill continuity \
  -a codex \
  -a claude-code \
  -a opencode \
  -a github-copilot
```

## Update

Update continuity:

```bash
npx skills update continuity
```

Or update all installed skills:

```bash
npx skills update
```

## Usage

Continuity is opt-in. Activate it when work should survive an interrupted session or switch to another agent.

If your coding agent supports native goals, the recommended workflow is:

```text
/goal Use the continuity skill while working toward this objective: <your goal>
```

For example:

```text
/goal Use the continuity skill while working toward this objective: implement the authentication redesign in docs/auth-redesign.md, satisfy every acceptance criterion, preserve existing behavior, and add appropriate automated coverage.
```

The native goal defines **what must be achieved**.

Continuity records **where the work currently stands**.

Continuity preserves enough of the goal in `.agent/CONTINUITY.md` for another session or agent to reconstruct it. Detailed requirements should remain in durable repository artifacts such as specs, plans, issues, or ADRs rather than being duplicated into the checkpoint.

## Resume interrupted work

Open the same working tree in another compatible agent and use:

```text
/goal Use the continuity skill. Resume the active objective from .agent/CONTINUITY.md and continue until it is complete and verified, or genuinely blocked.
```

The incoming agent reconciles the checkpoint with the actual repository state before continuing.

No conversation export or access to the previous session is required.

## Checkpoint behavior

The checkpoint is updated when recovery-relevant state materially changes, such as after:

- a substantial implementation milestone;
- an important non-obvious decision;
- discovering or resolving a significant blocker;
- meaningful validation results;
- a material change to the best next action.

When recovery-relevant state changes, the checkpoint is conceptually **dirty** until refreshed.

A dirty checkpoint must be refreshed before the agent:

- begins the next substantial step;
- intentionally pauses or hands off;
- declares the objective complete;
- ends the task.

Routine commands and minor edits do not require checkpoint updates.

A clean checkpoint remains clean until recovery-relevant state changes, avoiding unnecessary reads, writes, and token usage.

## Principles

- **Repository first.** Actual code, tests, and durable project artifacts are stronger evidence than the checkpoint.
- **Reference, don't duplicate.** Point to existing specs, plans, ADRs, issues, and code instead of copying them.
- **Current state, not history.** Replace stale information rather than accumulating a log.
- **Observed validation only.** Record checks only when they were actually performed and observed.
- **One active objective.** Do not silently overwrite an unrelated unfinished checkpoint.
- **Low overhead.** Keep the checkpoint concise and update it only when recovery-relevant state changes.

## Status

Current version: **1.3**

Continuity is intentionally an instruction-only skill. It has no hooks, daemons, background processes, helper scripts, or external services.

The checkpoint gates are behavioral instructions to the coding agent rather than mechanical enforcement by the harness.

More machinery should only be added if real-world usage demonstrates that it is necessary.

## License

See the repository license.
