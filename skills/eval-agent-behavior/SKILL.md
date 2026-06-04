---
name: eval-agent-behavior
description: Evaluate an Agent's runtime behavior from trajectory records. Use when analyzing agent traces, debugging agent failures, or assessing agent reliability. Triggers: "evaluate this agent trace", "analyze agent behavior", "check agent trajectory", "debug agent run".
---

# Agent Behavior Evaluator

Evaluates an Agent's runtime behavior by analyzing trajectory records. Produces a 6-dimension score with compound error detection.

## When to Use

**Use this when:**
- Analyzing agent run traces for quality issues
- Debugging why an agent failed end-to-end
- Comparing agent behavior across versions

**Do NOT use when:**
- Evaluating skill quality (use eval-skill-quality)
- Real-time execution — this skill analyzes existing traces only

## Evaluation Dimensions

### 1. Tool Selection (20%)
Did the agent pick the right tool for each step?

**Failure modes:** wrong tool, fabricated tool, no tool when needed

**Checklist:**
- [ ] Tool was appropriate for the task
- [ ] No unnecessary tool calls
- [ ] No fabricated or hallucinated tools
- [ ] Called tool when one was needed

### 2. Argument Extraction (15%)
Were tool arguments schema-correct and semantically accurate?

**Failure modes:** schema mismatch, semantic mismatch, edge case handling

**Checklist:**
- [ ] All required parameters provided
- [ ] Parameter values semantically correct
- [ ] Parameter format matches API requirements
- [ ] Parameter type matches API requirements
- [ ] Edge cases handled (nulls, empty values)

### 3. Result Utilization (15%)
Did the agent actually use tool return values?

**Failure modes:** number flipped, model knowledge substitution, context drift across turns

**Checklist:**
- [ ] Tool return values are referenced in next steps
- [ ] No number/value transcription errors
- [ ] No model knowledge used instead of tool results
- [ ] Context preserved across multiple turns

### 4. Error Recovery (15%)
How did the agent handle tool failures?

**Failure modes:** crash, hallucinate success, repeat same broken arguments

**Checklist:**
- [ ] Failed calls are retried
- [ ] Retry uses corrected parameters (not same broken arguments)
- [ ] Appropriate fallback or escalation used
- [ ] Retry count is reasonable (not infinite)
- [ ] Failure clearly communicated to user

### 5. Plan Coherence (15%)
Was the agent's trajectory well-structured?

**Failure modes:** loops, dead ends, sub-tree explosion

**Checklist:**
- [ ] No redundant tool calls
- [ ] No circular tool call patterns
- [ ] Steps follow a logical sequence
- [ ] No unnecessary depth or branching
- [ ] Progress toward goal is visible

### 6. Task Completion (20%)
Did the agent achieve the end-to-end goal?

**Failure modes:** per-step green but end-to-end failure (compound error)

**Checklist:**
- [ ] End user goal is fully achieved
- [ ] All sub-tasks completed
- [ ] Output or state change matches expected result
- [ ] No regression in existing functionality
- [ ] No unintended side effects from changes

## Compound Error Detection

If the average of dimensions 1-5 > 4.0 AND task_completion < 3.0:
→ **COMPOUND ERROR WARNING**: Per-step performance looks good but end goal was missed.
This indicates the agent is doing the right things in isolation but failing at the system level.

## Workflow

1. **Load trajectory** — Read the agent trace record (tool calls, arguments, return values, reasoning)
2. **Score each dimension** — For each of the 6 dimensions, assign a score (1-5) based on observed behavior
3. <EXTREMELY-IMPORTANT>**Check compound error** — If per-step avg(dim1-5) > 4 AND task_completion < 3, flag COMPOUND ERROR WARNING. This is the most important signal in agent behavior evaluation — do not miss it.</EXTREMELY-IMPORTANT>
4. **Generate report** — Output structured report with scores and findings

## Examples

### Example 1: Well-behaved agent

**Input:** An agent trace where the agent correctly selected tools, passed valid arguments, used return values, and completed the task.

**Expected Output:**
- All 6 dimensions scored 4+
- No compound error warning
- Overall assessment: strong

### Example 2: Agent with compound error

**Input:** An agent trace where each individual step looks correct but the final goal was missed (e.g., booked wrong dates).

**Expected Output:**
- Tool Selection: 4, Argument Extraction: 4, Result Utilization: 4, Error Recovery: 5, Plan Coherence: 4
- Task Completion: 2
- Compound Error: DETECTED

### Example 3: Agent with error recovery failure

**Input:** An agent trace where the first 3 tool calls succeed but then a 4th call fails, and the agent retries with the same broken parameters 5 times before giving up.

**Expected Output:**
- Tool Selection: 4
- Argument Extraction: 3
- Result Utilization: 4
- Error Recovery: 2
- Plan Coherence: 3
- Task Completion: 3
- Compound Error: NOT DETECTED
- Recommendation: implement retry with corrected parameters

### Example 4: Agent with partial completion

**Input:** An agent trace where the agent correctly researched a topic, found relevant information, but stopped before compiling the final response.

**Expected Output:**
- Tool Selection: 5
- Argument Extraction: 5
- Result Utilization: 4
- Error Recovery: N/A (no errors)
- Plan Coherence: 4
- Task Completion: 2
- Compound Error: NOT DETECTED (per-step avg > 4, task completion < 3, but the issue is incomplete plan rather than compound error)

## Input

A trajectory record containing:
- Tool call sequence (name + arguments + return values)
- Agent reasoning steps (optional)
- Task description / goal
- Final outcome

## Platform Compatibility

This evaluator uses Read/Glob/Bash tools. See `references/tool-mapping.md` for equivalent tool names on opencode, Copilot CLI, VS Code, and Claude Code.

## Rubric Reference

Detailed scoring criteria for each dimension are defined in:
`references/agent-behavior-rubric.yaml`

## Output Format

    ## Agent Behavior Report

    ### Trajectory: [trace ID]

    ### Verdict: approve / needs_work / reject

    ### Dimension Scores
    | Dimension | Score (1-5) | Status |
    |-----------|-------------|--------|
    | Tool Selection | X | PASS/FAIL |
    | Argument Extraction | X | PASS/FAIL |
    | Result Utilization | X | PASS/FAIL |
    | Error Recovery | X | PASS/FAIL |
    | Plan Coherence | X | PASS/FAIL |
    | Task Completion | X | PASS/FAIL |

    **Status:** PASS = score >= 3, FAIL = score < 3

    **Compound Error:** DETECTED / NOT DETECTED

    **Verdict Logic:**
    - approve: All dimensions >= 3, no compound error
    - needs_work: Any dimension < 3 or compound error detected
    - reject: Task Completion < 2

    ### Analysis
    1. [Finding] - [Detail]

    ### Improvement Suggestions
    1. [Suggestion] - [Rationale]
