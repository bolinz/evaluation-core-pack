---
name: eval-format
description: Validate document format compliance against standards and best practices. Use when checking document structure, format adherence, or completeness. Triggers: "check format", "validate format", "format compliance", "is this valid".
---

# Format Validator

Validates document format compliance. Checks structure, completeness, and consistency against defined standards.

## When to Use

**Use this when:**
- Checking if a document follows required format standards
- Validating SKILL.md structure and frontmatter
- Reviewing document completeness before publishing
- Auditing multiple documents for format consistency

**Do NOT use when:**
- Evaluating content quality (use eval-skill-quality)
- Evaluating technical content (use eval-feasibility or eval-architecture)

## Evaluation Dimensions

### 1. Structure Compliance (35%)
Does the document follow the required structure?

**L1 Gate: Critical structure violations → FAIL**

**Checklist:**
- [ ] Frontmatter "name" field is present
- [ ] Frontmatter "description" field is present
- [ ] Required sections exist per document type
- [ ] Section hierarchy is logical
- [ ] Section hierarchy is consistent
- [ ] File naming follows conventions

**Format violations (score ≤ 2 if 2+ present):**
- [ ] Frontmatter missing required fields (name, description)
- [ ] YAML parsing errors in frontmatter
- [ ] Wrong file extension (.txt instead of .md)
- [ ] Line endings are consistent
- [ ] Indentation is consistent

### 2. Completeness (35%)
Are all required elements present?

**Checklist:**
- [ ] All mandatory fields are populated
- [ ] No placeholder or TODO content
- [ ] Examples are present where required
- [ ] References are resolved (not broken)
- [ ] Edge cases and error states are documented

### 3. Consistency (30%)
Is the format consistent throughout?

**Checklist:**
- [ ] Terminology is used consistently
- [ ] Formatting style is uniform
- [ ] Code blocks have language tags
- [ ] Heading levels follow a consistent hierarchy
- [ ] Cross-references are correct

## Workflow

1. **Read the document** — Load the target document
2. **Check frontmatter** — Verify required metadata fields
3. **Evaluate each dimension** — Score 1-5 using rubric criteria
4. <EXTREMELY-IMPORTANT>**Check L1 gates** — Critical structure violations (missing frontmatter fields, invalid YAML) trigger format FAIL. Do not overlook structural issues.</EXTREMELY-IMPORTANT>
5. **Generate report** — Output structured format validation report

## Output Format

    ## Format Validation Report

    ### Target: [name]

    ### L1 Gate Result
    **Status:** PASS / FAIL

    ### Dimension Scores
    | Dimension | Score (1-5) | Weight | Status |
    |-----------|-------------|--------|--------|
    | Structure Compliance | X | 35% | PASS/FAIL |
    | Completeness | X | 35% | PASS/FAIL |
    | Consistency | X | 30% | PASS/FAIL |

    **Status:** PASS = score ≥ 3, FAIL = score < 3

    ### Issues Found
    1. [Critical/Warning/Info] - [Description] - [Location]

    ### Recommendations
    1. [Recommendation] - [Rationale]

### Example 3: Format violations

**Input:** A SKILL.md where the frontmatter is missing the `name` field, uses tabs instead of spaces in YAML, and has a 4-space indent inconsistency in the checklist.

**Expected Output:**
- Structure Compliance: 2 (missing name, YAML issues)
- Completeness: 3 (content exists but metadata incomplete)
- Consistency: 2 (formatting inconsistencies)
- Overall score: 35-45
- Verdict: needs_work — fix frontmatter and formatting

## Scoring

| Score | Meaning |
|-------|---------|
| 5 | Fully compliant, ready for publishing |
| 4 | Minor formatting issues |
| 3 | Notable issues but acceptable |
| 2 | Significant format problems |
| 1 | Completely non-compliant |

## Platform Compatibility

This evaluator uses Read/Glob/Bash tools. See `references/tool-mapping.md` for equivalent tool names on opencode, Copilot CLI, VS Code, and Claude Code.

## Rubric Reference

Detailed scoring criteria for each dimension are defined in:
`references/format-rubric.yaml`
