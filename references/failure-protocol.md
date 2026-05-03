# Failure Protocol

## Retry Rules

### 1st failure
Respawn with clarified instructions. Address what went wrong directly in the new prompt.

### 2nd failure
Respawn with a different approach. Split the task smaller, add concrete steps, or change strategy.

### 3rd failure — escalate
Stop. Report to the user:
- What the task was
- What was tried (both approaches)
- What went wrong
- Ask for guidance

Never retry more than 3 times.

---

## Rollback

If a sub-agent's work introduces problems:

### Agent detected the issue
Respawn to fix. If significant, spawn a verification agent after.

### Later stage detects issues with previous work
Spawn a fix agent with:
- The specific problem found
- File paths and what's wrong
- Instruction to fix minimally

### Catastrophic failure (build broken)
1. Spawn an agent to diagnose
2. Based on diagnosis: targeted fix or git revert (spawn agent for either)

---

## Partial Completion

If an agent completed some work but not all:

1. Read their report — identify exactly what was done and what wasn't
2. Spawn a new agent with a clear split:
   - "Already completed: [list from previous agent's report]"
   - "Your mission: complete the remaining work: [specific list]"

---

## Hallucination Detection

Signs a sub-agent might be hallucinating:
- Vague report ("fixed the issue" without specifics)
- No file paths or line numbers
- Changes described don't match what was asked
- Verification sounds implausible

### Response:
1. Spawn a verification agent to check the claims
2. If claims false → treat as 1st failure, respawn with evidence
3. If pattern repeats → escalate to user

---

## When to Escalate Immediately

- 3 retries have failed
- Task requires a decision only the user can make
- Sub-agent reports a bug outside the current task scope
- A dependency is missing or broken in a way that blocks the task
- Two sub-agents give contradictory information

### Escalation format:
```
Task: [what we were trying to do]
Attempted: [approaches tried]
Result: [what happened]
Question: [specific question — "should we do A or B?"]
```

---

## Prevention

1. Write better prompts — unclear prompts cause unclear output
2. Give agents the docs — every prompt must include the docs reading instruction
3. Verify critical stages — after database, API, or major refactor changes
4. Detect conflicts early — don't run two agents on the same file in parallel
5. Keep context bounded — if a task feels too big for one agent, split it
