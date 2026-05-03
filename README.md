# Orchestrator Agent

A skill that transforms you into the Orchestrator Agent — the manager and director of all sub-agents. Orchestrate, delegate, coordinate, and supervise without doing any direct work yourself.

## What It Does

This skill establishes a professional protocol for managing sub-agents:

- **Zero Direct Execution** — You never edit files, write code, or run tests yourself. You spawn sub-agents for everything.
- **Task Classification** — Split work into SIMPLE (parallel) vs COMPLEX (staged) tasks
- **Dependency Management** — Detect task dependencies and queue them properly
- **Prompt Engineering** — Write effective sub-agent prompts using role assignment, delimiters, structured output formats, and skill suggestions
- **Skill Discovery** — Proactively suggest relevant skills to sub-agents so they work with the right tools and knowledge
- **Stage Planning** — Break large tasks into ordered stages with context chains and verification gates

## Installation

```bash
npx skills add https://github.com/Can-0f-Tuna/orchestrator-agent.git --skill orchestrator-agent
```

## Setup

Run the setup script to configure which file agents should read to understand your project:

```bash
cd ~/.agents/skills/orchestrator-agent
node scripts/setup.js
```

This creates a `.orchestrator` config file. If not configured, agents default to reading `README.md`.

## Usage

1. Activate the skill: "Use the orchestrator-agent skill"
2. Read README.md yourself
3. Reply: `I am ready to orchestrate.`
4. For each request:
   - Split into atomic tasks
   - Classify: SIMPLE vs COMPLEX
   - Detect dependencies
   - Identify relevant skills for each sub-agent
   - Execute:
     - Independent SIMPLE → parallel
     - COMPLEX → sequential stages
     - DEPENDENT → queued after prerequisite

## Example

**User says:** "Change the button color to blue and create a new About page with a navbar link"

**Orchestrator does:**
1. Classifies:
   - "Change button color" → SIMPLE, independent
   - "Create About page" → COMPLEX (staged)
   - "Add navbar link" → SIMPLE, dependent on page creation
2. Identifies skills: suggests frontend-design for the About page agent
3. Executes:
   - Spawns button color agent (parallel)
   - Starts Stage 1 of About page creation
   - After Stage 1 completes, spawns navbar link agent
4. Reports results

## Core Principles

1. **Only the orchestrator reads README.md** — every sub-agent is told to read it too
2. **Every sub-agent prompt is a system prompt** — write it with care: role, delimiters, steps, constraints, output format
3. **Skills are tools — suggest them** — tell sub-agents which skills would help them before they start
4. **Large tasks are staged** — foundation first, verification gates, context chains
5. **Structured reports matter** — tell sub-agents exactly what to report so you can make informed decisions

## Structure

```
orchestrator-agent/
├── SKILL.md              # Entry point with core workflow
├── README.md             # This file
├── scripts/
│   └── setup.js          # Interactive configuration
└── references/
    ├── core-rules.md     # Rules and rationale
    ├── task-classification.md  # SIMPLE vs COMPLEX, staging
    ├── context-handoff.md      # Prompt engineering & context
    ├── execution-protocol.md   # Complete execution flow
    └── examples.md           # Real-world scenarios
```

## Requirements

- Agent framework with sub-agent spawning capability (Claude Code, Codex, etc.)
- `task` tool access for spawning sub-agents

## License

MIT License — Free to use, modify, and distribute.

---

**Remember:** You are the conductor, not the musician. Orchestrate only. Never perform.
