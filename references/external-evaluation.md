# 外部评价标准重新评估

> 基于 Anthropic、Hebbia、OpenAI、Four Pillars 等业界主流框架的综合评估。

## 行业评价标准来源

| 来源 | 核心方法论 | 适用要素 |
|------|-----------|---------|
| **Anthropic** — Demystifying Evals | 三类 grader（code/model/human）、capability vs regression、outcome verification、trajectory analysis | Eval 框架结构 |
| **Hebbia** — Hybrid Deterministic+Rubric | Atomic criteria、pass/fail binary、specificity、distinct coverage、required vs additional | Rubric 设计质量 |
| **Four Pillars** — MLMastery | Task success、tool usage、reasoning coherence、cost-performance | 评价维度覆盖 |
| **Agentic Design Rubric** — irinag2024 | Agent intelligence、technical foundation、QA、observability | Agent 场景适配 |
| **OpenAI** — Eval Skills | 完整 eval = prompt → trace → checks → score | Eval 闭环 |

---

## 外部评价：16 个维度

每个维度 1-5 分，满分 80，换算为百分制。

### Category A: Eval 框架完整性（Anthropic + OpenAI）

| 维度 | 得分 | 评分理由 |
|------|:---:|---------|
| **A1. 任务分解清晰度** | **4** | evals.json 有 7 个用例，每个含 prompt + expected_output + assertions。但缺少 "trial" 概念（多次运行取一致结果），缺少 grader 类型标记 |
| **A2. Grader 多样性** | **2** | 只有 model-based（LLM 打分），缺少 code-based（确定性断言、类型检查、schema 校验）和 human-in-the-loop 设计。完全依赖 LLM judge |
| **A3. 结果验证深度** | **3** | 有 assertions 作为预期检查，但缺少 outcome verification（验证最终状态，而非 agent 的声称）。例如 flight booking 应验证 DB 中是否存在 reservation |
| **A4. 轨迹/过程分析** | **4** | eval-agent-behavior 支持 trajectory 分析，含 compound error detection，是框架亮点。但不支持 process-focused evaluation（评估推理路径而非终点） |
| **A5. Capability vs Regression 区分** | **5** | 明确区分 capability/regression/benchmark 三种类型，evals.json 中有对应用例。符合 Anthropic 建议的最佳实践 |

### Category B: Rubric 设计质量（Hebbia）

| 维度 | 得分 | 评分理由 |
|------|:---:|---------|
| **B1. 原子性（一标准一缺陷）** | **2** | 大部分维度使用复合 checklist，例如缺少"一个检查项只测一个问题"的原子性原则。多个 check 混合在一个条目中 |
| **B2. 评分粒度选择** | **3** | 使用 1-5 分制（适合 hill-climbing），但缺少 Hebbia 推荐的 pass/fail binary 层（更快的 LLM 收敛）。可以混合使用 |
| **B3. 具体性（grounded in specifics）** | **4** | 大部分 checklist 描述具体（"description contains action verbs"），少数仍偏模糊（"Appropriate fallback or escalation used"） |
| **B4. 覆盖独特性（无冗余）** | **4** | 各维度覆盖不重叠。但 eval-architecture 的 architectural_soundness 和 maintainability 有轻微边界重叠 |
| **B5. 区分度（good vs great）** | **3** | 1-5 评分提供了区分空间，但缺少 Hebbia 的 "required vs additional" 分层设计。无法区分"勉强可用"和"优秀" |

### Category C: Agent 评价深度（Four Pillars）

| 维度 | 得分 | 评分理由 |
|------|:---:|---------|
| **C1. 任务成功率** | **3** | evals.json 有 assertions 做通过/失败判断，但缺少多次 trial 的聚合统计（pass rate across N trials） |
| **C2. 工具使用质量** | **4** | eval-agent-behavior 的 tool_selection + argument_extraction 覆盖了工具选择、参数正确性。但缺少工具调用效率分析（redundant calls、token waste） |
| **C3. 推理连贯性** | **3** | eval-agent-behavior 的 plan_coherence 覆盖了路径合理性，但缺少 reasoning evaluation（中间步骤是否逻辑自洽、是否考虑了替代方案） |
| **C4. 成本-性能分析** | **2** | eval-runner 在 benchmark 中跟踪 avg_tokens + avg_duration_ms，但 SKILL.md 中没有成本评估的明确指导。缺少 cost per successful task 指标 |

### Category D: 生产就绪度（Agentic Design Rubric）

| 维度 | 得分 | 评分理由 |
|------|:---:|---------|
| **D1. 可观测性** | **2** | 缺少 logging/tracing/monitoring 的设计。eval-runner 生成 benchmark.json 但无结构化日志输出。无 grader 一致性检查 |
| **D2. 安全与合规** | **5** | security 是 L1 门禁维度，eval-security 有完整的 4 维度 security rubric。OWASP 分类、硬编码凭据检查、最小权限原则覆盖全面 |
| **D3. 错误恢复评估** | **4** | eval-agent-behavior 的 error_recovery 维度覆盖了重试、回退、弃疗模式。但缺少 gradicer 自身错误处理（LLM grader 失败时的降级策略） |

---

## 评分对比

| 类别 | 内部自评分 | 外部评分 | 差异 |
|------|:---------:|:--------:|:----:|
| Category A — 框架完整性 | 4.6 | **3.6** | ▼ 1.0 |
| Category B — Rubric 设计 | 4.3 | **3.2** | ▼ 1.1 |
| Category C — Agent 评价深度 | 4.0 | **3.0** | ▼ 1.0 |
| Category D — 生产就绪度 | 3.7 | **3.7** | ＝ 0.0 |
| **总分（百分制）** | **86/100** | **67/100** | **▼ 19** |

## 关键差异分析

| 差异点 | 内部忽略了什么 | 外部标准要求什么 |
|--------|---------------|----------------|
| **Grader 单一化** | 只用了 LLM-as-judge | 需要 code-based（确定性）+ model-based（弹性）+ human（校准）混合 |
| **缺少原子性** | checklist 是复合的 | Hebbia: "One criterion, one failure mode" |
| **无多次 trial** | 单次运行直接断言 | Anthropic: 多次 trial 取一致结果，区分系统失效 vs 随机失效 |
| **缺少成本指标** | 只定性评估复杂度 | Four Pillars: cost per successful task, token efficiency |
| **缺少推理过程评估** | 只评估计划结构 | 业界：评估推理路径是否逻辑自洽 |
| **无 grader 校准** | 没有 grader 一致性检查 | Hebbia: 多次 grading pass 取聚合，校准 grader |

## 改进方向（按优先级）

1. **高** — 增加 grader 多样性：加入 code-based 断言（schema 校验、正则匹配、type check）
2. **高** — 增加多次 trial 支持：定义 trial 概念，聚合 pass rate
3. **中** — 引入 atomic criteria 原则：重构 checklist 确保一个条目测一个问题
4. **中** — 增加 cost-performance 维度的量化指标
5. **低** — 增加 grader 校准机制：多次 grader pass + 人工抽样验证
