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
