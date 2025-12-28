# Claude Code Project Configuration

This directory contains project-specific instructions for Claude Code AI assistant.

## How It Works

Claude Code automatically reads `.claude/instructions.md` and uses it to guide its behavior when working in this project.

## Files

- **instructions.md** - Project-specific workflow rules and guidelines
  - Loaded automatically by Claude Code
  - Overrides global `~/.claude/instructions.md` settings
  - Focuses on EXPLORE > PLAN > CONFIRM > CODE > VALIDATE workflow

## Customization

### 1. Update Validation Commands
Edit `instructions.md` step 5 with your actual commands:
```markdown
### 5. VALIDATE (All Must Pass):
- npm test                    # Your test command
- npm run lint                # Your linter
- python -m pytest            # Or your testing framework
```

### 2. Add Project-Specific Rules
Add project-specific requirements at the bottom of `instructions.md`:
```markdown
## Project-Specific Additions

- Always run database migrations after schema changes
- Update API documentation when adding endpoints
- Run security scanner before deploying
```

### 3. Configure Issue Tracking
If your project uses GitHub issues, Jira, etc., specify the format:
```markdown
### 6. COMMIT (Only After Validation):
- Reference GitHub issues: "fix: description (#123)"
- Reference Jira tickets: "feat: description (PROJ-456)"
```

## Integration with Global Rules

Claude Code loads instructions in this order:
1. **Global**: `~/.claude/instructions.md` (applies to all projects)
2. **Project**: `.claude/instructions.md` (this file - overrides global)
3. **Guidelines**: `AI_CODING_GUIDELINES.md` (if present in project)
4. **User**: Direct instructions in conversation

## For Team Setup

When team members clone this repo:
1. They get project-specific rules automatically
2. They should also set up global rules (see `docs/ai-coding-setup/GLOBAL_SETUP.md`)
3. Claude Code will follow both global and project rules

## Related Files

- `AI_CODING_GUIDELINES.md` - Comprehensive project guidelines
- `CRITICAL_RULES.md` - Quick reference for 6 core rules
- `.cursorrules` - Cursor AI configuration (same workflow)
- `hooks-template/` - Git hooks for automated enforcement
