# Neuron Agentic Development
This repository contains AI agents and skills for developing on [AWS Neuron](https://awsdocs-neuron.readthedocs-hosted.com/) (Trainium/Inferentia) hardware, including NKI kernel development, profiling and debugging. For an overview of Neuron Agentic Development and the tools it offers for agent-enabled workflows with Neuron, see [the overview of Neuron Agentic Development in the the public Neuron docs](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/about-neuron/agentic-development-overview.html).

## Installation

This repository supports three AI coding tools: **OpenCode**, **Claude Code**, and **Kiro**.

### OpenCode (Recommended)

Clone this repository into your project or home directory:

```bash
git clone https://github.com/jimburtoft/neuron-agentic-development.git
cd neuron-agentic-development
```

OpenCode automatically discovers skills and agents from the `.opencode/` directory:
- **Skills**: `.opencode/skills/` (symlinks to `skills/`)
- **Agents**: `.opencode/agents/` (OpenCode-native format)

Simply run `opencode` from the repo root -- all 5 skills and 4 agents will be available.

**Alternative: Global installation** (available in all projects):
```bash
# Copy skills to global config
cp -r skills/neuron-nki-* ~/.config/opencode/skills/

# Copy agents to global config
cp -r .opencode/agents/* ~/.config/opencode/agents/
```

### Claude Code / Kiro

```bash
# from neuron pypi repository
pip install --upgrade neuron-agentic-development \
    --extra-index-url https://pip.repos.neuron.amazonaws.com

# from local github clone
git clone https://github.com/aws-neuron/neuron-agentic-development.git
cd neuron-agentic-development
pip install .

# Deploy to your preferred tool:
deploy-neuron-agentic-development-to-kiro
# or
deploy-neuron-agentic-development-to-claude
```

## Compatibility

| Feature | OpenCode | Claude Code | Kiro |
|---------|----------|-------------|------|
| Skills (5) | Fully supported | Fully supported | Fully supported |
| Agents (4) | Fully supported | Fully supported | Fully supported |
| Session hooks | Manual (env check in agent prompt) | Automatic (`SessionStart`) | Automatic |
| Skill invocation | `skill({ name: "..." })` tool call | `/skill-name` slash command | `/skill-name` |
| Agent switching | Tab key (primary) / @ mention (sub) | N/A (single agent) | Agent routing |

### Compatibility Notes

| Item | Status | Details |
|------|--------|---------|
| Skills format | **Identical** | Same `SKILL.md` + `references/` + `examples/` structure works across all tools |
| Agent prompts | **Preserved** | System prompt body is unchanged; only frontmatter differs |
| `/skill-name` syntax in prompts | **Harmless** | OpenCode agents see this as documentation guidance, not literal commands |
| `argument-hint` in skill frontmatter | **Ignored** | OpenCode ignores unknown frontmatter fields |
| `SessionStart` hook | **Not supported in OpenCode** | Environment check is incorporated into the primary agent's system prompt |
| `{{aim:include:}}` template syntax | **Kiro-only** | OpenCode embeds prompts directly in markdown; Claude uses the deploy script |

### Breaking Changes from Upstream

**None.** This fork adds OpenCode support (`.opencode/` directory) without modifying any existing files. The original Claude Code and Kiro deployment paths remain fully functional.

## Agents

| Agent | Description |
|-------|-------------|
| [neuron-nki-agent](agents/neuron-nki-agent.md) | Unified NKI kernel development agent. Full lifecycle: writing kernels from PyTorch/NumPy/natural language, debugging compilation errors, profiling performance, optimizing bottlenecks, migrating between API versions, analyzing Perfetto traces, and NKI documentation lookup. |
| [neuron-nki-writer-agent](agents/neuron-nki-writer-agent.md) | NKI kernel authoring and modification. Translates from PyTorch/NumPy/natural language, adds shape/dtype support, refactors tiling strategies, and implements new features following Beta 3 API patterns. |
| [neuron-nki-debugger-agent](agents/neuron-nki-debugger-agent.md) | Autonomous NKI kernel compilation error debugging. Analyzes compiler errors, searches documentation and code examples for fixes, applies corrections following simplicity over performance, and validates fixes. |
| [neuron-nki-profile-analysis-agent](agents/neuron-nki-profile-analysis-agent.md) | Profile and analyze NKI kernels on Neuron hardware. Captures execution traces, computes performance bounds, identifies bottleneck engines, and runs investigations to localize inefficiencies to NKI source lines. |

### OpenCode Agent Roles

In OpenCode, the agents are organized as:

- **`neuron-nki-agent`** — Primary agent (Tab-switchable). Use for general NKI development.
- **`neuron-nki-writer-agent`** — Subagent (@ mentionable). Specialized for kernel authoring.
- **`neuron-nki-debugger-agent`** — Subagent (@ mentionable). Specialized for error debugging.
- **`neuron-nki-profile-analysis-agent`** — Subagent (@ mentionable). Specialized for profiling.

## Skills

| Skill | Description |
|-------|-------------|
| [neuron-nki-writing](skills/neuron-nki-writing/SKILL.md) | Write and modify NKI kernels. Covers new kernel creation from PyTorch/NumPy/natural language, editing existing kernels, adding shape/dtype support, refactoring tiling strategies, and implementing new features. |
| [neuron-nki-debugging](skills/neuron-nki-debugging/SKILL.md) | Debug NKI compilation errors on Neuron hardware. |
| [neuron-nki-docs](skills/neuron-nki-docs/SKILL.md) | Research NKI documentation for API lookups, tutorials, error codes, and architecture details. |
| [neuron-nki-profiling](skills/neuron-nki-profiling/SKILL.md) | Profile NKI kernels to analyze performance on Neuron hardware. |
| [neuron-nki-profile-querying](skills/neuron-nki-profile-querying/SKILL.md) | Query and analyze NKI kernel profile data from neuron-explorer parquet files via SQL and Python. |

## Repository Structure

```
neuron-agentic-development/
├── .opencode/                    # OpenCode-specific configuration
│   ├── agents/                   # OpenCode-format agent definitions
│   │   ├── neuron-nki-agent.md
│   │   ├── neuron-nki-writer-agent.md
│   │   ├── neuron-nki-debugger-agent.md
│   │   └── neuron-nki-profile-analysis-agent.md
│   └── skills/                   # Symlinks to skills/ for OpenCode discovery
│       ├── neuron-nki-writing -> ../../skills/neuron-nki-writing
│       ├── neuron-nki-debugging -> ../../skills/neuron-nki-debugging
│       ├── neuron-nki-docs -> ../../skills/neuron-nki-docs
│       ├── neuron-nki-profiling -> ../../skills/neuron-nki-profiling
│       └── neuron-nki-profile-querying -> ../../skills/neuron-nki-profile-querying
├── agents/                       # Claude Code / Kiro agent definitions
│   ├── neuron-nki-agent.md
│   ├── neuron-nki-agent.agent-spec.json
│   └── ...
├── skills/                       # Shared skill definitions (all tools)
│   ├── neuron-nki-writing/
│   ├── neuron-nki-debugging/
│   ├── neuron-nki-docs/
│   ├── neuron-nki-profiling/
│   └── neuron-nki-profile-querying/
├── hooks/                        # Claude Code lifecycle hooks
│   ├── hooks.json
│   └── scripts/check-env.sh
├── src/                          # Python deployment scripts
└── README.md                     # This file
```

## Contributing

We are evaluating the external contribution process. All capabilities undergo internal verification to ensure technical accuracy, security, and architectural alignment. In the interim, we welcome feedback and feature requests via [Issues](https://github.com/aws-neuron/neuron-agentic-development/issues) 


## License

Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
