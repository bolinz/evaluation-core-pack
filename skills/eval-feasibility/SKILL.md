---
name: eval-feasibility
description: Evaluate whether a technical solution is feasible given current constraints, risks, and resources. Use when assessing technical proposals, implementation plans, or architecture designs. Triggers: "assess feasibility", "is this feasible", "technical viability", "risk assessment".
---

# Feasibility Evaluator

Evaluates technical feasibility across 4 dimensions. Produces a structured feasibility report with scores, risk flags, and recommendations.

## When to Use

**Use this when:**
- Evaluating a technical proposal or design
- Assessing implementation risk before committing
- Comparing alternative approaches

**Do NOT use when:**
- Evaluating existing implementations (use eval-agent-behavior)
- Evaluating skill quality (use eval-skill-quality)

## Evaluation Dimensions

### 1. Technical Viability (30%)
Can the solution be built with current technology and constraints?

**Checklist:**
- [ ] Core technology stack is proven (has production track record)
- [ ] Core technology stack is available (accessible to the team)
- [ ] Dependencies are stable (not frequently breaking)
- [ ] Dependencies are well-understood (team has experience)
- [ ] No fundamental technical blockers identified
- [ ] Solution works within existing infrastructure constraints

### 2. Risk Assessment (30%)
What are the key risks and how manageable are they?

**Checklist:**
- [ ] Risks are identified
- [ ] Risks are categorized by severity
- [ ] Mitigation strategies exist for critical risks
- [ ] Unknown unknowns are acknowledged
- [ ] Risk-to-reward ratio is acceptable

### 3. Resource Feasibility (20%)
Do we have the resources (time, people, budget) to execute?

**Checklist:**
- [ ] Required skill sets are available
- [ ] Time estimate is realistic
- [ ] Budget costs are accounted for
- [ ] Infrastructure costs are accounted for
- [ ] External dependencies are gated appropriately

### 4. Timeline Realism (20%)
Is the proposed timeline achievable?

**Checklist:**
- [ ] Milestones are concrete (specific deliverables defined)
- [ ] Milestones are measurable (success criteria defined)
- [ ] Buffer exists for unexpected issues
- [ ] Dependencies are ordered correctly
- [ ] Timeline accounts for testing effort
- [ ] Timeline accounts for validation effort

## Workflow

1. **Understand the proposal** — Read the technical proposal or design document
2. **Evaluate each dimension** — Score 1-5 using the rubric criteria
3. **Flag critical risks** — Any dimension scoring ≤ 2 triggers a risk flag
4. **Generate report** — Output structured feasibility report

## Output Format

    ## Feasibility Report

    ### Target: [name]

    ### Dimension Scores
    | Dimension | Score (1-5) | Weight | Status |
    |-----------|-------------|--------|--------|
    | Technical Viability | X | 30% | PASS/FAIL |
    | Risk Assessment | X | 30% | PASS/FAIL |
    | Resource Feasibility | X | 20% | PASS/FAIL |
    | Timeline Realism | X | 20% | PASS/FAIL |

    **Status:** PASS = score ≥ 3, FAIL = score < 3

    ### Risk Flags
    - [Critical/Moderate/Low] - [Description]

    ### Recommendations
    1. [Recommendation] - [Rationale]

### Decision Path: Partial Feasibility

When a proposal is partially feasible, use these criteria to decide the path:

| Condition | Action |
|-----------|--------|
| Technical viable, resource constrained | Phase the implementation, reduce scope |
| Risk identified, mitigation clear | Approve with risk tracking |
| Risk identified, mitigation unclear | Flag for deeper analysis before proceeding |
| Timeline unrealistic | Re-estimate with buffer, identify critical path |
| Core technology unproven | Prototype first, spike before commitment |

### Example 3: Partially feasible proposal

**Input:** A proposal to migrate from REST to GraphQL. The technology is proven and the team has experience, but the timeline is aggressive and the data migration strategy is vague.

**Expected Output:**
- Technical Viability: 4 (proven tech, clear path)
- Risk Assessment: 3 (migration risk flagged)
- Resource Feasibility: 4 (team has skills)
- Timeline Realism: 2 (aggressive, migration vague)
- Recommendation: Staged migration, extend timeline by 2 sprints

## Scoring

| Score | Meaning |
|-------|---------|
| 5 | Fully feasible, minimal risk, clear path forward |
| 4 | Feasible with minor concerns, manageable risks |
| 3 | Feasible but significant concerns exist |
| 2 | Major feasibility issues, needs redesign |
| 1 | Not feasible in current form |

## Platform Compatibility

This evaluator uses Read/Glob/Bash tools. See `references/tool-mapping.md` for equivalent tool names on opencode, Copilot CLI, VS Code, and Claude Code.

## Rubric Reference

Detailed scoring criteria for each dimension are defined in:
`references/feasibility-rubric.yaml`
