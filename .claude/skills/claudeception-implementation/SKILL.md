# Implementing a Claude Code Skill System (Claudeception)

| Field | Value |
|-------|-------|
| Name | `claudeception-implementation` |
| Description | How to create an autonomous skill extraction system for Claude Code that captures debugging discoveries and saves them as reusable skills |
| Author | claudeception |
| Version | 1.0.0 |
| Date | 2026-01-19 |

## Problem

You want Claude Code to learn from sessions and preserve knowledge for future use. By default, every session starts fresh - debugging discoveries, workarounds, and project patterns are lost when the session ends.

## Context / Trigger Conditions

- User asks to "create a learning system" or "skill extraction system"
- User wants Claude to "remember" things between sessions
- User references "Claudeception" or autonomous learning
- User wants to set up continuous improvement for Claude Code

## Solution

### Step 1: Create Directory Structure

```bash
mkdir -p .claude/skills/claudeception/{examples,resources,scripts}
```

### Step 2: Create Main SKILL.md

This is the core specification that tells Claude how and when to extract skills.

Key sections to include:
- **When to Extract**: Non-obvious solutions, project patterns, tool integration knowledge, error resolution
- **Quality Criteria**: Reusable, non-trivial, specific, verified
- **Extraction Process**: Identify → Research → Structure → Write descriptions → Save
- **Activation Triggers**: Automatic (after debugging) and explicit (user commands)

```markdown
# Claudeception: Continuous Learning System

## When to Extract Skills
- Non-obvious solutions requiring investigation
- Project-specific patterns
- Error resolution with misleading messages

## Quality Criteria
- Reusable: Helps with future tasks
- Non-trivial: Requires discovery
- Specific: Clear trigger conditions
- Verified: Actually works

## Extraction Process
1. Identify what was learned
2. Research best practices
3. Structure using template
4. Write semantic descriptions
5. Save to .claude/skills/[name]/SKILL.md
```

### Step 3: Create Skill Template

Provide a consistent format for new skills in `resources/skill-template.md`:

```markdown
| Field | Value |
|-------|-------|
| Name | `skill-name-kebab-case` |
| Description | Semantic description with error codes, frameworks |

## Problem
What pain point does this solve?

## Context / Trigger Conditions
- Error messages (exact text)
- Symptoms
- Environment

## Solution
Step-by-step with code examples

## Verification
How to confirm it worked

## Example
Before/after demonstration
```

### Step 4: Create Activation Hook

Create `scripts/claudeception-activator.sh`:

```bash
#!/bin/bash
cat << 'EOF'
MANDATORY SKILL EVALUATION REQUIRED

After completing this request, evaluate:
- Did this require non-obvious investigation?
- Would this help in future similar situations?
- Was this not obvious from documentation?

If YES: Extract as a skill
If NO: Skip extraction
EOF
```

Make executable: `chmod +x scripts/claudeception-activator.sh`

### Step 5: Create Example Skills

Add 2-3 example skills in `examples/` to demonstrate the format:
- Real problems with specific error messages
- Step-by-step solutions
- Verification steps
- Before/after examples

### Step 6: Configure Hook (Optional)

Add to `~/.claude/settings.json`:

```json
{
  "hooks": {
    "UserPromptSubmit": [{
      "hooks": [{
        "type": "command",
        "command": "~/.claude/hooks/claudeception-activator.sh"
      }]
    }]
  }
}
```

## Verification

1. [ ] Directory structure exists at `.claude/skills/claudeception/`
2. [ ] SKILL.md defines extraction criteria and process
3. [ ] Template exists for creating new skills
4. [ ] At least one example skill demonstrates the format
5. [ ] Claude loads the skill on next session start

## Example

### Scenario

User wants to set up Claudeception in their project.

### Before

```
.claude/
└── (empty or non-existent)
```

### After

```
.claude/skills/claudeception/
├── SKILL.md                 # Main specification
├── README.md                # Setup instructions
├── resources/
│   └── skill-template.md    # Template for new skills
├── scripts/
│   └── claudeception-activator.sh
└── examples/
    ├── prisma-connection-pool-exhaustion/SKILL.md
    ├── nextjs-server-side-error-debugging/SKILL.md
    └── typescript-circular-dependency/SKILL.md
```

## Notes

- **Key Insight**: Skills are matched semantically. Write descriptions with specific error codes, framework names, and symptoms - not vague categories.
- **Caveats**: Don't over-extract. Not every fix needs a skill. Quality over quantity.
- **Related Skills**: Individual skills created by Claudeception
- **Known Limitations**: Skills are text-based; complex multi-step workflows may need multiple skills

## References

- [Original Claudeception](https://github.com/blader/Claudeception)
- [Claude Code Skills Documentation](https://docs.anthropic.com/claude-code/skills)
