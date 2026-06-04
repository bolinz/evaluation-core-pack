---
name: eval-security
description: Evaluate security risks, threat posture, and protective measures in designs and implementations. Use when assessing security, threat modeling, or reviewing access controls. Triggers: "security review", "threat assessment", "security analysis", "evaluate security".
---

# Security Evaluator

Evaluates security posture across 4 dimensions. L1 gate: any critical finding triggers automatic FAIL.

## When to Use

**Use this when:**
- Reviewing architecture or design for security risks
- Assessing authentication and authorization mechanisms
- Evaluating data protection and privacy measures
- Threat modeling a new feature or system

**Do NOT use when:**
- Evaluating format compliance (use eval-format)
- Evaluating general feasibility (use eval-feasibility)

## Evaluation Dimensions

### 1. Authentication & Authorization (30%)
Are identity and access controls properly designed?

**L1 Gate: Missing or broken auth → FAIL**

**Checklist:**
- [ ] Authentication mechanism is appropriate for the threat model
- [ ] Authorization follows least privilege principle
- [ ] Session tokens are rotated periodically
- [ ] Sessions expire after inactivity timeout
- [ ] Sessions can be explicitly invalidated
- [ ] API endpoints enforce proper access controls
- [ ] No hardcoded credentials or secrets in code/config

### 2. Data Protection (30%)
Are data assets adequately protected?

**Checklist:**
- [ ] Data is encrypted at rest
- [ ] Data is encrypted in transit
- [ ] Sensitive data is classified (PII, credentials, etc.)
- [ ] Each data classification has defined handling procedures
- [ ] Input validation is applied on all external inputs
- [ ] Output sanitization prevents injection attacks (XSS, SQLi)
- [ ] Output encoding prevents injection attacks (XSS, SQLi, etc.)
- [ ] Secrets management follows best practices (vault, env vars, not code)

### 3. Network & Infrastructure Security (20%)
Are network boundaries and infrastructure secure?

**Checklist:**
- [ ] Network segmentation is appropriate
- [ ] TLS/SSL configuration follows modern standards
- [ ] Open ports and services are minimized
- [ ] Dependency vulnerabilities are managed (SCA, updates)
- [ ] Logging and monitoring for security events is in place

### 4. Compliance & Governance (20%)
Does the solution meet regulatory and policy requirements?

**Checklist:**
- [ ] Relevant regulations are identified (GDPR, SOC2, HIPAA, etc.)
- [ ] Data retention and deletion policies exist
- [ ] Audit trail is maintained for sensitive operations
- [ ] Incident response plan is documented
- [ ] Third-party vendor security is assessed

## Workflow

1. **Understand the target** — Read the design, code, or architecture document
2. **Evaluate each dimension** — Score 1-5 using rubric criteria
3. <EXTREMELY-IMPORTANT>**Check L1 gate** — Any critical finding (auth bypass, hardcoded secrets, injection) → REJECT immediately. Do not skip this check regardless of time pressure.</EXTREMELY-IMPORTANT>
4. **Generate report** — Output structured security evaluation report

## Output Format

    ## Security Evaluation Report

    ### Target: [name]

    ### L1 Gate Result
    **Status:** PASS / FAIL

    ### Dimension Scores
    | Dimension | Score (1-5) | Weight | Status |
    |-----------|-------------|--------|--------|
    | Authentication & Authorization | X | 30% | PASS/FAIL |
    | Data Protection | X | 30% | PASS/FAIL |
    | Network & Infrastructure Security | X | 20% | PASS/FAIL |
    | Compliance & Governance | X | 20% | PASS/FAIL |

    **Status:** PASS = score ≥ 3, FAIL = score < 3

    ### Critical Findings (if any)
    1. [Critical] - [Description] - [OWASP Category]

    ### Recommendations
    1. [Recommendation] - [Rationale] - [Priority]

## Scoring

| Score | Meaning |
|-------|---------|
| 5 | Excellent security posture, production-ready |
| 4 | Good security, minor improvements |
| 3 | Acceptable security, notable gaps |
| 2 | Poor security, significant vulnerabilities |
| 1 | Critical vulnerabilities, not deployable |

## Platform Compatibility

This evaluator uses Read/Glob/Bash tools. See `references/tool-mapping.md` for equivalent tool names on opencode, Copilot CLI, VS Code, and Claude Code.

## Rubric Reference

Detailed scoring criteria for each dimension are defined in:
`references/security-rubric.yaml`
