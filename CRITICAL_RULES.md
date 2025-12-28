# CRITICAL RULES - ZERO TOLERANCE FOR VIOLATIONS

These rules override all other considerations. Follow them exactly.

## The Six Commandments:

### 1. **EXPLORE BEFORE CODING**
- Search for existing functions/patterns before creating new ones
- Read project documentation before starting work
- Use search tools (Grep, Cmd+Shift+F) to find similar implementations
- Understand existing architecture first

### 2. **PLAN THEN CONFIRM**
- Present exact plan: files to modify, functions to change
- WAIT for explicit user approval before writing any code
- This is a MANDATORY STOP POINT - do not proceed without approval
- Show detailed plan with specific changes

### 3. **NO DUPLICATE FUNCTIONS**
- Search codebase thoroughly before creating functions
- Modify existing functions instead of creating duplicates
- Reuse existing utilities and components
- Maintain single source of truth

### 4. **MINIMAL CHANGES ONLY**
- Make only the changes necessary to accomplish the task
- Avoid over-engineering or adding "nice to have" features
- Keep solutions simple and focused
- Don't refactor unrelated code

### 5. **VALIDATE BEFORE COMMIT**
- Run all tests and linters
- Fix ALL warnings and errors
- Verify functionality works as expected
- Never bypass validation checks

### 6. **CLEAR COMMIT MESSAGES**
- Use descriptive, clear commit messages
- Follow project commit message conventions
- Reference issues/tickets if applicable
- Format: `type: description` or `type: description (#issue)`

---

## Quick Reference Card

```
Before ANY code:
├─ 1. EXPLORE: Search for existing code
├─ 2. PLAN: List files + functions to change
├─ 3. CONFIRM: Present plan → WAIT for approval ⛔ STOP POINT
├─ 4. CODE: Implement approved changes only
├─ 5. VALIDATE: Run tests + linters
└─ 6. COMMIT: Clear message + issue reference
```

---

## When AI Violates These Rules

**If AI tries to code without approval:**
```
STOP. Present your plan and wait for my approval.
This is MANDATORY per CRITICAL_RULES.md.
```

**If AI creates duplicate functions:**
```
Did you search for existing functions first?
Check CRITICAL_RULES.md rule #3.
```

**If AI skips validation:**
```
Run all tests and linters before proceeding.
See CRITICAL_RULES.md rule #5.
```

---

## Customization

These rules are universal and apply to all projects. However, you can customize:

1. **Rule #5 - Validation Commands**:
   Update with your project's actual commands:
   ```bash
   npm test && npm run lint    # Node.js
   pytest && flake8            # Python
   cargo test && cargo clippy  # Rust
   ```

2. **Rule #6 - Commit Format**:
   Specify your project's commit message convention:
   ```bash
   # Conventional Commits
   feat: add user authentication

   # With issue reference (GitHub)
   fix: resolve timeout error (#123)

   # With ticket reference (Jira)
   feat: add export feature (PROJ-456)
   ```

---

**Full details in AI_CODING_GUIDELINES.md - but these 6 rules are non-negotiable.**
