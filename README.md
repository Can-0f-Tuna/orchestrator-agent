# Orchestrator Agent

A skill that transforms you into the Orchestrator Agent — the manager and director of all sub-agents. Orchestrate, delegate, coordinate, and supervise. Read to understand. Spawn to execute.

## What It Does

- **Project Docs Protocol** — Automatic creation and maintenance of `/orchestrator-agent-docs/`, a structured knowledge base that every agent reads to understand the project
- **Selective Read Access** — Orchestrator reads project docs and specific files to coordinate intelligently, without doing implementation work
- **Task Classification** — Split work into SIMPLE (parallel) vs COMPLEX (staged) tasks with conflict detection
- **Prompt Engineering** — Structured sub-agent prompts with XML delimiters, role assignment, report formats, and skill discovery
- **Stage Planning** — Break large tasks into ordered stages with context chains and verification gates
- **Failure Protocol** — Retry limits, escalation paths, rollback strategies, hallucination detection

## Installation

```bash
npx skills add https://github.com/Can-0f-Tuna/orchestrator-agent.git --skill orchestrator-agent
```

## Usage

1. Activate: "Use the orchestrator-agent skill"
2. Orchestrator checks for `/orchestrator-agent-docs/` — creates them if missing
3. Reads docs to understand the project
4. For each request: split, classify, detect conflicts, spawn sub-agents, update docs

## Core Principles

1. **Read to understand, spawn to execute** — orchestrator reads docs + relevant files, never writes or runs commands
2. **Every agent reads the project docs** — `/orchestrator-agent-docs/` is the single source of project truth
3. **Skills are discovered, not hardcoded** — tell sub-agents to check what's available
4. **Prompts are system prompts** — role, delimiters, steps, constraints, report format
5. **Fail gracefully** — retry twice, then escalate. Never loop. Verify critical stages.
6. **Docs stay current** — update after every task

## Structure

```
orchestrator-agent/
├── SKILL.md                          # Entry point — core workflow
├── README.md                         # This file
├── references/
│   ├── core-rules.md                 # Rules and permission boundaries
│   ├── project-docs-protocol.md      # Full docs creation and maintenance protocol
│   ├── task-classification.md        # Classification + conflict detection
│   ├── prompt-templates.md           # All spawn templates + prompt engineering
│   ├── failure-protocol.md           # Retry, escalation, rollback, hallucination
│   └── examples.md                   # Real-world scenarios
```

## Requirements

- Agent framework with sub-agent spawning capability
- Write access to create `/orchestrator-agent-docs/` in the project root

## License

MIT License
