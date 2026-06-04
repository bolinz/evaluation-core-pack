---
name: eval-interoperability
description: Evaluate cross-platform compatibility, system integration capability, and standardization level. Use when reviewing integration designs, cross-platform support, or API compatibility. Triggers: "evaluate interoperability", "cross-platform review", "integration assessment", "compatibility check".
---

# Interoperability Evaluator

Evaluates cross-platform compatibility and system integration capability across 4 dimensions.

## When to Use

**Use this when:**
- Reviewing cross-platform or cross-browser support
- Assessing API or service integration design
- Evaluating data exchange formats and protocols
- Checking third-party integration quality

**Do NOT use when:**
- Evaluating security (use eval-security)
- Evaluating internal architecture (use eval-architecture)

## Evaluation Dimensions

### 1. Platform Compatibility (30%)
Does the solution work across target platforms?

**Checklist:**
- [ ] Supported platforms are explicitly documented
- [ ] Platform-specific code is isolated from shared logic
- [ ] Platform-specific code is tested on each target platform
- [ ] Feature parity across platforms is assessed
- [ ] Fallback behavior for unsupported platforms exists
- [ ] Platform-specific build/package configurations are maintained

### 2. Integration Quality (30%)
How well does the system integrate with others?

**Checklist:**
- [ ] APIs follow standards (REST, GraphQL, gRPC) consistently
- [ ] API versioning strategy exists
- [ ] Backward compatibility is maintained
- [ ] Error responses follow a standard format
- [ ] Error responses contain useful diagnostic information
- [ ] Integration tests cover boundary conditions

### 3. Data Exchange (25%)
Are data formats and protocols standardized?

**Checklist:**
- [ ] Data formats use standard schemas (JSON Schema, Protobuf, etc.)
- [ ] Encoding is consistent (UTF-8, Base64 for binary)
- [ ] Date/time handling is timezone-aware
- [ ] Data validation is performed at boundaries
- [ ] Schema evolution strategy exists

### 4. Standardization (15%)
Does the solution follow industry standards?

**Checklist:**
- [ ] Relevant industry standards are identified
- [ ] Relevant industry standards are followed
- [ ] Proprietary extensions are minimized
- [ ] Proprietary extensions are documented
- [ ] Standard protocols are preferred over custom implementations
- [ ] Compliance with accessibility standards (WCAG, etc.)
- [ ] Standardization compliance is documented
- [ ] Standardization compliance is verifiable (tests exist)

## Workflow

1. **Read the design** — Load the integration or cross-platform design document
2. **Evaluate each dimension** — Score 1-5 using rubric criteria
3. **Flag integration risks** — Any dimension scoring ≤ 2 flags a risk
4. **Generate report** — Output structured interoperability report

## Output Format

    ## Interoperability Report

    ### Target: [name]

    ### Dimension Scores
    | Dimension | Score (1-5) | Weight | Status |
    |-----------|-------------|--------|--------|
    | Platform Compatibility | X | 30% | PASS/FAIL |
    | Integration Quality | X | 30% | PASS/FAIL |
    | Data Exchange | X | 25% | PASS/FAIL |
    | Standardization | X | 15% | PASS/FAIL |

    **Status:** PASS = score ≥ 3, FAIL = score < 3

    ### Integration Risks
    - [Risk] - [Description]

    ### Recommendations
    1. [Recommendation] - [Rationale]

## Scoring

| Score | Meaning |
|-------|---------|
| 5 | Fully interoperable, standard-compliant |
| 4 | Good interoperability with minor issues |
| 3 | Acceptable, but notable compatibility concerns |
| 2 | Poor interoperability, significant integration risk |
| 1 | Not interoperable, custom/proprietary only |

## Platform Compatibility

This evaluator uses Read/Glob/Bash tools. See `references/tool-mapping.md` for equivalent tool names on opencode, Copilot CLI, VS Code, and Claude Code.

## Rubric Reference

Detailed scoring criteria for each dimension are defined in:
`references/interoperability-rubric.yaml`
