# Project-Specific AI Configuration Setup

This guide shows you how to customize AI coding assistant behavior for a specific project.

## When to Use Project-Specific Config

Use project-specific configuration when:
- Project has unique validation requirements (specific tests, linters)
- Project uses issue tracking (GitHub Issues, Jira, Linear)
- Project has specific commit message format
- Project has architectural patterns to follow
- Team wants consistent AI behavior for this project

## What's Included in This Template

This repository already includes project-specific configuration:

```
.claude/
├── instructions.md    # Claude Code project rules
└── README.md         # Documentation

.cursorrules          # Cursor project rules

AI_CODING_GUIDELINES.md   # Comprehensive guidelines
CRITICAL_RULES.md         # Quick reference (6 core rules)

hooks-template/
├── pre-commit        # Git hook for validation
├── commit-msg        # Git hook for commit format
└── README.md         # Hook documentation
```

## Customization Steps

### Step 1: Update Validation Commands

The template uses placeholder commands. Replace them with your actual commands.

**Edit `.claude/instructions.md`** (Step 5):

```bash
vim .claude/instructions.md

# Find step 5 and replace with your commands:
### 5. VALIDATE (All Must Pass):
- npm test                    # Your test command
- npm run lint                # Your linter
- tsc --noEmit                # TypeScript check (if applicable)
```

**Edit `.cursorrules`** (Step 5):

```bash
vim .cursorrules

# Find step 5 and replace:
```bash
npm test && npm run lint      # Node.js project
pytest && flake8              # Python project
cargo test && cargo clippy    # Rust project
go test ./... && golangci-lint run  # Go project
```

### Step 2: Configure Issue Tracking (Optional)

If your project uses issue tracking:

**Edit `AI_CODING_GUIDELINES.md`**:

```bash
vim AI_CODING_GUIDELINES.md

# Find section 5 "Issue-Driven Development"
# Replace [ISSUE_TRACKER] with your tracker:
# - GitHub Issues
# - Jira
# - Linear
# - Asana
# etc.

# Update commit message format:
## Commit Message Format

**GitHub Issues:**
```
fix: resolve timeout error (#123)
feat: add user authentication (#456)
```

**Jira:**
```
fix: resolve timeout error (PROJ-123)
feat: add user authentication (PROJ-456)
```
```

**Edit `.claude/instructions.md` and `.cursorrules`** (Step 6):

Update the commit message examples to match your format.

### Step 3: Set Up Git Hooks

Git hooks enforce validation automatically.

**Customize hooks**:

```bash
# Edit pre-commit hook
vim hooks-template/pre-commit

# Uncomment the section for your project type:
# - Node.js/JavaScript
# - Python
# - Rust
# - Go
# Or add your own commands

# Example for Node.js:
if [ -f package.json ]; then
    if ! npm test; then
        echo "❌ Tests failed"
        exit 1
    fi
    if ! npm run lint; then
        echo "❌ Linter failed"
        exit 1
    fi
fi
```

```bash
# Edit commit-msg hook
vim hooks-template/commit-msg

# Uncomment the option you want:
# - Option 1: Require issue reference
# - Option 2: Conventional commits
# - Option 3: Both
# - Option 4: Custom

# Example for GitHub issues + conventional commits:
if ! echo "$commit_msg" | grep -qE '^(feat|fix|docs): .+ \(#[0-9]+\)'; then
    echo "❌ Format: 'type: description (#123)'"
    exit 1
fi
```

**Install hooks**:

```bash
# Copy to .git/hooks/
cp hooks-template/pre-commit .git/hooks/pre-commit
cp hooks-template/commit-msg .git/hooks/commit-msg

# Make executable
chmod +x .git/hooks/pre-commit .git/hooks/commit-msg

# Verify
ls -la .git/hooks/pre-commit .git/hooks/commit-msg
```

### Step 4: Add Project-Specific Rules

Add any project-specific requirements to the end of `AI_CODING_GUIDELINES.md`:

```bash
vim AI_CODING_GUIDELINES.md

# Scroll to bottom, add project-specific rules:
## Project-Specific Rules

### Database Changes
- Always create migration files for schema changes
- Test migrations in development before committing
- Document breaking changes in MIGRATIONS.md

### API Changes
- Update OpenAPI spec when adding/modifying endpoints
- Add integration tests for new endpoints
- Version breaking API changes

### Security
- Run security scanner before deploying
- Never commit secrets or credentials
- Use environment variables for configuration

### Documentation
- Update README.md when adding features
- Update CHANGELOG.md with user-facing changes
- Add JSDoc/docstrings for public APIs
```

### Step 5: Test the Configuration

**Test Claude Code**:

```bash
# Start Claude Code in this project
claude

# Ask: "What validation should you run before committing?"
# Should mention your project-specific commands (npm test, etc.)
```

**Test Cursor**:

```bash
# Open project in Cursor
cursor .

# In chat (Cmd+L), ask same question
# Should mention the same validation commands
```

**Test Git Hooks**:

```bash
# Create a test file
echo "test" > test.txt
git add test.txt

# Try committing (hooks should run)
git commit -m "test"

# Should run validation and/or check commit message format
# Clean up:
git reset HEAD test.txt && rm test.txt
```

## Configuration Examples

### Example 1: Node.js/TypeScript Project

**Validation commands**:
```bash
npm test
npm run lint
tsc --noEmit
npm run format:check
```

**Commit format**:
```
feat: add user authentication (#123)
fix: resolve memory leak in API (#456)
```

**Git hooks**:
- pre-commit: Run tests, linter, type check
- commit-msg: Require conventional format + GitHub issue

### Example 2: Python Data Science Project

**Validation commands**:
```bash
pytest tests/
flake8 .
mypy .
black --check .
```

**Commit format**:
```
Add feature: description
Fix bug: description
```

**Git hooks**:
- pre-commit: Run pytest, flake8, mypy, black
- commit-msg: None (no strict format)

### Example 3: Rust CLI Tool

**Validation commands**:
```bash
cargo test
cargo clippy -- -D warnings
cargo fmt --check
```

**Commit format**:
```
feat(cli): add new command
fix(parser): handle edge case
```

**Git hooks**:
- pre-commit: Run tests, clippy, fmt
- commit-msg: Require conventional format with scope

## Team Onboarding

When team members clone this repository:

### What They Get Automatically

✅ Project-specific AI configuration (`.claude/`, `.cursorrules`)
✅ Comprehensive guidelines (`AI_CODING_GUIDELINES.md`)
✅ Quick reference (`CRITICAL_RULES.md`)
✅ Git hook templates (`hooks-template/`)

### What They Need to Do

1. **Set up global config** (one-time, for all projects):
   ```bash
   # See GLOBAL_SETUP.md
   ```

2. **Install git hooks** (for this project):
   ```bash
   cp hooks-template/* .git/hooks/
   chmod +x .git/hooks/pre-commit .git/hooks/commit-msg
   ```

3. **Install dependencies and test**:
   ```bash
   # Install project dependencies
   npm install  # or pip install -r requirements.txt, etc.

   # Test validation works
   npm test
   npm run lint
   ```

## Updating Configuration

### When to Update

Update configuration when:
- Adding new validation tools
- Changing commit message format
- Adding new project-specific rules
- Team adopts new conventions

### How to Update

1. **Update validation commands**:
   ```bash
   vim .claude/instructions.md  # Step 5
   vim .cursorrules             # Step 5
   vim hooks-template/pre-commit
   ```

2. **Update guidelines**:
   ```bash
   vim AI_CODING_GUIDELINES.md
   vim CRITICAL_RULES.md  # If core rules changed
   ```

3. **Reinstall hooks**:
   ```bash
   cp hooks-template/* .git/hooks/
   chmod +x .git/hooks/*
   ```

4. **Notify team**:
   - Commit changes
   - Team pulls updates
   - Team reinstalls hooks

## Troubleshooting

### AI Not Using Project Config?

**Claude Code:**
```bash
# Check project file exists
cat .claude/instructions.md

# Check global config isn't overriding
cat ~/.claude/instructions.md

# Start new conversation
# Explicitly say: "Follow .claude/instructions.md"
```

**Cursor:**
```bash
# Check project file exists
cat .cursorrules

# Reload Cursor window (Cmd+R)
# In chat: "Follow .cursorrules"
```

### Validation Commands Failing?

```bash
# Test commands manually
npm test
npm run lint

# If commands don't exist, add them to package.json:
{
  "scripts": {
    "test": "jest",
    "lint": "eslint ."
  }
}
```

### Git Hooks Not Working?

```bash
# Check hooks are executable
ls -la .git/hooks/pre-commit .git/hooks/commit-msg

# Make executable if needed
chmod +x .git/hooks/pre-commit .git/hooks/commit-msg

# Test manually
.git/hooks/pre-commit
```

## Related Documentation

- **GLOBAL_SETUP.md** - Set up global configuration first
- **../AI_SETUP_GUIDE.md** - Complete setup overview
- **../AI_CODING_GUIDELINES.md** - Full workflow guide
- **../CRITICAL_RULES.md** - Quick reference
- **../hooks-template/README.md** - Git hooks documentation

---

**Your project is now configured for consistent AI coding assistance!**
