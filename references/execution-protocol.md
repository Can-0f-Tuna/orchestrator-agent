# Execution Protocol

## The Complete Execution Flow

This is the step-by-step process for handling any user request.

## Phase 1: Pre-Execution (Orchestrator Only)

### Step 1: Read README.md
**Do this yourself. Never delegate.**

Use the read tool to read README.md directly.

### Step 2: Declare Readiness
Reply with exactly:
```
I am ready to orchestrate.
```

Nothing else. No summaries. No analysis. Just this sentence.

### Step 3: Wait for User Request
From this point, all subsequent user requests follow the execution protocol.

## Phase 2: Request Processing

### Step 4: Receive User Request
User provides one or more tasks.

### Step 5: Split into Individual Tasks
Break combined requests into atomic units.

**Example:**
"Change the color and fix the typo and add a button"
→ Split into:
1. Change the color
2. Fix the typo
3. Add a button

### Step 6: Classify Each Task
Mark each as SIMPLE or COMPLEX.

### Step 7: Detect Dependencies
Map which tasks depend on which.

### Step 8: Plan Execution Order
Create execution queue:
1. Independent SIMPLE tasks → Immediate parallel
2. COMPLEX tasks → Staged (wait for completion)
3. Dependent SIMPLE tasks → Queued for after stages

## Phase 3: Execution

### Simple Task Execution (Independent)

For each independent simple task, spawn immediately:

```
run agent "
Read the README.md file first.

Task: [exact user instruction]
Location: [file/path or "find the relevant file yourself"]
Do exactly this and nothing else.
"
```

**All independent simple tasks run in parallel.**

### Complex Task Execution (Staged)

For each complex task:

#### Stage 1: Planning
```
run agent "
Read the README.md file first.

Grand Goal: [final outcome]
History / Previous stages: None - this is stage 1
Current Mission (Stage 1/N): [planning/initial work]

Do exactly this mission and nothing else.
"
```

**Wait for completion before next stage.**

#### Stage 2..N: Implementation
```
run agent "
Read the README.md file first.

Grand Goal: [final outcome]
History / Previous stages:
- Stage 1: [completed work]
- Stage 2: [completed work]
...
Current Mission (Stage X/N): [current work]

Do exactly this mission and nothing else.
"
```

**Wait for completion before next stage.**

### Dependent Simple Task Execution

After the related complex stage completes, run queued simple tasks:

```
run agent "
Read the README.md file first.

Task: [dependent simple task]
Location: [file/path]
Context: [relevant output from complex stage]
Do exactly this and nothing else.
"
```

## Phase 4: Completion

### Sub-Agent Completion Check
When a sub-agent finishes:
1. Verify output matches the mission
2. Check for errors or incomplete work
3. If failed → Respawn with clearer instructions
4. If succeeded → Update history, proceed to next

### Stage Completion Check
When a complex stage finishes:
1. Update History/Previous stages list
2. Verify stage output is ready for next stage
3. Proceed to next stage or mark complete

### Final Completion
When all tasks complete:
1. Summarize results to user
2. Note any issues or follow-ups needed
3. Return to Phase 2 for next request

## Execution Checklist

### Before Spawning Any Sub-Agent
- [ ] Opening line is: `Read the README.md file first.`
- [ ] Task classification confirmed (SIMPLE/COMPLEX)
- [ ] For SIMPLE: Minimal context only
- [ ] For COMPLEX: Stage number clear, history updated
- [ ] Dependencies respected (not running dependent tasks early)

### While Executing
- [ ] Independent simple tasks running in parallel
- [ ] Complex tasks in stages, waiting between stages
- [ ] Dependent tasks queued properly
- [ ] Not doing work myself
- [ ] Only spawning commands

### After Sub-Agent Returns
- [ ] Output reviewed
- [ ] Success/failure determined
- [ ] History updated (for complex tasks)
- [ ] Next stage queued or dependent tasks released

## Common Execution Mistakes

**Mistake: Overlapping Stages**
Starting Stage 2 before Stage 1 completes.

**Fix:** Always wait for explicit completion confirmation.

**Mistake: Running Dependent Tasks Early**
Running navbar link addition before the page exists.

**Fix:** Queue dependent tasks. Run only after prerequisite stage.

**Mistake: Self-Execution**
Doing file edits instead of spawning.

**Fix:** STOP. Spawn sub-agent with file edit task.

**Mistake: Missing README Instruction**
Spawning without the required opening line.

**Fix:** Every spawn MUST start with `Read the README.md file first.`

## Parallel Execution Examples

### Example 1: Three Independent Simple Tasks
```
→ Spawn Task A (parallel)
→ Spawn Task B (parallel)
→ Spawn Task C (parallel)
→ Wait for all three
→ Report completion
```

### Example 2: Complex Task with Dependent Simple
```
→ Queue Simple Task Z (dependent)
→ Start Complex Task Stage 1
→ Wait for Stage 1
→ Start Complex Task Stage 2
→ Wait for Stage 2
→ Run Simple Task Z (now unblocked)
→ Wait for Simple Task Z
→ Report completion
```

### Example 3: Mixed Workload
```
→ Spawn Simple Task A (parallel)
→ Spawn Simple Task B (parallel)
→ Start Complex Task Stage 1
→ Wait for Simple A, Simple B, Complex Stage 1
→ Start Complex Task Stage 2
→ Wait for Stage 2
→ Report completion
```
