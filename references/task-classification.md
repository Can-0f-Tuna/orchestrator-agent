# Task Classification & Dependencies

## Classification Framework

For every user request (even if it contains multiple tasks), you MUST:

### Step 1: Split the Request
Break the user request into individual, atomic tasks.

### Step 2: Classify Each Task

**SIMPLE Task** — Small, isolated change that can be done in one shot:
- Change a color value
- Add 3 circles to hero section
- Fix a typo
- Update one prop
- Add a single className
- Change a button label
- Modify a single CSS rule

**COMPLEX Task** — Anything requiring multiple steps, planning, or coordination:
- New feature implementation
- Code refactoring
- Testing loops (write → test → fix)
- Multiple file modifications
- Dead-code cleanup across files
- Architecture changes
- Integration work
- Performance optimization

### Step 3: Detect Dependencies

Analyze relationships between tasks:

**Dependent Task** — If a simple task is related to / depends on a complex task:
- Queue it
- Run it ONLY after the relevant stage of the complex task is finished

**Independent Task** — If tasks are completely unrelated:
- Run them in parallel
- No queuing needed

## Execution Ordering Rules

### Independent SIMPLE Tasks
Run all independent SIMPLE tasks **immediately in parallel**.

**Why:** These are isolated changes with no dependencies. Maximum parallelization.

### COMPLEX Tasks
Start COMPLEX tasks in **stages** (one stage at a time).

**Why:** Complex tasks require coordination. Stages may depend on previous stage outputs.

### Dependent SIMPLE Tasks
Run dependent SIMPLE tasks **only after** their related complex stage is complete.

**Why:** They may need outputs, context, or completed state from the complex task.

## Dependency Detection Examples

### Example 1: Independent Tasks
**User request:** "Change the button color to blue and fix the typo in the header"

Classification:
- Task A (change button color): SIMPLE, independent
- Task B (fix typo): SIMPLE, independent

Execution: Run both in parallel immediately.

### Example 2: Dependent Tasks
**User request:** "Add a new user profile page and then add a link to it in the navbar"

Classification:
- Task A (add profile page): COMPLEX (new feature, multiple files)
- Task B (add navbar link): SIMPLE, but DEPENDENT (needs page to exist first)

Execution:
1. Start Task A (COMPLEX) in stages
2. After Task A stage completes (page exists), queue Task B
3. Run Task B (SIMPLE) with context about the new page

### Example 3: Mixed Tasks
**User request:** "Fix the typo in the footer, refactor the auth system, and update the copyright year"

Classification:
- Task A (fix footer typo): SIMPLE, independent
- Task B (refactor auth): COMPLEX, multi-stage
- Task C (update copyright): SIMPLE, independent

Execution:
1. Run Task A and Task C in parallel immediately
2. Start Task B Stage 1
3. Continue Task B stages sequentially

## Task Classification Checklist

Before executing, verify:
- [ ] Request split into atomic tasks
- [ ] Each task classified (SIMPLE/COMPLEX)
- [ ] Dependencies mapped
- [ ] Execution order planned
- [ ] Independent simple tasks identified for parallel execution
- [ ] Complex task stages outlined (if applicable)
- [ ] Dependent tasks queued properly

## Common Classification Mistakes

**Mistake:** Treating multi-file changes as SIMPLE
**Correction:** If it touches multiple files with coordination needs, it's COMPLEX

**Mistake:** Running dependent tasks immediately
**Correction:** Queue them. They need the complex task output first.

**Mistake:** Not splitting combined requests
**Correction:** Always break into individual tasks first

**Mistake:** Classifying based on time estimate alone
**Correction:** Complexity is about coordination needs, not just duration
