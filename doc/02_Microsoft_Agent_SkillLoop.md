# 项目深度解析：微软 BingViz Agents & SkillLoop

## 项目背景 (Context)
随着 LLM 的兴起，团队面临两个核心痛点：
1.  **业务分析效率低**: Bing 业务每天产生海量数据，分析师（Analyst）需要花费大量时间在 Kusto (SQL) 上查询，且难以进行深度的多维度归因（例如：为什么德国市场 RPM 掉了？）。
2.  **Agent 开发难**: 在构建自动化工具时，传统的 ReAct 模式极不稳定，容易陷入死循环或产生幻觉，且长周期任务（如写代码）容易丢失上下文。

---

## 详细自述 (Script)

### 1. BingViz Agents - 深度分析引擎
“关于 BingViz，我的核心贡献是构建了一套**基于 MCP (Model Context Protocol) 的深度业务分析引擎**。
传统的 Text-to-SQL 只能回答简单的‘是什么’，无法回答‘为什么’。我设计了一个基于 **LangGraph** 的图导向执行框架，取代了不稳定的 ReAct 循环。
具体来说，我设计了 **Automatic Context Propagation (自动上下文传播)** 机制。当 Agent 发现‘欧洲 RPM 下降’这一线索时，系统会自动将这个 Context 锁定，并触发下一级子任务——‘维度下钻’，自动去检查 Query 分布、广告覆盖率等指标，而不是完全依赖 LLM 随机发散。
利用这套架构，我们成功实现了一个全自动归因 Case：Agent 自动发现欧洲市场 RPM 低不是因为广告系统差，而是因为用户大量搜索 News/Weather 等无商业意图的词。这个结论直接纠正了业务的战略方向。”

### 2. SkillLoop - 意图驱动开发范式 (Vibe Coding)
“SkillLoop 是我在 Hackathon 中的获奖项目，主要解决 Agent 在长周期任务中‘记不住、做不对’的问题。
我首创了 **Document-Driven Development (文档驱动开发)** 范式。我强制 Agent 在执行任何操作前，必须维护一份 **Structured Task Log (结构化任务日志)**。
这不仅仅是一个 Memory，而是一份动态更新的‘工程文档’。Agent 必须先在文档里写下‘我决定做什么’、‘依赖是什么’、‘已完成什么’，然后才去写代码。这就像强迫 AI 养成写技术方案的习惯。
这个设计极大地降低了 LLM 的幻觉率，使得长达数小时的 Coding 任务成为可能。这个项目最终获得了全球季军，并得到了 Mustafa 的认可。”

---

## 模拟问答 (Q&A)

### Q1: 为什么选择 LangGraph 而不是直接用 LangChain 的 Chain？
**A:**
LangGraph 提供了**循环（Cyclic）**和**状态管理（State Management）**的能力，这对于复杂 Agent 至关重要。
*   LangChain 的 Chain 通常是 DAG（有向无环图），是一条路走到黑的。
*   但在业务归因中，Agent 经常需要“回头看”或者“重试”。例如，查了维度 A 没问题，需要回退去查维度 B。LangGraph 允许我定义图的节点和边，能够更灵活地控制 Agent 的跳转逻辑，实现更可控的自愈和规划。

### Q2: 既然 ReAct 不稳定，你是怎么改进的？具体提到的 Automatic Context Propagation 是什么？
**A:**
ReAct 的问题是过于依赖 LLM 自身的推理能力，容易跑偏。
我的改进方案是 **Plan-and-Execute** 的变体。
*   **Automatic Context Propagation** 指的是：我将分析过程建模为一颗树。父节点（如“RPM分析”）产生的关键信息（如“问题出在德国”），会被强制写入一个全局的 Context Object，并显式地传递给子节点（如“Query分析 Agent”）。
*   子节点不需要重新去原来的对话历史里翻找线索，而是直接从 Context Object 读取约束条件。这极大减少了 Token 消耗，也保证了逻辑的一致性。

### Q3: SkillLoop 中的 Structured Task Log 具体包含哪些字段？为什么能解决幻觉？
**A:**
Task Log 主要包含：`Current Goal`, `Planned Steps`, `Decision History`, `Known Dependencies`, `Validation Results`。
*   **解决幻觉**: 幻觉通​​常源于 LLM 忘记了之前的约束或捏造了不存在的依赖。通过强制 AI 显式地“写下来”它已知的事实，相当于是做了一次 **Chain-of-Thought 的外部化 (Externalized CoT)**。
*   **Self-Correction**: 如果代码运行报错，AI 会对比 Log 中的 `Planned Steps` 和实际结果，更容易发现偏差。

### Q4: 看起来你结合了 ClickHouse 和 Databricks，数据一致性和延迟怎么处理？
**A:**
这是一个典型的 **Lambda 架构** 变体。
*   **ClickHouse** 用于 T+0 的实时数据扫描，用来发现突发的指标异动（Alerting）。
*   **Databricks** 用于 T+1 的离线深度归因，因为复杂的 Join 和大规模 Query Log 分析在 ClickHouse 上太贵且慢。
*   Agent 在设计时有明确的分工：先用 CK 快速定位“哪里出了问题”，再生成 PySpark 代码推送到 Databricks 跑“为什么出问题”。

### Q5: 你在 Agent 落地中遇到的最大坑是什么？（Bad Case）
**A:**
最大的坑是 **Prompt Injection** 和 **Tool Arguments Hallucination**。
*   比如 Agent 经常捏造数据库里不存在的列名。
*   **解决**: 我引入了 **Schema Validation** 层。在 Agent 调用工具前，先拉取最新的 Table Schema 做一次 RAG 校验，确保生成的 SQL 字段是真实存在的。

### Q6: 这里的 MCP (Model Context Protocol) 起了什么作用？
**A:**
MCP 实现了 Tool 的**标准化解耦**。
*   在没有 MCP 之前，Tool Definition 是硬编码在 Agent 代码里的。
*   用了 MCP 后，Databricks 和 Kusto 被封装成了独立的 MCP Server。Agent 只需要握手，就能动态获取工具有哪些能力。这意味着我的 Agent 可以无缝对接任何支持 MCP 的数据源，极大地提高了扩展性。
