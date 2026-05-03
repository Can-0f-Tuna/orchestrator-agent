# Failure Protocol

When sub-agents fail. Not "if" — "when."

---

## Retry Rules

### 1st failure
Respawn the same agent with clarified instructions. Identify what went wrong from their report and address it directly in the new prompt.

Example: If the agent couldn't find the file, specify the exact path. If the agent misunderstood the scope, add more specific constraints.

### 2nd failure
Respawn with a different approach. The first approach didn't work. Change strategy: give more concrete steps, add an example of expected behavior, or split the task into smaller pieces.

### 3rd failure — ESCALATE
Stop. Do not retry. Report to the user:
- What the task was
- What was tried (approach 1 and approach 2)
- What went wrong each time
- **Ask the user for guidance**

Never retry more than 3 times. Burning tokens on a loop is worse than asking for help.

---

## Rollback Protocol

If a sub-agent's work introduces problems:

### The agent itself detected the issue
- If the agent reports they couldn't complete the task or introduced errors → respawn to fix
- Always verify their fix with a verification agent if the change was significant

### A later stage detects an issue with previous work
- Spawn a dedicated fix agent with:
  - The specific problem found
  - The code that's broken (file paths, what's wrong)
  - Instruction to fix it minimally

### Catastrophic failure (build broken, tests failing everywhere)
1. Spawn an agent to diagnose what went wrong
2. Based on diagnosis, either:
   - Spawn a fix agent for a targeted fix
   - Or revert changes using git (spawn an agent to do this)
3. After the situation is stabilized, re-run the failed stage

---

## Partial Completion

If an agent completed some work but not all:

### What to do
1. Read their report carefully — identify exactly what was done and what wasn't
2. Spawn a new agent with:
   - "The following work was already completed: [list from previous agent's report]"
   - "Your mission is to complete the remaining work: [specific list]"
   - Include the previous agent's report in context

### What NOT to do
- Don't try to complete the work yourself
- Don't ignore the partial work and start over
- Don't spawn without telling the new agent what was already done

---

## Hallucination Detection

Sub-agents sometimes claim they did something they didn't.

### Signs of possible hallucination:
- Report is vague ("fixed the issue" without saying how)
- No specific file paths or line numbers
- Changes described don't match what was asked
- Verification sounds implausible

### Response:
1. Spawn a verification agent to check the claims
2. If verification fails → treat as a 1st failure, respawn with specific evidence of what was wrong
3. If this happens repeatedly → escalate to user with the pattern observed

---

## When to Escalate to User

Escalate immediately (don't retry) when:
- 3 retries have failed
- The task requires a decision only the user can make (design choice, tradeoff, priority call)
- The sub-agent reports a bug that's outside the current task scope
- A dependency is missing or broken in a way that blocks the task
- Two sub-agents give contradictory information and neither is clearly wrong

### Escalation format:
```
Task: [what we were trying to do]
Attempted: [approaches tried]
Result: [what happened, what the sub-agents reported]
Question: [specific question for the user — not "what should I do" but "should we do A or B?"]
```

---

## Preventing Failures

Prevention beats recovery:

1. **Write better prompts** — unclear prompts cause unclear output. See prompt-templates.md.
2. **Give agents the docs** — every prompt must include the docs reading instruction
3. **Verify critical stages** — after database changes, API creation, or major refactors, spawn a verification agent
4. **Detect conflicts early** — don't run two agents on the same file in parallel
5. **Keep context bounded** — if a task feels too big for one agent, it is. Split it.
