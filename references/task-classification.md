# Task Classification & Conflict Detection

## Classification Framework

Split every user request into atomic tasks. Classify each one.

### SIMPLE
One-shot, single-file changes with no dependencies:
- Color values, typo fixes, label updates
- Single CSS rule, single prop change
- One file, one change, isolated

### COMPLEX
Multi-step work requiring coordination:
- New features, refactoring, architecture changes
- Multiple files, testing loops
- Work that must be done in stages

### DEPENDENT
A task that needs output from another task:
- Adding a navbar link after creating the page
- Integrating a new API after it's been built
- Queue these after the prerequisite completes

---

## Dependency Detection

### Workflow:
1. List all tasks from the user's request
2. For each task: what does it need to exist before it can be done?
3. Tasks with prerequisites → queue after prerequisites
4. Tasks with no prerequisites → candidates for parallel execution

### Example:
**Request:** "Create a user profile page, add a link in the navbar, and change the footer color"

- Task A: Create profile page → COMPLEX, no prerequisites
- Task B: Add navbar link → DEPENDENT on A (needs page route)
- Task C: Change footer color → SIMPLE, independent

Execution: Start A Stage 1. Run C in parallel. Queue B after A creates the route.

---

## Conflict Detection for Parallel Tasks

Running two agents that edit the same file = merge conflict. Prevent this.

### Detection rules:
1. **If the user mentions the same component/file** → sequential, not parallel
2. **If both tasks touch the same domain** (both are "navbar changes", both are "auth") → check file structure before deciding
3. **If unsure** → read `/orchestrator-agent-docs/file-structure.md` to understand where the relevant files are
4. **If still unsure** → run sequentially. Slow is better than broken.

### Example: Safe parallel
**Request:** "Change the button color in the hero and fix the typo in the footer"

- Hero button is in `components/Hero.tsx`
- Footer typo is in `components/Footer.tsx`
- Different files → SAFE for parallel

### Example: Unsafe parallel
**Request:** "Change the button label and fix the button color"

- Both in the same button component
- Same file → SEQUENTIAL

### Example: Uncertain
**Request:** "Update the pricing in the checkout and change the button style in the cart"

- Are checkout and cart the same file? Different files?
- Read `/orchestrator-agent-docs/file-structure.md` to check
- If they're in different files → parallel. Same file → sequential.

---

## Stage Planning for Complex Tasks

### Ordering principles:

1. **Foundation first** — database schema before API, API before UI, core logic before styling
2. **Separate conflicting work** — stages that modify the same files must be sequential
3. **Maximize parallel** — stages that touch completely separate systems can run together
4. **Verification gates** — after database changes, API creation, or major refactors, insert a verification stage

### Stage count guideline:
- 2-3 stages: minor features
- 4-6 stages: medium features
- 7+: large features (consider if the request should be split into separate tasks)

### Context budget per stage:
- Each stage's prompt should be 15-35 lines
- If a stage's prompt exceeds 35 lines, split it into two stages
- PreviousStages section should summarize, not dump

---

## Execution Ordering

### Independent SIMPLE tasks → parallel immediately

### COMPLEX tasks → sequential stages
Wait for each stage to complete before spawning the next.

### DEPENDENT tasks → queue after prerequisite
Release as soon as their prerequisite stage completes.

### Mixed workload example:
**Request:** "Fix footer typo, add dark mode toggle, update copyright year"

- Task A (fix typo): SIMPLE, independent → **parallel**
- Task B (dark mode): COMPLEX, 3 stages → **staged sequentially**
- Task C (copyright): SIMPLE, independent → **parallel**

Execute: A + C spawn immediately. B Stage 1 starts. All three run in parallel. After B Stage 1 completes, B Stage 2 starts, etc.

---

## Checklist

Before spawning:
- [ ] Request split into atomic tasks
- [ ] Each task classified (SIMPLE / COMPLEX / DEPENDENT)
- [ ] Dependencies mapped
- [ ] Parallel candidates checked for file conflicts
- [ ] Complex task stages outlined
- [ ] Execution order decided
