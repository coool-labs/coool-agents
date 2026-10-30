# continuity

**Live cross-agent continuity for long-running coding tasks.**

`continuity` maintains a tiny repository-local checkpoint while an agent works, so another session or coding agent can recover after an unexpected interruption.

It is designed for workflows that move between agents such as:

- Codex
- Claude Code
- GitHub Copilot
- other Agent Skills-compatible coding tools

## Why?

Traditional handoff workflows assume you know when a session is about to end:

```text
work → prepare handoff → switch agent
```

Long autonomous coding sessions often end differently:

```text
work → work → work → quota / context / crash
```

At that point, asking the previous agent to summarize its work is no longer possible.

`continuity` solves this by maintaining a small live checkpoint during the task rather than generating a handoff only at the end.

> Handoff assumes you know you're about to stop.  
> Continuity assumes you don't.

## How it works

When activated, the skill creates and maintains:

```text
.agent/CONTINUITY.md
```

The file records only the state another agent needs to resume effectively.

For example:

```markdown
# Continuity

Status: active

## Objective

Implement offline synchronization while preserving existing API behavior.

## Current

Sync engine and persistence are implemented.
Conflict resolution is incomplete.

## Decisions

- SQLite remains the local source of truth.
- Server timestamps determine conflict ordering.

## Validation

- Unit tests: 184 passed
- ConflictResolutionTests: 2 failing
- UI tests: not run

## Blockers

None.

## Next

Fix timestamp tie handling in ConflictResolver and rerun SyncTests.

## References

- docs/offline-sync.md
```

The checkpoint is deliberately not a:

- conversation log;
- task manager;
- implementation history;
- chain-of-thought record.

The repository remains the source of truth.

## Install

Install from the `coool-labs/coool-agents` repository:

```bash
npx skills add coool-labs/coool-agents --skill continuity
```

You can also target supported agents explicitly:

```bash
npx skills add coool-labs/coool-agents \
  --skill continuity \
  -a codex \
  -a claude-code \
  -a github-copilot
```

## Usage

Activate the skill when starting substantial autonomous work that may outlive the current session.

For example:

```text
$continuity
```

or, in agents that expose skills as slash commands:

```text
/continuity
```

Then continue working normally.

The skill will:

1. create an initial checkpoint immediately;
2. keep it current after meaningful milestones;
3. record important decisions, validation, blockers, and the best next action;
4. rewrite stale state rather than accumulating a history log.

## Taking over in another agent

Open the same working tree and activate `continuity`.

The incoming agent should reconcile the checkpoint with the actual repository state, including:

```bash
git status
git diff
```

and any relevant code, tests, specs, issues, plans, ADRs, or other durable artifacts.

No conversation export is required.

## Designed for interruption

The checkpoint is updated after meaningful events such as:

- completing a substantial implementation step;
- making an important non-obvious decision;
- discovering or resolving a blocker;
- obtaining significant test or validation results;
- materially changing the best next action.

It is not updated after every command or small edit.

This keeps the file small enough to remain useful to the next agent.

## Principles

### Repository first

`CONTINUITY.md` may become stale.

The actual working tree, tests, code, and durable project artifacts are stronger evidence of the current state.

### Reference, don't duplicate

If information already exists in a spec, issue, ADR, plan, commit, or other durable artifact, continuity should reference it rather than copy it.

### Current state, not history

The checkpoint describes where the task is now.

Old information is replaced rather than appended indefinitely.

### Observed verification only

Tests, builds, runtime checks, and UI verification are recorded only when they were actually performed and their result was observed.

### One active objective

A working tree has one active continuity-tracked objective at a time.

An old or unrelated checkpoint must not silently redirect new work.

## What continuity is not

`continuity` is intentionally small.

It does not provide:

- task management;
- session transcripts;
- conversation export;
- automatic commits;
- branch management;
- agent orchestration;
- project planning;
- implementation history;
- hidden reasoning storage.

Most of that information either already exists in the repository or belongs in another tool.

## Handoff vs. continuity

A conventional handoff is still useful when intentionally transferring work and wanting a curated summary.

`continuity` addresses a different failure mode.

| | Handoff | Continuity |
|---|---|---|
| Created when | Before switching | Throughout work |
| Requires advance notice | Usually | No |
| Handles sudden quota exhaustion | Limited | Yes |
| State style | Transfer summary | Live checkpoint |
| Persistent repository state | Optional | Yes |
| Primary purpose | Intentional transfer | Recovery |

They can complement each other.

## Repository layout

Within `coool-labs/coool-agents`:

```text
skills/
└── continuity/
    ├── README.md
    └── SKILL.md
```

The skill itself is defined by `SKILL.md`.

This README is human-facing documentation.

## Status

`continuity` intentionally starts as a lean, instruction-only skill.

There are currently no:

- hooks;
- daemons;
- background processes;
- helper scripts;
- external services.

The initial design tests a simple hypothesis:

> Once continuity is activated, capable coding agents can reliably maintain a small live checkpoint during autonomous work.

More machinery should only be added if real-world usage demonstrates that it is necessary.

## Contributing

Issues and improvements are welcome in:

`coool-labs/coool-agents`

When proposing changes, preserve the core design goals:

- portable across agents;
- minimal persistent state;
- low context overhead;
- repository-first recovery;
- no unnecessary infrastructure;
- useful after unexpected interruption.

## License

See the repository license.
