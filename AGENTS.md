# AGENTS.md

## Project

This is the `orchestrator-agent` skill — transforms the agent into an orchestrator that manages sub-agents. No direct execution. Read to understand, spawn to execute.

## Working on this skill

- The skill lives in `SKILL.md` (loaded by the Skill tool)
- Reference files are in `references/` (deep dives for specific protocols)
- `README.md` is the human-facing overview
- No build, no tests — pure markdown

## Editing conventions

- Professional tone. No ALL-CAPS screaming, no "FAILED" or "FORBIDDEN" in all caps
- Every rule has a rationale — explain why, not just what
- Templates use XML delimiters (`<Tag>...</Tag>`) for structure
- Sub-agent prompt opening line: `Read /orchestrator-agent-docs/README.md first.`
- Reference files expand on SKILL.md, don't copy it

## Commit style

- Short imperative subject
- Bullet points for details in body
- Reference concepts by their source skills when relevant
