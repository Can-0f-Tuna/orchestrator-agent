# Examples

## Example 1: Three Simple Independent Tasks

**User Request:** "Change the hero button color to blue, fix the typo in the footer, and add a border to the navbar"

### Classification
- Task A: Change hero button color → SIMPLE, independent
- Task B: Fix footer typo → SIMPLE, independent
- Task C: Add navbar border → SIMPLE, independent

### Execution — All three spawned in parallel

**Agent 1:**
```
Read /orchestrator-agent-docs/README.md first.

Role: You are a developer making a focused CSS change.
Before starting, check if vercel-react-best-practices or frontend-design apply.

<Task>
Change the hero button color to blue in the hero component.
</Task>

<Constraints>
- Do exactly this and nothing else
</Constraints>

<Report>
1. File changed
2. Exact change made
3. Confirmation it works
</Report>
```

**Agent 2:**
```
Read /orchestrator-agent-docs/README.md first.

Role: You are a developer making a focused text fix.

<Task>
Fix the typo in the footer component.
</Task>

<Constraints>
- Do exactly this and nothing else
</Constraints>

<Report>
1. File changed
2. Exact change made
3. Confirmation it works
</Report>
```

**Agent 3:**
```
Read /orchestrator-agent-docs/README.md first.

Role: You are a developer making a focused CSS change.

<Task>
Add a border to the navbar component.
</Task>

<Constraints>
- Do exactly this and nothing else
</Constraints>

<Report>
1. File changed
2. Exact change made
3. Confirmation it works
</Report>
```

**All three run in parallel. Orchestrator waits for all to complete, reports success.**

---

## Example 2: Complex Task with Dependent Simple Task

**User Request:** "Create a new About page and add a link to it in the navbar"

### Classification
- Task A: Create About page → COMPLEX (new feature)
- Task B: Add navbar link → SIMPLE, DEPENDENT (needs page route)

### Execution

**Stage 1/2: Create the About page**
```
Read /orchestrator-agent-docs/README.md first.

Role: You are a developer creating a new page.
Before starting, check if frontend-design applies.

<GrandGoal>
Add an About page at /about and link it in the navbar.
</GrandGoal>

<PreviousStages>
None — this is stage 1.
</PreviousStages>

<YourMission>
Stage 1/2: Create the About page component at the /about route.
</YourMission>

<Steps>
1. Create the page component with proper layout and content
2. Add the route in the routing configuration
3. Verify the page renders at /about
</Steps>

<Constraints>
- Only create the page and route
- Follow existing project patterns for pages
</Constraints>

<Report>
1. Files created/modified
2. Route path used
3. Key decisions
4. Verification performed
5. Suggestions for navbar link stage
</Report>
```

**After Stage 1 completes, run dependent task:**
```
Read /orchestrator-agent-docs/README.md first.

Role: You are a developer adding a single navigation link.

<Task>
Add a link to the About page in the navbar.
</Task>

<Context>
The About page is available at /about route.
</Context>

<Constraints>
- Do exactly this and nothing else
- Place the link in a logical position in the navbar
</Constraints>

<Report>
1. File changed
2. Where the link was added
3. Confirmation it navigates correctly
</Report>
```

---

## Example 3: Large Multi-Stage Project

**User Request:** "We are forking this ABC repo from GitHub and our goal is to turn it into DEFG. Go through the entire codebase, remove what we don't need, and make sure we don't break the project."

### Classification
- Task: Codebase cleanup → COMPLEX, requires multiple careful stages

### Stage Planning
This is a large task. If given to one agent, it would overflow context and produce unreliable results. Break it into stages:

1. **Stage 1: Exploration and mapping** — Understand the codebase structure, map dependencies
2. **Stage 2: Identify removable code** — List files, modules, and features to remove
3. **Stage 3: Remove non-breaking code** — Delete clearly unused code, verify project builds
4. **Stage 4: Remove features** — Delete identified features one at a time, verify after each
5. **Stage 5: Final cleanup** — Remove dead imports, unused dependencies, verify everything works

### Execution

**Stage 1/5: Exploration**
```
Read /orchestrator-agent-docs/README.md first.

Role: You are a codebase analyst mapping project structure.

<GrandGoal>
Transform the ABC repo into DEFG by removing all unnecessary code without breaking the project.
</GrandGoal>

<PreviousStages>
None — this is stage 1.
</PreviousStages>

<YourMission>
Stage 1/5: Explore the entire codebase and document its structure.
</YourMission>

<Steps>
1. Map the directory structure and key entry points
2. Identify which packages/modules are used
3. Document how features connect to each other
4. List all external dependencies
5. Identify the core of the project (what must stay)
</Steps>

<Constraints>
- Do not delete or modify any files
- Focus only on understanding and documenting
</Constraints>

<Report>
1. Complete directory tree
2. Feature/module map with dependencies between them
3. List of what appears to be the core vs. peripheral code
4. External dependency list with usage locations
5. Suggestions for what can safely be removed
6. Any areas that need deeper investigation
</Report>
```

**Stage 2/5: Identify Removable Code**
```
Read /orchestrator-agent-docs/README.md first.

Role: You are a codebase analyst specializing in dead code detection.

<GrandGoal>
Transform the ABC repo into DEFG by removing all unnecessary code without breaking the project.
</GrandGoal>

<PreviousStages>
- Stage 1 completed: Full codebase mapped. Core identified as [core modules]. Peripheral features: [list]. Dependencies documented at [locations].
</PreviousStages>

<YourMission>
Stage 2/5: Identify all code that can be safely removed.
</YourMission>

<Steps>
1. Review the exploration report from Stage 1
2. For each peripheral feature, trace all imports and usages
3. Classify each item: REMOVE (no dependents), DEPENDS (other code needs it), UNCERTAIN
4. Create a prioritized removal plan — items with no dependents first
</Steps>

<Constraints>
- Do not delete or modify anything
- Be conservative — if unsure, mark it UNCERTAIN
</Constraints>

<Report>
1. Complete removal plan ordered by safety
2. For each item: path, what it does, why it can be removed, dependent count
3. UNCERTAIN items with explanation
4. Recommended removal order
</Report>
```

**Stage 3/5: Remove Non-Breaking Code**
```
Read /orchestrator-agent-docs/README.md first.

Role: You are a developer executing a carefully planned code removal.

<GrandGoal>
Transform the ABC repo into DEFG by removing all unnecessary code without breaking the project.
</GrandGoal>

<PreviousStages>
- Stage 1 completed: Full codebase exploration and mapping
- Stage 2 completed: Removal plan created. Tier 1 (no dependents): [list]. Tier 2 (one dependent): [list]. UNCERTAIN: [list].
</PreviousStages>

<YourMission>
Stage 3/5: Remove Tier 1 items (no dependents) from the removal plan, and verify the project still builds.
</YourMission>

<Steps>
1. Remove each Tier 1 item according to the plan
2. After each removal, run the build to verify nothing broke
3. If a removal breaks the build, restore it and document why
4. Update the removal plan with results
</Steps>

<Constraints>
- Remove only Tier 1 items
- Stop and report immediately if the build breaks
- Do not touch Tier 2 or UNCERTAIN items
</Constraints>

<Report>
1. Files deleted (full paths)
2. Build verification results after each removal
3. Any items that couldn't be removed and why
4. Updated removal plan reflecting what was removed
5. Any new insights about remaining items
</Report>
```

**The orchestrator continues with Stage 4 (remove features), then Stage 5 (final cleanup), each building on the previous stage's report.**

---

## Example 4: Verification Stage Pattern

After a significant stage completes, spawn a verification agent:

```
Read /orchestrator-agent-docs/README.md first.

Role: You are a quality assurance engineer verifying recent changes.

<GrandGoal>
[Same as other stages]
</GrandGoal>

<PreviousStages>
- Stage 1: [completed work]
- Stage 2: [completed work]
- Stage 3: [completed work — THIS is what you're verifying]
</PreviousStages>

<YourMission>
Verify Stage 3: Check that the changes are correct, complete, and don't introduce issues.
</YourMission>

<Steps>
1. Build the project — confirm it succeeds
2. Run existing tests — confirm they pass
3. Review the changes for:
   - Consistency with project conventions
   - Missing edge cases
   - Unused imports or dead code introduced
   - Integration with previous stages' work
4. Test the affected functionality manually if applicable
</Steps>

<Report>
1. Build status
2. Test results
3. Issues found (if any)
4. Overall assessment: READY / NEEDS FIXES
</Report>
```

---

## Common Patterns Summary

### Pattern: Simple Chain
Multiple independent simple tasks → All in parallel

### Pattern: Complex Cascade
Multi-stage complex task → Sequential, wait between stages, context chain

### Pattern: Dependent Trigger
Complex task + dependent simple task → Queue simple, run after prerequisite stage

### Pattern: Mixed Load
Simple + Complex together → Simple in parallel, Complex staged

### Pattern: Startup Model
Large project → Stages ordered by dependency (exploration → planning → execution → verification → cleanup), each stage inherits from previous, verification gates between significant stages

---

## Troubleshooting

**Sub-agent asking for clarification** → Context was too minimal. Add necessary details while staying focused.

**Sub-agent doing wrong thing** → Task description wasn't specific enough. Add steps, constraints, and expected output format.

**Stage depends on output from previous stage** → Ensure you're waiting for completion and including the structured report in next stage's PreviousStages.

**Tasks conflicting with each other** → Check dependency detection. They may not be as independent as you thought.

**Build breaks after a stage** → Spawn a fix agent immediately. Don't continue other stages. Add a verification stage before continuing.

**Context too large for one stage** → Split into another stage. Each stage's prompt should be 15-35 lines.
