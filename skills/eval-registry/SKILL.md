---
name: eval-registry
description: Define and look up evaluation dimensions, templates, aggregation rules, and output schema. Use when mapping target types to evaluators, computing weighted scores, or formatting reports. Triggers: "look up dimension", "aggregation rules", "evaluation schema", "dimension registry", "what dimensions".
---

# Evaluation Registry

Central registry for all evaluation dimensions, templates, aggregation logic, and the unified output schema. Source files: `references/dimension-registry.yaml`, `references/evaluation-result.schema.yaml`.

## Dimensions

| id | name | weight | score_range | l1_gate | evaluator_ref |
|----|------|--------|-------------|---------|---------------|
| technical_feasibility | 技术可行性 | 1.0 | 1-5 | — | eval-feasibility |
| architectural_soundness | 架构合理性 | 1.0 | 1-5 | — | eval-architecture |
| security | 安全 | 1.5 | 1-5 | pass | eval-security |
| scalability | 可扩展性 | 0.7 | 1-5 | — | eval-architecture |
| maintainability | 可维护性 | 0.7 | 1-5 | — | eval-architecture |
| complexity | 实现复杂度 | 0.7 | 1-5 *invert | — | eval-complexity |
| cost | 成本 | 0.5 | 1-5 | — | eval-tech-choice |
| interoperability | 跨平台兼容性 | 0.5 | 1-5 | — | eval-interoperability |
| documentation | 文档质量 | 0.5 | 1-5 | needs_work | eval-documentation |
| format | 格式合规 | 0.3 | 1-5 | warning | eval-format |
| skill_quality | 技能质量 | 1.5 | 1-5 | fail | eval-skill-quality |
| agent_behavior | Agent 行为 | 1.5 | 1-5 | — | eval-agent-behavior |
| reasoning | 推理质量 | 1.0 | 1-5 | — | eval-reasoning |

### Complex Dimension: skill_quality

| sub_dimension | weight |
|---------------|--------|
| description_and_discoverability | 0.15 |
| structure_and_documentation | 0.20 |
| instruction_clarity | 0.25 |
| security_and_safety | 0.25 |
| integration_quality | 0.15 |

### Complex Dimension: agent_behavior

| sub_dimension | weight |
|---------------|--------|
| tool_selection | 0.20 |
| argument_extraction | 0.15 |
| result_utilization | 0.15 |
| error_recovery | 0.15 |
| plan_coherence | 0.15 |
| task_completion | 0.20 |

### Complex Dimension: reasoning

| sub_dimension | weight |
|---------------|--------|
| premise_accuracy | 0.25 |
| logical_consistency | 0.25 |
| alternative_consideration | 0.20 |
| confidence_calibration | 0.15 |
| belief_update | 0.15 |

## Templates

| target | dimensions | extra |
|--------|-----------|-------|
| skill | [skill_quality] | require_l1 |
| agent_behavior | [agent_behavior] | require_transcript |
| architecture | [architectural_soundness, maintainability, complexity, cost] | — |
| tech_choice | [cost, complexity, maintainability] | — |
| complete | [technical_feasibility, architectural_soundness, maintainability, complexity, cost, format, skill_quality, agent_behavior, reasoning] | require_l1: [format] |

## Aggregation Rules

```
score = Σ(dimension.score × dimension.weight) / Σ(dimension.weight)

verdict:
  - ANY L1 gate dimension == fail -> reject
  - score >= 80 -> approve
  - score >= 60 -> needs_work
  - else -> reject

confidence:
  - has trajectory data -> high
  - L3 present -> high
  - L1 + L2 only -> medium
  - L1 only -> low
```

## Output Schema

### Required Fields

```
meta:
  evaluator: string      // evaluator 名称
  version: string        // evaluator 版本
  timestamp: datetime    // ISO 8601

target:
  type: string           // skill | agent_behavior | architecture | tech_choice | complete
  id: string
  path: string?          // optional

result:
  verdict: string        // approve | needs_work | reject
  score: number          // 0-100
  confidence: string     // high | medium | low
  dimensions: [{         // min 1 dimension
    name: string
    score: integer       // 1-5
    weight: number
    detail: string?      // optional
    sub_dimensions?: [{  // optional, for complex dimensions
      name: string
      score: integer     // 1-5
      weight: number
      detail: string?
    }]
  }]
```

### Optional Fields

```
outcome?: {
  verified: boolean
  claim_match: boolean
  state_checks?: [{ check: string, passed: boolean, evidence: string }]
}

cost_analysis?: {
  total_tokens: integer
  total_cost_usd: number
  cost_per_pass: number
  token_efficiency: number
  latency_avg_ms: number
}

trajectory?: {
  tool_selection: number
  argument_extraction: number
  result_utilization: number
  error_recovery: number
  plan_coherence: number
  task_completion: number
}

trials?: {
  count: integer
  pass_count: integer
  pass_rate: number        // 0-1
  scores: [number]
  aggregate: string        // majority | average | all_pass
}

artifacts?: {
  report: string           // path
  grading: string          // path
  transcript: string       // path
}
```

## Cross-Platform Tool Mapping

Tool names differ across agent platforms. When an evaluator SKILL.md references a tool like `Read`, `Glob`, or `Bash`, use the following equivalents:

| Task | opencode | Copilot CLI / VS Code | Claude Code |
|------|----------|----------------------|-------------|
| Read file | `Read` | `read` file or `cat` | `Read` |
| Search content | `Grep` | shell `grep`/`rg` | `Grep` |
| Find files | `Glob` | shell `find`/`ls` | `Glob` |
| Write file | `Write` | `write` / file edit | `Write` |
| Run command | `Bash` | shell execution | `Bash` |
| Load skill | `skill` tool | auto-discovered from `skills/` dir | `Skill` tool |
| Delegate agent | `Task` subagent | built-in agents (explore, task, research) | `Task` subagent |

Full reference: `references/tool-mapping.md`

## References

- Source YAML: `references/dimension-registry.yaml`
- Source YAML: `references/evaluation-result.schema.yaml`
- Cross-platform mapping: `references/tool-mapping.md`
