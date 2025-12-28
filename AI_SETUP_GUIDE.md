# AI Coding Assistant Setup Guide

This guide helps you set up AI coding assistants (Claude Code, Cursor) to follow consistent coding guidelines.

## Overview

This repository includes configuration for:
- **Claude Code** - Anthropic's official CLI tool
- **Cursor** - AI-powered IDE
- **Git Hooks** - Automated validation enforcement

The setup has two levels:
1. **Global Configuration** - Applies to ALL projects on your computer
2. **Project Configuration** - Specific to this project (already included)

## Quick Start (5 Minutes)

### 1. Set Up Global Configuration

Run these commands to set up rules for ALL projects:

```bash
# Create global directories
mkdir -p ~/.claude ~/.cursor

# Copy global templates (customize paths as needed)
cp docs/ai-coding-setup/global-claude-instructions.md ~/.claude/instructions.md
cp docs/ai-coding-setup/global-cursorrules.md ~/.cursorrules

# Verify files were created
ls -la ~/.claude/instructions.md ~/.cursorrules
```

### 2. Set Up Git Hooks (This Project)

```bash
# Copy hooks from templates
cp hooks-template/pre-commit .git/hooks/pre-commit
cp hooks-template/commit-msg .git/hooks/commit-msg

# Make them executable
chmod +x .git/hooks/pre-commit .git/hooks/commit-msg

# Verify
ls -la .git/hooks/pre-commit .git/hooks/commit-msg
```

### 3. Customize for Your Project

Edit the validation commands in these files:

**`.claude/instructions.md`** - Step 5:
```bash
vim .claude/instructions.md
# Update validation commands with your actual test/lint commands
```

**`.cursorrules`** - Step 5:
```bash
vim .cursorrules
# Update validation commands
```

**`hooks-template/pre-commit`**:
```bash
vim hooks-template/pre-commit
# Update with your actual validation commands
```

### 4. Test the Setup

**Test Claude Code:**
```bash
# Start Claude Code
claude

# Ask: "What workflow should you follow before writing code?"
# Should mention: EXPLORE → PLAN → CONFIRM → CODE → VALIDATE
```

**Test Cursor:**
```bash
# Open project in Cursor
cursor .

# In chat (Cmd+L), ask: "What workflow should you follow?"
# Should mention the same workflow
```

**Test Git Hooks:**
```bash
# Try committing without proper format
echo "test" > test.txt
git add test.txt
git commit -m "test"

# Should fail if hooks are working
# Clean up:
git reset HEAD test.txt && rm test.txt
```

## Configuration Files Explained

### Global Configuration (All Projects)

**Claude Code:**
- `~/.claude/instructions.md` - Universal workflow rules

**Cursor:**
- `~/.cursorrules` - Universal workflow rules

**Purpose**: Ensures consistent AI behavior across all your projects.

### Project Configuration (This Repo)

**Claude Code:**
- `.claude/instructions.md` - Project-specific rules
- `.claude/README.md` - Documentation

**Cursor:**
- `.cursorrules` - Project-specific rules

**Shared:**
- `AI_CODING_GUIDELINES.md` - Comprehensive guidelines (both tools reference)
- `CRITICAL_RULES.md` - Quick reference for 6 core rules

**Git Hooks:**
- `hooks-template/pre-commit` - Runs validation before commits
- `hooks-template/commit-msg` - Enforces commit message format

**Purpose**: Project-specific customizations that override global rules.

## The Six Critical Rules

Both Claude Code and Cursor are configured to follow:

1. **EXPLORE BEFORE CODING** - Search for existing code first
2. **PLAN THEN CONFIRM** - Present plan and WAIT for approval (MANDATORY)
3. **NO DUPLICATE FUNCTIONS** - Modify existing, don't create new
4. **MINIMAL CHANGES ONLY** - Only what's necessary
5. **VALIDATE BEFORE COMMIT** - All tests/linters must pass
6. **CLEAR COMMIT MESSAGES** - Follow project conventions

See `CRITICAL_RULES.md` for full details.

## Customization Checklist

When using this template for a new project:

- [ ] Update validation commands in `.claude/instructions.md`
- [ ] Update validation commands in `.cursorrules`
- [ ] Update validation commands in `hooks-template/pre-commit`
- [ ] Customize commit message format if needed
- [ ] Add project-specific rules to `AI_CODING_GUIDELINES.md`
- [ ] Update git hooks and copy to `.git/hooks/`
- [ ] Test that AI assistants follow the rules
- [ ] Test that git hooks work

## Usage Tips

### Starting a Coding Session

**Claude Code:**
```
"Follow CRITICAL_RULES.md and AI_CODING_GUIDELINES.md strictly"
```

**Cursor:**
```
Open chat (Cmd+L):
"Follow .cursorrules and AI_CODING_GUIDELINES.md strictly"
```

### During Long Sessions

Every 5-10 exchanges, checkpoint:
```
Checkpoint:
- Searched for existing functions?
- Presented plan and got approval?
- Following the workflow?
```

### When AI Skips CONFIRM Step

```
STOP. Present your plan and wait for approval.
This is MANDATORY per CRITICAL_RULES.md.
```

## Troubleshooting

### AI Not Following Rules?

**Claude Code:**
1. Check file exists: `cat ~/.claude/instructions.md`
2. Check project file: `cat .claude/instructions.md`
3. Start new conversation
4. Explicitly remind: "Follow CRITICAL_RULES.md exactly"

**Cursor:**
1. Check file exists: `cat ~/.cursorrules`
2. Completely restart Cursor
3. In chat: "Follow .cursorrules exactly"

### Git Hooks Not Running?

```bash
# Verify hooks exist and are executable
ls -la .git/hooks/pre-commit .git/hooks/commit-msg

# Make them executable if needed
chmod +x .git/hooks/pre-commit .git/hooks/commit-msg

# Test manually
.git/hooks/pre-commit
```

### Validation Commands Failing?

Update the commands in:
1. `.claude/instructions.md` (step 5)
2. `.cursorrules` (step 5)
3. `hooks-template/pre-commit`
4. Copy updated hooks to `.git/hooks/`

## Advanced Setup

### Global Setup for Your Organization

To standardize across your entire team:

1. **Share global templates**:
   ```bash
   # Create a shared repository with:
   # - global-claude-instructions.md
   # - global-cursorrules.md

   # Team members run:
   curl -o ~/.claude/instructions.md https://your-org/global-claude-instructions.md
   curl -o ~/.cursorrules https://your-org/global-cursorrules.md
   ```

2. **Use this template repository**:
   - Make it a GitHub template repo
   - Team creates new projects from template
   - Gets AI configuration automatically

3. **Enforce with CI/CD**:
   - Add validation to CI pipeline
   - Ensures all commits follow standards
   - See `hooks-template/` for examples

## Related Documentation

- **AI_CODING_GUIDELINES.md** - Comprehensive workflow guide
- **CRITICAL_RULES.md** - Quick reference for 6 core rules
- **.claude/README.md** - Claude Code configuration details
- **hooks-template/README.md** - Git hooks documentation
- **docs/ai-coding-setup/** - Detailed setup guides

## Support

If you encounter issues:
1. Check the troubleshooting section above
2. Review the related documentation
3. Verify files exist and are readable
4. Test with a simple coding task

---

**Last Updated**: 2025-12-27
**Compatible With**: Claude Code, Cursor AI
