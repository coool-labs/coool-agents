# AGENTS.md

This repository contains portable Agent Skills and supporting tooling.

## Principles

- Prefer portable, standards-based Agent Skills over harness-specific solutions.
- Keep each skill focused on one clear workflow problem.
- Keep `SKILL.md` files concise and high-signal.
- Add complexity only when real usage demonstrates the need.
- Avoid duplicating information already available in code, documentation, specs, diffs, issues, or other durable artifacts.
- Prefer instruction-only skills before adding scripts, packages, hooks, or background processes.
- Preserve compatibility with Codex, Claude Code, GitHub Copilot, and other Agent Skills-compatible tools where practical.

## Repository structure

Published skills live under:

`skills/<skill-name>/`

A typical skill may contain:

- `SKILL.md` — agent-facing skill definition.
- `README.md` — human-facing documentation.
- `references/` — optional supporting material.
- `scripts/` — optional executable helpers.
- `assets/` — optional static resources.

Executable packages or shared tooling belong under:

`packages/`

Do not create an npm package for an instruction-only skill.

Packages intended for npm publication use the `@coool` scope.

## Creating or modifying skills

Before adding a new skill:

1. Confirm it solves a real workflow problem.
2. Check whether an existing skill already solves the problem adequately.
3. Prefer the smallest viable solution.
4. Prefer instructions over executable tooling when instructions are sufficient.
5. Keep the public behavior and scope narrow.

For every `SKILL.md`:

- Follow the Agent Skills specification.
- Keep `name` stable and aligned with the skill directory name.
- Write a precise `description` that explains both what the skill does and when it should be used.
- Prefer standard metadata over harness-specific frontmatter.
- Keep the main instructions concise.
- Use progressive disclosure: move lengthy supporting material into `references/` when needed.
- Do not store secrets, credentials, hidden reasoning, conversation transcripts, or unnecessary sensitive information.
- Do not duplicate information already available in repository artifacts.

## README expectations

Each published skill should have a nearby `README.md` for humans.

It should explain:

- the problem the skill solves;
- installation;
- usage;
- important design decisions;
- compatibility or limitations where relevant.

Do not simply duplicate the full `SKILL.md`.

## Portability

Do not create separate Codex, Claude Code, or GitHub Copilot variants unless their capabilities genuinely require different behavior.

When platform-specific behavior is necessary:

- isolate it;
- document it clearly;
- keep the portable core shared.

Do not claim support for behavior a target agent cannot actually perform.

## Scripts and packages

Only add executable tooling when instructions alone are insufficient.

Scripts must:

- have a clear purpose;
- minimize side effects;
- avoid destructive behavior by default;
- avoid hidden network access;
- never embed credentials or secrets;
- fail clearly rather than masking errors.

Keep package responsibilities narrow and reusable.

Do not introduce shared infrastructure before more than one skill genuinely needs it.

## Quality

For every changed skill:

- validate its structure and metadata;
- review the activation description carefully;
- check for unnecessary context usage;
- verify referenced paths and commands;
- check for accidental secrets or sensitive data;
- test intended behavior with relevant agents where practical.

For instruction-only skills, prefer behavioral evaluation over artificial unit tests around Markdown syntax.

When a skill claims cross-agent behavior, test it across the agents it claims to support where practical.

## Scope discipline

Keep changes focused on the requested skill or tooling.

Do not opportunistically redesign unrelated skills.

Do not introduce speculative abstractions for hypothetical future needs.

Prefer simple local solutions over premature shared infrastructure.

Extract shared tooling only after a genuine shared responsibility exists.

## Documentation consistency

When behavior changes, update the relevant:

- `SKILL.md`;
- skill `README.md`;
- root `README.md` if installation, compatibility, repository structure, or public behavior changes.

Keep examples consistent with:

- GitHub repository: `coool-labs/coool-agents`
- npm scope: `@coool`

## Security

Never introduce or persist:

- secrets;
- credentials;
- API tokens;
- private keys;
- signing material;
- unnecessary sensitive information;
- hidden chain-of-thought.

Any script or package that uses network access, filesystem mutation, credentials, or destructive operations must make those behaviors explicit.

## Completion

Before considering repository work complete:

1. Confirm the requested behavior is implemented.
2. Validate affected skills.
3. Run relevant tests or behavioral checks.
4. Review the diff for unrelated changes or unnecessary complexity.
5. Ensure documentation matches the implementation.
