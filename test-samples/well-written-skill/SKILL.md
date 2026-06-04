---
name: example-good-skill
description: Generate consistent and well-formatted commit messages from staged changes following conventional commits standard. Use when preparing commits, writing changelogs, or enforcing commit conventions. Triggers: "write commit message", "generate commit", "conventional commit", "commit format".
---

# Commit Message Generator

Generates conventional commit messages by analyzing staged git changes.

## When to Use

**Use this when:**
- Preparing a commit and need a properly formatted message
- Enforcing conventional commit standards
- Generating changelog entries

**Do NOT use when:**
- No staged changes exist
- You need to review code (use code review tools)

## Workflow

1. **Check for staged changes** — Run `git diff --cached --stat` to verify there are staged files
2. **Analyze changes** — Review the diff to understand what changed and why
3. **Determine type** — Classify the change (feat/fix/docs/refactor/test/chore)
4. **Write message** — Format as `type(scope): description`
5. **Output** — Present the commit message for user approval

## Examples

### Example 1: Feature commit

**Input:** User has staged changes adding a login endpoint.

**Expected Output:**
```
feat(auth): add login endpoint with JWT token generation
```

### Example 2: Bug fix commit

**Input:** User has staged changes fixing a null pointer in user profile.

**Expected Output:**
```
fix(profile): handle null pointer when user has no avatar
```
