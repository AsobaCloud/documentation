# Git Hooks Templates

This directory contains git hook templates for automated validation enforcement.

## What Are Git Hooks?

Git hooks are scripts that run automatically before or after git commands (like commit, push). They enforce quality standards mechanically, preventing mistakes before they enter the repository.

## Available Hooks

### pre-commit
Runs **before** a commit is created.

**Purpose**: Validate code quality before allowing commit
- Run tests
- Run linters
- Run type checkers
- Run security scanners
- Check code formatting

**When it runs**: Before `git commit` completes
**Effect if fails**: Commit is rejected

### commit-msg
Runs **before** a commit message is finalized.

**Purpose**: Enforce commit message format
- Require issue/ticket references (#123, PROJ-456)
- Enforce conventional commit format (feat:, fix:, etc.)
- Ensure descriptive messages
- Check message length

**When it runs**: After you write commit message, before commit is created
**Effect if fails**: Commit is rejected

## Installation

### Step 1: Customize the Hooks

Edit the hook files to match your project:

**pre-commit**:
```bash
# Edit hooks-template/pre-commit
vim hooks-template/pre-commit

# Uncomment and customize validation commands for your project:
# - Node.js: npm test && npm run lint
# - Python: pytest && flake8
# - Rust: cargo test && cargo clippy
# - Go: go test ./... && golangci-lint run
```

**commit-msg**:
```bash
# Edit hooks-template/commit-msg
vim hooks-template/commit-msg

# Choose and uncomment one option:
# - Option 1: Require issue reference (e.g., #123)
# - Option 2: Enforce conventional commits (e.g., feat:, fix:)
# - Option 3: Both
# - Option 4: Custom format
```

### Step 2: Copy to .git/hooks/

```bash
# Copy hooks to git hooks directory
cp hooks-template/pre-commit .git/hooks/pre-commit
cp hooks-template/commit-msg .git/hooks/commit-msg

# Make them executable
chmod +x .git/hooks/pre-commit .git/hooks/commit-msg

# Verify
ls -la .git/hooks/pre-commit .git/hooks/commit-msg
```

### Step 3: Test the Hooks

**Test pre-commit**:
```bash
# Create a test file
echo "test" > test.txt
git add test.txt

# Try to commit (should run validation)
git commit -m "test: testing hooks"

# Clean up
git reset HEAD test.txt
rm test.txt
```

**Test commit-msg**:
```bash
# Try committing with wrong format
echo "test" > test.txt
git add test.txt
git commit -m "bad format"

# Should fail if validation is configured
# Clean up
git reset HEAD test.txt
rm test.txt
```

## Customization Examples

### Example 1: Node.js/JavaScript Project

**pre-commit**:
```bash
if [ -f package.json ]; then
    echo "→ Running tests..."
    if ! npm test; then
        echo "❌ Tests failed"
        exit 1
    fi

    echo "→ Running linter..."
    if ! npm run lint; then
        echo "❌ Linter failed"
        exit 1
    fi
fi
```

**commit-msg**:
```bash
# Require conventional commits with GitHub issue
if ! echo "$commit_msg" | grep -qE '^(feat|fix|docs|style|refactor|test|chore): .+ \(#[0-9]+\)'; then
    echo "❌ Format: 'type: description (#123)'"
    exit 1
fi
```

### Example 2: Python Project

**pre-commit**:
```bash
if [ -f requirements.txt ]; then
    echo "→ Running tests..."
    if ! pytest; then
        echo "❌ Tests failed"
        exit 1
    fi

    echo "→ Running flake8..."
    if ! flake8 .; then
        echo "❌ Linter failed"
        exit 1
    fi

    echo "→ Running mypy..."
    if ! mypy .; then
        echo "❌ Type checking failed"
        exit 1
    fi
fi
```

**commit-msg**:
```bash
# Require Jira ticket reference
if ! echo "$commit_msg" | grep -qE '\([A-Z]+-[0-9]+\)'; then
    echo "❌ Format: 'description (PROJ-123)'"
    exit 1
fi
```

### Example 3: Multi-Language Project

**pre-commit**:
```bash
# Run different checks based on what files changed
changed_files=$(git diff --cached --name-only)

if echo "$changed_files" | grep -q '\.py$'; then
    echo "→ Python files changed, running pytest..."
    pytest
fi

if echo "$changed_files" | grep -q '\.js$\|\.ts$'; then
    echo "→ JavaScript files changed, running npm test..."
    npm test
fi

if echo "$changed_files" | grep -q '\.rs$'; then
    echo "→ Rust files changed, running cargo test..."
    cargo test
fi
```

## Bypassing Hooks (Use Sparingly!)

Sometimes you need to bypass hooks (e.g., work-in-progress commits):

```bash
# Bypass all hooks
git commit --no-verify -m "WIP: work in progress"

# WARNING: Only use for WIP commits, never for production code!
```

## Troubleshooting

### Hooks Not Running?

```bash
# Check hooks exist
ls -la .git/hooks/pre-commit .git/hooks/commit-msg

# Check they're executable
chmod +x .git/hooks/pre-commit .git/hooks/commit-msg

# Check for errors
.git/hooks/pre-commit  # Run manually
```

### Hooks Failing?

```bash
# Run the failing command manually to debug
npm test
npm run lint

# Check hook script for errors
bash -x .git/hooks/pre-commit
```

### Different Behavior on Team Members' Machines?

Git hooks are **not** tracked in version control (.git/hooks/ is local).

**Solution**: Keep templates in `hooks-template/` and document installation:
```bash
# Add to README.md or onboarding docs:
cp hooks-template/* .git/hooks/
chmod +x .git/hooks/pre-commit .git/hooks/commit-msg
```

## Advanced: Shared Hooks with Husky (Node.js)

For Node.js projects, consider using [Husky](https://typicode.github.io/husky/) to share hooks via package.json:

```bash
npm install --save-dev husky
npx husky init
npx husky add .husky/pre-commit "npm test"
npx husky add .husky/commit-msg 'npx --no -- commitlint --edit "$1"'
```

## Integration with AI Coding Guidelines

These hooks enforce the same rules as:
- `.claude/instructions.md` (step 5: VALIDATE)
- `.cursorrules` (step 5: VALIDATE)
- `AI_CODING_GUIDELINES.md` (Validation Requirements)

By using hooks, validation happens automatically even when developers forget!

## Related Documentation

- **AI_SETUP_GUIDE.md** - Overall setup instructions
- **AI_CODING_GUIDELINES.md** - Validation requirements
- **.claude/instructions.md** - Claude Code validation step
- **.cursorrules** - Cursor validation step

---

**Remember**: Hooks are your last line of defense against committing code that doesn't meet standards. Customize them for your project and keep them updated!
