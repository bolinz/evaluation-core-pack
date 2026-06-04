---
name: eval-complexity
description: Estimate implementation complexity, cognitive load, and change impact of proposed changes. Use when assessing implementation effort, reviewing change proposals, or prioritizing work. Triggers: "estimate complexity", "how complex", "implementation effort", "change impact".
---

# Complexity Estimator

Evaluates implementation complexity across 4 dimensions. Note: `complexity` is an inverted score dimension — higher complexity means lower score.

## When to Use

**Use this when:**
- Estimating effort for a new feature or change
- Comparing implementation alternatives
- Identifying high-risk or high-effort changes
- Prioritizing backlog items

**Do NOT use when:**
- Evaluating architecture quality (use eval-architecture)
- Assessing technical feasibility (use eval-feasibility)

## Evaluation Dimensions

### 1. Implementation Complexity (30%)
How difficult is the actual implementation?

**Checklist:**
- [ ] New code vs. modification of existing code
- [ ] Number of components/systems touched
- [ ] Algorithmic complexity involved
- [ ] Third-party integration complexity
- [ ] Data migration or transformation needed

### 2. Dependency Complexity (25%)
How complex are the dependency chains?

**Checklist:**
- [ ] Number of internal dependencies affected
- [ ] External service dependencies involved
- [ ] Dependency chains are manageable
- [ ] Breaking changes required in dependencies
- [ ] Dependency updates or migrations needed
- [ ] Cross-team coordination required
- [ ] Multiple deployment pipelines affected
- [ ] Feature flags or toggles needed for safe rollout

### 3. Change Impact (25%)
What is the blast radius of the change?

**Checklist:**
- [ ] Affected user-facing features
- [ ] API or contract changes required
- [ ] Database schema changes needed
- [ ] Testing scope is well-understood
- [ ] Rollback strategy exists

### 4. Cognitive Load (20%)
How hard is it to understand and reason about?

**Checklist:**
- [ ] Domain complexity is manageable
- [ ] Codebase context is well-understood
- [ ] Learning curve for the team
- [ ] State management complexity

## Scoring Note

Complexity scores are **inverted** for aggregation: a high complexity score (5) means LOW complexity (easy). The orchestrator applies `invert_score: true` during aggregation.

## Workflow

1. **Understand the change** — Read the change proposal, diff, or specification
2. **Evaluate each dimension** — Score 1-5 using rubric criteria
3. **Flag high complexity items** — Any dimension scoring ≤ 2 indicates high complexity
4. **Generate report** — Output structured complexity report

## Output Format

    ## Complexity Report

    ### Target: [name]

    ### Dimension Scores
    | Dimension | Score (1-5) | Weight | Status |
    |-----------|-------------|--------|--------|
    | Implementation Complexity | X | 30% | PASS/FAIL |
    | Dependency Complexity | X | 25% | PASS/FAIL |
    | Change Impact | X | 25% | PASS/FAIL |
    | Cognitive Load | X | 20% | PASS/FAIL |

    **Status:** PASS = score ≥ 3 (lower complexity), FAIL = score < 3 (higher complexity)

    ### Complexity Flags
    - [High/Medium/Low] - [Description]

    ### Recommendations
    1. [Recommendation] - [Rationale]

### Example 3: Cross-team complexity

**Input:** A change that requires updates to 3 microservices owned by 2 different teams, with a shared database schema migration.

**Expected Output:**
- Implementation Complexity: 3 (multiple services but clear patterns)
- Dependency Complexity: 2 (cross-team, schema migration)
- Change Impact: 3 (affects multiple services, rollback strategy needed)
- Cognitive Load: 3 (distributed system knowledge required)
- Overall score: 40-55
- Verdict: needs_work — requires coordination plan and migration strategy

## Scoring

| Score | Meaning |
|-------|---------|
| 5 | Trivial change, low effort |
| 4 | Simple change, moderate effort |
| 3 | Moderate complexity, manageable |
| 2 | Complex, significant effort required |
| 1 | Very complex, high risk, consider alternatives |

## Platform Compatibility

This evaluator uses Read/Glob/Bash tools. See `references/tool-mapping.md` for equivalent tool names on opencode, Copilot CLI, VS Code, and Claude Code.

## Rubric Reference

Detailed scoring criteria for each dimension are defined in:
`references/complexity-rubric.yaml`
