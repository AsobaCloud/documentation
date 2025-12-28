# AI Coding Guidelines - Project Template

> **Customization Required**: Replace [PROJECT_NAME], [ISSUE_TRACKER], and validation commands with your project-specific values.

## Core Principles

### 1. EXPLORE > PLAN > CONFIRM > CODE > VALIDATE > COMMIT
**NEVER write code without following this exact sequence:**

1. **EXPLORE**:
   - Read project documentation (README.md, docs/, architecture docs)
   - Thoroughly examine the codebase to understand existing patterns and architecture
   - Search for similar implementations to understand established patterns

2. **PLAN**:
   - Write out a detailed plan of what needs to be changed and how
   - List specific files and functions to modify

3. **CONFIRM**:
   - Present the exact plan to the user
   - Get explicit approval before proceeding
   - **MANDATORY STOP POINT**

4. **CODE**:
   - Implement only the minimal changes necessary
   - Follow approved plan exactly

5. **VALIDATE**:
   - Run tests and linters
   - Verify functionality works as expected

6. **COMMIT**:
   - Use clear, descriptive commit messages
   - Follow project conventions

### 2. Codebase Continuity
- **Thoroughly examine similar areas of the codebase** to ensure your approach fits seamlessly with established patterns
- **Make only minimal and necessary changes**, avoiding disruption to existing design
- **Reuse existing components, utilities, and logic** to maintain consistency and reduce duplication

### 3. Function Management
- **ALWAYS search for existing functions before creating new ones**
- **MODIFY existing functions instead of duplicating them**
- **Use proper search tools to find existing implementations**
- **Maintain single source of truth**

### 4. Validation Requirements
- **Run project test suite before committing**
- **Address ALL linter warnings and errors**
- **Test functionality after changes**
- **Commit changes only after validation passes**

### 5. Issue-Driven Development (Optional - Customize for Your Project)

**If your project uses issue tracking ([ISSUE_TRACKER]):**

- Create or reference an issue for all significant work
- Reference issue numbers in commits (e.g., "fix: resolve bug (#123)")
- Update issue status as work progresses
- Close issues only after deployment and verification

**If you don't use issue tracking, remove this section.**

## Pre-Code Checklist

Before writing ANY code, answer these questions:

1. **Have I read the relevant documentation?**
2. **What already exists?** (Search the codebase)
3. **What needs to be changed?** (Identify specific functions/files)
4. **How does this fit with existing patterns?**
5. **What's the minimal change needed?**
6. **Have I checked for duplicates?**

## Human Decision Gate

**CRITICAL**: After completing the PLAN phase, you MUST:

1. **Present the exact plan** to the user with specific details
2. **List all files** that will be modified
3. **List all functions** that will be added/modified/removed
4. **STOP AND WAIT** for explicit user approval
5. **Do not make additional changes** beyond what was approved

**Example:**
```
PLAN COMPLETE - Awaiting Approval:

Files to modify:
- src/components/Button.tsx
- src/utils/validation.ts

Functions to modify:
- validateInput() in validation.ts (add email validation)
- Button component (add loading state)

Changes:
1. Add email validation regex to validateInput()
2. Add isLoading prop to Button component
3. Update Button styles for loading state
4. Add tests for new functionality

Do you approve these exact changes? (Yes/No)
```

## Code Quality Standards

- **No duplicate functions** - Always modify existing functions
- **Consistent naming** - Follow established patterns
- **Proper error handling** - Use try/catch blocks where appropriate
- **Clean code** - Avoid unnecessary complexity
- **Documentation** - Comment complex logic

## Validation Protocol

**Customize these commands for your project:**

1. **Run Tests**:
   ```bash
   # Replace with your test command:
   npm test              # Node.js/JavaScript
   pytest                # Python
   cargo test            # Rust
   go test ./...         # Go
   ```

2. **Run Linter**:
   ```bash
   # Replace with your linter:
   npm run lint          # Node.js/JavaScript
   flake8 .              # Python
   cargo clippy          # Rust
   golangci-lint run     # Go
   ```

3. **Type Checking** (if applicable):
   ```bash
   # Replace with your type checker:
   tsc --noEmit          # TypeScript
   mypy .                # Python
   ```

4. **Integration Tests** (if applicable):
   ```bash
   # Add your integration test command
   ```

5. **Verify**: Functionality works as expected

6. **Commit**: Only after all checks pass

## Commit Message Format

**Customize for your project conventions:**

```bash
# Conventional Commits format (recommended):
type(scope): description

# Types: feat, fix, docs, style, refactor, test, chore
# Examples:
feat(auth): add password reset functionality
fix(api): handle null response from database
docs(readme): update installation instructions

# With issue reference (if using issue tracking):
fix(api): handle timeout errors (#123)
```

## Common Anti-Patterns to Avoid

- ❌ Adding new functions without checking if they exist
- ❌ Ignoring linter warnings
- ❌ Making changes without understanding existing code
- ❌ Over-engineering simple solutions
- ❌ Breaking existing functionality
- ❌ Creating duplicate code
- ❌ Skipping the CONFIRM step

## Success Metrics

- ✅ All tests pass
- ✅ No linter warnings
- ✅ No duplicate function definitions
- ✅ Minimal code changes
- ✅ Existing functionality preserved
- ✅ New functionality works as expected
- ✅ Code follows established patterns
- ✅ Clear commit messages

---

## Customization Checklist

When setting up this template for your project:

- [ ] Replace [PROJECT_NAME] with your actual project name
- [ ] Replace [ISSUE_TRACKER] with your issue tracker (GitHub, Jira, Linear, etc.) or remove section
- [ ] Update validation commands (section "Validation Protocol")
- [ ] Update commit message format to match your conventions
- [ ] Add project-specific rules below
- [ ] Update .claude/instructions.md with same validation commands
- [ ] Update .cursorrules with same validation commands
- [ ] Set up git hooks from hooks-template/ directory

---

## Project-Specific Rules

[Add any project-specific rules here]

Examples:
- Always update CHANGELOG.md when adding features
- Run database migrations after schema changes
- Update API documentation when adding endpoints
- Notify team in Slack before deploying to production
- Use feature flags for experimental features

---

**Remember: The goal is to maintain and enhance the existing system with minimal, well-tested changes. The CONFIRM step ensures alignment before implementation.**
