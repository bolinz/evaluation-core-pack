---
name: eval-skill-quality
description: Evaluate the quality of a Skill definition. Use when reviewing SKILL.md files, assessing skill completeness, or checking skill quality before publishing. Triggers: "evaluate this skill", "check skill quality", "review SKILL.md", "is this skill well-written".
---

# Skill Quality Evaluator

Evaluates a Skill definition across 5 dimensions. Produces a structured quality report with dimension scores, letter grade, and actionable improvement suggestions.

## When to Use

**Use this when:**
- Reviewing a SKILL.md before publishing
- Assessing whether a skill meets quality standards
- Comparing multiple skill implementations

**Do NOT use when:**
- Evaluating architecture or tech choices (use architecture-evaluator or tech-choice-evaluator)
- Evaluating agent runtime behavior (use eval-agent-behavior)

## Evaluation Dimensions

### 1. Description & Discoverability (15%)
Evaluates whether the skill can be found and understood by AI agents.

**Failure modes:** description is too generic (no action verbs), description lists all features (too long), description doesn't match actual content

**Checklist:**
- [ ] description contains action verbs
- [ ] Has 3+ natural trigger phrases
- [ ] Description length 100-300 characters
- [ ] Description matches actual content

### 2. Structure & Documentation (20%)
Evaluates the completeness of SKILL.md structure.

**Failure modes:** missing frontmatter, sections out of order, no examples, code blocks without language tags

**Checklist:**
- [ ] Frontmatter has "name" field
- [ ] Frontmatter has "description" field
- [ ] Has Overview section
- [ ] Has Use Cases section
- [ ] Has Workflow section
- [ ] Has Examples section
- [ ] Has When NOT to Use section
- [ ] Code blocks have language tags
- [ ] Has references/ directory for L3 resources

### 3. Instruction Clarity (25%)
Evaluates how actionable the instructions are.

**Failure modes:** steps skip intermediate states, decision points without criteria, error handling not documented

**Checklist:**
- [ ] Steps are numbered
- [ ] Steps are sequential
- [ ] Each step has expected output
- [ ] Decision points marked with criteria
- [ ] Error handling documented
- [ ] Examples show complete input → output flow

### 4. Security & Safety (25%)
Evaluates security posture and permission declarations.

**L1 Gate: Any critical finding → FAIL**
**Failure modes (critical):** exec() or eval() in skill, hardcoded API keys, file operations without path validation

**Checklist:**
- [ ] Declares minimum required permissions
- [ ] No exec() calls in skill instructions
- [ ] No eval() calls in skill instructions
- [ ] No other dangerous function calls
- [ ] File operations are scoped to allowed paths
- [ ] File operations are validated before execution
- [ ] Network requests are limited to necessary endpoints
- [ ] Network request purpose is explained in skill instructions
- [ ] No hardcoded credentials

### 5. Integration Quality (15%)
Evaluates tool usage efficiency and cross-platform readiness.

**Failure modes:** excessive tool calls, SKILL.md >500 lines, no cross-platform notes

**Checklist:**
- [ ] Tool calls are appropriate for the task
- [ ] Number of tool calls is reasonable (not excessive)
- [ ] Cross-platform adaptations documented
- [ ] SKILL.md size within reasonable bounds
- [ ] L3 resources loaded on-demand via references/

## Workflow

1. **Read SKILL.md** — Load the target skill's SKILL.md file
2. **Check frontmatter** — Verify name and description fields exist
3. **Evaluate each dimension** — For each of the 5 dimensions, run the checklist items and assign a score (1-5)
4. **Check L1 gate** — If Security & Safety has any critical finding, set verdict to reject
5. **Compute overall score** — Weighted average of dimension scores, convert to letter grade
6. **Generate report** — Output structured report with scores, findings, and suggestions

## Examples

### Example 1: Evaluating a well-structured skill

**Input:** A SKILL.md with complete frontmatter, clear workflow, and security declarations.

**Expected Output:**
- All 5 dimensions scored 4+
- Overall score 80-100
- Verdict: approve

### Example 2: Evaluating a poorly-written skill

**Input:** A SKILL.md with missing frontmatter, no When NOT to Use section, and hardcoded credentials.

**Expected Output:**
- Security & Safety score: 1 (L1 fail → REJECT)
- Structure & Documentation score: 2
- Overall score < 50
- Verdict: reject

## Output Format

    ## Skill Quality Report

    ### Target: [skill path/id]

    ### Dimension Scores
    | Dimension | Score (1-5) | Weight | Status |
    |-----------|-------------|--------|--------|
    | Description & Discoverability | X | 15% | PASS/FAIL |
    | Structure & Documentation | X | 20% | PASS/FAIL |
    | Instruction Clarity | X | 25% | PASS/FAIL |
    | Security & Safety | X | 25% | PASS/FAIL |
    | Integration Quality | X | 15% | PASS/FAIL |

    **Status:** PASS = score ≥ 3, FAIL = score < 3

    **Overall Score:** XX/100
    **Letter Grade:** A/B/C/D/F
    **Verdict:** approve / needs_work / reject

    ### Key Findings
    1. [Strength/Issue] - [Detail]

    ### Improvement Suggestions
    1. [Suggestion] - [Rationale]

    ### Failed Checklist Items
    - [ ] [Item] - [Detail]

### Example 3: Evaluating a partially well-written skill

**Input:** A SKILL.md with good frontmatter and workflow but missing security declarations and no When NOT to Use section.

**Expected Output:**
- Description & Discoverability: 4
- Structure & Documentation: 3
- Instruction Clarity: 4
- Security & Safety: 2 (L1 gate check)
- Integration Quality: 3
- Overall score: 60-70
- Verdict: needs_work

## Scoring

| Score | Letter | Meaning |
|-------|--------|---------|
| 90-100 | A | Ready for publishing |
| 70-89 | B | Minor improvements suggested |
| 60-69 | C | Needs significant work |
| 50-59 | D | Needs major improvements |
| 0-49 | F | Not publishable |
