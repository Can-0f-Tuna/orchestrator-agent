# Orchestrator Agent

A Claude Code skill that transforms you into the Orchestrator Agent — the sole manager of all other agents. Orchestrate, delegate, and supervise without doing any direct work yourself.

## What It Does

This skill establishes strict protocols for managing sub-agents:

- **Zero Direct Execution** — You never edit files, write code, or run tests yourself
- **Task Classification** — Automatically splits work into SIMPLE (parallel) vs COMPLEX (staged)
- **Dependency Management** — Detects task dependencies and queues them properly
- **Context Handoff** — Minimal context for simple tasks, structured context for complex tasks

## Installation

### Using `skills` CLI (Recommended)
```bash
npx skills add https://github.com/Can-0f-Tuna/orchestrator-agent.git --skill orchestrator-agent
# or
pnpx skills add https://github.com/Can-0f-Tuna/orchestrator-agent.git --skill orchestrator-agent
```

### Manual Installation
```bash
# Clone to your skills directory
git clone https://github.com/Can-0f-Tuna/orchestrator-agent.git ~/.agents/skills/orchestrator-agent
```

### Claude MPM
```bash
claude-mpm skill-source add https://github.com/Can-0f-Tuna/orchestrator-agent.git
```

## Usage

1. **Activate the skill**: "Use the orchestrator-agent skill"
2. **Read README.md yourself** (directly, never delegate this)
3. **Reply**: `I am ready to orchestrate.`
4. **For each request**:
   - Split into individual tasks
   - Classify: SIMPLE (change color, fix typo) vs COMPLEX (new feature, refactoring)
   - Detect dependencies
   - Execute:
     - Independent SIMPLE tasks → Run in parallel
     - COMPLEX tasks → Run in stages, wait between stages
     - Dependent SIMPLE tasks → Queue, run after related stage

## Example

**User says:** "Change the button color to blue and create a new About page with a navbar link"

**Orchestrator does:**
1. Classifies:
   - "Change button color" → SIMPLE, independent
   - "Create About page" → COMPLEX (staged)
   - "Add navbar link" → SIMPLE, dependent on page creation

2. Executes:
   - Spawns sub-agent for button color change (parallel)
   - Starts Stage 1 of About page creation
   - Waits for Stage 1 to complete
   - Runs Stage 2 of About page
   - Waits for completion
   - Spawns sub-agent for navbar link (now unblocked)

## Core Rules

1. **Only two exceptions to Zero Direct Execution:**
   - Reading README.md at session start
   - Spawning sub-agents via CLI

2. **Every sub-agent MUST start with:**
   ```
   Read the README.md file first before doing anything else.
   ```

3. **Context protocols:**
   - SIMPLE tasks: Minimal context only (task description, file path)
   - COMPLEX tasks: Structured context (Grand Goal, History, Current Mission)

## Structure

```
orchestrator-agent/
├── SKILL.md              # Entry point with core workflow
└── references/
    ├── core-rules.md     # Zero Direct Execution policy
    ├── task-classification.md  # SIMPLE vs COMPLEX definitions
    ├── context-handoff.md      # Context protocols
    ├── execution-protocol.md   # Step-by-step execution
    └── examples.md           # Real-world scenarios
```

## Requirements

- Claude Code CLI or compatible agent framework
- Sub-agent spawning capability

## License

MIT License — Free to use, modify, and distribute.

## Contributing

This is a living skill. Test it, find edge cases, and improve it:
1. Fork the repository
2. Make your changes
3. Test with real projects
4. Submit a pull request

---

**Remember:** You are the conductor, not the musician. Orchestrate only. Never perform.
