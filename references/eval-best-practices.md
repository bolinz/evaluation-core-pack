# Evaluation Best Practices

## Rubric Design

1. **Specific scoring criteria** — Each score level (1-5) should have a concrete, observable description
2. **Binary checklist items** — Supplement scores with pass/fail checklist for objective verification
3. **L1 gates for critical dimensions** — Security and safety should always have a hard gate

## Atomic Criteria

1. **One criterion, one failure mode** — Each checklist item must test exactly one thing. A compound item like "Input is validated and sanitized" should be split into "Input is validated" + "Input is sanitized"
2. **Binary pass/fail first** — Before assigning a 1-5 score, each criterion should be clearly passable or failable. Use scoring for hill-climbing, binary for gatekeeping
3. **Specific over vague** — "Title is descriptive, 5-10 words, without units" is better than "Title is appropriate"
4. **Distinct coverage** — If two criteria always pass or fail together, they measure the same thing. Remove or merge redundant criteria
5. **Required vs additional** — Distinguish minimum acceptable criteria (SLAs) from advanced criteria that differentiate good from great

**Before:**
```
- [ ] Error handling is comprehensive and well-documented
```

**After (atomic):**
```
- [ ] All error paths return structured error responses
- [ ] Error messages are user-actionable (not technical dumps)
- [ ] Error handling is documented in API reference
```

## Eval Design

1. **Start small** — 2-3 test cases, expand based on failures
2. **Cover both paths** — Happy path and failure/edge cases
3. **Vary prompts** — Different phrasing, detail levels, formality
4. **Baseline comparison** — Always run with_skill and without_skill

## Grader Diversity

Not all assertions need an LLM judge. Use the right grader for the job:

| Grader Type | Best For | Speed | Determinism |
|-------------|----------|-------|-------------|
| **Code-based** | Schema validation, range checks, regex matching, binary tests | Fast (ms) | ✅ Deterministic |
| **Model-based (LLM)** | Tone, completeness, subjective quality, rubric scoring | Slow (s) | ❌ Non-deterministic |
| **Human** | Domain expertise, edge case calibration, gold standard validation | Very slow | ✅ Gold standard |

### Code-Based Grader (预留接口)

```json
{
  "type": "code",
  "assertions": [
    {"check": "json_schema", "schema": "evaluation-result.schema.yaml"},
    {"check": "range", "field": "score", "min": 0, "max": 100},
    {"check": "regex", "field": "verdict", "pattern": "^(approve|needs_work|reject)$"}
  ]
}
```

### Model-Based Grader (当前实现)

```json
{
  "type": "model",
  "weight": 1.0,
  "rubric": "skills/eval-skill-quality/references/skill-quality-rubric.yaml",
  "assertions": [
    "Report contains all 5 sub_dimension scores"
  ]
}
```

### Human Grader (预留接口)

```json
{
  "type": "human",
  "weight": 0.5,
  "reviewer": "domain-expert",
  "assertions": [
    "Domain terminology is accurate",
    "Recommendations are actionable"
  ]
}
```

### When to Use Each

- **Start with code-based** for anything that can be deterministically checked
- **Add model-based** for subjective quality dimensions
- **Use human** for periodic calibration (every 10th eval) and golden dataset creation

## Grading

1. **Concrete evidence required** for a PASS
2. **Review assertions during grading** — fix ones too easy, too hard, or unverifiable
3. **Remove assertions** that always pass (no signal) or always fail (bad test)

## Trial Aggregation

Run each eval case multiple times to distinguish systematic failures from stochastic noise.

### Trial Count Guidelines

| Eval Type | Trials | Rationale |
|-----------|--------|-----------|
| Capability | 3 | Measure variance, hill-climbing signal |
| Regression | 3 | Confirm no regression, catch flaky tests |
| Benchmark | 5+ | Stable comparison, statistical confidence |
| Quick check | 1 | Iteration speed, skip variance analysis |

### Aggregation Strategies

| Strategy | Logic | Use When |
|----------|-------|----------|
| `average` | Mean score across all trials | Continuous scoring (1-5) |
| `majority` | Most common verdict | Binary pass/fail decisions |
| `all_pass` | All trials must pass | Regression gates, hard requirements |

### Interpreting Results

- **pass_rate = 1.0** (always passes): Strong signal, low variance
- **pass_rate ~ 0.5-0.7** (sometimes passes): High variance — investigate model stochasticity or prompt ambiguity
- **pass_rate = 0.0** (always fails): Systematic issue — fix in skill instructions or eval design

Grading variance across trials is itself a signal:
- If LLM grader scores fluctuate widely for the same input → rubric criteria are ambiguous
- If agent outputs vary widely for the same prompt → skill instructions have gaps

## Cost-Performance Analysis

Track economic efficiency alongside quality metrics. High-quality evaluations mean nothing if they're too expensive to run.

### Key Metrics

| Metric | How to Measure | Warning Threshold |
|--------|---------------|-------------------|
| **Cost per successful eval** | Total tokens × token price / passed cases | > $0.50/eval |
| **Token efficiency** | Score gained per 1000 tokens | < 5 points/1K tokens |
| **Latency budget** | Time per eval case | > 60s |
| **Delta efficiency** | (score_delta / token_delta) × 1000 | < 2 points per 1K extra tokens |

### When to Optimize

| Signal | Action |
|--------|--------|
| Cost per eval > $1.00 | Reduce trial count or use cheaper model |
| Token efficiency declining | Look for redundant tool calls in trace |
| Latency > 60s | Simplify skill instructions |
| Delta efficiency < 2 | Skill not worth the token cost |

### Example: Cost-Performance Report Entry

```json
"cost_analysis": {
  "total_tokens": 45000,
  "total_cost_usd": 0.90,
  "cost_per_pass": 0.11,
  "token_efficiency": 6.2,
  "latency_avg_ms": 15000
}
```

## Outcome Verification

Agent evaluations should verify the final state, not just what the agent claims. An agent may say "booking confirmed" but no reservation exists in the database.

### What to Verify

| Claim Type | Verification Method | Example |
|------------|-------------------|---------|
| **State change** | Check the actual state (DB, API, filesystem) | Query booking table for reservation |
| **Side effects** | Check no unintended changes | Verify no unintended file modifications |
| **Data integrity** | Check data consistency | Verify total = sum of line items |
| **User-facing output** | Check what user actually sees | Verify email was sent with correct content |

### Workflow Integration

In eval-runner, after the agent completes its task, add an outcome verification step BEFORE grading:

1. Agent completes task → claims "done"
2. **Run outcome checks** — query actual state (DB, file system, API)
3. **Compare** — outcome matches claim? If not, flag hallucination
4. **Grade** — grade both the outcome and the transcript

### Schema Integration

The evaluation-result.schema.yaml supports outcome verification through:

```yaml
result:
  outcome:
    verified: true/false
    state_checks:
      - check: "booking exists in database"
        passed: true
        evidence: "SELECT * FROM bookings WHERE id = BK-78901 → 1 row"
    claim_match: true/false (did agent's claim match the outcome?)
```

## Grader Calibration

Model-based graders (LLM judges) can be inconsistent. Calibrate them regularly against human judgment.

### Calibration Frequency

| Workload | Frequency | Sample Size |
|----------|-----------|-------------|
| Daily evals (> 100) | Weekly | 20 cases |
| Weekly evals (10-100) | Monthly | 10 cases |
| Ad-hoc evals (< 10) | Per session | 3-5 cases |

### Calibration Workflow

1. **Run model grader** on sample cases → record scores
2. **Human expert** grades the same cases independently → record scores
3. **Compare** — calculate agreement rate (Cohen's kappa or simple %)
4. **Identify drift** — if agreement < 80%, investigate:
   - Are rubric criteria ambiguous? → Refine rubric
   - Is the model grader too lenient/strict? → Adjust grader prompt
   - Are edge cases not covered? → Expand rubric
5. **Update grader** — fix rubric or grader prompt
6. **Re-calibrate** — repeat on a fresh sample
7. **Log calibration** — record agreement rate, date, and changes made

### Agreement Targets

| Agreement | Action |
|-----------|--------|
| > 90% | Good — no action needed |
| 80-90% | Monitor — track which criteria drift |
| 70-80% | Investigate — refine ambiguous rubric criteria |
| < 70% | Fix — rewrite rubric or upgrade grader model |

### Grader Consistency Log

```json
{
  "calibration": {
    "date": "YYYY-MM-DD",
    "sample_size": 20,
    "agreement_rate": 0.85,
    "model_grader": "gpt-4",
    "human_reviewer": "domain-expert",
    "criteria_drift": ["instruction_clarity", "integration_quality"],
    "changes_made": ["Refined instruction_clarity rubric level 3-4 descriptions"]
  }
}
```

## Iteration

1. Failed assertions -> specific instruction gaps in SKILL.md
2. Human feedback -> broader quality issues
3. Execution transcripts -> why things went wrong

## Confidence Levels

| Confidence | When |
|------------|------|
| High | Trajectory data available + multiple dimensions agree |
| Medium | Only L1+L2 scores, no runtime data |
| Low | Only L1 checks, mostly automated scanning |

## Compound Error

When evaluating agent behavior: per-step scores can look good while end-to-end fails.
Always check: avg(5 step dimensions) vs task_completion.

## Versioning

- Rubric versions are independent of Pack versions
- Increment rubric version when scoring criteria change
- Old evaluations remain valid against the rubric version they used
