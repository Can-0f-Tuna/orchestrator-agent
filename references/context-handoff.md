# Context Handoff Rules

## Overview

How you package and transmit context to sub-agents is critical. Too much context wastes tokens and confuses. Too little context leaves sub-agents without needed information.

## SIMPLE Tasks: Minimal Focused Context

### The Principle
Give **Minimal Focused Context ONLY** — never full history, never grand goal, never previous stages.

### What to Include
The sub-agent must receive ONLY:
- Exact description of what to do
- Exact file/path if mentioned by user
- Any specific detail the user gave

### Template Format

```
run agent "
Read the README.md file first.

Task: [exact user instruction, word for word if possible]
Location: [file/path if known, otherwise "find the relevant file yourself"]
Do exactly this and nothing else.
"
```

### Example: Simple Task

**User says:** "Change the button color in the navbar to blue"

**Correct spawn:**
```
run agent "
Read the README.md file first.

Task: Change the button color in the navbar to blue
Location: navbar component (find the relevant file yourself)
Do exactly this and nothing else.
"
```

**Incorrect spawn (too much context):**
```
run agent "
Read the README.md file first.

Grand Goal: We're building a complete website redesign with new branding... [100 lines of history]
Task: Change the button color...
" 
```

## COMPLEX Tasks: Structured Context

### The Principle
Break into clear sequential STAGES. Wait for current stage to finish completely before starting next stage.

### What to Include
For each sub-agent in a complex task use this exact structure:

```
run agent "
Read the README.md file first.

Grand Goal: [one short sentence - the final desired outcome]
History / Previous stages: [short bullet list of what was already done]
Current Mission (Stage X/Y): [exactly what this sub-agent needs to do now]

Do exactly this mission and nothing else.
"
```

### Example: Complex Task

**User says:** "Build a new user authentication system"

**Stage 1/3: Setup database schema**
```
run agent "
Read the README.md file first.

Grand Goal: Build a complete user authentication system
History / Previous stages: None - this is stage 1
Current Mission (Stage 1/3): Create database schema for users table with email, password_hash, created_at fields

Do exactly this mission and nothing else.
"
```

**Stage 2/3: Create API endpoints**
```
run agent "
Read the README.md file first.

Grand Goal: Build a complete user authentication system
History / Previous stages:
- Stage 1 completed: Database schema created with users table
Current Mission (Stage 2/3): Create /register and /login API endpoints that use the new schema

Do exactly this mission and nothing else.
"
```

**Stage 3/3: Build frontend forms**
```
run agent "
Read the README.md file first.

Grand Goal: Build a complete user authentication system
History / Previous stages:
- Stage 1 completed: Database schema created
- Stage 2 completed: /register and /login API endpoints created
Current Mission (Stage 3/3): Create login and registration forms in the frontend that call the API endpoints

Do exactly this mission and nothing else.
"
```

## Context Handoff Checklist

### For SIMPLE Tasks
- [ ] No grand goal mentioned
- [ ] No history from previous work
- [ ] No previous stages referenced
- [ ] Exact task description included
- [ ] File/path specified (or "find it yourself")

### For COMPLEX Tasks
- [ ] Grand Goal: One short sentence
- [ ] History: Short bullet list only
- [ ] Current Mission: Specific and bounded
- [ ] Stage number clear (X/Y format)
- [ ] Previous stage completion confirmed

## What NOT to Include

**Never include in SIMPLE task context:**
- ❌ "We're building a website..." (grand goal)
- ❌ "Previously we did..." (stage history)
- ❌ "This is part of a larger refactor..." (context pollution)
- ❌ Full conversation history
- ❌ Project background

**Never include in COMPLEX task context:**
- ❌ Irrelevant file contents
- ❌ Unrelated feature discussions
- ❌ Future stages (only current)
- ❌ Overly verbose descriptions

## Context Size Guidelines

**SIMPLE task context:** 3-10 lines optimal
**COMPLEX task context:** 10-20 lines optimal

If your context is longer, review and trim.

## Anti-Patterns

**Pattern: The Dump**
Dumping everything you know into sub-agent context.

**Why Bad:** Wastes tokens, confuses focus, sub-agent gets lost in noise.

**Pattern: The Mystery**
Giving almost no context.

**Why Bad:** Sub-agent doesn't have enough to succeed, will fail or ask questions.

**Pattern: The Leak**
Including context from unrelated tasks.

**Why Bad:** Creates confusion about scope, may cause unwanted side effects.
