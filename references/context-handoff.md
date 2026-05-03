# Context Handoff & Prompt Engineering

## Overview

How you package and transmit context to sub-agents is critical. Write prompts like system prompts — they shape the sub-agent's entire behavior. Too much context wastes tokens and confuses. Too little leaves sub-agents without needed information.

System prompts are orders of magnitude more important than casual instructions. Your sub-agent prompts ARE their system prompts. Write them with care.

---

## Prompt Engineering Principles

### 1. Assign a clear role
Tell the sub-agent what kind of agent it is. This frames their entire approach:
- "You are a developer making a focused, isolated change"
- "You are a debugging specialist investigating a bug"
- "You are a developer executing one stage of a multi-stage project"

### 2. Use delimiters to separate sections
XML-style tags help distinguish context, task, constraints, and expected output:

```
<GrandGoal>...</GrandGoal>
<PreviousStages>...</PreviousStages>
<YourMission>...</YourMission>
<Constraints>...</Constraints>
<Report>...</Report>
```

### 3. Specify clear steps
Break the task into sequential steps. Explicit steps produce better output than vague instructions.

### 4. Define the output format
Tell the agent exactly what to report back. This is critical for the orchestrator to make decisions about next stages.

### 5. Set boundaries
What the agent should do AND what it should not do. When should it stop and ask?

### 6. Suggest relevant skills
Before the task, tell the sub-agent to check for skills that would help them succeed.

---

## SIMPLE Tasks: Minimal Focused Context

### Principle
Give only what's needed. No grand goal. No history. No previous stages.

### What to include:
- Role assignment
- Exact task description
- File/path if specified
- Constraints
- Output format
- Optional: relevant skill suggestions

### Template

```
Read the README.md file first.

Role: [one sentence describing the agent's identity for this task]
Before starting, check if [skill names] would help you.

<Task>
[Exact instruction]
</Task>

<Constraints>
- Do exactly this and nothing else
- Do not refactor, rename, or reorganize unrelated code
</Constraints>

<Report>
1. File(s) changed
2. Exact changes made
3. Confirmation it works
</Report>
```

### What NOT to include in simple tasks:
- Grand Goal
- History of previous work
- "This is part of a larger..."
- Full conversation context

---

## COMPLEX Tasks: Structured Context

### Principle
Break into sequential stages. Each stage inherits context from previous stages.

### What to include:
- Role assignment
- Grand Goal (one sentence)
- Summary of all previous stages
- Current mission with clear steps
- Constraints
- Detailed output format
- Optional: relevant skill suggestions

### Template

```
Read the README.md file first.

Role: [one sentence describing the agent's identity]
Before starting, check if [skill names] would help you.

<GrandGoal>
[One sentence — the final outcome]
</GrandGoal>

<PreviousStages>
- Stage 1: [what was done, key files, decisions]
- Stage 2: [what was done, key files, decisions]
</PreviousStages>

<YourMission>
Stage [X/N]: [Exactly what this agent must do]
</YourMission>

<Steps>
1. [Step one]
2. [Step two]
3. [Step three]
</Steps>

<Constraints>
- Only work on YourMission
- Do not modify files from previous stages unless told to
- Follow existing code conventions
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

---

## Investigation Tasks: Discovery-Focused Context

For bugs or issues where the solution isn't known upfront:

```
Read the README.md file first.

Role: [debugging specialist / code investigator]
Before starting, check if systematic-debugging would help you.

<Problem>
[Description of the issue]
</Problem>

<Instructions>
1. Explore relevant code to understand the implementation
2. Identify the root cause
3. Implement the fix
4. Verify the fix works
</Instructions>

<Report>
1. Root cause found
2. Files changed (with paths and line references)
3. Verification method
4. Side effects or related issues to watch
</Report>
```

---

## Skill Discovery in Sub-Agent Prompts

Always consider whether skills would help your sub-agent. Include skill suggestions:

### When to suggest skills to sub-agents:
- **brainstorming** — if the sub-agent is designing a new feature or component
- **systematic-debugging** — if the sub-agent is investigating a bug
- **vercel-react-best-practices** — if working with React/Next.js code
- **frontend-design** — if building UI components or pages
- **fullstack-dev** — if building features with backend + frontend
- **subagent-driven-development** — if the sub-agent's task is large enough to need delegation

### How to include skill suggestions:

```
Before starting, check if any of these skills apply to your task:
- brainstorming — if you need to design or plan before coding
- systematic-debugging — if you need to investigate bugs
- vercel-react-best-practices — if working with React code

Load any that apply before you begin.
```

---

## Context Size Guidelines

- **SIMPLE tasks:** 5-15 lines
- **COMPLEX task stages:** 15-35 lines

If your prompt is longer, the scope is too wide — split into another stage.

---

## Anti-Patterns

**The Dump:** Dumping everything you know into the sub-agent's context. Wastes tokens, confuses focus.

**The Mystery:** Giving almost no context. Sub-agent doesn't have enough to succeed.

**The Leak:** Including context from unrelated tasks. Creates confusion about scope.

**The Missing Output Format:** Not specifying what the sub-agent should report. You get back unstructured information you can't use effectively.

**The Skill Blindness:** Not suggesting skills. The sub-agent works without specialized knowledge that's available.
