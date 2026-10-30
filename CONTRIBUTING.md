# Contributing to Coool Agents
Thanks for contributing to Coool Agents.
This repository contains portable Agent Skills and supporting tooling for modern coding agents.
The main goals are:
- solve real agent workflow problems;
- keep skills focused and easy to understand;
- remain portable across agents where practical;
- minimize unnecessary context and infrastructure;
- add complexity only when real usage justifies it.
## Repository structure
Published skills live under:
```text
skills/<skill-name>/
```
A typical skill contains:
```text
skills/<skill-name>/
├── SKILL.md
├── README.md
├── references/   # optional
├── scripts/      # optional
└── assets/       # optional
```
Supporting executable tooling and reusable packages belong under:
```text
packages/
```
Packages intended for npm publication use the `@coool` scope.
Do not create an npm package for an instruction-only skill.
## Before adding a new skill
Before creating a skill:
1. Confirm that it solves a real workflow problem.
2. Check whether an existing skill already solves the problem adequately.
3. Prefer the smallest viable solution.
4. Prefer instructions over executable tooling when instructions are sufficient.
5. Consider whether the skill can remain portable across multiple coding agents.
A new skill should usually begin with only:
```text
skills/my-skill/
├── README.md
└── SKILL.md
```
Add scripts, references, assets, or packages only when they provide clear value.
## Skill design
Each skill should solve one clear problem.
Avoid turning a focused workflow primitive into a large framework.
Prefer:
- small, explicit workflows;
- durable repository state over conversation state;
- references to existing artifacts instead of duplication;
- standard Agent Skills conventions;
- progressive disclosure for detailed supporting material.
Avoid:
- giant `SKILL.md` files;
- unnecessary state files;
- conversation transcripts;
- duplicated project documentation;
- speculative abstractions;
- hidden background behavior;
- unnecessary dependencies;
- harness-specific forks without a real need.
## SKILL.md requirements
Every skill must contain a valid `SKILL.md`.
At minimum, use:
```yaml
---
name: my-skill
description: Describe what this skill does and when an agent should use it.
---
```
### Name
The skill name should:
- match the skill directory name;
- use lowercase kebab-case;
- remain stable after publication unless there is a strong reason to rename it.
### Description
The description is particularly important because agents may use it to decide whether the skill should be loaded.
A good description should explain:
- what the skill does;
- when it should be used;
- the important workflow problem it solves.
Avoid vague descriptions such as:
```text
Helps with development.
```
Prefer descriptions that make activation conditions clear.
### Body
Keep the main skill instructions concise and high-signal.
Do not put information in `SKILL.md` simply because it might someday be useful.
If substantial supporting documentation is necessary, place it under:
```text
references/
```
and reference it from the main skill.
## README requirements
Every published skill should include a human-facing `README.md`.
The README should generally explain:
- what problem the skill solves;
- why the skill exists;
- how to install it;
- how to use it;
- compatibility considerations;
- important limitations or design decisions.
Do not simply duplicate the full contents of `SKILL.md`.
The README explains the skill to humans.
`SKILL.md` instructs the agent.
## Portability
Skills should target the open Agent Skills format wherever practical.
The default goal is compatibility with:
- Codex;
- Claude Code;
- GitHub Copilot;
- other Agent Skills-compatible tools.
Do not create separate agent-specific versions unless the underlying agent capabilities genuinely require different behavior.
When platform-specific behavior is necessary:
- isolate it;
- document it clearly;
- keep the portable core shared.
Do not claim support for behavior that a target agent cannot actually perform.
## Scripts
Only add scripts when instructions alone are insufficient.
Scripts must:
- have a clear purpose;
- minimize side effects;
- avoid destructive behavior by default;
- avoid hidden network access;
- never embed credentials or secrets;
- fail clearly when something goes wrong.
If a script mutates files, accesses the network, uses credentials, or performs destructive operations, that behavior must be obvious from the surrounding documentation.
## Packages
Reusable executable tooling belongs under:
```text
packages/
```
Packages intended for npm publication use the namespace:
```text
@coool/*
```
Keep package responsibilities narrow.
Do not introduce shared infrastructure before multiple skills genuinely need the same capability.
Prefer a simple local implementation over premature shared abstractions.
## Security
Skills, scripts, documentation, and examples must never encourage storing:
- passwords;
- credentials;
- API tokens;
- private keys;
- signing material;
- secrets;
- unnecessary personal or sensitive information;
- hidden chain-of-thought or private reasoning.
Never include real credentials in examples.
Do not hide network access, filesystem mutation, credential usage, or destructive operations behind an apparently harmless skill invocation.
## Quality and validation
Before submitting a change:
- validate affected skill metadata and structure;
- review skill descriptions carefully;
- check referenced paths and commands;
- check documentation for stale behavior;
- check for accidental secrets or sensitive data;
- review context usage and unnecessary verbosity;
- test the intended workflow where practical.
For instruction-only skills, behavioral evaluation is usually more valuable than artificial unit tests around Markdown.
If a skill claims cross-agent compatibility, test it with the relevant agents where practical.
For example, verify whether:
- the skill is discovered correctly;
- explicit invocation works;
- the agent follows the intended workflow;
- expected files or artifacts are produced;
- resuming or recovery works where applicable;
- state does not grow unnecessarily;
- agent-specific behavior matches the documentation.
## Scope discipline
Keep changes focused.
Do not opportunistically redesign unrelated skills while implementing another change.
Avoid speculative infrastructure intended only for hypothetical future needs.
When shared behavior appears in one place, keep it local.
Extract shared tooling when a genuine shared responsibility exists.
## Documentation consistency
When changing skill behavior, review and update the relevant:
- `SKILL.md`;
- skill `README.md`;
- root `README.md` when public installation, compatibility, repository structure, or available skills change.
Examples should use the canonical project identities:
```text
GitHub: coool-labs/coool-agents
npm:    @coool
```
## Pull requests
Keep pull requests focused and easy to review.
A good pull request should explain:
- the workflow problem being addressed;
- why the change is needed;
- what behavior changes;
- how the behavior was validated.
For new skills, include the motivation for creating a new skill rather than extending or using an existing one.
Avoid combining unrelated changes into the same pull request.
## Completion checklist
Before considering a contribution ready:
1. Confirm the intended behavior is implemented.
2. Validate affected skills.
3. Run relevant tests or behavioral checks.
4. Review the diff for unrelated changes.
5. Remove unnecessary complexity.
6. Ensure documentation matches actual behavior.
7. Confirm no secrets or sensitive information were introduced.
## License
By contributing to this repository, you agree that your contributions may be distributed under the repository's license.
