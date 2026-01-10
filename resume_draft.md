# 张思凡 Sifan Zhang (Detailed Draft / 深度底稿)
13260151230 | zhangsifanbj@163.com | 北京市海淀区

## 个人简介
拥有 5 年大厂核心算法与工程落地经验（阿里 + 微软）。深耕推荐系统、搜索引擎与计算广告领域。近期专注于 Generative AI 与 Agent 方向，探索 "Vibe Coding" 与 "Doc-First" 开发范式。

---

## 核心项目深度解析 (Deep Dive)

### 1. 微软 (Microsoft) - AI Agent & Vibe Coding
**项目：SkillLoop (Hackathon 2025) - Vibe Coding & Doc-First 实践**
*   **核心定义**：一个基于 AI Copilot 的企业内部技能交易平台，用于验证 "Vibe Coding"（意图驱动开发）和 "Doc-First"（文档驱动开发）的工程范式。
*   **荣誉**：Microsoft Global Hackathon 2025 **全球三等奖 (Global 3rd Place)**；OPE (Product Excellence) 标杆项目，向 **Mustafa Suleyman** 汇报。
*   **Doc-First 开发流程 (System Prompt 核心机制)**：
    *   **强制约束**：在 System Prompt 中写入 "You are a project historian"，要求 AI 在写任何代码前，必须先阅读 `Dev.md`（开发说明书）和 `SkillLoop_TaskLog.md`。
    *   **Task Log 机制**：AI 必须先生成/更新 Task Log，记录 "Decision Taken", "Methods", "Dependencies"，防止在多轮对话中上下文丢失或逻辑漂移（Hallucination）。这是解决复杂 Agent 开发不可控的关键。
*   **业务闭环**：
    *   **意图识别**：前端 React + Copilot 对话框，后端 NLU 识别 “找网球搭子” vs “学 Excel” 的意图区别。
    *   **多维匹配**：不只是关键词匹配，还引入了信誉分、社交距离（Graph API）、日历忙闲状态进行加权排序。

**项目：BingViz Agents - 深度业务分析框架 (Sapphire RPM Analysis)**
*   **背景**：Bing App (Sapphire) 在欧洲市场（DE/GB）去除 Rewards 用户后 RPM 仍显著低于竞品。
*   **技术架构 (Deep Analytic Framework)**：
    *   **引擎**：`DeepAnalyticEngine`，支持 Scenario-based Execution（剧本式执行），取代不稳定的 ReAct 循环。
    *   **协议**：基于 **MCP (Model Context Protocol)**，通过 `kusto_tool` 和 `databricks_tool` 直连底层数据。
    *   **上下文管理**：`ContextManager` 负责在 "大盘扫描" -> "竞品对比" -> "归因下钻" 的长链路中传递关键中间态结论。
*   **分析结论**：
    *   **现象**：SuperApp 在欧洲 RPM 低并非因为广告系统（Ads Stack）能力差。
    *   **归因**：Query 结构分析显示，SuperApp 用户在欧洲大量搜索 "News", "Privacy", "Weather" 等**无广告意图（Commercial Intent）**的 Query。
    *   **证据**：在同 Query 下，SuperApp 的 RPM ≥ Chrome。差距完全来自 Query 分布（Coverage 问题）。

### 2. 微软 (Microsoft) - 搜推算法方向 (MSM)
**项目：Meta Smart Match (MSM) - Bing 搜索广告智能语义匹配系统**
*   **痛点**：EEM/EPM（Exact/Phrase Match）只能匹配字面，无法处理语义鸿沟。
*   **L1 召回/粗排 (Recall & Scoring)**：
    *   **Graph-based Mining**：构建 Query-Ad 点击二部图，利用 Transitive Join (A->Ad->B) 挖掘字面不同但意图相同的 Query-Keyword 对。
    *   **语义扩展**：利用 Topic Model 和 Query Rewrite 生成离线映射表。
*   **L2 精排 (Re-ranking)**：
    *   **多目标融合**：Score = $w_1 \cdot \text{RankScore} + w_2 \cdot \text{CTR} + w_3 \cdot \text{Bid}$。
    *   **Pareto Optimization**：在 RPM 最大化的同时，引入约束项确保 Relevance（相关性）不低于基线。
*   **产出**：北美 RPM +10%，CN +2%。获 Greatness Award。

---

### 3. 阿里巴巴 (Alibaba) - 推荐系统 (技术细节详述)

**排序：针对推荐策略实时调控的需要，提出实时深度控制模块，并横向拓展助力全场域提升**
*   **深度控制 LTR**：作为大模型的后链路，摒弃传统的手工调权，直接设定线上期望业务目标，通过深度控制网络（PID），动态调整各 label 所对应的 loss 融合权重，以寻找当前限制下的最优参数。通过控制离线 auc 及模型参数变化的阈值，来保证在线效果的稳定性。取得 UV 价值+12%的效果。
*   **全场域优化**：对实时流程的上下游进行了抽象。样本侧，设置缓存区以归因延迟 label，设置缓冲队列以保持正负样本比例认定。利用 drop rank 选取大模型 top10%的特征用于 LTR 训练。构建了从数据源、样本构造、到模型训练、线上生效等一整套配置化流程。根据不同场景的业务目标及场域特性，分别选用相应的特征、模型及打分组合公式，并进行超参寻优。于各个场域均取得了 IPV +2%，UV 价值 +2%以上的收益。

**召回：针对多路召回合并时带来的马太效应，提出个性化多路召回融合模块**
*   **个性化召回融合**：针对线上多路召回(>=10)合并时，截断数量由人工拍定造成的马太效应与效率损失问题，提出了个性化召回融合方法。利用黑盒优化的思想，引入投票理论和 borda 计数法，根据 item 的相对顺序及召回对应的权重，确定 item 最终顺序。离线侧，采用生成器动态生成各路召回融合权重；在线侧，利用评估器对不同权重的线上效果进行评估，同时指导生成器的训练。取得了 GMV +2.4%的效果。

**全链路：离线评估召回有效性，研究召回与排序的漏斗模式带来的耦合问题**
*   **离线评估**：搭建 hitrate 评估流程，通过对超过 20 种召回源进行统一尺度下的评估，以离线验证召回数据源的有效性；通过各路召回组合评估，以发现可互补的召回源；通过粗排打分结果评估，以评定粗排的有效性。
*   **解除耦合**：针对推荐系统漏斗模式所带来的上下游链路耦合问题，评估各路召回曝光占比及转化效率，以验证耦合现象的存在。与精排联动，对召回来源进行泛化，削减耦合程度，助力更多召回源上线。

---
## 教育背景
*   **北京理工大学 (985)** | 硕士 | 计算机学院 计算机科学与技术 | 2017.09 – 2020.07
*   **暨南大学 (211)** | 本科 | 电气信息学院 包装工程 | 2012.09 – 2016.07
