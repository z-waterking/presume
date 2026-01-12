# 简历修改建议 (Review Feedback)

你好！我是你的 AI 简历顾问。基于你提供的代码库 (`BingVizAgents`, `SkillLoop`, `MSM DeepDive`) 和原简历 (`resume_final.md`)，我为你整理了以下修改建议。

你的简历底子非常好，涵盖了 **LLM Agent**、**搜推算法** 和 **Hackathon 创新** 三个核心亮点。我的建议主要集中在：**增强技术颗粒度 (Tech Depth)**、**补充缺失的关键技术栈 (Missing Tech Stack)** 以及 **强化方法论的描述 (Methodology Refinement)**。

---

## 1. 总体建议 (General Advice)

*   **技术栈显性化**: 你的项目代码中包含了很多业界热门且高价值的技术栈（如 `LangGraph`, `ClickHouse`, `Databricks`, `FastMCP`, `UV`），但在简历中体现得不够具体。建议在“专业技能”栏或项目描述中明确列出。
*   **方法论命名**: 你非常有“造词”和总结方法论的能力（如 "Doc-First", "Deep Analytic Engine"）。这很棒，建议进一步不仅描述“是什么”，还要描述“解决了什么痛点”以及“底层的技术实现机制”。

---

## 2. 项目细节深度挖掘 (Deep Dive by Project)

### 项目一：BingViz Agents (Agent 平台)
**现状**: 描述了 Deep Analytic Engine 和 Workflow-based 范式。
**代码发现**: 
*   使用了 `LangGraph` (在 README dependencies 中) 来做语义层。
*   实现了 `FastMCP` (Model Context Protocol) 做轻量级服务器。
*   数据源连接了 **ClickHouse** (Titan API) 和 **Databricks**。
*   Deep Analytic Framework 实现了 "Automatic Context Propagation" (上下文自动尤其是跨步骤传播) 和 "Graph-based Dependency" (从 `step_dependency` 推断)。

**修改建议**:
*   **强化架构描述**: 明确提到使用了 **ClickHouse** 处理实时数据和 **Databricks** 处理离线数据，展示大数据处理能力。
*   **技术关键词**: 增加 `LangGraph` (如果确实使用了 DAG 结构)、`Semantic RAG` (README 中提到 feature) 和 `FastMCP`。
*   **措辞优化**:
    *   *原*: "设计了 Workflow-based 范式..."
    *   *改*: "基于 **LangChain/LangGraph** 构建图导向 (Graph-based) 的执行引擎，设计 **Context Propagation** 机制实现多步骤分析中的上下文自动流转，解决复杂任务中信息丢失的问题。"

### 项目二：SkillLoop (Vibe Coding / SkillLoop)
**现状**: 强调了 Doc-First 和 System Prompt。
**代码发现**: 
*   `SystemPrompt` 文件中定义了极具结构化的 **Task Log** (包含 Goal, Methods, Functions, Decisions, Dependencies)。
*   这不仅是 "Doc-First"，更是一种 **"Stateful Interactive Coding"** 或 **"Resumable Context Management"** (可恢复上下文管理)。
*   核心价值在于解决 Agent 的“失忆”和“逻辑断层”。

**修改建议**:
*   **深化 "Doc-First" 定义**: 我们可以把它描述得更具技术含量。例如 "Single Source of Truth (SSOT) driven development"。
*   **具体化 Prompt Engineering**: 提到你设计了结构化的 **Task Log Protocol**，强制 LLM 进行 **Chain-of-Thought (CoT)** 记录，甚至可以提到 "Self-contained State" (自包含状态)，使得任何新 Agent 接入时能通过读取 Log 零成本恢复上下文。
*   **影响力升级**: "Global 3rd Place" 非常棒，可以补充这是 "Microsoft Global Hackathon" (如果还没写全)，强调竞争的激烈程度。

### 项目三：Meta Smart Match (搜索广告)
**现状**: 提到语义召回、Transitive Join、Pareto Optimization。
**代码/文档发现**: 
*   `DeepDive1` 提到了具体的 pipeline 步骤： Porter Stemmer (词干提取), Co-occurrence (共现), Transitive Join (传递连接 $Q_1-K_1-Q_2-K_2 \rightarrow Q_1-K_2$).
*   提到了 Scope 脚本 (Microsoft 内部的大数据处理语言) 和 L1/L2 漏斗。

**修改建议**:
*   **通用化术语**: 如果面试微软以外的公司，`Scope` 可以转化为 **"Distributed Dataflow System"** (分布式数据流系统) 或类比 **Spark/MapReduce**。
*   **量化 L1/L2 差异**: 你的笔记里提到了 L1 粗排用了三个模型 (Rank, CTR, AD) 并进行阈值过滤。可以强调你在 L1 阶段引入了 **"Multi-Objective Constraints"** (多目标约束) 而不仅仅是 Pareto。
*   **NLP 技术**: 可以补充使用了 **Porter Stemmer** 或 **Query Rewriting** (查询改写) 技术来增强语义匹配的泛化能力。

---

## 3. 简历具体修改示例 (Draft Revisions)

**建议将对应经历修改为如下风格（请根据实际情况微调）：**

### 微软 (Microsoft) | Software Engineering II

*   **Agent 平台架构 (BingViz)**: 主导基于 **LLM + MCP (Model Context Protocol)** 的下一代数据分析平台。
    *   **架构设计**: 融合 **ClickHouse** (实时) 与 **Databricks** (离线) 数据源，构建基于 **LangGraph** 的图导向执行引擎。设计 Deep Analytic Framework，通过 **Automatic Context Propagation** (自动上下文传播) 机制，实现复杂分析任务的 Step-by-Step 状态保持与推理。
    *   **业务落地**: 在欧洲市场 RPM 分析中，利用 Agent 自动化执行“大盘扫描 -> 维度下钻 -> 竞品对比”的长链路分析，精准定位 Query 意图结构性偏差问题，结论被采纳并作为战略调整依据。

*   **AI 开发范式探索 (SkillLoop - Hackathon Global 3rd)**: 
    *   **Doc-First Engine**: 首创 "Document-Driven Development" 范式，设计基于 **Structured Task Log** 的系统提示词协议。强制 Agent 维护一份“自包含”的工程状态日志，解决长周期开发中的上下文丢失与幻觉问题，实现 AI 编码任务的可中断与可恢复执行。
    *   **影响力**: 从全球数万名参与者中脱颖而出获 **Global 3rd Place**，并作为 OPE 标杆项目向 Microsoft AI CEO 汇报，推动了内部 "Vibe Coding" 工具链的演进。

*   **搜索广告算法 (Meta Smart Match)**:
    *   **语义召回**: 构建基于 **Transitive Join** (传递连接) 的图挖掘链路，结合 **NLP Rewriting** (Porter Stemmer/语义扩展) 技术，突破仅靠 Query-Keyword 表面匹配的限制，显著提升长尾流量变现能力。
    *   **多目标排序**: 设计 L1(粗排) + L2(精排) 漏斗架构。在 L1 阶段引入 **Pareto Optimization** 与 PID 动态调控，在由 Revenue、CTR 和 Relevance 构成的多目标空间中寻找最优解，北美市场 **RPM +10%**。

---

希望这些建议能帮到你！如果有针对某个具体段落的修改需求，欢迎随时告诉我。
