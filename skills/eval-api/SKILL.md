---
name: eval-api
description: Route evaluation requests to the correct evaluator based on target type. Use when evaluating any skill, agent behavior, architecture, or tech choice. Triggers: "evaluate", "assess quality", "run evaluation", "review my code".
---

# Evaluation API

Unified entry point for all evaluation requests. Parses the request, identifies the target type, and delegates to the evaluation-orchestrator agent.

## When to Use

**Use this when:**
- You want to evaluate a skill's quality
- You need an architecture review
- You want to compare technology choices
- You need agent behavior analysis

**Do NOT use this when:**
- You want to run a specific evaluator directly (call that evaluator instead)

## Supported Target Types

| Target | Description | Routes To |
|--------|-------------|-----------|
| `skill` | Skill definition quality | evaluation-orchestrator -> eval-skill-quality |
| `architecture` | Architecture design review | evaluation-orchestrator -> external evaluators |
| `tech_choice` | Technology choice comparison | evaluation-orchestrator -> external evaluators |
| `agent_behavior` | Agent runtime behavior | evaluation-orchestrator -> eval-agent-behavior |
| `complete` | Full multi-dimensional evaluation | evaluation-orchestrator -> all evaluators |

## Workflow

1. **Parse request** — Determine target type from user input
2. **Look up dimension registry** — Activate eval-registry skill to find required dimensions
3. **Delegate to orchestrator** — Pass target and dimensions to evaluation-orchestrator agent
4. **Return report** — Present the structured evaluation report to the user

## Examples

### Example 1: Skill quality evaluation

**Input:** "Evaluate the skill at ./my-skill/"

**Flow:** Parse target type (skill) -> Delegate to orchestrator -> orchestrator runs eval-skill-quality -> Returns quality report with 5 dimension scores and letter grade

### Example 2: Architecture review

**Input:** "Assess this architecture design"

**Flow:** Parse target type (architecture) -> Look up dimensions from registry -> Delegate to orchestrator -> orchestrator runs external evaluators -> Returns architecture evaluation report

## Output Format

The output follows the unified evaluation-result schema. See eval-registry skill for the full schema definition.

## Platform Compatibility

This evaluator uses Read/Glob/Bash tools. See `references/tool-mapping.md` for equivalent tool names on opencode, Copilot CLI, VS Code, and Claude Code.

