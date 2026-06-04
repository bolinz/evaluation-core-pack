---
name: eval-architecture
description: Evaluate architecture design quality, modularity, scalability, and maintainability. Use when reviewing architecture decisions, design proposals, or system structure. Triggers: "evaluate architecture", "architecture review", "design quality", "architectural soundness".
---

# Architecture Evaluator

Evaluates architecture design across 4 dimensions. Produces a structured architecture quality report with scores and improvement suggestions.

## When to Use

**Use this when:**
- Reviewing architecture design documents
- Assessing system modularity and coupling
- Evaluating scalability and maintainability

**Do NOT use when:**
- Evaluating technical feasibility (use eval-feasibility)
- Evaluating implementation details (use eval-complexity)

## Evaluation Dimensions

### 1. Architectural Soundness (35%)
Is the architecture well-structured and robust?

**Checklist:**
- [ ] Clear separation of concerns
- [ ] Single responsibility per module/component
- [ ] Component boundaries are well-defined
- [ ] Interfaces between components are well-defined
- [ ] Architecture supports current requirements
- [ ] Error handling strategy is defined
- [ ] Failure modes are identified and addressed

**Anti-patterns (score ≤ 2 if present):**
- [ ] God class / God module (single component does everything)
- [ ] Dependency cycle between modules
- [ ] Shared mutable state across boundaries
- [ ] Leaky abstractions (implementation details exposed)
- [ ] Hard-coded configuration instead of environment/configuration system

### 2. Modularity & Coupling (25%)
Are concerns properly separated with minimal coupling?

**Checklist:**
- [ ] Modules have high cohesion
- [ ] Inter-module coupling is minimal (low)
- [ ] Inter-module coupling is explicit (not hidden)
- [ ] Dependencies point in the right direction
- [ ] Interfaces are stable (not frequently changing)
- [ ] Interfaces are well-documented

### 3. Scalability (20%)
Can the architecture scale to meet future demands?

**Checklist:**
- [ ] Horizontal scaling path is clear
- [ ] Vertical scaling path is clear
- [ ] Bottlenecks are identified
- [ ] Data access patterns support scaling
- [ ] State management is appropriate for scale

### 4. Maintainability (20%)
Is the architecture easy to maintain and evolve?

**Checklist:**
- [ ] Code/module organization is logical
- [ ] Testing is feasible at each layer
- [ ] Configuration process is straightforward
- [ ] Deployment process is straightforward
- [ ] Documentation covers architecture decisions
- [ ] Change impact is localized

## Workflow

1. **Read architecture document** — Load the design document or system description
2. **Evaluate each dimension** — Score 1-5 using rubric criteria
3. **Check cross-cutting concerns** — Security, performance, operability
4. **Generate report** — Output structured architecture evaluation report

## Output Format

    ## Architecture Evaluation Report

    ### Target: [name]

    ### Dimension Scores
    | Dimension | Score (1-5) | Weight | Status |
    |-----------|-------------|--------|--------|
    | Architectural Soundness | X | 35% | PASS/FAIL |
    | Modularity & Coupling | X | 25% | PASS/FAIL |
    | Scalability | X | 20% | PASS/FAIL |
    | Maintainability | X | 20% | PASS/FAIL |

    **Status:** PASS = score ≥ 3, FAIL = score < 3

    ### Key Findings
    1. [Strength/Issue] - [Detail]

    ### Recommendations
    1. [Recommendation] - [Rationale]

### Example 3: Architecture with anti-patterns

**Input:** A design where the "OrderService" handles orders, payments, inventory, shipping, and notifications — a clear god module.

**Expected Output:**
- Architectural Soundness: 2 (god module, single responsibility violated)
- Modularity & Coupling: 2 (high coupling)
- Scalability: 2 (god module is a bottleneck)
- Maintainability: 2 (changing anything risks breaking everything)
- Overall score: 30-45
- Verdict: reject — split OrderService into domain services

### Example 4: Improved architecture

**Input:** The same design, but refactored with OrderService → OrderProcessor + PaymentGateway + InventoryManager + ShippingService + NotificationService.

**Expected Output:**
- Architectural Soundness: 4
- Modularity & Coupling: 4
- Scalability: 4
- Maintainability: 4
- Overall score: 75-85
- Verdict: approve

## Scoring

| Score | Meaning |
|-------|---------|
| 5 | Excellent architecture, production-ready |
| 4 | Good architecture, minor improvements suggested |
| 3 | Acceptable architecture with notable concerns |
| 2 | Poor architecture, significant redesign needed |
| 1 | Unacceptable architecture, fundamental flaws |

## Platform Compatibility

This evaluator uses Read/Glob/Bash tools. See `references/tool-mapping.md` for equivalent tool names on opencode, Copilot CLI, VS Code, and Claude Code.

## Rubric Reference

Detailed scoring criteria for each dimension are defined in:
`references/architecture-rubric.yaml`
