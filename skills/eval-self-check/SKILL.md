---
name: eval-self-check
description: Self-evaluate the evaluation-core-pack itself. Run this after any change to the pack to verify no regressions. Use when completing feature work on the pack, before publishing a new version, or after modifying any evaluator SKILL.md. Triggers: "self-evaluate", "run self check", "check pack quality", "verify no regression", "self review".
---

# Self-Check Evaluator

Runs the evaluation-core-pack evaluators against the pack itself. This is the pack's own self-evaluation mechanism, inspired by recursive self-improvement patterns from superpowers.

## When to Use

**Use this when:**
- After modifying any evaluator SKILL.md or rubric
- Before publishing a new version of the pack
- After adding or removing a skill
- After modifying the dimension registry or schema
- Periodically to check for quality drift

**Do NOT use when:**
- Evaluating external skills or projects (use the individual evaluators instead)
- Running benchmark iterations (use eval-runner)

## Workflow

1. **Load eval-registry** — Activate eval-registry skill for dimension definitions and aggregation rules
2. **Run skill_quality evaluation** — Activate eval-skill-quality and evaluate all 13 SKILL.md files under `skills/`
3. **Run format evaluation** — Activate eval-format and validate all SKILL.md files
4. **Check references** — Verify that `references/tool-mapping.md` is present and up-to-date
5. **Verify cross-platform support** — Confirm `plugin.json` matches skill directory structure
6. **Aggregate results** — Use eval-registry aggregation rules to compute overall score
7. **Compare with baseline** — If a `SELF-CHECK-BASELINE.md` exists, compare scores and flag regressions

## Self-Check Scorecard

| Check | Evaluator | Target | Critical |
|-------|-----------|--------|----------|
| Skill quality | eval-skill-quality | All `skills/*/SKILL.md` | Yes |
| Format compliance | eval-format | All `skills/*/SKILL.md` | Yes |
| Architecture soundness | eval-architecture | Current project structure | No |
| Maintainability | eval-architecture | Current project structure | No |
| Cross-platform | manual | `plugin.json` + `pack.yaml` | Yes |

## Critical Rules

<EXTREMELY-IMPORTANT>
- If any SKILL.md fails the L1 gate check → BLOCKER, fix before publishing
- If `plugin.json` is out of sync with `skills/` directory → BLOCKER, fix before publishing
- If any SKILL.md has Chinese characters in description or frontmatter → BLOCKER, must be English-only
</EXTREMELY-IMPORTANT>

## Baseline

After each run, save the result to `SELF-CHECK-BASELINE.md` at the project root. On subsequent runs, compare new scores against the baseline and flag any dimension that dropped by more than 0.5 points.

## Output

    ## Self-Check Report
    ### Version: [pack version]
    ### Date: [timestamp]
    ### Overall: PASS / NEEDS_WORK / FAIL
    ### Score: XX/100

    ### Dimensions
    | Check | Score (1-5) | Status | vs Baseline |
    |-------|-------------|--------|-------------|
    | Skill Quality | X | PASS/FAIL | +/-X |
    | Format Compliance | X | PASS/FAIL | +/-X |
    | Architecture | X | PASS/FAIL | +/-X |
    | Cross-Platform | X | PASS/FAIL | — |

    ### Blocker Check
    - L1 gates: PASS / FAIL
    - plugin.json sync: PASS / FAIL
    - Chinese check: PASS / FAIL

    ### Regressions
    1. [Dimension] dropped from X to Y — [recommendation]
