# Coool Agents

**Portable skills and tooling for modern coding agents.**

`coool-agents` is a collection of focused, reusable Agent Skills and supporting tools designed to work across coding agents such as:

- Codex
- Claude Code
- GitHub Copilot
- other Agent Skills-compatible harnesses

The goal is simple: solve missing workflow primitives once, keep them portable, and avoid unnecessary framework complexity.

## Install

Install skills directly from this repository:

```bash
npx skills add coool-labs/coool-agents
```

Install a specific skill:

```bash
npx skills add coool-labs/coool-agents --skill continuity
```

Or target specific supported agents:

```bash
npx skills add coool-labs/coool-agents \
  --skill continuity \
  -a codex \
  -a claude-code \
  -a github-copilot
```

## Skills

### continuity

**Live cross-agent continuity for long-running coding tasks.**

`continuity` maintains a small repository-local checkpoint while an agent works, so another session or coding agent can recover after unexpected interruption from quota limits, context loss, crashes, or agent switching.

> Handoff assumes you know you're about to stop.  
> Continuity assumes you don't.

Install:

```bash
npx skills add coool-labs/coool-agents --skill continuity
```

See [`skills/continuity`](./skills/continuity) for documentation.

## Philosophy

Skills in this repository should be:

### Portable

Prefer standards-based Agent Skills over harness-specific implementations.

A skill should work across Codex, Claude Code, GitHub Copilot, and other compatible agents whenever the underlying capability allows it.

### Focused

Each skill should solve one clear problem.

Avoid turning a useful workflow primitive into a large framework.

### Lean

Keep `SKILL.md` concise.

Agents already have access to the repository, code, diffs, tests, specs, issues, and other artifacts. Skills should not duplicate information the agent can discover directly.

### Practical

Skills should solve problems that appear in real coding-agent workflows.

Features should be added because they improve real usage, not because they make the implementation look more sophisticated.

### Repository-first

Code and durable project artifacts are generally stronger sources of truth than transient conversation state.

Where possible, skills should use and reference existing repository state rather than invent parallel state-management systems.

### Agent-agnostic by default

Avoid separate Codex, Claude, and Copilot variants unless their capabilities genuinely require different implementations.

Harness-specific behavior should be isolated rather than baked into otherwise portable skills.

## Repository structure

```text
coool-agents/
├── README.md
├── LICENSE
├── AGENTS.md
├── skills/
│   ├── continuity/
│   │   ├── README.md
│   │   └── SKILL.md
│   └── ...
├── packages/
│   └── ...
└── .github/
    └── workflows/
```

### `skills/`

Portable Agent Skills.

Each skill lives in its own directory:

```text
skills/<skill-name>/
├── SKILL.md
├── README.md
├── references/   # optional
├── scripts/      # optional
└── assets/       # optional
```

`SKILL.md` is the skill itself.

`README.md` is human-facing documentation and should explain the problem, intended usage, and relevant design decisions without duplicating the full skill instructions.

### `packages/`

Optional executable tooling and shared libraries.

This directory should remain empty unless a skill genuinely benefits from code that does not belong directly inside its skill directory.

Packages intended for npm publication use the `@coool` namespace.

For example:

```text
@coool/example
```

A Markdown-only Agent Skill does not need an npm package.

## Creating a skill

A new skill should normally start with only:

```text
skills/my-skill/
├── README.md
└── SKILL.md
```

The `SKILL.md` frontmatter should include a concise name and a strong description:

```yaml
---
name: my-skill
description: Describe what this skill does and when an agent should use it.
---
```

The description is important because agents may use it to decide whether the skill is relevant before loading the full instructions.

Prefer adding supporting files only when they materially improve the skill.

## Skill design guidelines

Before adding a new skill, ask:

1. Is there a real workflow problem?
2. Does a good existing skill already solve it?
3. Can the problem be solved cleanly with instructions alone?
4. Can the skill remain portable across multiple agents?
5. Is every persistent instruction worth its context cost?

Avoid:

- giant `SKILL.md` files;
- conversation transcripts;
- duplicated project documentation;
- unnecessary state files;
- speculative abstractions;
- platform-specific assumptions without need;
- helper scripts when instructions are sufficient;
- hidden background behavior;
- unnecessary dependencies.

Prefer progressive disclosure: keep the core skill small and place detailed supporting material in `references/` when needed.

## Compatibility

Skills should target the open Agent Skills format where practical.

Compatibility may vary by harness depending on support for:

- skill discovery;
- explicit skill invocation;
- project instruction files;
- hooks;
- shell access;
- computer use;
- filesystem access.

A skill should not claim support for behavior a target agent cannot actually perform.

When a feature is harness-specific, document that limitation clearly.

## Validation and quality

Every published skill should be checked for:

- valid skill metadata;
- clear activation description;
- portability;
- unnecessary context usage;
- unsafe or surprising side effects;
- secret or credential leakage;
- duplicated instructions;
- stale documentation.

Instruction-only skills should be tested behaviorally where practical.

For example, evaluate whether different agents:

- activate the skill appropriately;
- follow its main workflow;
- produce the expected durable artifacts;
- recover correctly from incomplete state;
- avoid unnecessary output or state growth.

Behavioral evaluation is more valuable than manufacturing tests around Markdown syntax alone.

## Security

Skills must never encourage agents to persist:

- secrets;
- credentials;
- API tokens;
- signing material;
- private keys;
- unnecessary sensitive information;
- hidden chain-of-thought.

Scripts must make their purpose and side effects clear.

Network access, destructive filesystem operations, credential access, or external service calls should never be hidden behind an innocuous skill invocation.

## Publishing

Skills are distributed directly from this GitHub repository.

For example:

```bash
npx skills add coool-labs/coool-agents --skill continuity
```

Published skills may also appear on [skills.sh](https://skills.sh/) through normal installation and discovery.

No npm publication is required for instruction-only skills.

npm packages should only be introduced when executable tooling provides clear value.

## Contributing

Contributions are welcome.

When proposing a new skill or significant change, preserve the project's core principles:

- solve a real problem;
- stay focused;
- stay portable;
- keep context overhead low;
- avoid unnecessary infrastructure;
- prefer durable repository state over conversational state;
- add complexity only when usage demonstrates the need.

For substantial additions, explain the workflow problem first and the proposed mechanism second.

## About Coool Labs

**Coool Labs** builds small, focused tools for developers and coding agents.

This repository is specifically for agent-facing skills, workflows, and supporting tooling.

GitHub organization:

`coool-labs`

npm namespace:

`@coool`

## License

See [`LICENSE`](./LICENSE).
