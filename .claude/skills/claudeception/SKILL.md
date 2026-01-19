# Claudeception: Continuous Learning System

## Overview

Claudeception is a skill for autonomous skill extraction and continuous learning. Instead of starting from scratch each session, it captures non-obvious discoveries (debugging techniques, workarounds, project patterns) and saves them as reusable skills for future reference.

**The Problem**: Every time you use an AI coding agent, it starts from zero. You spend an hour debugging some obscure error, the agent figures it out, session ends. Next time you hit the same issue? Another hour.

**The Solution**: Claude Code's native skills system loads skill descriptions at startup. Claudeception leverages this by writing new skills when it detects extractable knowledge.

## When to Extract Skills

Extract skills when encountering:

- **Non-obvious Solutions**: Debugging requiring significant investigation not in documentation
- **Project-Specific Patterns**: Conventions or configurations unique to the codebase
- **Tool Integration Knowledge**: Proper usage not well-covered in docs
- **Error Resolution**: Specific errors with misleading messages or non-obvious fixes
- **Workflow Optimizations**: Multi-step processes or efficiency patterns

## Quality Criteria

Before extracting, verify:

- **Reusable**: Helps with future tasks, not just current instance
- **Non-trivial**: Requires discovery, not documentation lookup
- **Specific**: Exact trigger conditions and solutions documented
- **Verified**: Solution actually works, not theoretical

## Extraction Process

### Step 1: Identify Knowledge

Analyze what was learned:
- What was the core discovery?
- Why was it non-obvious?
- What are the exact trigger conditions?
- What symptoms indicate this problem?

### Step 2: Research Best Practices

When dealing with technologies, frameworks, or tools:
- Search for current best practices
- Check official documentation
- Verify the solution aligns with recommended approaches

Skip searching for project-specific patterns or internal conventions.

### Step 3: Structure the Skill

Use the template in `resources/skill-template.md` with these sections:
- **Problem**: Clear description of what this skill addresses
- **Context/Trigger Conditions**: When to activate (errors, symptoms, environment)
- **Solution**: Step-by-step instructions with code examples
- **Verification**: How to confirm the fix worked
- **Example**: Real-world before/after demonstration
- **Notes**: Caveats, edge cases, related skills

### Step 4: Write Semantic Descriptions

Descriptions must be optimized for semantic matching:
- Include specific error messages
- Include framework/tool names
- Include observable symptoms
- Use action phrases

Good: "Fix for PrismaClientKnownRequestError P2024 connection pool exhaustion in serverless"
Bad: "Helps with database problems"

### Step 5: Save the Skill

Save to one of these locations:
- **Project-level**: `.claude/skills/[skill-name]/SKILL.md`
- **User-level**: `~/.claude/skills/[skill-name]/SKILL.md`

Use kebab-case for skill names.

## Activation Triggers

### Automatic Activation

Trigger skill evaluation after tasks involving:
- Non-obvious debugging (>10 minutes of investigation)
- Workarounds or trial-and-error discovery
- Error resolution with misleading messages
- Performance optimization discoveries

### Explicit Activation

Activate when user:
- Runs `/claudeception` command
- Says "save this as a skill"
- Asks "what did we learn?"
- Requests knowledge preservation

### Self-Check Protocol

After each significant task, ask yourself:
> "Would future-me benefit from this being documented?"

If yes, extract the skill.

## Anti-Patterns to Avoid

- **Over-extraction**: Not every fix needs a skill
- **Vague descriptions**: "Helps with errors" is useless
- **Unverified solutions**: Only save what actually worked
- **Duplicating docs**: Don't replicate official documentation
- **Outdated knowledge**: Research current best practices first

## Skill Discovery

Skills are discovered through semantic matching. Write descriptions that match how problems manifest:

```
User asks: "Getting P2024 timeout error in my Vercel deployment"
Skill description: "Fix for PrismaClientKnownRequestError P2024 connection pool exhaustion in serverless environments like Vercel, Netlify, AWS Lambda"
Result: Match!
```

## Directory Structure

```
.claude/skills/claudeception/
├── SKILL.md                 # This file - main specification
├── README.md                # Setup and usage instructions
├── resources/
│   └── skill-template.md    # Template for new skills
├── scripts/
│   └── claudeception-activator.sh  # Auto-activation hook
└── examples/                # Reference implementations
    ├── nextjs-server-side-error-debugging/
    ├── prisma-connection-pool-exhaustion/
    └── typescript-circular-dependency/
```

## Integration with Claude Code

Claude Code automatically loads skills from:
1. `.claude/skills/*/SKILL.md` (project-level)
2. `~/.claude/skills/*/SKILL.md` (user-level)

Skills are loaded at session start and matched semantically against user queries.
