# Claudeception

**Autonomous Skill Extraction and Continuous Learning for Claude Code**

Claudeception enables Claude to learn from debugging sessions and preserve that knowledge as reusable skills. Instead of starting from scratch each session, discoveries are saved and automatically loaded in future sessions.

## The Problem

Every time you use an AI coding agent, it starts from zero. You spend an hour debugging some obscure error, the agent figures it out, session ends. Next time you hit the same issue? Another hour.

## The Solution

Claude Code has a native skills system that loads skill descriptions at startup. Claudeception leverages this by writing new skills when it detects extractable knowledge. Skills are optimized for semantic matching—"Fix for PrismaClientKnownRequestError in serverless" triggers better than "Helps with database problems."

## Installation

### Option 1: Project-Level (Recommended for teams)

```bash
# Clone into your project's .claude directory
git clone https://github.com/YOUR_USERNAME/Recursive-Learning.git
# Or just copy the .claude/skills/claudeception folder to your project
```

The skill will be available to anyone working on this project.

### Option 2: User-Level (Personal use)

```bash
# Clone to your home .claude directory
mkdir -p ~/.claude/skills
cp -r .claude/skills/claudeception ~/.claude/skills/
```

The skill will be available in all your projects.

## Activation Hook Setup (Optional)

To have Claude automatically evaluate each session for extractable knowledge:

### 1. Create hooks directory

```bash
mkdir -p ~/.claude/hooks
cp ~/.claude/skills/claudeception/scripts/claudeception-activator.sh ~/.claude/hooks/
chmod +x ~/.claude/hooks/claudeception-activator.sh
```

### 2. Configure Claude settings

Add to `~/.claude/settings.json`:

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/hooks/claudeception-activator.sh"
          }
        ]
      }
    ]
  }
}
```

## Usage

### Automatic Mode

With the activation hook installed, Claude will evaluate every session for extractable knowledge after completing your request.

### Explicit Commands

- Say **"save this as a skill"** - extracts current discovery
- Say **"what did we learn?"** - triggers learning retrospective
- Ask **"would this be useful for future sessions?"** - evaluation prompt

## What Gets Extracted

Skills are only created when knowledge:

- **Required actual discovery** - not just documentation lookup
- **Applies broadly** - useful for future similar situations
- **Has clear triggers** - specific errors, symptoms, or conditions
- **Has been verified** - solution actually worked

## Skill Quality Standards

Good skill descriptions are **specific and semantic**:

| Bad | Good |
|-----|------|
| "Database helper" | "Fix for PostgreSQL too many connections error in serverless Vercel deployment" |
| "TypeScript error fix" | "Resolve Cannot access before initialization circular dependency in TypeScript ESM" |
| "API debugging" | "Debug Next.js 500 errors by checking server logs instead of browser console" |

## Directory Structure

```
.claude/skills/claudeception/
├── SKILL.md                 # Main specification (how to extract skills)
├── README.md                # This file
├── resources/
│   └── skill-template.md    # Template for new skills
├── scripts/
│   └── claudeception-activator.sh  # Auto-activation hook
└── examples/                # Reference implementations
    ├── nextjs-server-side-error-debugging/
    ├── prisma-connection-pool-exhaustion/
    └── typescript-circular-dependency/
```

## Creating Skills Manually

1. Copy the template from `resources/skill-template.md`
2. Fill in all sections with specific details
3. Save to `.claude/skills/[skill-name]/SKILL.md`
4. Claude will load it on next session

## How Skill Matching Works

Claude Code loads all skills at session start and matches them semantically to user queries:

```
User: "Getting P2024 timeout error in Vercel"
                    ↓
Skills loaded: [prisma-connection-pool, nextjs-debugging, ...]
                    ↓
Semantic match: "PrismaClientKnownRequestError P2024 connection pool exhaustion in serverless"
                    ↓
Skill activated! Claude applies the documented solution.
```

## Examples Included

1. **prisma-connection-pool-exhaustion** - Fix serverless database connection limits
2. **nextjs-server-side-error-debugging** - Find real errors hidden in server logs
3. **typescript-circular-dependency** - Detect and resolve circular imports

## Contributing Skills

Found something useful? Create a skill!

1. Use the template in `resources/skill-template.md`
2. Focus on non-obvious discoveries
3. Include specific error messages and trigger conditions
4. Add verification steps
5. Submit a PR

## License

MIT
