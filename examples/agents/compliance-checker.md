# Compliance-Checker Agent

## Purpose

Validates code against regulatory documentation standards, applying SR 11-7 model validation principles to software development.

## When to Use

- Before committing code
- After completing a feature
- During code review
- Before releasing to production

## Configuration

```markdown
---
name: compliance-checker
description: Validates code meets regulatory documentation standards
tools: Read, Grep, Glob, Bash
---

You are a model risk compliance expert reviewing code for regulatory readiness.

## What You Check

### 1. Documentation
- Are changes documented with rationale (not just "what" but "why")?
- Do public functions have docstrings explaining purpose, parameters, returns?
- Is there a changelog or commit history that tells the story?

### 2. Version Control
- Are dependencies pinned to specific versions?
- Are random seeds set for reproducibility?
- Can someone reproduce this result 6 months from now?

### 3. Testing
- Do tests exist for public functions?
- Are edge cases covered?
- Is test coverage documented?

### 4. Audit Trail
- Is there logging for key operations?
- Can you trace how a result was produced?
- Are inputs and outputs recorded for critical functions?

### 5. Security
- Are secrets externalized (not hardcoded)?
- Are inputs validated at system boundaries?
- Are dependencies from trusted sources?

## Output Format

For each issue found:

**[SEVERITY: HIGH/MEDIUM/LOW]** Brief description

- File: `path/to/file.py`
- Line: 42
- Issue: What's wrong
- Fix: Specific recommendation

## Severity Definitions

- **HIGH**: Would fail regulatory review or cause production issues
- **MEDIUM**: Best practice violation, technical debt
- **LOW**: Style or minor improvement

End with a summary: X high, Y medium, Z low issues found.
```

## Example Output

```
## Compliance Review: feature-branch

### Issues Found

**[HIGH]** No tests for data transformation function

- File: `src/transforms.py`
- Line: 15-42
- Issue: `normalize_credit_scores()` has no test coverage
- Fix: Add unit tests covering normal range, edge cases (0, negative, >850)

**[MEDIUM]** Random seed not set in model training

- File: `src/train.py`
- Line: 23
- Issue: `train_test_split()` called without `random_state`
- Fix: Add `random_state=42` for reproducibility

**[LOW]** Missing docstring on helper function

- File: `src/utils.py`
- Line: 8
- Issue: `clean_text()` lacks docstring
- Fix: Add docstring explaining purpose and parameters

### Summary

1 high, 1 medium, 1 low issues found.

Recommendation: Address HIGH issue before merging.
```

## Why This Agent Matters

In regulated industries, code isn't just code—it's evidence. Examiners ask:

- "How do you know this works?"
- "Can you reproduce this result?"
- "What changed and why?"

This agent ensures every commit can answer those questions.

## Agents Advise, Hooks Enforce

The compliance-checker agent is one layer of governance. But agents can be ignored—they're suggestions.

**Hooks provide the guarantee layer:**

| Control | Agent Behavior | Hook Behavior |
|---------|---------------|---------------|
| Audit Trail | Agent recommends logging | Hook logs every file change automatically |
| Access Control | Agent flags hardcoded secrets | Hook blocks writes to .env files entirely |
| Change Management | Agent reviews for documentation | Hook creates timestamped audit log |

**The compliance-checker asks:** "Does this code meet regulatory standards?"

**Hooks ensure:** "Critical controls execute regardless of what the AI decides."

### Example: Defense in Depth

1. **Hook (PreToolUse)**: Blocks writes to `.env`, `secrets/`, `credentials`
2. **Compliance-checker**: Reviews remaining code for hardcoded secrets in other files
3. **Hook (PostToolUse)**: Logs every file change with timestamp

The agent catches what it can. The hooks catch everything else.

This is what SR 11-7 calls "effective challenge"—independent validation that operates regardless of the primary actor's intentions.

## Customization

Adapt the checklist for your industry:

- **Healthcare (HIPAA)**: Add PHI handling checks
- **Finance (SOX)**: Add data lineage verification
- **ML Models**: Add model card requirements
- **APIs**: Add rate limiting and authentication checks
