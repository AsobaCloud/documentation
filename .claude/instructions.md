# Project-Specific Claude Code Instructions

**IMPORTANT**: Read AI_CODING_GUIDELINES.md at the start of EVERY coding session.

## MANDATORY WORKFLOW FOR ALL CODE CHANGES

### 1. EXPLORE (Required First Step):
- Read project documentation (README.md, docs/, etc.)
- Search codebase for existing similar functions/patterns
- Identify what already exists that can be reused
- Never create functions without searching first

### 2. PLAN (Document Everything):
- List exact files to modify
- List exact functions to add/modify/delete
- Explain minimal changes needed
- Present structured plan to user

### 3. CONFIRM (MANDATORY STOP POINT):
- Present complete plan to user
- WAIT for explicit approval ("Yes", "Approved", "Proceed")
- DO NOT proceed to CODE phase without approval
- This is NON-NEGOTIABLE

### 4. CODE (Only After Approval):
- Make ONLY the approved changes
- Modify existing functions rather than creating duplicates
- Maintain existing patterns and architecture

### 5. VALIDATE (All Must Pass):
- Run project test suite
- Run linters and safety checkers
- Fix ALL warnings before proceeding
- Never ignore or bypass validation

### 6. COMMIT (Only After Validation):
- Use clear, descriptive commit messages
- Reference issue numbers if project uses issue tracking
- Follow project commit message conventions

## Non-Negotiable Rules:

### 1. EXPLORE BEFORE CODING
- Search for existing functions before creating new ones
- Read project documentation before starting work
- Use Grep/Glob to find similar implementations

### 2. PLAN THEN CONFIRM
- Present exact plan: files to modify, functions to change
- WAIT for explicit user approval before writing any code
- MANDATORY STOP POINT - never skip

### 3. NO DUPLICATE FUNCTIONS
- Search codebase thoroughly before creating functions
- Modify existing functions instead of duplicating
- Maintain single source of truth

### 4. MINIMAL CHANGES ONLY
- Make only necessary changes
- Avoid over-engineering
- Keep solutions simple and focused

### 5. FOLLOW PROJECT CONVENTIONS
- Match existing code style and patterns
- Use established naming conventions
- Maintain architectural consistency

### 6. VALIDATE BEFORE COMMIT
- Run all tests and linters
- Fix ALL warnings
- Verify functionality works as expected

---

**Customization Instructions**:
1. Update validation commands in step 5 with your project's actual test/lint commands
2. Adjust commit message format in step 6 to match your project conventions
3. Add project-specific rules below this line

---

## Project-Specific Additions

[Add any project-specific rules, patterns, or requirements here]

Example:
- Always update CHANGELOG.md when adding features
- Run `npm test` before committing
- Use conventional commit format: "type: description"
- Reference GitHub issues in commits: (#123)
