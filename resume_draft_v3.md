# 张思凡 (Sifan Zhang) - 资深技术专家 / AI 领域探索者

## 个人简介
拥有 5 年大厂核心算法与工程落地经验（阿里 + 微软）。深耕推荐系统、搜索引擎与计算广告领域，具备从底层数据挖掘、召回排序算法设计到全链路架构优化的完整技术视野。
近期专注于 Generative AI 与 Agent 方向，致力于探索 LLM Native 的应用开发新范式（Vibe Coding）。具备极客精神，善于用跨学科思维（控制论、投票理论）解决复杂工程难题。

---

## 工作经历

### 微软 (Microsoft) | Software Engineering II | 2022.11 - 至今

**项目一：AI Agent 平台 & BingViz Agents (Vibe Coding)**
*核心开发者 / Hackathon Team Lead*

> **背景与痛点**：
> 在处理 Bing App (Sapphire) 的复杂业务分析（如跨市场 RPM 归因）时，传统流程极其依赖分析师手动编写 SQL、清洗多源数据（SLAPI, MvLog）并进行归因拆解，效率低下且难以复用。现有的 Copilot 往往只能回答单点问题，缺乏执行长链路复杂思考（Chain of Thought）的能力。

*   **BingViz Agents 架构设计 (Deep Analytic Framework)**：
    *   主导开发了基于 **LLM + MCP (Model Context Protocol)** 的深度分析 Agent 框架。
    *   **Workflow Agent 范式**：不同于传统的 ReAct 循环，设计了 **DeepAnalyticEngine**，支持基于 Scenario（场景剧本）的 Step-by-Step 执行模式。系统能够自动加载分析剧本（如 `comprehensive_rewards_analysis`），按顺序执行“大盘扫描 -> 维度拆解 -> 竞品对比 -> 归因分析”的完整思维链。
    *   **上下文管理 (Context Management)**：实现了精细化的 Context Manager，在多步执行中智能传递关键结论与中间数据表，解决了 LLM 容易遗忘前序步骤结果的问题。
    *   **技术栈**：OpenAI API, LangChain, Azure Kusto (ADX), Databricks SQL, MCP Server, Python。

*   **Vibe Coding 探索：SkillLoop (Microsoft Global Hackathon 2025)**：
    *   **概念验证**：在 Hackathon 中提出并实现了 **"SkillLoop"** —— 一个基于 AI Copilot 的内部技能交易平台。项目验证了 **"Vibe Coding"** 的核心理念：通过自然语言意图（Vibe）驱动复杂系统的构建与运行。
    *   **AI 驱动的技能匹配**：开发了基于 NLU/NLP 的 Copilot 对话框，自动识别用户意图（"学习" vs "找搭子"）并提取实体（技能、地点、熟练度），通过多维度匹配算法（信誉、时间、社交关系）推荐最佳人选，而非简单的关键词搜索。
    *   **深度集成与闭环**：实现了从意图识别 -> Teams 跳转沟通 -> Microsoft Graph 日历集成 -> 时间币支付 -> 互评反馈的完整业务闭环。
    *   **工程化实践**：建立了严格的 "Doc-First" 开发流程，使用 System Prompt 强制要求 AI 在开发前先进行文档分析，再生成 Task Log，确保了在 Hackathon 高强度开发下的代码质量与架构一致性。

> **影响力 (Impact)**：
> *   **Global Award**：SkillLoop 项目荣获 Microsoft Global Hackathon 2025 **全球三等奖** (Top Tier)。
> *   **高层汇报**：作为 OPE (Office of the President / Product Excellence) 的标杆项目，代表中国区向 **Mustafa Suleyman** (Microsoft AI CEO) 进行汇报展示。
> *   **技术复用**：SkillLoop 验证的 Agent 范式与 "Doc-First" 工程化方法论，后续被成功迁移至 **BingViz Agents** 项目中，助力了 Bing App (Sapphire) 业务分析的自动化落地。

**项目二：Meta Smart Match (MSM) - Bing 搜索广告智能语义匹配系统**
*担任 Tech Lead (核心技术负责人)*

> **业务背景**：
> Bing 搜索广告系统原有的匹配机制（EEM/EPM）严重依赖于字面或短语层面的精确匹配，难以捕捉长尾查询（Long-tail Queries）背后的真实用户意图，导致系统处于“被动匹配”状态，损失了巨大的潜在商业价值。

*   **多源异构图挖掘与语义扩展 (Graph-based Mining & Semantic Expansion)**：
    *   构建 **Query-Ad 点击二部图 (Co-click Graph)**，利用 Transitive Join 和 Random Walk 算法挖掘非直观的共现关系。即使 Query 和 Keyword 字面全不相同，只要它们共享相似的广告点击行为，即可被系统捕获。
    *   引入 **离线生成式语义扩展** 流程。结合 Topic Model 和 Query Rewrite 技术，利用预训练模型（PLM）泛化出高置信度的 Query-Keyword 映射表，实现高效检索。

*   **分层排序架构与多目标融合 (Cascading Ranking System)**：
    *   设计了 **L1 粗排 (Scoring)** 与 **L2 精排 (Re-ranking)** 的漏斗架构。
    *   L1 阶段融合 **Rank Score**（语义相关性）、**CTR Score**（点击率预估）与 **Bid**（出价），引入 **Pareto Optimization** 思想，在保证用户体验前提下最大化 RPM。

> **量化产出 (Key Results)**：
> *   **收入提升**：在北美市场（North America）主要负责 L1 层级优化，实现 **RPM +10%** 的显著增长。
> *   **奖项荣誉**：荣获微软内部 **Greatness Award**。

---

### 阿里巴巴 (Alibaba) | 推荐算法工程师 (SDE 2) | AE 技术部 | 2020.07 - 2022.10

*   **排序模块：深度控制网络 (Deep Control Network via PID)**
    *   将 **PID 控制理论** 引入深度学习训练（LTR），设计实时深度控制模块，根据业务目标（Loss/AUC）动态调整融合权重，取得 **UV 价值 +12%**。
*   **召回模块：基于投票理论的个性化融合 (Borda Count Fusion)**
    *   提出基于 **Borda 计数法** 的多路召回融合方案，利用黑盒优化思想解决“截断马太效应”，取得 **GMV +2.4%**。
*   **全场域优化**：构建全链路评估体系与缓冲队列机制，解决 Label 延迟与链路耦合问题。
