# 张思凡 (Sifan Zhang) - 资深技术专家 / AI 领域探索者

## 个人简介
拥有 5 年大厂核心算法与工程落地经验（阿里 + 微软）。深耕推荐系统、搜索引擎与计算广告领域，具备从底层数据挖掘、召回排序算法设计到全链路架构优化的完整技术视野。
近期专注于 Generative AI 与 Agent 方向，致力于探索 LLM Native 的应用开发新范式。具备极客精神，善于用跨学科思维（控制论、投票理论）解决复杂工程难题。

---

## 工作经历

### 微软 (Microsoft) | Software Engineering II | 2022.11 - 至今

**项目一：BingViz Agents - 深度业务分析 Agent 框架 (Deep Analytic Framework)**
*核心开发者 / 业务落地*

> **背景与痛点**：
> Bing App (Sapphire) 业务面临复杂的跨市场变现效率（RPM）归因难题（如欧洲市场 RPM 显著低于美洲），传统分析流程依赖人工编写 SQL 且难以复用，现有 Copilot 无法处理长链路的深度归因分析。

*   **Deep Analytic Engine 架构设计**：
    *   主导开发了基于 **LLM + MCP (Model Context Protocol)** 的深度分析 Agent 框架。设计了 **Workflow-based Agent** 范式，支持基于 Scenario（场景剧本）的 Step-by-Step 执行模式，取代不稳定的 ReAct 循环。
    *   **上下文管理 (Context Management)**：实现了精细化的 Context Manager，在“大盘扫描 -> 维度拆解 -> 竞品对比”的多步分析链条中，智能传递关键结论与数据切片，确保持续推理的连贯性。
    *   **业务落地 (Sapphire RPM Analysis)**：无需人工干预，Agent 自动产出了关于欧洲市场 RPM 低下的关键结论（用户搜索 Query 结构偏向无广告意图，而非广告系统能力不足），并通过数据验证了竞品差异，直接支持了业务战略决策。

**项目二：SkillLoop - 内部技能交易平台 (Vibe Coding 实践)**
*Team Lead / Hackathon 项目发起人*

> **项目背景**：
> 在 Microsoft Global Hackathon 2025 中，探索 **"Vibe Coding"** 新范式——即通过自然语言意图（Vibe）驱动复杂系统的构建与运行，解决企业内部隐性知识流动难题。

*   **技术实现 (Doc-First Engineering)**：
    *   **AI 驱动的技能匹配**：开发了基于 NLU/NLP 的 Copilot 对话框，自动识别用户意图（学习/社交）与实体（技能/时间），通过多维度匹配算法（信誉/社交图谱）实现精准人脉推荐。
    *   **Doc-First 开发流程**：设计了基于 System Prompt 的 **Task Log 机制**，强制 AI 在编写代码前先分析文档（Dev.md, 开发说明书）并生成详细的任务日志。这种方法有效解决了 AI 编程中的上下文丢失与逻辑幻觉问题，保证了 Hackathon 高压环境下的代码质量与架构一致性。
    *   **完整闭环**：实现了从自然语言交互到 Teams 跳转、Graph API 日历集成及时间币支付的完整业务流。

> **项目影响力 (Impact)**：
> *   **Global Award**：荣获 Microsoft Global Hackathon 2025 **全球三等奖** (Global 3rd Place)，从全球数千个项目中脱颖而出。
> *   **高层汇报**：入选 **OPE (Office of the President)** 标杆项目，代表中国区向 **Mustafa Suleyman** (Microsoft AI CEO) 进行汇报与演示，获得高度评价。

**项目三：Meta Smart Match (MSM) - Bing 搜索广告智能语义匹配系统**
*担任 Tech Lead (核心技术负责人)*

> **业务背景**：
> Bing 搜索广告系统原有的匹配机制（EEM/EPM）严重依赖字面精确匹配，导致长尾流量变现效率低，系统处于“被动匹配”状态。

*   **多源异构图挖掘与语义扩展 (Graph-based Mining)**：
    *   构建 **Query-Ad 点击二部图**，利用 Transitive Join 算法挖掘非直观的共现关系，突破词法限制。
    *   引入生成式语义扩展，结合 Topic Model 与 PLM 离线泛化生成高置信度映射表，实现毫秒级语义检索。
*   **分层排序架构 (Cascading Ranking)**：
    *   设计 **L1 粗排 + L2 精排** 漏斗架构。在 L1 阶段融合 **Rank Score**（语义相关性）、**CTR Score** 与 **Bid**，利用 **Pareto Optimization** 平衡用户体验与变现效率。

> **量化产出**：
> *   **商业价值**：在北美市场实现 **RPM +10%** 的显著提升。
> *   **荣誉**：荣获微软内部 **Greatness Award**。

---

### 阿里巴巴 (Alibaba) | 推荐算法工程师 (SDE 2) | AE 技术部 | 2020.07 - 2022.10

*   **排序模块：深度控制网络 (Deep Control Network via PID)**
    *   将 **PID 控制理论** 引入 LTR 训练，构建实时深度控制模块，动态调整 Loss 权重以对齐业务目标，取得 **UV 价值 +12%**。
*   **召回模块：基于投票理论的个性化融合 (Borda Count Fusion)**
    *   提出基于 **Borda 计数法** 的多路召回融合方案，利用黑盒优化解决“截断马太效应”，取得 **GMV +2.4%**。
*   **全链路稳定性**：构建全场域评估体系与缓冲队列，解决 Label 回流延迟与链路耦合问题。
