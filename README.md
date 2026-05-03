# Orchestrator Agent

A skill that transforms you into the Orchestrator Agent — the manager and director of all sub-agents. Orchestrate, delegate, coordinate, and supervise without doing any direct work yourself.

## What It Does

This skill establishes a professional protocol for managing sub-agents:

- **Project Docs Protocol** — Mandatory `/orchestrator-agent-docs/` knowledge base, auto-created if missing, updated after every task
- **Zero Direct Execution** — You never edit files, write code, or run tests yourself. You spawn sub-agents for everything.
- **Selective Read Access** — Orchestrator reads docs and project files for coordination, not implementation
- **Task Classification** — Split work into SIMPLE (parallel) vs COMPLEX (staged) tasks with conflict detection
- **Dependency Management** — Detect task dependencies and queue them properly
- **Prompt Engineering** — Write effective sub-agent prompts using role assignment, delimiters, structured output formats
- **Skill Discovery** — Proactively suggest relevant skills to sub-agents
- **Failure Protocol** — Retry limits (3), escalation, rollback, hallucination detection

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
2. Orchestrator checks for `/orchestrator-agent-docs/` — creates them if missing
3. Reads project docs to understand the project
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

1. **Every agent reads the project docs** — `/orchestrator-agent-docs/` is the single source of project truth. Created automatically if missing, updated after every task.
2. **Read to understand, spawn to execute** — orchestrator reads docs and relevant files for coordination, never writes or runs commands
3. **Every sub-agent prompt is a system prompt** — write it with care: role, delimiters, steps, constraints, output format
4. **Skills are tools — suggest them** — tell sub-agents which skills would help them before they start
5. **Large tasks are staged** — foundation first, verification gates, context chains
6. **Fail gracefully** — retry twice, then escalate. Never loop.

## Structure

```
orchestrator-agent/
├── SKILL.md                      # Entry point with core workflow
├── README.md                     # This file
└── references/
    ├── core-rules.md             # Rules and permission boundaries
    ├── project-docs-protocol.md  # Full docs creation and maintenance protocol
    ├── task-classification.md    # SIMPLE vs COMPLEX, staging, conflict detection
    ├── context-handoff.md        # Prompt engineering and context
    ├── execution-protocol.md     # Complete execution flow
    ├── failure-protocol.md       # Retry, escalation, rollback
    └── examples.md               # Real-world scenarios
```

## Requirements

- Agent framework with sub-agent spawning capability (Claude Code, Codex, etc.)
- `task` tool access for spawning sub-agents

## License

MIT License — Free to use, modify, and distribute.

---

**Remember:** You are the conductor, not the musician. Orchestrate only. Never perform.
