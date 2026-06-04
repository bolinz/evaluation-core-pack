---
name: eval-tech-choice
description: Evaluate technology choices for cost, fit, ecosystem health, and migration impact. Use when comparing technologies, reviewing tech stack decisions, or assessing vendor choices. Triggers: "evaluate tech choice", "compare technologies", "technology assessment", "tech stack review".
---

# Technology Choice Evaluator

Evaluates technology decisions across 4 dimensions. Produces a structured comparison report with scores and trade-off analysis.

## When to Use

**Use this when:**
- Comparing two or more technology options
- Reviewing a proposed tech stack decision
- Assessing migration or upgrade paths
- Evaluating vendor or open-source choices

**Do NOT use when:**
- Evaluating architecture design (use eval-architecture)
- Assessing implementation complexity (use eval-complexity)

## Evaluation Dimensions

### 1. Cost Analysis (30%)
Total cost of ownership including direct and indirect costs.

**Checklist:**
- [ ] Licensing costs are understood
- [ ] Infrastructure/hosting costs are estimated
- [ ] Operational costs (maintenance, support) are included
- [ ] Migration/training costs are accounted for
- [ ] Long-term cost trajectory is favorable

### 2. Technical Fit (30%)
How well does the technology match the use case?

**Checklist:**
- [ ] Core functionality aligns with requirements
- [ ] Performance characteristics meet needs
- [ ] Integration with existing stack is feasible
- [ ] Scalability characteristics match growth plan
- [ ] Security requirements are met
- [ ] Compliance requirements are met

### 3. Ecosystem Health (20%)
Is the technology well-supported and sustainable?

**Checklist:**
- [ ] Community is active
- [ ] Community is growing
- [ ] Documentation is comprehensive
- [ ] Release cadence is healthy
- [ ] Talent pool exists for hiring
- [ ] Commercial support options exist if needed
- [ ] Uses open standards (not proprietary protocols)
- [ ] Supports multi-vendor alternatives
- [ ] Exit strategy exists if technology becomes unsupported
- [ ] Data portability is ensured

### 4. Migration Impact (20%)
What is the cost and risk of adopting or switching?

**Checklist:**
- [ ] Migration path is clearly defined
- [ ] Migration path is documented
- [ ] Data migration requirements are understood
- [ ] Team retraining needs are estimated
- [ ] Rollback options exist
- [ ] Coexistence with existing systems is possible
- [ ] Vendor lock-in risk has been assessed
- [ ] Vendor lock-in risk level is acceptable
- [ ] Contractual exit clauses exist (for commercial vendors)

## Workflow

1. **Identify options** — List the technology alternatives being compared
2. **Evaluate each dimension** — Score 1-5 for each option
3. **Compare and contrast** — Highlight key trade-offs between options
4. **Generate report** — Output structured comparison report

## Output Format

    ## Technology Choice Report

    ### Target: [name]

    ### Dimension Scores
    | Dimension | Score (1-5) | Weight | Status |
    |-----------|-------------|--------|--------|
    | Cost Analysis | X | 30% | PASS/FAIL |
    | Technical Fit | X | 30% | PASS/FAIL |
    | Ecosystem Health | X | 20% | PASS/FAIL |
    | Migration Impact | X | 20% | PASS/FAIL |

    **Status:** PASS = score ≥ 3, FAIL = score < 3

    ### Comparison Summary
    | Option | Overall Score | Key Strength | Key Risk |
    |--------|---------------|--------------|----------|
    | ... | ... | ... | ... |

    ### Trade-off Analysis
    1. [Trade-off] - [Recommendation]

    ### Recommendations
    1. [Recommendation] - [Rationale]

### Example 3: Vendor lock-in assessment

**Input:** Evaluating AWS DynamoDB vs PostgreSQL for a new service. DynamoDB offers better performance but is proprietary. PostgreSQL is open source but requires more operational overhead.

**Expected Output:**
| Dimension | DynamoDB | PostgreSQL |
|-----------|----------|------------|
| Cost Analysis | 4 | 4 |
| Technical Fit | 5 | 4 |
| Ecosystem Health | 4 | 5 |
| Migration Impact | 3 (vendor lock-in) | 5 (portable) |

**Recommendation:** Use PostgreSQL for core business logic, DynamoDB only if proven performance requirement exists. For DynamoDB, abstract data access layer to enable future migration.

## Scoring

| Score | Meaning |
|-------|---------|
| 5 | Excellent choice, strong recommendation |
| 4 | Good choice, minor concerns |
| 3 | Acceptable choice with notable trade-offs |
| 2 | Poor choice, significant drawbacks |
| 1 | Wrong choice, do not select |

## Platform Compatibility

This evaluator uses Read/Glob/Bash tools. See `references/tool-mapping.md` for equivalent tool names on opencode, Copilot CLI, VS Code, and Claude Code.

## Rubric Reference

Detailed scoring criteria for each dimension are defined in:
`references/tech-choice-rubric.yaml`
