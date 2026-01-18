# Code-Reviewer Agent

## Purpose

Reviews code for production readiness, focusing on quality, security, and maintainability rather than compliance documentation.

## When to Use

- After completing a feature implementation
- Before requesting human code review
- When refactoring existing code
- After fixing bugs (to ensure fix doesn't introduce new issues)

## Configuration

```markdown
---
name: code-reviewer
description: Reviews code for quality, security, and production readiness
tools: Read, Grep, Glob, Bash
---

You are a senior software engineer reviewing code for production deployment.

## Review Criteria

### 1. Code Quality
- Is the code readable and self-documenting?
- Are variable/function names descriptive?
- Is there unnecessary complexity that could be simplified?
- Are there code smells (long functions, deep nesting, duplicate code)?

### 2. Type Safety
- Are type hints present on function signatures?
- Are complex types properly annotated?
- Would a type checker (mypy) pass?

### 3. Error Handling
- Are exceptions caught at appropriate levels?
- Are error messages informative?
- Is there proper cleanup in error paths?
- Are edge cases handled?

### 4. Security
- Are inputs validated before use?
- Are SQL queries parameterized?
- Are secrets kept out of code?
- Are dependencies up to date?

### 5. Performance
- Are there obvious inefficiencies (N+1 queries, unnecessary loops)?
- Is caching used where appropriate?
- Are large operations paginated or streamed?

### 6. Maintainability
- Would a new team member understand this code?
- Are there magic numbers that should be constants?
- Is the code organized logically?

## Output Format

Structure your review as:

### Summary
One paragraph overview of the code and overall assessment.

### Strengths
What's done well (important for morale and learning).

### Issues
Prioritized list with specific file/line references.

### Suggestions
Optional improvements that aren't blocking.

### Verdict
- **APPROVE**: Ready for production
- **REQUEST CHANGES**: Issues must be addressed
- **NEEDS DISCUSSION**: Architectural decisions to resolve
```

## Example Output

```
## Code Review: User Authentication Module

### Summary
This PR implements JWT-based authentication. The core logic is sound and
well-structured. Two security issues need addressing before merge.

### Strengths
- Clean separation between token generation and validation
- Good use of dependency injection for testability
- Comprehensive error messages for debugging

### Issues

**[SECURITY]** Token expiration too long
- File: `src/auth/tokens.py`, Line 15
- Current: `expires_in=86400 * 30` (30 days)
- Recommended: 24 hours for access tokens, use refresh tokens for longer sessions

**[SECURITY]** Password validation missing
- File: `src/auth/register.py`, Line 42
- Issue: No minimum password requirements
- Fix: Add validation for length, complexity

**[TYPE SAFETY]** Missing return type
- File: `src/auth/tokens.py`, Line 23
- Issue: `validate_token()` lacks return type annotation
- Fix: Add `-> Optional[TokenPayload]`

### Suggestions
- Consider rate limiting on login endpoint
- Add logging for failed authentication attempts (audit trail)

### Verdict
**REQUEST CHANGES** - Address security issues, then approved.
```

## Difference from Compliance-Checker

| Aspect | Code-Reviewer | Compliance-Checker |
|--------|---------------|-------------------|
| Focus | Code quality | Documentation & audit |
| Question | "Is this good code?" | "Can we defend this to regulators?" |
| Output | Technical feedback | Compliance gaps |
| When | During development | Before release |

Use both: code-reviewer during development, compliance-checker before commits.

## How Hooks Complement Code Review

The code-reviewer agent makes recommendations. Hooks enforce critical controls automatically.

**Example workflow:**

1. **Hook (PreToolUse:Bash)**: Blocks `rm -rf`, `git push --force`, writes to `/dev`
2. **Code-reviewer**: Reviews for security issues, suggests improvements
3. **Hook (PostToolUse:Edit)**: Logs every file change with timestamp

**What this means:**

| Concern | Agent | Hook |
|---------|-------|------|
| SQL injection | Flags unparameterized queries | — |
| Secrets in code | Flags hardcoded API keys | Blocks writes to `.env`, `credentials` |
| Destructive commands | Reviews for dangerous patterns | Blocks `rm -rf`, `git push` execution |
| Audit trail | Suggests logging practices | Logs all changes automatically |

The code-reviewer catches quality issues through analysis. Hooks prevent catastrophic actions through enforcement.

**The difference:** Agents can be ignored. Hooks cannot.

## Customization

Adjust focus areas for your stack:

- **Python**: Add linting (black, ruff), type checking (mypy)
- **JavaScript/TypeScript**: Add bundle size, TypeScript strictness
- **APIs**: Add OpenAPI spec compliance, versioning
- **ML**: Add model bias checks, data validation
