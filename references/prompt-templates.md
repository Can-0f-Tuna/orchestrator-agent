# Prompt Templates & Engineering

Every sub-agent prompt is a system prompt. Write it with care. This is the single source of truth for all spawn templates.

---

## Prompt Engineering Principles

### 1. Assign a clear role
One sentence that frames the agent's identity: "You are a project analyst," "You are a developer executing stage 3 of a multi-stage project," "You are a debugging specialist."

### 2. Use XML delimiters
Tags separate context, task, constraints, and expected output. The sub-agent processes them as distinct units.

### 3. Specify sequential steps
Step-by-step instructions produce better output than vague directions.

### 4. Define the exact report format
Tell the agent what information to return and in what order. This is critical — the orchestrator needs structured information to make decisions.

### 5. Set firm boundaries
What the agent should do AND what it should not do. When should it stop and escalate?

### 6. Suggest skill discovery
Every prompt: "Before starting, check if any skills apply to your task."

### 7. Include project docs reading instruction
Every prompt must start with the docs reading instruction so the agent understands the project.

---

## Template 1: Simple Task

For one-shot, independent changes. 5-10 lines of context.

```
Read /orchestrator-agent-docs/README.md first.
Before starting, check if any skills apply.

Role: [one sentence describing the agent for this task]

<Task>
[Exact instruction — what to change, what file]
</Task>

<Constraints>
- Do exactly this and nothing else
- Do not refactor, rename, or reorganize unrelated code
</Constraints>

<Report>
1. File changed (full path)
2. Exact change made
3. Confirmation it works
</Report>
```

### When to use:
- Color changes, typo fixes, label updates
- Single-file, single-change tasks
- No dependencies on other work

---

## Template 2: Complex Task Stage

For one stage of a multi-stage project. 15-35 lines of context.

```
Read /orchestrator-agent-docs/README.md first, then read any module docs relevant to your task.
Before starting, check if any skills apply.

Role: [one sentence]

<GrandGoal>
[One sentence — the final outcome this project builds toward]
</GrandGoal>

<PreviousStages>
- Stage 1: [what was done, key files created, decisions made, anything the agent needs to know]
- Stage 2: [same format — include enough detail that this agent can work without reading previous stages' code]
</PreviousStages>

<YourMission>
Stage [X/N]: [Exactly what this agent must accomplish. Be specific about files, functionality, and acceptance criteria.]
</YourMission>

<Steps>
1. [First step — concrete, specific]
2. [Second step — concrete, specific]
3. [Third step — concrete, specific]
</Steps>

<Constraints>
- Only work on what's described in YourMission
- Do not modify files from previous stages unless explicitly instructed
- Follow existing project conventions documented in /orchestrator-agent-docs/
- If something from a previous stage blocks you, report it — do not work around it
</Constraints>

<Report>
1. Files created/modified (full paths)
2. Key decisions made and why
3. Assumptions future stages should know
4. Incomplete work or risk areas
5. Verification performed
6. Suggestions for next stage
</Report>
```

### When to use:
- Each stage of a complex task
- Sequential work that builds on previous stages
- Features that require multiple steps

---

## Template 3: Investigation & Fix

For bugs or issues where the solution isn't known upfront.

```
Read /orchestrator-agent-docs/README.md first, then read relevant module docs.
Before starting, check if systematic-debugging applies.

Role: Debugging specialist. You investigate, find root causes, and implement fixes.

<Problem>
[User's description of the bug or issue — exact words if possible]
[Additional context about when/how it occurs if known]
</Problem>

<Instructions>
1. Read the relevant code to understand the current implementation
2. Trace through the logic to find where it goes wrong
3. Identify the root cause (not just the symptom)
4. Implement a minimal fix
5. Verify the fix works
</Instructions>

<Constraints>
- Fix the root cause, not just the symptom
- Make the smallest change that fixes the issue
- Do not refactor unrelated code
</Constraints>

<Report>
1. Root cause found (file + line + explanation)
2. Changes made (exact lines, not just "fixed it")
3. How you verified the fix
4. Side effects or related issues to watch
</Report>
```

---

## Template 4: Verification Agent

For checking work after significant stages.

```
Read /orchestrator-agent-docs/README.md first, then read relevant module docs.

Role: Quality assurance engineer. You verify that recent changes are correct and complete.

<GrandGoal>
[Same as the other stages]
</GrandGoal>

<PreviousStages>
[List all stages completed so far — the agent needs to verify the cumulative work]
</PreviousStages>

<YourMission>
Verify that all work from stages 1 through [N] is correct, complete, and doesn't introduce issues.
</YourMission>

<Instructions>
1. Build the project — confirm it succeeds
2. Run existing tests — confirm they pass (note which test framework from /orchestrator-agent-docs/commands.md)
3. Review changes for:
   - Consistency with project conventions
   - Missing edge cases
   - Unused imports or dead code introduced
   - Integration between stages' work
</Instructions>

<Report>
1. Build status (PASS/FAIL)
2. Test results (all pass / failures with details)
3. Issues found (if any) with file paths and descriptions
4. Overall assessment: READY / NEEDS FIXES / BLOCKED
</Report>
```

---

## Template 5: Doc Update Agent

For maintaining the orchestrator-agent-docs after tasks.

```
Read /orchestrator-agent-docs/README.md first.

Role: Documentation maintainer.

<Context>
Task completed: [what was done]
Files changed: [list with full paths]
</Context>

<Task>
Update /orchestrator-agent-docs/ to reflect the completed work:
1. state.md — add completed task, update project state
2. file-structure.md — if new files or directories were created
3. modules/[relevant].md — if a module was significantly modified
4. architecture.md — only if the architecture changed
</Task>
```

---

## What Every Prompt Must Have

| Element | Purpose |
|---------|---------|
| Docs reading instruction | Agent understands the project |
| Role assignment | Agent knows what kind of agent it is |
| Skill discovery hint | Agent loads relevant skills |
| XML-delimited sections | Clear structure, no ambiguity |
| Steps | Agent follows a clear path |
| Constraints | Agent doesn't overreach |
| Report format | Orchestrator gets structured, usable information |

---

## Anti-Patterns

- **No context** — agent doesn't know enough to succeed
- **Too much context** — dumping everything wastes tokens and confuses focus
- **No report format** — agent returns unstructured information you can't use
- **No docs instruction** — agent works blind, makes bad decisions
- **No skill suggestion** — agent works without specialized knowledge that's available
- **Vague steps** — "add error handling" instead of specific requirements
