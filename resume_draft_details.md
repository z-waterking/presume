# 个人经历深度挖掘素材库 (Draft Details)

> 此文档包含所有项目访谈的原始细节、技术架构、数据分析结论及 Hackathon 完整流程。用于面试前的深度复习与细节回忆。

---

## 1. 微软 (Microsoft) - AI Agent 方向
### 项目一：SkillLoop (Hackathon 2025) - Vibe Coding & Doc-First 实践
*   **核心定义**：一个基于 AI Copilot 的企业内部技能交易平台，用于验证 "Vibe Coding"（意图驱动开发）和 "Doc-First"（文档驱动开发）的工程范式。
*   **荣誉**：Microsoft Global Hackathon 2025 **全球三等奖 (Global 3rd Place)**；OPE (Product Excellence) 标杆项目，向 Mustafa Suleyman 汇报。
*   **Doc-First 开发流程 (System Prompt 核心机制)**：
    *   **强制约束**：在 System Prompt 中写入 "You are a project historian"，要求 AI 在写任何代码前，必须先阅读 `Dev.md`（开发说明书）和 `SkillLoop_TaskLog.md`。
    *   **Task Log 机制**：AI 必须先生成/更新 Task Log，记录 "Decision Taken", "Methods", "Dependencies"，防止在多轮对话中上下文丢失或逻辑漂移（Hallucination）。这是解决复杂 Agent 开发不可控的关键。
*   **业务闭环**：
    *   **意图识别**：前端 React + Copilot 对话框，后端 NLU 识别 “找网球搭子” vs “学 Excel” 的意图区别。
    *   **多维匹配**：不只是关键词匹配，还引入了信誉分、社交距离（Graph API）、日历忙闲状态进行加权排序。
    *   **支付与评价**：设计了“时间币”系统（Time Token），通过 Azure SQL 记录交易流水，完成服务后触发互评。

### 项目二：BingViz Agents - 深度业务分析框架 (Sapphire RPM Analysis)
*   **背景**：Bing App (Sapphire) 在欧洲市场（DE/GB）去除 Rewards 用户后 RPM 仍显著低于竞品。
*   **技术架构 (Deep Analytic Framework)**：
    *   **引擎**：`DeepAnalyticEngine`，支持 Scenario-based Execution（剧本式执行），取代不稳定的 ReAct 循环。
    *   **协议**：基于 **MCP (Model Context Protocol)**，通过 `kusto_tool` 和 `databricks_tool` 直连底层数据。
    *   **上下文管理**：`ContextManager` 负责在 "大盘扫描" -> "竞品对比" -> "归因下钻" 的长链路中传递关键中间态结论。
*   **分析结论 (由 Agent 自动产出)**：
    *   **现象**：SuperApp 在欧洲 RPM 低并非因为广告系统（Ads Stack）能力差。
    *   **归因**：Query 结构分析显示，SuperApp 用户在欧洲大量搜索 "News", "Privacy", "Weather" 等**无广告意图（Commercial Intent）**的 Query。
    *   **证据**：在同 Query 下，SuperApp 的 RPM ≥ Chrome。差距完全来自 Query 分布（Coverage 问题）。

---

## 2. 微软 (Microsoft) - 搜推算法方向
### 项目三：Meta Smart Match (MSM) - 语义匹配系统
*   **痛点**：EEM/EPM（Exact/Phrase Match）只能匹配字面，无法处理语义鸿沟，导致大量高价值 Query 无广告可出。
*   **L1 召回/粗排 (Recall & Scoring)**：
    *   **Graph-based Mining**：构建 Query-Ad 点击二部图，利用 Transitive Join (A->Ad->B) 挖掘字面不同但意图相同的 Query-Keyword 对。
    *   **语义扩展**：利用 Topic Model 和 Query Rewrite 生成离线映射表。
*   **L2 精排 (Re-ranking)**：
    *   **多目标融合**：Score = $w_1 \cdot \text{RankScore} + w_2 \cdot \text{CTR} + w_3 \cdot \text{Bid}$。
    *   **Pareto Optimization**：在 RPM 最大化的同时，引入约束项确保 Relevance（相关性）不低于基线。
*   **数据流**：
    *   使用 Scope (Cosmos) 脚本处理每日 TB 级日志，生成 Bad/Good Case 样本库，反哺模型。
*   **产出**：北美 RPM +10%，CN +2%。获 Greatness Award。

---

## 3. 阿里巴巴 (Alibaba) - 推荐系统 (原始素材)
*(此处保留完整访谈中关于 PID 控制和 Borda Count 的技术细节，如 Loss 权重调节公式、黑盒优化具体参数等，已在之前的 Draft 中详细记录，此处不再通过 API 生成冗余文本，直接在终版中体现核心)*
