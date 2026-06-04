---
name: eval-reasoning
description: Evaluate an Agent's reasoning quality from trajectory records: premise accuracy, logical consistency, alternative consideration, confidence calibration, and belief updates. Use when analyzing agent reasoning quality. Triggers: "evaluate reasoning", "analyze reasoning", "check reasoning quality".
---

# Reasoning Evaluator

Evaluates an Agent's reasoning quality by analyzing chain-of-thought and decision-making logic from trajectory records. Complements eval-agent-behavior (which focuses on external actions) by assessing internal reasoning quality.

## When to Use

**Use this when:**
- Analyzing agent reasoning traces for logical flaws
- Debugging why an agent made incorrect decisions despite correct tool usage
- Evaluating reasoning quality across multiple agent versions
- Detecting hallucinations, confirmation bias, or logical leaps in agent thinking

**Do NOT use when:**
- Evaluating external agent behavior (use eval-agent-behavior)
- Real-time execution — this skill analyzes existing traces only

## Evaluation Dimensions

### 1. Premise Accuracy (25%)
Does the agent's reasoning rely on correct premises from tool results?

**Failure modes:** hallucinated facts, numbers flipped from tool output, assumptions not grounded in data

**Checklist:**
- [ ] All factual claims in reasoning are traceable to tool return values
- [ ] Numerical values match tool output (no transcription errors)
- [ ] Agent acknowledges when information is incomplete
- [ ] No hallucinated API responses or fabricated tool results
- [ ] Reasoning references specific data points from previous steps

### 2. Logical Consistency (25%)
Is the reasoning internally consistent across steps?

**Failure modes:** contradiction between steps, goal drift, non-sequitur conclusions

**Checklist:**
- [ ] Conclusions follow from premises
- [ ] No contradiction between earlier and later reasoning
- [ ] Chain of reasoning connects each step to the goal
- [ ] Same criteria applied consistently to similar situations
- [ ] No logical leaps (missing intermediate reasoning)

### 3. Alternative Consideration (20%)
Did the agent consider multiple options before deciding?

**Failure modes:** premature commitment, no comparison, anchoring bias

**Checklist:**
- [ ] Multiple options were explicitly considered before selection
- [ ] Trade-offs between options were evaluated
- [ ] Decision criteria were stated before evaluating options
- [ ] Agent reconsidered choices when new information arrived
- [ ] No evidence of anchoring on first plausible option

### 4. Confidence Calibration (15%)
Does the agent's confidence match the evidence?

**Failure modes:** overconfidence with weak evidence, underconfidence with strong evidence

**Checklist:**
- [ ] Confidence level is appropriate for available evidence
- [ ] Uncertainty is expressed when data is ambiguous
- [ ] Agent escalates or asks for clarification when needed
- [ ] No false certainty about speculative conclusions

### 5. Belief Update (15%)
Does the agent correctly update beliefs when new information contradicts prior assumptions?

**Failure modes:** confirmation bias, ignoring contradictory evidence, doubling down on wrong assumptions

**Checklist:**
- [ ] Agent revises plans when tool results contradict assumptions
- [ ] Contradictory evidence is acknowledged, not ignored
- [ ] Retry strategies change after repeated failures
- [ ] Agent adapts reasoning to new context, not repeating same mistakes
- [ ] No evidence of confirmation bias

## Workflow

1. **Load trajectory** — Read the agent trace record including reasoning steps
2. **Extract reasoning** — Identify chain-of-thought, decision rationale, confidence statements
3. **Score each dimension** — For each of the 5 dimensions, assign a score (1-5) based on observed reasoning
4. **Cross-reference with behavior** — Compare reasoning scores with tool_selection/argument_extraction from eval-agent-behavior for compound pattern detection
5. **Generate report** — Output structured reasoning evaluation report

## Cross-Reference Pattern Detection

When reasoning scores are LOW but behavioral scores are HIGH:
→ **LATENT RISK WARNING**: Agent appears to do the right things but for wrong reasons. Future failures likely when context changes.

When reasoning scores are HIGH but task_completion is LOW:
→ **EXECUTION GAP**: Good reasoning but execution failed. Focus on tool usage or external dependencies.

## Output Format

    ## Reasoning Evaluation Report

    ### Trajectory: [trace ID]

    ### Dimension Scores
    | Dimension | Score (1-5) | Weight | Status |
    |-----------|-------------|--------|--------|
    | Premise Accuracy | X | 25% | PASS/FAIL |
    | Logical Consistency | X | 25% | PASS/FAIL |
    | Alternative Consideration | X | 20% | PASS/FAIL |
    | Confidence Calibration | X | 15% | PASS/FAIL |
    | Belief Update | X | 15% | PASS/FAIL |

    **Status:** PASS = score >= 3, FAIL = score < 3

    ### Cross-Reference Status
    - **Latent Risk:** DETECTED / NOT DETECTED
    - **Execution Gap:** DETECTED / NOT DETECTED

    ### Reasoning Flaws
    1. [Flaw Type] - [Detail] - [Step Reference]

    ### Improvement Suggestions
    1. [Suggestion] - [Rationale]

## Examples

### Example 1: Agent with hallucinated premise

**Input:** An agent trace where the agent states "the flight costs $450" (correct) but then reasons "so I can book 3 tickets for $900" (incorrect).

**Expected Output:**
- Premise Accuracy: 3 (numbers correct initially, multiplication wrong)
- Logical Consistency: 3 (arithmetic error breaks consistency)
- Alternative Consideration: 4 (considered multiple flights)
- Confidence Calibration: 3 (confident about wrong total)
- Belief Update: N/A
- Latent Risk: DETECTED

### Example 2: Agent with confirmation bias

**Input:** An agent trace where the agent prefers Python solution, encounters a performance issue, but keeps trying Python optimizations instead of considering alternatives.

**Expected Output:**
- Premise Accuracy: 4
- Logical Consistency: 3
- Alternative Consideration: 2 (no alternatives considered)
- Confidence Calibration: 4
- Belief Update: 2 (ignores performance signal)
- Latent Risk: DETECTED

## Scoring

| Score | Meaning |
|-------|---------|
| 5 | Excellent reasoning, fully logical and self-correcting |
| 4 | Good reasoning with minor logical gaps |
| 3 | Acceptable reasoning but notable flaws |
| 2 | Poor reasoning, multiple logical errors |
| 1 | No coherent reasoning or completely illogical |

## Platform Compatibility

This evaluator uses Read/Glob/Bash tools. See `references/tool-mapping.md` for equivalent tool names on opencode, Copilot CLI, VS Code, and Claude Code.

## Rubric Reference

Detailed scoring criteria for each dimension are defined in:
`references/reasoning-rubric.yaml`

## Related Skills

- **eval-agent-behavior** — Evaluates external tool usage and task completion
- **eval-skill-quality** — Evaluates skill definition quality
