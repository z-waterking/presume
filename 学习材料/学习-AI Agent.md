---
tags: [求职, 学习, Agent, P1]
aliases: [Agent学习]
created: 2026-04-13
related: "[[面试-BingViz Agents]], [[简历底稿]]"
---

# 学习材料 — AI Agent 🟡 (辅攻)

> 对应简历: BingViz Agents + SkillLoop, Hackathon 全球季军
> 精选最重要的材料，按优先级分层

---

## P0 必读

- [ ] [Building Effective Agents — Anthropic](https://www.anthropic.com/engineering/building-effective-agents) — ⭐⭐ **最重要**。6 种 Agent 架构模式
- [ ] [ReAct: Reasoning and Acting](https://arxiv.org/abs/2210.03629) — Yao et al., 2022。你的 Context Propagation 改进了 ReAct
- [ ] [MCP 官方规范](https://modelcontextprotocol.io/) — 你用 FastMCP 做 Tool 标准化解耦

---

## P1 推荐阅读

- [ ] [LangGraph 官方文档](https://langchain-ai.github.io/langgraph/) — BingViz 核心框架
- [ ] [OpenAI: Building Agents Guide](https://platform.openai.com/docs/guides/building-agents) — Guardrails / Handoffs
- [ ] [Agentic Design Patterns — Andrew Ng](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/) — 4 种模式 (Reflection / Tool Use / Planning / Multi-Agent)
- [ ] [MCP Architecture](https://modelcontextprotocol.io/docs/learn/architecture) — Client-Server / Tool-Resource-Prompt

---

## P2 补充阅读

- [ ] [LLM Agent Survey](https://arxiv.org/abs/2308.11432) — Wang et al., 2023。最全面综述
- [ ] [Toolformer](https://arxiv.org/abs/2302.04761) — Schick et al., 2023
- [ ] [AutoGen](https://arxiv.org/abs/2308.08155) — Microsoft, 2023。多智能体框架
- [ ] [FastMCP GitHub](https://github.com/jlowin/fastmcp)

---

## 关键词自测

### 核心架构

> ReAct Loop (Thought → Action → Observation)
> 你的 Context Propagation 替代方案 — 减少冗余推理/降低 token/提高确定性
> Graph-based vs Chain-based Execution / LangGraph DAG
> Tool Use / Function Calling / Tool schema
> Agent Memory: Working Memory vs Long-term Memory

### 协议 & 工程

> MCP 三大原语: Tool / Resource / Prompt
> Context Engineering (超越 Prompt Engineering)
> Structured Output / Schema Validation
> Guardrails / Human-in-the-loop

### 高级模式

> Multi-Agent: Orchestrator-Workers / Debate / Delegation
> Handoff 机制 / Agent 间任务交接
> Evaluation & Observability / LangSmith trace
> Document-Driven Development (你的 SkillLoop 方法论)

---

## 4. 深化阅读地图：按 BingViz / SkillLoop 反推

> Agent 是辅攻，但你的项目很新。面试重点不是背框架名，而是讲清「为什么 ReAct 不稳、为什么 MCP 解耦、为什么 Task Log 能让长任务可控」。

### 4.1 Agent 架构模式：支撑「Context Propagation 替代 ReAct」

- [ ] [Plan-and-Solve Prompting](https://arxiv.org/abs/2305.04091) — ReAct 之外的规划范式，帮你解释「先规划再执行」。
- [ ] [Tree of Thoughts](https://arxiv.org/abs/2305.10601) — 多路径搜索式推理，可用于对比你的树形归因分析。
- [ ] [Reflexion](https://arxiv.org/abs/2303.11366) — 通过反思和记忆改善 Agent，适合和 Validation Results 对比。
- [ ] [Voyager](https://arxiv.org/abs/2305.16291) — 长期技能库和自我改进，适合补「Agent memory」概念。
- [ ] [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/) — 当前主流 Agent SDK，重点看 handoffs、guardrails、tracing。
- [ ] [Microsoft AutoGen](https://microsoft.github.io/autogen/) — 多 Agent 协作框架，和 orchestrator-workers 模式对齐。
- [ ] [Semantic Kernel](https://learn.microsoft.com/en-us/semantic-kernel/overview/) — 微软 Agent/插件编排框架，适合面微软生态问题。

读完要能回答：

- ReAct、Plan-and-Execute、Graph-based workflow 的控制权分别在哪里？
- Context Propagation 是 memory、state 还是 workflow constraint？如何准确命名？
- 树形归因遇到新线索时，如何动态扩展节点而不是写死流程？
- 什么时候应该用多 Agent，什么时候单 Agent + 多工具更稳？

### 4.2 Tool Use / MCP / Schema：支撑「工具标准化解耦」

- [ ] [MCP Specification](https://modelcontextprotocol.io/specification/latest) — 重点看 lifecycle、capabilities、tools、resources。
- [ ] [MCP Transports](https://modelcontextprotocol.io/docs/concepts/transports) — stdio、HTTP/SSE 的取舍，面试可能问部署形态。
- [ ] [FastMCP Docs](https://gofastmcp.com/) — 你项目相关实现，重点看 server、tool schema、client。
- [ ] [Gorilla: LLM Connected with Massive APIs](https://arxiv.org/abs/2305.15334) — Tool/API 调用经典论文。
- [ ] [ToolLLM](https://arxiv.org/abs/2307.16789) — 工具学习数据与评估，补充 Tool-use benchmark 视角。
- [ ] [Pydantic](https://docs.pydantic.dev/latest/) — Schema validation 和 structured output 的工程基础。

读完要能回答：

- MCP 的 Tool、Resource、Prompt 三类原语分别解决什么问题？
- MCP 和普通 function calling 的区别是什么？为什么说它是「生态协议」而不是「函数调用格式」？
- Tool Arguments Hallucination 怎么拦截？schema validation、dry run、permission boundary 怎么设计？
- 数据工具里最危险的操作是什么？如何做只读权限、限流、SQL allowlist？

### 4.3 Evaluation / Observability：支撑「Agent 能上线」

- [ ] [Ragas](https://docs.ragas.io/) — RAG/Agent 输出质量评估，重点看 faithfulness、answer relevance、context precision。
- [ ] [DeepEval](https://docs.confident-ai.com/) — LLM 单元测试框架，和 SkillLoop 的 Validation Results 很搭。
- [ ] [LangSmith](https://docs.smith.langchain.com/) — Trace、dataset、evaluation，适合讲 Agent observability。
- [ ] [OpenTelemetry GenAI Semantic Conventions](https://opentelemetry.io/docs/specs/semconv/gen-ai/) — 生产级 tracing 的标准化方向。
- [ ] [SWE-bench](https://www.swebench.com/) — 代码 Agent 评测基准，适合 SkillLoop/Vibe Coding 的方法论背景。
- [ ] [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — Prompt injection、data leakage、tool misuse 安全材料。

读完要能回答：

- Agent 归因分析怎么评估？是看最终答案、过程 trace，还是业务决策是否正确？
- Faithfulness、tool success rate、SQL validity、human acceptance rate 怎么定义？
- 线上 Agent 出错怎么定位：prompt、model、tool、schema、数据源、权限还是上下文传播？
- Prompt injection 进入数据表内容时，Agent 如何防御？

### 4.4 数据分析 Agent：支撑「BingViz 业务归因」

- [ ] [Text-to-SQL Survey](https://arxiv.org/abs/2208.13629) — SQL Agent 的基础综述，补 Text-to-SQL 局限。
- [ ] [Spider Text-to-SQL Benchmark](https://yale-lily.github.io/spider) — 了解 SQL 生成评测，不必深刷。
- [ ] [Data Formulator](https://github.com/microsoft/data-formulator) — 微软数据分析与可视化 Agent 参考。
- [ ] [LIDA: Automatic Generation of Visualizations](https://arxiv.org/abs/2303.02927) — LLM 数据可视化/分析材料。
- [ ] [Databricks Mosaic AI Agent Framework](https://docs.databricks.com/aws/en/generative-ai/agent-framework/) — Databricks 生态 Agent 参考，和你 CK + Databricks 架构相关。

读完要能回答：

- 为什么传统 Text-to-SQL 只能回答「是什么」，很难回答「为什么」？
- 归因分析的搜索空间如何定义：地域、query、广告覆盖、bid、CTR、商业意图？
- ClickHouse 和 Databricks 的分工是性能、成本还是数据新鲜度驱动？
- Agent 发现根因后，如何让业务方信任结论？需要哪些证据链？

---

## 5. BingViz / SkillLoop 专属追问

### Context Propagation

- Q: Context Object 里存什么？事实、假设、约束、指标、SQL 结果分别怎么表示？
- Q: 父节点结论如果错了，会不会把错误传播到所有子节点？怎么纠偏？
- Q: Context Propagation 和普通 memory 有什么区别？
- Q: 为什么不用一个超长 prompt 保存全历史？token、噪声、可控性分别有什么问题？

### MCP / 工具层

- Q: MCP Server 如何发现工具能力？schema 如何版本化？
- Q: Databricks/Kusto/ClickHouse 三类工具的权限和成本控制怎么做？
- Q: Tool 调用失败时，Agent 是重试、换工具、降级，还是回问用户？
- Q: 如何防止 Agent 生成危险 SQL 或扫描过大表？

### SkillLoop / Vibe Coding

- Q: Structured Task Log 和 Chain-of-Thought 有什么关系？是否会泄露敏感推理？
- Q: Task Log 字段为什么选 Current Goal、Planned Steps、Decision History、Known Dependencies、Validation Results？
- Q: 如果 Agent 把 Task Log 写错了怎么办？如何用 tests/trace 校正？
- Q: Document-Driven Development 和传统 TDD/ADR/PRD 有什么异同？

---

## 6. 实战练习

- [ ] 写一个最小 MCP Server：暴露 `get_schema` 和 `run_readonly_query` 两个工具，并用 schema validation 拦截非法字段。
- [ ] 用 LangGraph 画一个 RPM 归因 DAG：Root -> Market Drilldown -> Query Intent -> Ad Coverage -> Bid/CTR。
- [ ] 设计一个 Context Object JSON schema，包含 `finding`, `confidence`, `evidence`, `constraints`, `next_nodes`。
- [ ] 为一个 Agent trace 设计评估表：tool success、SQL validity、evidence completeness、human acceptance。
- [ ] 写一份 SkillLoop Task Log 模板，并用一个小功能开发任务跑一遍计划、执行、验证闭环。

---

## 7. 复习输出物

- [ ] 1 页纸：BingViz Agent 架构图，标出 LLM、LangGraph、MCP、CK、Databricks、Context Object。
- [ ] 1 页纸：ReAct vs Context Propagation 对比表。
- [ ] 1 页纸：MCP vs Function Calling vs 内置 Tool Registry。
- [ ] 5 个 30 秒答案：ReAct 不稳、Context Propagation、MCP、Schema Validation、Structured Task Log。
- [ ] 1 个 5 分钟答案：从零设计一个业务归因分析 Agent 平台。

---

## 8. 7 天冲刺路线

### Day 1：把 Agent 控制流讲清

- 读 Anthropic Building Effective Agents，区分 workflow 和 autonomous agent。
- 读 ReAct 摘要和方法部分，只抓住 Thought / Action / Observation 的控制权问题。
- 输出：一张 ReAct 失败模式表，列出发散、循环、遗忘、工具误用、token 膨胀。

### Day 2：把 Context Propagation 讲成工程方案

- 画 BingViz 归因 DAG：root、market drilldown、query intent、coverage、bid/CTR、final synthesis。
- 写 Context Object JSON schema，字段必须包含 `facts`、`hypotheses`、`constraints`、`evidence`、`confidence`、`next_actions`。
- 输出：30 秒版和 3 分钟版两套回答。

### Day 3：MCP 和工具安全

- 读 MCP lifecycle、tools、resources、transport。
- 对比 function calling、plugin registry、MCP server 三种模式。
- 输出：一个只读 SQL 工具的参数 schema、权限边界、失败重试策略。

### Day 4：Agent Evaluation

- 读 Ragas / DeepEval / LangSmith 的核心指标。
- 把 BingViz evaluation 拆成 tool-level、step-level、answer-level、business-level。
- 输出：一张 evaluation rubric，包含 success、smart-fail、understand-fail、tech-fail、no-response。

### Day 5：SkillLoop 方法论

- 对比 TDD、ADR、PRD、runbook、Structured Task Log。
- 输出：一个开发任务的 Task Log 样例，必须包含目标、计划、依赖、决策、验证结果。

### Day 6：安全与生产化

- 读 OWASP LLM Top 10 中 prompt injection、data leakage、excessive agency、tool misuse。
- 输出：BingViz 的安全边界：只读、SQL allowlist、scan limit、schema validation、human approval。

### Day 7：系统设计串讲

- 用 5 分钟讲完「从零设计业务归因 Agent 平台」。
- 必须覆盖数据源、语义层、工具层、workflow、context、evaluation、observability、deployment。

## 9. 论文与资料卡片模板

每篇材料读完只写 6 行，避免陷入全文摘抄：

```text
资料名：
解决的问题：
核心方法：
和我简历的连接：
面试可用一句话：
局限与风险：
```

建议优先做这些卡片：

| 资料 | 解决的问题 | 简历连接 | 一句话口径 |
| ---- | ---------- | -------- | ---------- |
| ReAct | 推理与行动交替 | Context Propagation 的基线 | ReAct 强在灵活，弱在长链路可控性 |
| Plan-and-Solve | 先规划再求解 | 归因 DAG 的 planning 层 | 规划把开放式搜索变成可检查步骤 |
| Tree of Thoughts | 多路径搜索 | 多根因假设探索 | 适合探索多个 hypothesis，但成本更高 |
| Reflexion | 失败后反思改进 | Validation Results | 反思要落到外部状态和验证结果，否则仍会漂移 |
| MCP Spec | 工具协议 | FastMCP 工具层 | MCP 是工具生态协议，不只是函数调用格式 |
| Ragas / DeepEval | 输出评估 | Agent evaluation | Agent 评估要看答案、证据、工具和业务接受度 |
| OWASP LLM Top 10 | 安全风险 | 企业数据 Agent | 工具越强，权限边界和审计越重要 |

## 10. BingViz 系统设计模板

### 需求定义

- 输入：业务问题、市场、时间窗口、指标、对照基线。
- 输出：根因候选、证据链、置信度、反例检查、建议动作。
- 约束：只读数据、低延迟探索、可回放 trace、不能泄露敏感数据。

### 模块拆分

| 模块 | 职责 | 关键风险 | 防护 |
| ---- | ---- | -------- | ---- |
| Query Planner | 把问题拆成分析 DAG | 过度拆分或漏拆 | 模板化 drilldown + 动态扩展 |
| Semantic Layer | 指标、表、dashboard 血缘 | 用错指标 | metadata validation |
| MCP Tools | CK/Databricks/Kusto 查询 | 危险 SQL、成本过高 | readonly、limit、allowlist |
| Context Store | 保存事实和假设 | 错误传播 | fact/hypothesis 分层、confidence |
| Evaluator | 评估过程和答案 | 只看最终文本 | trace + answer + business rubric |
| Observability | trace 和 debug | 出错不可定位 | tool spans、prompt version、dataset version |

### 5 分钟讲法骨架

1. 先定义业务问题和指标口径，避免 Agent 自己猜。
2. 通过 semantic layer 找到相关表、字段和 dashboard 血缘。
3. Planner 生成分析 DAG，不让模型每一步自由发散。
4. 每个节点调用 MCP 工具，只读查询并写入 Context Object。
5. Synthesis 节点汇总事实、假设、置信度和反例检查。
6. Evaluator 从工具成功率、SQL 合法性、证据完整性、人工接受度打分。

## 11. SkillLoop 深挖材料

SkillLoop 最好不要只讲「写文档」。更强的表达是：把文档变成 Agent 的外部状态机。

| 问题 | 普通 coding agent | SkillLoop |
| ---- | ----------------- | --------- |
| 目标漂移 | 依赖聊天历史 | `Current Goal` 每轮重锚定 |
| 决策反复 | 模型记忆不可靠 | `Decision History` 保留原因 |
| 依赖幻觉 | 直接假设库存在 | `Known Dependencies` 必须验证 |
| 假装完成 | 生成代码后停止 | `Validation Results` 记录测试输出 |
| 交接困难 | 只能看对话 | Task Log 可被人和 Agent 共同读取 |

面试可用类比：

> TDD 让代码通过测试，ADR 记录架构决策，PRD 记录产品意图；SkillLoop 把这些压缩成一个 Agent 可读写的执行协议，让长周期开发不依赖模型短期记忆。

## 12. 高频追问答案素材

- Q：为什么说 Context Propagation 不是简单 memory？
  - A：Memory 只回答「存了什么」，Context Propagation 还规定「哪些事实必须传给哪个子任务，以及子任务必须在什么约束下执行」。它更接近 workflow state 和 constraint passing。
- Q：如果数据源返回互相矛盾的结论怎么办？
  - A：Context Object 里把结论标为 hypothesis，触发 cross-check 节点；例如 RPM 下降同时看 coverage、CTR、bid、query mix、market mix，不能单路径定因。
- Q：Agent 如何处理工具失败？
  - A：先判断是参数错误、权限错误、数据为空、超时还是 schema mismatch。参数错误可修正重试，权限错误要降级或请求人审，数据为空要把 negative evidence 写进 trace。
- Q：为什么不用多 Agent 互相辩论？
  - A：业务归因更需要可控工具调用和证据链。多 Agent 适合角色分工明显的任务，但会增加协调成本和评估难度；单 Agent + 图工作流 + 多工具更稳。
- Q：SkillLoop 会不会把 CoT 暴露出来？
  - A：Task Log 不存模型私有推理，而存工程状态：目标、计划、决策依据、依赖、验证结果。它是可审计执行记录，不是无限制暴露思维链。
