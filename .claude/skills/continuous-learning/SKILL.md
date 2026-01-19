# Claudeception: Continuous Learning

| Field | Value |
|-------|-------|
| Name | `continuous-learning` |
| Description | Capture valuable discoveries across ANY domain—debugging solutions, research strategies, communication patterns, workflows—and preserve them as reusable skills. Activates on "aha moments", extended investigation, or user request to save knowledge. |
| Category | workflow |
| Author | claudeception |
| Version | 1.0.0 |
| Date | 2026-01-19 |

## Problem / Challenge

Knowledge is lost between sessions. You spend time figuring something out, the session ends, and next time you face the same challenge you start from scratch.

- Every breakthrough, workaround, and insight evaporates
- Patterns that took effort to discover must be rediscovered
- No systematic way to improve capabilities over time

## Context / Trigger Conditions

### When to Extract

**Technical**
- Non-obvious debugging solutions
- Tool integrations not well-documented
- Architecture decisions that worked

**Research & Analysis**
- Effective search strategies for specific domains
- Methods to verify conflicting information
- Synthesis approaches for complex topics

**Communication**
- Framing that clarified complex ideas
- How to adapt tone for specific audiences
- Ways to give feedback that landed well

**Problem-Solving**
- Mental models that unlocked solutions
- How constraints were reframed productively
- Ways to break down overwhelming problems

**Workflows**
- Multi-step processes worth repeating
- Tool combinations that work well together
- Organization systems for specific tasks

### Activation Triggers

**Automatic** - Evaluate for extraction after:
- Extended investigation (>10 min)
- "Aha moments" where something clicked
- Successfully navigating tricky situations
- Discovering something counter-intuitive

**Explicit** - When user says:
- "save this as a skill"
- "what did we learn?"
- "remember this"

## Approach / Solution

### Step 1: Quality Check

Before extracting, verify:

- [ ] **Reusable**: Will this help in future similar situations?
- [ ] **Non-trivial**: Did this require actual discovery?
- [ ] **Specific**: Can you describe exactly when this applies?
- [ ] **Verified**: Did this actually work?

If any answer is "no", don't extract.

### Step 2: Structure the Skill

Include these sections:

1. **Problem/Challenge**: What situation does this address?
2. **Triggers**: When should this activate? Be specific.
3. **Approach**: Step-by-step or principles to apply
4. **Verification**: How do you know it worked?
5. **Limitations**: When does this NOT apply?

### Step 3: Write for Semantic Matching

Descriptions must match how situations naturally arise:

| Domain | Good | Bad |
|--------|------|-----|
| Technical | "Fix connection pool exhaustion in serverless Prisma" | "Database helper" |
| Research | "Find primary sources on contested historical events" | "Research help" |
| Communication | "Explain technical concepts to executives without condescension" | "Communication tips" |
| Problem-solving | "Break decision paralysis when all options have tradeoffs" | "Decision help" |

### Step 4: Save the Skill

Save to:
- **Project-level**: `.claude/skills/[skill-name]/SKILL.md`
- **User-level**: `~/.claude/skills/[skill-name]/SKILL.md`

Use kebab-case for skill names.

## Verification

After extraction, confirm:

1. [ ] Skill follows the template structure
2. [ ] Description enables semantic matching
3. [ ] Triggers are specific and actionable
4. [ ] Approach provides clear guidance
5. [ ] Limitations are documented

## Example

### Scenario

After debugging a serverless deployment issue for 20 minutes, you discover that Prisma's connection pooling behaves differently on Vercel.

### Before

Session ends. Knowledge lost. Next developer (or future you) hits the same P2024 timeout error and spends another 20 minutes debugging.

### After

Skill extracted: `prisma-serverless-connections/SKILL.md`

```markdown
| Name | `prisma-serverless-connections` |
| Description | Fix PrismaClientKnownRequestError P2024 connection pool exhaustion in serverless Vercel/Lambda deployments |

## Problem
Serverless functions create new Prisma clients on each invocation, exhausting the database connection pool...

## Triggers
- Error: "Timed out fetching a new connection from the connection pool"
- Running Prisma on Vercel, AWS Lambda, or Netlify Functions
...
```

Future sessions automatically match this skill when similar errors appear.

## Limitations

- **Over-extraction**: Not every task produces extractable knowledge
- **Vague descriptions**: "Helps with problems" matches nothing useful
- **Unverified approaches**: Only save what actually worked
- **Obvious information**: Don't document what's easily searchable
- **Context-dependent luck**: Distinguish insights from one-off wins

## References

- See `.claude/skills/claudeception/SKILL.md` for the full specification
- See `.claude/skills/claudeception/resources/skill-template.md` for creating new skills
- See `.claude/skills/claudeception/examples/` for reference implementations

---

## Self-Check Protocol

After significant tasks, ask:

> "Would future-me benefit from having this documented?"

> "If I faced this in 6 months, what would I want to know?"

If yes, extract the skill.
