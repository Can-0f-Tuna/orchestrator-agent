# Examples

## Example 1: First Session — Docs Don't Exist

**User Request:** "Change the button color to blue"

### What the orchestrator does:

1. Checks for `/orchestrator-agent-docs/` — not found
2. **Stops the user's request.** Spawns a project analyst to create docs.
3. After docs are created, reads them to understand the project.
4. Now proceeds with the original request.

### Orchestrator spawns the doc creation agent:
```
Read the project README.md and any key config files first.

Role: You are a project analyst. Your job is to understand this project completely and document it for AI agents.

<Task>
Create the directory /orchestrator-agent-docs/ with every required file:
- README.md (project overview, 100-150 lines)
- architecture.md
- file-structure.md
- conventions.md
- commands.md
- dependencies.md
- state.md
- modules/ (one per major feature)

Each file must contain real, specific content — no placeholders.
</Task>

[Full instructions from project-docs-protocol.md]
```

### After docs are created, orchestrator reads them, then spawns:
```
Read /orchestrator-agent-docs/README.md first.
Before starting, check if any skills apply.

Role: Developer making a focused CSS change.

<Task>
Change the hero button color to blue.
</Task>

<Constraints>
- Do exactly this and nothing else
</Constraints>

<Report>
1. File changed
2. Exact change
3. Confirmation
</Report>
```

### After task completes, orchestrator updates docs:
```
Read /orchestrator-agent-docs/README.md first.

Role: Documentation maintainer.

<Task>
Update state.md: "Changed hero button color to blue. File: components/Hero.tsx"
</Task>
```

---

## Example 2: Three Simple Independent Tasks

**User Request:** "Change the hero button color to blue, fix the typo in the footer, and add a border to the navbar"

### Orchestrator analysis:
1. Task A: Hero button color → SIMPLE, in `components/Hero.tsx`
2. Task B: Footer typo → SIMPLE, in `components/Footer.tsx`
3. Task C: Navbar border → SIMPLE, in `components/Navbar.tsx`
4. Conflict check: three different files → safe for parallel

### Execution — all three spawned in parallel:

**Agent 1:**
```
Read /orchestrator-agent-docs/README.md first.
Before starting, check if any skills apply.
Role: Developer making a focused CSS change.
<Task>Change the hero button color to blue.</Task>
<Constraints>Do exactly this and nothing else.</Constraints>
<Report>1. File changed 2. Exact change 3. Confirmation</Report>
```

**Agent 2:**
```
Read /orchestrator-agent-docs/README.md first.
Before starting, check if any skills apply.
Role: Developer fixing a typo.
<Task>Fix the typo in the footer.</Task>
<Constraints>Do exactly this and nothing else.</Constraints>
<Report>1. File changed 2. Exact change 3. Confirmation</Report>
```

**Agent 3:**
```
Read /orchestrator-agent-docs/README.md first.
Before starting, check if any skills apply.
Role: Developer adding a CSS border.
<Task>Add a border to the navbar.</Task>
<Constraints>Do exactly this and nothing else.</Constraints>
<Report>1. File changed 2. Exact change 3. Confirmation</Report>
```

### After all complete:
Spawn doc update agent. Report to user.

---

## Example 3: Complex Task with Dependent Simple Task

**User Request:** "Create a new About page and add a link to it in the navbar"

### Orchestrator analysis:
1. Reads `/orchestrator-agent-docs/` — understands it's a Next.js app with file-based routing
2. Task A: Create About page → COMPLEX (new page, new route)
3. Task B: Add navbar link → DEPENDENT on A (needs route to exist)

### Execution:

**Stage 1/1: Create About page**
```
Read /orchestrator-agent-docs/README.md first, then read modules/frontend.md.
Before starting, check if frontend-design applies.

Role: Developer creating a new page.

<GrandGoal>
Add an About page at /about and link it in the navbar.
</GrandGoal>

<PreviousStages>
None — this is stage 1.
</PreviousStages>

<YourMission>
Stage 1/1: Create the About page at /about route.
</YourMission>

<Steps>
1. Create the page component at app/about/page.tsx following existing page patterns
2. Add basic content: title, description, and a placeholder image section
3. Verify the page renders at /about
</Steps>

<Constraints>
- Use existing page component patterns from /orchestrator-agent-docs/conventions.md
- Only create the page — do not add the navbar link
</Constraints>

<Report>
1. Files created
2. Route path used
3. Key decisions about component structure
4. Verification performed
5. Note for navbar link stage: route is at /about, component is at app/about/page.tsx
</Report>
```

**After Stage 1 completes, spawn dependent task:**
```
Read /orchestrator-agent-docs/README.md first, then read the navbar component's module docs.
Before starting, check if any skills apply.

Role: Developer adding a navigation link.

<Task>
Add a link to the About page at /about in the navbar component.
</Task>

<Context>
Stage 1 created the About page. Route: /about. Component: app/about/page.tsx.
</Context>

<Constraints>
- Add a single link — do not refactor the navbar
- Place it in a logical position among existing links
</Constraints>

<Report>
1. File changed
2. Where the link was added
3. Confirmation clicking navigates to /about
</Report>
```

### After both complete:
Spawn doc update agent. Report to user.

---

## Example 4: Large Multi-Stage Project

**User Request:** "We are forking this ABC repo from GitHub. Our goal is to turn it into DEFG. Go through the entire codebase, remove what we don't need, and make sure we don't break the project."

### Orchestrator analysis:
This is massive. One agent cannot do this. Must be staged.

### Stage plan:
1. **Stage 1: Exploration** — Map the entire codebase, understand dependencies
2. **Stage 2: Identification** — List all removable code with justifications
3. **Stage 3: Safe removal** — Remove non-breaking code, verify build
4. **Stage 4: Feature removal** — Remove identified features one at a time
5. **Stage 5: Cleanup** — Dead imports, unused deps, final verification
6. **Stage 6: Verification** — Full build + test, confirm nothing is broken

### Execution (abbreviated):

**Stage 1 spawn:**
```
Read /orchestrator-agent-docs/README.md first, then read architecture.md and file-structure.md.

Role: Codebase analyst.

<GrandGoal>
Transform ABC repo into DEFG by removing all unnecessary code without breaking the project.
</GrandGoal>

<YourMission>
Stage 1/6: Map the entire codebase. Understand what everything is, how it connects, and what depends on what.
</YourMission>

<Steps>
1. Document the complete directory structure with annotations
2. Identify all features, modules, and their dependencies
3. Distinguish core vs. peripheral code
4. List all external dependencies and where they're used
</Steps>

<Report>
1. Complete annotated directory tree
2. Feature/module dependency map
3. Core vs. peripheral classification
4. External dependency usage map
5. Initial recommendations for removal candidates
</Report>
```

**After Stage 1, orchestrator reads the report, updates context for Stage 2:**

```
<PreviousStages>
- Stage 1 completed: Full codebase mapped. Core identified as [X]. 
  Peripheral features: [A, B, C, D]. Dependency map saved.
</PreviousStages>

<YourMission>
Stage 2/6: Using the Stage 1 map, identify all removable code. 
Classify as: SAFE (no dependents), RISKY (has dependents), KEEP (core).
</YourMission>
```

### Key: After each stage, the orchestrator reads the report and builds an informed context chain for the next stage. Each stage's prompt includes:
- Grand Goal (unchanged)
- Summary of all previous stages (what was done, key findings, files created)
- Current mission with specific instructions based on what was learned

### After all stages:
Spawn verification agent. Update all docs with the new project state.

---

## Example 5: Failure and Recovery

**User Request:** "Add dark mode toggle to the settings page"

### What happens:
1. Orchestrator spawns Stage 1 agent to add the toggle
2. Agent reports: "I changed Settings.tsx and added a ThemeContext.tsx, but the toggle doesn't work. I think the context provider isn't set up."

### Orchestrator response:
- **1st retry:** Spawn fix agent with the specific problem and code locations
- Agent reports: "Fixed the context provider in _app.tsx. Toggle now works."
- **Verification:** Spawn verification agent to confirm toggle works and nothing else broke
- Verification confirms → update docs, report to user

### If 1st fix fails:
- **2nd retry:** Different approach — spawn agent to implement toggle as local state instead of context
- If that fails too → **escalate to user** with both approaches documented
