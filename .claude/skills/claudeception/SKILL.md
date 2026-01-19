# Claudeception: Continuous Learning System

## Overview

Claudeception is a skill for autonomous knowledge extraction and continuous learning. Instead of starting from scratch each session, it captures valuable discoveries across ANY domain—problem-solving approaches, research methods, communication patterns, workflows, technical solutions—and saves them as reusable skills for future reference.

**The Problem**: Every session starts from zero. You spend time figuring something out, the session ends, knowledge is lost. Next time you face the same challenge? Start over.

**The Solution**: Extract and preserve knowledge when genuine learning occurs. Skills are loaded at startup and matched semantically to future situations.

## When to Extract Skills

### Technical & Coding
- Non-obvious debugging solutions
- Tool/framework integration patterns
- Error resolution with misleading messages
- Performance optimizations
- Architecture decisions that worked

### Research & Analysis
- Effective search strategies for specific domains
- How to find reliable sources on niche topics
- Methods to verify conflicting information
- Synthesis approaches for complex topics
- Data interpretation techniques

### Communication & Writing
- Framing approaches that clarified complex ideas
- Persuasion structures that worked
- How to adapt tone for specific audiences
- Templates for recurring communication types
- Ways to give feedback that landed well

### Problem-Solving & Decision Making
- Mental models that unlocked solutions
- How constraints were reframed productively
- Decision frameworks for specific scenarios
- Ways to break down overwhelming problems
- Approaches that avoided common pitfalls

### Workflows & Productivity
- Multi-step processes worth repeating
- Tool combinations that work well together
- Automation patterns
- Organization systems for specific tasks
- Prioritization methods that helped

### Domain-Specific Knowledge
- Industry-specific conventions learned
- Regulatory/compliance patterns
- Best practices discovered through experience
- Cultural or contextual nuances
- Jargon translations that helped

## Quality Criteria

Before extracting, verify:

- **Reusable**: Will this help in future similar situations?
- **Non-trivial**: Did this require actual discovery or insight?
- **Specific**: Can you describe exactly when this applies?
- **Verified**: Did this actually work, not just sound good?
- **Transferable**: Can this be explained clearly to future-you?

## Extraction Process

### Step 1: Identify the Learning

Ask yourself:
- What was the core insight or discovery?
- Why wasn't this obvious from the start?
- What situation triggered the need for this?
- What would have helped me know this earlier?

### Step 2: Validate & Research

- For technical knowledge: verify against current best practices
- For domain knowledge: cross-reference sources
- For approaches: consider if it generalizes or was context-specific
- Skip validation for personal preferences or project-specific conventions

### Step 3: Structure the Skill

Include these sections:
- **Problem/Challenge**: What situation does this address?
- **Context/Triggers**: When should this activate? Be specific.
- **Approach/Solution**: Step-by-step or principles to apply
- **Verification**: How do you know it worked?
- **Example**: Real scenario showing before/after
- **Limitations**: When does this NOT apply?

### Step 4: Write for Semantic Matching

Descriptions must match how the situation naturally comes up:

**Technical:**
- Good: "Fix connection pool exhaustion in serverless Prisma deployments"
- Bad: "Database helper"

**Research:**
- Good: "Find primary sources on contested historical events with multiple narratives"
- Bad: "Research help"

**Communication:**
- Good: "Explain technical concepts to non-technical stakeholders without condescension"
- Bad: "Writing tips"

**Problem-solving:**
- Good: "Break decision paralysis when all options have significant tradeoffs"
- Bad: "Decision making"

### Step 5: Save the Skill

Save to:
- **Project-level**: `.claude/skills/[skill-name]/SKILL.md`
- **User-level**: `~/.claude/skills/[skill-name]/SKILL.md`

Use kebab-case for skill names (e.g., `explain-technical-to-executives`).

## Activation Triggers

### Automatic Activation

Evaluate for extraction after:
- Extended investigation or research (>10 minutes)
- Trial-and-error that led to breakthrough
- Reframing that unlocked progress
- "Aha moments" where something clicked
- Successfully navigating a tricky situation
- Discovering something counter-intuitive

### Explicit Activation

Activate when user:
- Says "save this as a skill"
- Asks "what did we learn?"
- Requests "remember this for next time"
- Says "this would be useful to know in the future"

### Self-Check Protocol

After significant tasks, ask:
> "Would future-me benefit from having this documented?"
> "If I faced this situation again in 6 months, what would I want to know?"

If yes, extract the skill.

## Anti-Patterns to Avoid

- **Over-extraction**: Not every task produces extractable knowledge
- **Vague descriptions**: "Helps with problems" matches nothing useful
- **Unverified approaches**: Only save what actually worked
- **Obvious information**: Don't document what's easily searchable
- **Context-dependent luck**: Distinguish generalizable insights from one-off wins
- **Opinion as fact**: Be clear about what's preference vs. proven

## Skill Categories

Organize skills by domain for easier discovery:

```
.claude/skills/
├── claudeception/           # This meta-skill
├── coding/
│   ├── prisma-serverless-connections/
│   └── typescript-circular-deps/
├── research/
│   ├── finding-primary-sources/
│   └── validating-conflicting-claims/
├── communication/
│   ├── technical-to-executives/
│   └── giving-critical-feedback/
├── problem-solving/
│   ├── breaking-decision-paralysis/
│   └── reframing-constraints/
└── workflows/
    ├── code-review-checklist/
    └── research-synthesis-process/
```

## Integration with Claude

Claude automatically loads skills from:
1. `.claude/skills/*/SKILL.md` (project-level)
2. `~/.claude/skills/*/SKILL.md` (user-level)

Skills are matched semantically—write descriptions that match how situations naturally arise.
