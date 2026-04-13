# Examples

## Example 1: Three Simple Independent Tasks

**User Request:** "Change the hero button color to blue, fix the typo in the footer, and add a border to the navbar"

### Classification
- Task A: Change hero button color → SIMPLE, independent
- Task B: Fix footer typo → SIMPLE, independent
- Task C: Add navbar border → SIMPLE, independent

### Execution

**Orchestrator spawns all three in parallel:**

```
run agent "
Read the README.md file first.

Task: Change the hero button color to blue
Location: hero component (find the relevant file yourself)
Do exactly this and nothing else.
"
```

```
run agent "
Read the README.md file first.

Task: Fix the typo in the footer
Location: footer component (find the relevant file yourself)
Do exactly this and nothing else.
"
```

```
run agent "
Read the README.md file first.

Task: Add a border to the navbar
Location: navbar component (find the relevant file yourself)
Do exactly this and nothing else.
"
```

**All three run in parallel. Orchestrator waits for all to complete, then reports success.**

---

## Example 2: Complex Task with Dependent Simple Task

**User Request:** "Create a new About page and add a link to it in the navbar"

### Classification
- Task A: Create About page → COMPLEX (new feature, multiple components)
- Task B: Add navbar link → SIMPLE, but DEPENDENT (needs page route first)

### Execution

**Step 1: Queue dependent task**
- Task B queued (will run after Task A stage 1)

**Step 2: Start Complex Task Stage 1/2**

```
run agent "
Read the README.md file first.

Grand Goal: Create a new About page and link it in the navbar
History / Previous stages: None - this is stage 1
Current Mission (Stage 1/2): Create the About page component at /about route with proper layout and content

Do exactly this mission and nothing else.
"
```

**Wait for Stage 1 completion...**

**Step 3: Unblock and run dependent simple task**

```
run agent "
Read the README.md file first.

Task: Add a link to the About page in the navbar
Location: navbar component
Context: The About page is now available at /about route
Do exactly this and nothing else.
"
```

**Wait for Task B completion...**

**Step 4: Report success to user**

---

## Example 3: Multi-Stage Complex Task

**User Request:** "Build a complete authentication system with login, register, and logout"

### Classification
- Task: Build auth system → COMPLEX, requires multiple stages

### Stage Breakdown
1. Stage 1: Database schema
2. Stage 2: API endpoints
3. Stage 3: Frontend forms
4. Stage 4: Integration testing

### Execution

**Stage 1/4: Database Schema**
```
run agent "
Read the README.md file first.

Grand Goal: Build a complete authentication system with login, register, and logout
History / Previous stages: None - this is stage 1
Current Mission (Stage 1/4): Create database schema with users table (id, email, password_hash, created_at, updated_at)

Do exactly this mission and nothing else.
"
```

**Wait...**

**Stage 2/4: API Endpoints**
```
run agent "
Read the README.md file first.

Grand Goal: Build a complete authentication system with login, register, and logout
History / Previous stages:
- Stage 1 completed: Database schema created with users table
Current Mission (Stage 2/4): Create /api/auth/register, /api/auth/login, and /api/auth/logout endpoints using the database schema

Do exactly this mission and nothing else.
"
```

**Wait...**

**Stage 3/4: Frontend Forms**
```
run agent "
Read the README.md file first.

Grand Goal: Build a complete authentication system with login, register, and logout
History / Previous stages:
- Stage 1 completed: Database schema created
- Stage 2 completed: API endpoints created at /api/auth/*
Current Mission (Stage 3/4): Create LoginForm and RegisterForm components that call the API endpoints

Do exactly this mission and nothing else.
"
```

**Wait...**

**Stage 4/4: Integration**
```
run agent "
Read the README.md file first.

Grand Goal: Build a complete authentication system with login, register, and logout
History / Previous stages:
- Stage 1 completed: Database schema created
- Stage 2 completed: API endpoints created
- Stage 3 completed: Frontend forms created
Current Mission (Stage 4/4): Add the auth forms to the appropriate pages and verify the complete flow works end-to-end

Do exactly this mission and nothing else.
"
```

**Wait... Report success.**

---

## Example 4: Mixed Simple and Complex

**User Request:** "Fix the contact form validation, build a new dashboard page, and update the copyright year"

### Classification
- Task A: Fix contact form validation → COMPLEX (may require debugging, testing)
- Task B: Build dashboard page → COMPLEX (new feature)
- Task C: Update copyright year → SIMPLE, independent

### Execution

**Step 1: Run independent simple task immediately (parallel with complex starts)**
```
run agent "
Read the README.md file first.

Task: Update the copyright year
Location: footer component (find the relevant file yourself)
Do exactly this and nothing else.
"
```

**Step 2: Start Complex Task A Stage 1**
```
run agent "
Read the README.md file first.

Grand Goal: Fix the contact form validation
History / Previous stages: None - this is stage 1
Current Mission (Stage 1/N): Diagnose the validation issue by examining the contact form code and identifying what's failing

Do exactly this mission and nothing else.
"
```

**Step 3: Start Complex Task B Stage 1**
```
run agent "
Read the README.md file first.

Grand Goal: Build a new dashboard page
History / Previous stages: None - this is stage 1
Current Mission (Stage 1/N): Create the basic dashboard page structure and routing

Do exactly this mission and nothing else.
"
```

**Step 4: Wait for all three parallel tasks**
- Copyright update completes
- Complex Task A Stage 1 completes
- Complex Task B Stage 1 completes

**Step 5: Continue complex tasks with Stage 2+**

Continue staging each complex task independently until complete.

---

## Common Patterns

### Pattern: Simple Chain
Multiple independent simple tasks → Run all in parallel

### Pattern: Complex Cascade
Complex task with stages → Run sequentially, wait between stages

### Pattern: Dependent Trigger
Complex task + dependent simple task → Queue simple, run after stage

### Pattern: Mixed Load
Simple + Complex tasks together → Run simple parallel, stage complex

## Troubleshooting

**Problem: Sub-agent asking for clarification**
→ Context was too minimal. Add necessary details while staying focused.

**Problem: Sub-agent doing wrong thing**
→ Task description wasn't specific enough. Make it exact and bounded.

**Problem: Stage depends on output from previous stage**
→ Ensure you're waiting for completion and including relevant output in next stage's context.

**Problem: Tasks conflicting with each other**
→ Check dependency detection. They may not be as independent as you thought.
