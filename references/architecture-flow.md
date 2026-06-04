# Evaluation Core Pack — 架构框图

```mermaid
flowchart TB
    User([User Request])

    subgraph Entry["🗂️ 入口 Skill"]
        API(("eval-api"))
    end

    subgraph Orchestration["🤖 Orchestration Agent"]
        EO[["evaluation-orchestrator"]]
    end

    subgraph Skills["📐 Evaluator Skills (8)"]
        SQ(("eval-skill-quality"))
        AB(("eval-agent-behavior"))
        FE(("eval-feasibility"))
        AR(("eval-architecture"))
        CX(("eval-complexity"))
        TC(("eval-tech-choice"))
        FM(("eval-format"))
    end

    subgraph Runner["🤖 Eval Runner Agent"]
        ER[["eval-runner"]]
    end

    subgraph Data["📄 数据文件"]
        DR[/references/dimension-registry.yaml/]
        SC[/references/evaluation-result.schema.yaml/]
        BP[/references/eval-best-practices.md/]
        EJ[/references/evals.json/]
    end

    User --> API
    API -->|"route by target type"| EO

    EO -->|"read"| DR
    EO -->|"output via"| SC
    EO -.->|"internal:eval-skill-quality"| SQ
    EO -.->|"internal:eval-agent-behavior"| AB
    EO -.->|"internal:eval-feasibility"| FE
    EO -.->|"internal:eval-architecture"| AR
    EO -.->|"internal:eval-complexity"| CX
    EO -.->|"internal:eval-tech-choice"| TC
    EO -.->|"internal:eval-format"| FM

    ER -->|"read test cases"| EJ
    ER -->|"deploy evaluator"| EO

    subgraph Legend["图例"]
        A1[["方括号 = Agent"]]
        A2(("圆括号 = Skill"))
        A3[/斜杠 = 数据文件/]
    end
```

## 维度 → Skill 映射

| 维度 | 对应 Skill |
|------|-----------|
| `skill_quality` | `eval-skill-quality` |
| `agent_behavior` | `eval-agent-behavior` |
| `technical_feasibility` | `eval-feasibility` |
| `architectural_soundness`, `scalability`, `maintainability` | `eval-architecture` |
| `complexity` | `eval-complexity` |
| `cost` | `eval-tech-choice` |
| `format` | `eval-format` |
| `security`, `interoperability`, `documentation` | orchestrator 直接评估 |

## 核心流程说明

| 步骤 | 谁 | 做什么 |
|------|----|--------|
| 1 | `eval-api` **Skill** | 接收用户请求，解析 target type |
| 2 | `evaluation-orchestrator` **Agent** | 查询 `dimension-registry.yaml`，确定维度列表 |
| 3 | `evaluation-orchestrator` | 按 `evaluator_ref` 调度到对应 skill |
| 4 | 各 Evaluator **Skill** | 按各自 rubric 评分 1-5，含 L1 门禁检查 |
| 5 | `evaluation-orchestrator` | 聚合加权得分 → verdict → confidence → 统一 Schema |
| 6 | `eval-runner` **Agent** | 读 `evals.json`，跑迭代，with/without 对比，生成 benchmark |
