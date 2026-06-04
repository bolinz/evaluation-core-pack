---
name: eval-documentation
description: Evaluate documentation completeness, readability, and consistency. Use when reviewing docs, API references, or project guides. Triggers: "review documentation", "document quality", "check docs", "documentation assessment".
---

# Documentation Evaluator

Evaluates documentation quality across 4 dimensions. L1 gate: needs_work — missing critical docs triggers flag.

## When to Use

**Use this when:**
- Reviewing project documentation completeness
- Assessing API reference or user guide quality
- Checking onboarding documentation for new team members
- Evaluating README or setup documentation

**Do NOT use when:**
- Evaluating format compliance (use eval-format)
- Evaluating skill definition quality (use eval-skill-quality)

## Evaluation Dimensions

### 1. Completeness (35%)
Are all necessary documents present and comprehensive?

**L1 Gate: Missing critical documentation (architecture, API, setup) → needs_work**

**Checklist:**
- [ ] README explains what the project does
- [ ] README explains how to start using the project
- [ ] Setup/installation instructions are complete
- [ ] Setup/installation instructions are tested
- [ ] API documentation covers all endpoints/functions
- [ ] Architecture documentation describes the system design
- [ ] Contributing guidelines exist for new contributors
- [ ] Changelog or version history is maintained

### 2. Readability (25%)
Is the documentation clear and easy to understand?

**Checklist:**
- [ ] Language is clear (jargon is explained)
- [ ] Writing is concise (no unnecessary verbosity)
- [ ] Code examples are correct (compile/run as documented)
- [ ] Code examples are well-formatted (consistent style, syntax highlighted)
- [ ] Screenshots or diagrams are used where helpful
- [ ] Content is well-organized with logical flow
- [ ] Glossary exists for domain-specific terms

### 3. Accuracy (25%)
Is the documentation technically correct and up-to-date?

**Checklist:**
- [ ] Commands and code snippets are tested
- [ ] Commands and code snippets produce correct output
- [ ] Configuration examples match current versions
- [ ] Outdated or deprecated content is marked
- [ ] Links and references are valid (not broken)
- [ ] Version compatibility is documented

### 4. Structure & Navigation (15%)
Is the documentation easy to navigate?

**Checklist:**
- [ ] Table of contents is present for long docs
- [ ] Search functionality or index exists
- [ ] Cross-references between related docs exist
- [ ] Consistent heading hierarchy
- [ ] File naming convention is intuitive

## Workflow

1. **Read the documentation** — Load the target documentation
2. **Evaluate each dimension** — Score 1-5 using rubric criteria
3. **Check L1 needs_work gate** — Missing critical sections → flag
4. **Generate report** — Output structured documentation evaluation report

## Output Format

    ## Documentation Quality Report

    ### Target: [name]

    ### Dimension Scores
    | Dimension | Score (1-5) | Weight | Status |
    |-----------|-------------|--------|--------|
    | Completeness | X | 35% | PASS/FAIL |
    | Readability | X | 25% | PASS/FAIL |
    | Accuracy | X | 25% | PASS/FAIL |
    | Structure & Navigation | X | 15% | PASS/FAIL |

    **Status:** PASS = score >= 3, FAIL = score < 3

    ### Completeness Gaps
    - [Missing doc] - [Impact]

    ### Recommendations
    1. [Recommendation] - [Rationale]

## Scoring

| Score | Meaning |
|-------|---------|
| 5 | Excellent documentation, well-maintained |
| 4 | Good documentation with minor gaps |
| 3 | Adequate documentation, notable gaps |
| 2 | Poor documentation, significant missing content |
| 1 | No useful documentation |

## Platform Compatibility

This evaluator uses Read/Glob/Bash tools. See `references/tool-mapping.md` for equivalent tool names on opencode, Copilot CLI, VS Code, and Claude Code.

## Rubric Reference

Detailed scoring criteria for each dimension are defined in:
`references/documentation-rubric.yaml`
