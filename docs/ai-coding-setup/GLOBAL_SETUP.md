# Global AI Coding Assistant Setup

This guide shows you how to set up global configuration for AI coding assistants that applies to **ALL projects** on your computer.

## Why Global Configuration?

Global configuration ensures consistent AI behavior across all your projects:
- Same workflow (EXPLORE → PLAN → CONFIRM → CODE)
- Same critical rules
- Same code quality standards
- No need to configure each project individually

## What You'll Set Up

1. **Claude Code global config** - `~/.claude/instructions.md`
2. **Cursor global config** - `~/.cursorrules`

These files are automatically loaded by the AI tools for every project.

## Installation (5 Minutes)

### Step 1: Create Global Directories

```bash
# Create directories
mkdir -p ~/.claude ~/.cursor

# Verify
ls -la ~ | grep -E "\.claude|\.cursor"
```

### Step 2: Create Global Claude Code Instructions

Create `~/.claude/instructions.md`:

```bash
cat > ~/.claude/instructions.md << 'EOF'
# Global Claude Code Instructions

These instructions apply to ALL projects unless overridden by project-specific .claude/instructions.md

## Universal Workflow Rules

### 1. EXPLORE BEFORE CODING
- ALWAYS search the codebase for existing functions/patterns before creating new ones
- Read relevant documentation (README, docs/, etc.) before starting work
- Use Grep/Glob tools to find similar implementations
- Understand existing architecture before proposing changes

### 2. PLAN THEN CONFIRM (MANDATORY STOP POINT)
- Present exact plan to user: files to modify, functions to add/change/delete
- WAIT for explicit user approval ("Yes", "Approved", "Proceed")
- DO NOT write any code until receiving approval
- This is NON-NEGOTIABLE - never skip this step

### 3. MINIMAL CHANGES ONLY
- Make only the changes necessary to accomplish the task
- Modify existing functions instead of creating duplicates
- Reuse existing utilities and components
- Avoid over-engineering or adding "nice to have" features

### 4. NO DUPLICATE FUNCTIONS
- Before creating any function, search if it already exists
- Use Grep to search for function names in the codebase
- If similar function exists, modify it or reuse it
- Never create multiple functions that do the same thing

### 5. FOLLOW PROJECT CONVENTIONS
- Read project-specific guidelines (AI_CODING_GUIDELINES.md, CONTRIBUTING.md, etc.)
- Match existing code style and patterns
- Use existing naming conventions
- Maintain architectural consistency

### 6. VALIDATION BEFORE COMMIT
- Run project-specific test suites and linters
- Fix ALL warnings before proceeding
- Verify functionality works as expected
- Never bypass or ignore validation failures

## Project-Specific Guidelines Priority
If a project has .claude/instructions.md or AI_CODING_GUIDELINES.md, those rules take precedence over these global rules.

## Critical Mindset
- **Think first, code second** - Understanding the existing system prevents mistakes
- **Less is more** - Minimal changes are easier to review and less likely to break things
- **Get approval** - Users want to review plans before implementation
- **Reuse over recreate** - Existing code is tested and trusted
EOF

echo "✅ Created ~/.claude/instructions.md"
```

### Step 3: Create Global Cursor Rules

Create `~/.cursorrules`:

```bash
cat > ~/.cursorrules << 'EOF'
# Global Cursor AI Rules - All Projects

Read project-specific AI_CODING_GUIDELINES.md or .cursorrules if present.

## MANDATORY WORKFLOW:

1. EXPLORE: Search for existing code first (Cmd+Shift+F)
2. PLAN: Draft changes with file/function list
3. CONFIRM: Present plan in chat (Cmd+L) and WAIT for approval
4. CODE: Make minimal changes only
5. VALIDATE: Run tests/linters
6. COMMIT: With clear message

## Critical Rules:

1. EXPLORE BEFORE CODING - Always search for existing functions
2. PLAN THEN CONFIRM - Present plan and WAIT for approval (MANDATORY)
3. NO DUPLICATE FUNCTIONS - Modify existing, don't create new
4. MINIMAL CHANGES - Only what's necessary
5. FOLLOW PROJECT RULES - Project guidelines override global rules
6. VALIDATE FIRST - Fix all warnings before committing

## Cursor Usage:

- Cmd+L (Chat): Discuss plan BEFORE implementing
- Cmd+K (Edit): Only use AFTER plan is approved
- Cmd+Shift+F (Search): Use extensively to find existing code
- @-mentions: Reference files/folders when presenting plans

## Before Using Cmd+K:
1. Search for existing code (Cmd+Shift+F)
2. Discuss plan in chat (Cmd+L)
3. Get user approval
4. Then use Cmd+K

**CRITICAL**: You MUST present your plan and WAIT for approval before writing code.
EOF

echo "✅ Created ~/.cursorrules"
```

### Step 4: Verify Installation

```bash
# Check files exist
ls -lh ~/.claude/instructions.md ~/.cursorrules

# Preview contents
echo "=== Claude Code Global Instructions ==="
head -10 ~/.claude/instructions.md

echo ""
echo "=== Cursor Global Rules ==="
head -10 ~/.cursorrules
```

## Testing the Setup

### Test Claude Code

```bash
# Navigate to ANY project
cd ~/path/to/any/project

# Start Claude Code
claude

# In conversation, ask:
"What workflow should you follow before writing code?"

# Expected response should mention:
# - EXPLORE → PLAN → CONFIRM → CODE → VALIDATE
# - Searching for existing functions first
# - Waiting for approval before coding
```

### Test Cursor

```bash
# Open ANY project in Cursor
cursor ~/path/to/any/project

# In chat (Cmd+L), ask:
"What workflow should you follow before writing code?"

# Expected response should mention:
# - The same workflow
# - Presenting plan before using Cmd+K
# - Mandatory approval step
```

## How It Works

### Configuration Hierarchy

AI tools load instructions in this order:

```
1. Global Config (what you just set up)
   ├── Claude: ~/.claude/instructions.md
   └── Cursor: ~/.cursorrules

2. Project Config (if present)
   ├── Claude: <project>/.claude/instructions.md
   └── Cursor: <project>/.cursorrules

3. Project Guidelines (if present)
   └── <project>/AI_CODING_GUIDELINES.md

4. User Direct Instructions
   └── Highest priority
```

Later items override earlier ones.

### What This Means

- **All projects** get the global workflow by default
- **Specific projects** can add or override rules with project-specific config
- **You always** have final say with direct instructions

## Customizing Global Rules

### For Your Coding Style

Edit `~/.claude/instructions.md` or `~/.cursorrules` to add:

```markdown
## Personal Coding Preferences

- Always use TypeScript over JavaScript
- Prefer functional programming patterns
- Use descriptive variable names (no single letters)
- Add JSDoc comments for all functions
```

### For Your Organization

If your company has coding standards:

```markdown
## Company Standards

- Follow [Company] Style Guide
- Use [Company] commit message format
- Always add copyright headers
- Reference ticket numbers in commits
```

### For Specific Languages

```markdown
## Language-Specific Rules

### Python
- Use type hints (PEP 484)
- Follow PEP 8 style guide
- Use Black for formatting

### JavaScript/TypeScript
- Use ESLint recommended rules
- Prefer const over let
- Use async/await over promises

### Rust
- Follow Rust API guidelines
- Use clippy recommendations
- Document all public APIs
```

## Maintenance

### Updating Global Rules

```bash
# Edit Claude Code global config
vim ~/.claude/instructions.md

# Edit Cursor global config
vim ~/.cursorrules

# Changes apply to NEW conversations immediately
# For existing conversations, restart or remind AI
```

### Sharing with Team

If your team wants the same global config:

```bash
# Save your config to a shared location
cp ~/.claude/instructions.md ~/shared/global-claude-instructions.md
cp ~/.cursorrules ~/shared/global-cursorrules.md

# Team members can install:
cp ~/shared/global-claude-instructions.md ~/.claude/instructions.md
cp ~/shared/global-cursorrules ~/.cursorrules
```

## Troubleshooting

### AI Not Following Global Rules?

**Claude Code:**
1. Check file exists: `cat ~/.claude/instructions.md`
2. Check it's readable: `ls -la ~/.claude/instructions.md`
3. Start new conversation
4. Explicitly remind: "Follow global coding rules"

**Cursor:**
1. Check file exists: `cat ~/.cursorrules`
2. Completely restart Cursor
3. In chat: "Follow .cursorrules"

### Global Rules Being Ignored?

This can happen if:
1. Project has its own `.claude/instructions.md` or `.cursorrules` that override
2. Conversation is very long (AI may drift)
3. User instructions contradict global rules

**Solution**: Periodically remind AI to follow rules every 5-10 exchanges.

## Next Steps

1. ✅ Global configuration is complete
2. → Set up project-specific configuration (see `PROJECT_SETUP.md`)
3. → Install git hooks for automated enforcement
4. → Customize validation commands for your projects

## Related Documentation

- **PROJECT_SETUP.md** - Project-specific configuration
- **../AI_SETUP_GUIDE.md** - Complete setup overview
- **../AI_CODING_GUIDELINES.md** - Detailed workflow guide
- **../CRITICAL_RULES.md** - Quick reference

---

**Congratulations!** Your global AI coding assistant configuration is complete. All projects will now benefit from consistent AI behavior.
