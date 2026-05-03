# Task Classification & Dependencies

## Classification Framework

For every user request, split it into atomic tasks, classify, and detect dependencies.

### Step 1: Split the Request
Break the user's request into individual, atomic tasks.

### Step 2: Classify Each Task

**SIMPLE** — One-shot, isolated changes:
- Change a color value
- Fix a typo
- Update a prop or label
- Add a single className
- Modify a single CSS rule
- Any single-file, single-change task

**COMPLEX** — Multi-step, coordinated work:
- New feature implementation
- Code refactoring
- Multiple file modifications
- Dead-code cleanup across files
- Architecture changes
- Integration work
- Performance optimization
- Testing loops (write → test → fix)

### Step 3: Detect Dependencies

**Dependent tasks** need output from another task first:
- Adding a navbar link needs the page to exist first
- Integration testing needs the feature to be built
- Queue these after the prerequisite completes

**Independent tasks** have no relationship:
- Changing a button color and fixing a footer typo
- Run these in parallel

---

## Execution Ordering

### Independent SIMPLE tasks
Run immediately in parallel. These are isolated changes.

### COMPLEX tasks
Run in sequential stages, waiting between each.

### Dependent SIMPLE tasks
Queue after the relevant complex stage completes.

---

## Staging Complex Tasks

When a task is COMPLEX, break it into stages following these principles:

### 1. Avoid conflict — separate interdependent tasks
If two stages modify the same file or system, they should run sequentially, not in parallel. Changes that interact should be in the same stage or clearly ordered.

### 2. Maximize parallel — group truly independent work
If Stage A touches frontend and Stage B touches backend with no interaction, they can run in parallel.

### 3. Foundation first — order by dependency
Database schema → API endpoints → Frontend forms → Styling → Testing. Each layer depends on the previous.

### 4. Context chain — each stage inherits from previous
Every sub-agent receives: Grand Goal + Summary of previous stages + Their specific mission.

### 5. Verification gates — catch issues early
After critical stages, have the next stage begin by verifying the previous stage's work before building on it.

### 6. Structured reporting — ask for specific information
Tell sub-agents exactly what to report: files created, decisions made, assumptions, risks, suggestions. This feeds the context chain.

---

## Dependency Detection Examples

### Example 1: Independent Tasks
**Request:** "Change the button color to blue and fix the typo in the header"

- Task A (button color): SIMPLE, independent
- Task B (fix typo): SIMPLE, independent

Run both in parallel.

### Example 2: Dependent Tasks
**Request:** "Add a new user profile page and add a link to it in the navbar"

- Task A (profile page): COMPLEX (new feature, multiple files)
- Task B (navbar link): SIMPLE, dependent (needs page route to exist)

Run Task A Stage 1 first. After page exists, run Task B.

### Example 3: Mixed Tasks
**Request:** "Fix the typo in the footer, refactor the auth system, and update the copyright year"

- Task A (fix typo): SIMPLE, independent
- Task B (refactor auth): COMPLEX, multi-stage
- Task C (update copyright): SIMPLE, independent

Run A and C in parallel. Start B Stage 1 simultaneously.

---

## Task Classification Checklist

Before executing:
- [ ] Request split into atomic tasks
- [ ] Each task classified (SIMPLE/COMPLEX)
- [ ] Dependencies mapped
- [ ] Execution order planned
- [ ] Independent simple tasks identified for parallel execution
- [ ] Complex task stages outlined
- [ ] Dependent tasks queued properly

## Common Mistakes

- Treating multi-file changes as SIMPLE → If it touches multiple files with coordination needs, it's COMPLEX
- Running dependent tasks immediately → Queue them. They need the prerequisite output.
- Not splitting combined requests → Always break into individual tasks first
- Classifying by time estimate alone → Complexity is about coordination needs, not duration
- Staging without verification → Always verify critical stages before building on them
