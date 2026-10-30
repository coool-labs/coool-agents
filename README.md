# Coool Agents
**Portable skills and tooling for modern coding agents.**
Coool Agents is a collection of focused, reusable Agent Skills for improving real-world coding-agent workflows.
Designed to work across:
- Codex
- Claude Code
- GitHub Copilot
- other Agent Skills-compatible tools
## Install
Install skills directly from this repository:
```bash
npx skills add coool-labs/coool-agents
```
Install a specific skill:
```bash
npx skills add coool-labs/coool-agents --skill continuity
```
Or install for specific agents:
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
`continuity` maintains a small repository-local checkpoint while an agent works, allowing another session or coding agent to recover after:
- quota limits;
- context loss;
- crashes;
- intentional agent switching.
> Handoff assumes you know you're about to stop.  
> Continuity assumes you don't.
Install:
```bash
npx skills add coool-labs/coool-agents --skill continuity
```
Documentation:
[`skills/continuity`](./skills/continuity)
## Compatibility
Skills in this repository target the open Agent Skills format and are designed to remain agent-agnostic wherever practical.
Individual capabilities may depend on the features available in each coding-agent harness.
Compatibility and any agent-specific limitations are documented with each skill.
## Repository structure
```text
coool-agents/
├── skills/
│   ├── continuity/
│   │   ├── README.md
│   │   └── SKILL.md
│   └── ...
├── packages/
│   └── ...
├── AGENTS.md
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```
### `skills/`
Portable Agent Skills.
Each skill has its own directory containing at least:
```text
skills/<skill-name>/
├── README.md
└── SKILL.md
```
### `packages/`
Optional executable tooling or libraries used by skills.
Packages intended for npm publication use the `@coool` namespace.
Instruction-only skills do not require npm packages.
## Contributing
Contributions, ideas, and bug reports are welcome.
See [`CONTRIBUTING.md`](./CONTRIBUTING.md) for development guidelines.
## Coool Labs
Built by **Coool Labs**.
GitHub: `coool-labs`  
npm: `@coool`
## License
See [`LICENSE`](./LICENSE).
