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

## Example 4: Explicit Dependency Plan with Parallel Execution

**User Request:** "Add user authentication to my Express app using a plan with dependencies"

### Plan File: `auth-plan.md`

```markdown
# Plan: Add Authentication to Express App

## Overview
Implement JWT-based authentication with login, signup, and protected routes.

## Dependency Graph
```
T1 ──┬── T3 ──┐
     │        ├── T5 ── T6
T2 ──┴── T4 ──┘
```

## Tasks

### T1: Create User Model
- **depends_on**: []
- **location**: src/models/user.ts
- **description**: Define User entity with email, password_hash, created_at fields
- **validation**: Model compiles, TypeScript types correct
- **status**: Not Started
- **log**: 
- **files edited/created**: 

### T2: Install Dependencies
- **depends_on**: []
- **location**: package.json
- **description**: Install bcrypt, jsonwebtoken, express-validator
- **validation**: npm install succeeds, packages in node_modules
- **status**: Not Started
- **log**: 
- **files edited/created**: 

### T3: Create Auth Service
- **depends_on**: [T1]
- **location**: src/services/auth.ts
- **description**: Implement register(), login(), verifyToken() functions
- **validation**: Unit tests pass for all three functions
- **status**: Not Started
- **log**: 
- **files edited/created**: 

### T4: Create Auth Middleware
- **depends_on**: [T1]
- **location**: src/middleware/auth.ts
- **description**: Implement requireAuth middleware for protected routes
- **validation**: Middleware correctly validates JWT and sets req.user
- **status**: Not Started
- **log**: 
- **files edited/created**: 

### T5: Add Auth Routes
- **depends_on**: [T3, T4]
- **location**: src/routes/auth.ts
- **description**: Create POST /register, POST /login endpoints
- **validation**: API tests pass, endpoints return correct status codes
- **status**: Not Started
- **log**: 
- **files edited/created**: 

### T6: Protect Routes
- **depends_on**: [T5, T2]
- **location**: src/routes/*.ts
- **description**: Apply requireAuth middleware to sensitive routes
- **validation**: Protected routes return 401 without token, 200 with valid token
- **status**: Not Started
- **log**: 
- **files edited/created**: 

## Parallel Execution Groups
| Wave | Tasks | Can Start When |
|------|-------|----------------|
| 1 | T1, T2 | Immediately |
| 2 | T3, T4 | Wave 1 complete |
| 3 | T5 | Wave 2 complete |
| 4 | T6 | Wave 3 complete |

## Risks & Mitigations
- Risk: JWT secret management — Store in .env, add to .env.example
- Risk: Password hashing performance — Use bcrypt with appropriate rounds
```

### Execution

**Wave 1: Launch T1 and T2 in parallel**

**Agent for T1:**
```
Read /orchestrator-agent-docs/README.md first.

Role: You are a developer implementing the User model.

## Context
- Plan: auth-plan.md
- Your Task: T1: Create User Model
- Dependencies: None (root task)
- Related tasks: T3 and T4 depend on this

## Your Mission
Create the User model at src/models/user.ts with fields: email, password_hash, created_at.

## Instructions
1. Read the plan and understand the full context
2. Read existing models to follow patterns
3. **RED Phase:** Write tests for User model first, confirm they fail
4. **GREEN Phase:** Implement the model, run tests until they pass
5. Commit: git add src/models/user.ts src/models/__tests__/user.test.ts && git commit -m "feat: add User model"
6. Update auth-plan.md: set status: Completed, add log and files

## Validation
- Tests show RED (failing) → GREEN (passing)
- Model has correct TypeScript types

## Report
1. Files created/modified
2. Changes made
3. Test evidence: RED → GREEN
4. Any issues encountered
```

**Agent for T2:**
```
Read /orchestrator-agent-docs/README.md first.

Role: You are a developer installing dependencies.

## Context
- Plan: auth-plan.md
- Your Task: T2: Install Dependencies
- Dependencies: None (root task)

## Your Mission
Install bcrypt, jsonwebtoken, express-validator.

## Instructions
1. Install packages: npm install bcrypt jsonwebtoken express-validator
2. Verify packages are in node_modules
3. Commit: git add package.json package-lock.json && git commit -m "deps: add auth dependencies"
4. Update auth-plan.md: set status: Completed, add log and files

## Validation
- npm install succeeds
- All packages in node_modules

## Report
1. Packages installed
2. Any version conflicts or issues
```

**Orchestrator waits for both to complete, verifies, then launches Wave 2.**

**Wave 2: Launch T3 and T4 in parallel**
- T3 depends on T1 (completed) ✓
- T4 depends on T1 (completed) ✓

[Similar prompt structure for each task]

**Continue until all waves complete.**

---

## Example 5: TDD RED-GREEN Validation Pattern

**Subagent Task with Required TDD:**

```
Read /orchestrator-agent-docs/README.md first.

Role: You are a developer implementing a feature using TDD.

## Context
- Plan: auth-plan.md
- Your Task: T3: Create Auth Service
- Depends on: T1 (User model — completed)

## Your Mission
Implement register(), login(), verifyToken() in src/services/auth.ts

## Instructions

### Step 1: RED Phase (Test First)
1. Read the acceptance criteria in the plan
2. Write comprehensive tests at src/services/__tests__/auth.test.ts
3. Run tests: npm test src/services/__tests__/auth.test.ts
4. **Confirm tests FAIL (RED)** — capture the output
5. If tests pass immediately, your tests aren't testing the right thing — revise them

### Step 2: GREEN Phase (Implementation)
1. Implement register(), login(), verifyToken() to satisfy acceptance criteria
2. Run tests until they PASS (GREEN)
3. Refactor if needed while keeping tests green

### Step 3: Validation
- Required evidence: Screenshot or terminal output showing:
  - Initial test run: X tests, Y failures (RED)
  - Final test run: X tests, 0 failures (GREEN)

### Step 4: Commit and Update Plan
1. Stage only your files: git add src/services/auth.ts src/services/__tests__/auth.test.ts
2. Commit: git commit -m "feat: add auth service with tests"
3. Update auth-plan.md:
   - status: Completed
   - log: "Implemented auth service with register/login/verifyToken. Tests: 3/3 passing."
   - files: src/services/auth.ts, src/services/__tests__/auth.test.ts
   - verification: "Test output shows RED (3 tests, 3 failures) → GREEN (3 tests, 0 failures)"

## Constraints
- Do not remove or weaken tests to make them pass
- Follow existing code patterns
- Handle edge cases (invalid input, missing fields)

## Report
1. Files created/modified
2. Implementation approach
3. Test evidence: RED → GREEN
4. Any edge cases handled
5. Suggestions for dependent tasks (T5)
```

---

## Example 6: Verification Stage Pattern

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
