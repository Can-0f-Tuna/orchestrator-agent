# Project Docs Protocol

The `/orchestrator-agent-docs/` directory is the mandatory project knowledge base. Every orchestrator session begins by checking for these docs. Every sub-agent reads relevant docs before working.

---

## Why This Exists

A sub-agent arriving at a project with no context makes bad decisions. The orchestrator-agent-docs give every agent exactly the information they need using progressive disclosure — overview first, then details, then deep dives.

The docs replace generic "Read the README.md" with a structured, AI-optimized understanding of the project.

---

## Doc Structure (Progressive Disclosure)

```
/orchestrator-agent-docs/
├── README.md              # Level 1: Project identity, goal, tech stack (100-150 lines)
├── architecture.md        # Level 2: System structure, component map, data flow
├── file-structure.md      # Level 2: Directory tree with one-line annotations
├── conventions.md         # Level 2: Code style, patterns, naming, linting
├── commands.md            # Level 2: Build, test, lint, dev, deploy commands
├── dependencies.md        # Level 2: Packages, versions, why each was chosen
├── state.md               # Level 2: Current state, completed, in progress, known issues
└── modules/               # Level 3: Deep dives — one per major feature/module
    ├── database.md
    ├── auth.md
    ├── api.md
    └── frontend.md
```

### Reading order for orchestrator:
1. README.md (always — 100-150 lines, quick overview)
2. architecture.md (to understand structure)
3. commands.md (to know what sub-agents need to run)
4. Specific Level 2/3 files relevant to the user's task

### Reading order for sub-agents:
1. README.md (always)
2. Module files relevant to their specific task

---

## Doc Creation Protocol (When Docs Don't Exist)

This takes absolute priority over the user's request. Understanding the project is the foundation of good orchestration.

Spawn a project analyst agent:

```
Read the project README.md and any key config files first.

Role: You are a project analyst. Your job is to understand this project completely and document it for AI agents.

<Task>
Create the directory /orchestrator-agent-docs/ with every file listed below.
Each file must contain real, accurate, detailed content — no placeholders, no "TBD", no guesses.
</Task>

<FilesToCreate>

1. /orchestrator-agent-docs/README.md (100-150 lines)
   - Project name and one-sentence purpose
   - What this project does and who it's for
   - Tech stack summary
   - Architecture at a glance (2-3 sentences)
   - Key commands
   - How to navigate the rest of the docs

2. /orchestrator-agent-docs/architecture.md
   - High-level system design
   - Key components and how they communicate
   - Data flow through the system
   - Important design decisions and patterns used

3. /orchestrator-agent-docs/file-structure.md
   - Full directory tree with one-line annotations per directory
   - Key file locations (entry points, config, main modules)
   - Where to find each type of thing (pages, components, tests, API routes)

4. /orchestrator-agent-docs/conventions.md
   - Code style and formatting
   - Naming conventions
   - Patterns used throughout
   - Linting and formatting rules

5. /orchestrator-agent-docs/commands.md
   - Build command
   - Test command and test framework
   - Lint command
   - Dev server command
   - Deploy command
   - Any other common commands

6. /orchestrator-agent-docs/dependencies.md
   - Key dependencies with versions
   - Why each major dependency was chosen
   - Version constraints and compatibility notes

7. /orchestrator-agent-docs/state.md
   - Current project status
   - What features are implemented
   - What's in progress
   - Known issues or tech debt
   - Recent changes

8. /orchestrator-agent-docs/modules/
   - One file per major feature area
   - Purpose of each module, key files, how it connects to other modules
   - Descriptive names: auth.md, database.md, api.md, frontend.md, etc.

</FilesToCreate>

<Instructions>
1. Read README.md, package.json (or equivalent), and all config files first
2. Read key source files to understand architecture
3. Map the full directory structure
4. Identify tech stack from dependencies and config
5. Understand component connections by tracing imports
6. Write every file with real, specific content
7. Use exact file paths, real command strings, actual package names and versions
</Instructions>

<Constraints>
- No placeholders, no "TBD", no "TODO" anywhere
- No guesses — if you can't determine something, note it as "Not found — checked [what]"
- Be thorough — these docs are the sole knowledge source for all future agents
</Constraints>

<Report>
1. Full list of created files with paths
2. Summary of what the project is (2-3 sentences)
3. Tech stack identified
4. Any gaps that need human input
</Report>
```

---

## Doc Update Protocol (After Every Task)

After sub-agents complete work, the docs must be updated so they stay accurate.

### Light update (most tasks):

```
Read /orchestrator-agent-docs/README.md first.

Role: Documentation maintainer. Update project docs after a completed task.

<Context>
Task completed: [what was done]
Files changed: [list with paths]
</Context>

<Task>
Update /orchestrator-agent-docs/ files:
1. state.md — add completed task, update "in progress" if needed
2. file-structure.md — if new files/directories were created
3. modules/[relevant].md — if a module was significantly changed
4. architecture.md — only if the architecture changed
</Task>
```

### Full update (after major changes):

Spawn the full doc creation agent again: "Re-analyze the project and update all docs. Preserve content that is still accurate."

---

## Doc Quality Standards

Every doc file must:
- Contain real, specific information (not generic descriptions)
- Use exact file paths, command strings, package names
- Be self-contained enough that an agent reading it understands the topic
- Be updated after every relevant change

Docs are the foundation. Every agent decision builds on them. Keep them accurate.
