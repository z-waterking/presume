# 综合素质与行为面试 (Behavioral & Soft Skills)

---

## 经历挑战 (Challenges)

### 1. Microsoft Global Hackathon — SkillLoop
*   **Situation**: 我们只有3天时间，要开发一个完整的 Agent 协作平台，且队员分布在不同国家（时差）。
*   **Task**: 我作为队长，需要定义技术路线并协调进度。
*   **Action**:
    *   **架构定调**: 力排众议，决定不做花哨的 UI，而是专注于后端 "Task Log" 协议的健壮性（Doc-Driven），这是我们的核心差异化（Differentiator）。
    *   **分工**: 我负责核心 Prompt 和状态机设计，让前端队友专注于可视化。
    *   **Demo 策略**: 在向评委（包括 Mustafa）展示时，特意设计了一个"出错-自愈"的环节（Agent 写错代码→读取 Log→自动修正），展示系统的 Resilience。
*   **Result**: 获得了 Global 3rd Place。证明了我在高压下的技术决策能力和 Storytelling 能力。

### 2. 推动 MSM 项目落地
*   **Situation**: 刚接手 MSM 时，组内对引入 Graph 召回有争议，担心工程复杂度过高，维护成本大。
*   **Action**:
    *   **MVP**: 没有一开始就推全量图，先离线跑小规模 Query 集合，算出潜在 Revenue 增益。
    *   **数据说话**: 展示 bad case vs good case 对比，证明现有 Keyword 方法确实到了瓶颈。
    *   **渐进式重构**: 设计对现有系统侵入性最小的 Side-car 架构，独立部署 Graph Server，打消架构师顾虑。
*   **Result**: 项目顺利上线，成为年度重点项目。

---

## 领导力与影响力 (Leadership)

### 3. 创业经历 — 智法互动
*   **Situation**: 研究生期间创立法律科技创业团队。
*   **收获**:
    *   培养了 **Product Sense**——不再只把自己当"写代码的"，而是会算帐（ROI）。
    *   在做 BingViz 时，不是为了炫技用 LangGraph，而是因为能切实解决分析师痛点。
    *   更关注 User Experience（Latency 也是体验的一部分）。
*   **为什么进大厂**: 意识到 AI 产品需要强大的数据基础设施和算力支持，大厂提供了最好的练兵场。

---

## 模拟问答 (Q&A)

### Q1: 创业过，为什么后来选择进大厂？
**A:**
*   那个阶段我意识到要想把 AI 产品做好，需要强大的数据基础设施和算力支持，在初创团队很难具备。大厂提供了最好的练兵场（海量 Query、大规模集群）。
*   创业经历培养了我的 Product Sense。我做技术决策时始终会考虑 ROI 和用户价值。

### Q2: 同事怎么评价你？
**A:**
*   **Reliable (靠谱)**: 交付的东西不仅 Code Quality 高，文档也齐全（Doc-Driven Dev style）。
*   **Innovative (极客)**: 总是能把新的论文（如 Pareto, MCP）转化成生产力的工程实践。
*   **Collaborative**: 愿意分享，经常在组内做 Tech Talk 分享新技术。

### Q3: 面对 Deadline 压力，你如何管理优先级？
**A:**
*   **Eisenhower Matrix**: 区分紧急和重要。
*   **Communication**: 及时向上管理。如果发现进度有风险，第一时间同步 Risk，并给出 Plan B（如砍掉非核心 Feature，保住 MVP 上线）。
*   **Focus**: 在 SkillLoop Hackathon 中就是典型例子，砍掉所有非必要 UI，只保留最核心的 Log 机制。

### Q4: 描述一次你与团队成员产生分歧的经历？
**A:**
*   **Situation**: MSM 项目初期，架构师认为 Graph 召回工程复杂度太高，建议放弃。
*   **Action**: 没有硬刚，而是用数据说服——先跑了一个小规模 MVP，展示了 Revenue 增益和 Bad Case 对比。然后设计了侵入性最小的 Side-car 架构，证明不会影响现有系统稳定性。
*   **Result**: 架构师被数据说服，项目顺利推进。
*   **Takeaway**: 用 MVP + 数据说话，比争论更有效。

### Q5: 你最自豪的技术成就是什么？
**A:**
*   BingViz 的归因 Case。Agent 发现欧洲 RPM 下降不是广告系统问题，而是用户搜索意图结构性偏差。
*   这不仅是技术成就，更体现了 AI 系统真正帮助人类做出更好决策的价值。整个分析过程从"发现异常"到"定位根因"全自动完成，这是从 Text-to-SQL 到 Text-to-Insight 的质变。
