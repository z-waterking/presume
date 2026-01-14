# 综合素质与行为面试 (Behavioral & Soft Skills)

## 经历挑战 (Challenges)

### 1. Microsoft Global Hackathon - SkillLoop
*   **Situation**: 我们只有3天时间，要开发一个完整的 Agent 协作平台，且队员分布在不同国家（时差）。
*   **Task**: 我作为队长，需要定义技术路线并协调进度。
*   **Action**:
    *   **架构定调**: 我力排众议，决定不做花哨的 UI，而是专注于后端 "Task Log" 协议的健壮性（Doc-Driven），这是我们的核心差异化（Differentiator）。
    *   **分工**: 我负责核心 Prompt 和 状态机设计，让前端队友专注于可视化。
    *   **Demo 策略**: 在向评委（包括 Mustafa）展示时，我特意设计了一个“出错-自愈”的环节（Agent 写错代码-读取 Log-自动修正），展示系统的 Resilience。
*   **Result**: 获得了 Global 3rd Place。这证明了我在高压下的技术决策能力和 Storytelling 能力。

## 领导力与影响力 (Leadership)

### 2. 推动 MSM 项目落地
*   **Situation**: 刚接手 MSM 时，组内对于引入 Graph 召回有争议，担心工程复杂度过高，维护成本大。
*   **Action**:
    *   **MVP (最小可行性产品)**: 我没有一开始就推全量图，而是先离线跑了一个小规模的 Query 集合，算出潜在 Revenue 增益。
    *   **数据说话**: 我展示了 bad case vs good case 的对比，证明现有基于 Keyword 的方法确实到了瓶颈。
    *   **渐进式重构**: 我设计了对现有系统侵入性最小的 Side-car 架构，独立部署 Graph Server，打消了架构师的顾虑。
*   **Result**: 项目顺利上线，成为年度重点项目。

---

## 模拟问答 (Q&A)

### Q1: 你也是创业过的（智法互动），为什么后来选择进大厂？创业经历给了你什么？
**A:**
*   **原因**: 那个阶段我意识到要想把 AI 产品做好，需要强大的数据基础设施和算力支持，这在初创团队很难具备。大厂提供了最好的练兵场（海量 Query、大規模集群）。
*   **收获**: 创业经历培养了我的 **Product Sense**。我不再只把自己当做一个“写代码的”，而是会算帐（ROI）。在做 BingViz 时，我不是为了炫技用 LangGraph，而是因为这样能切实解决分析师的痛点。我也这更关注 User Experience（比如 Latency 也是体验的一部分）。

### Q2: 同事怎么评价你？
**A:**
*   **Reliable (靠谱)**: 交付的东西不仅 Code Quality 高，文档也齐全（Like my Doc-Driven Dev style）。
*   **Innovative (极客)**: 总是能把新的论文（比如 Pareto, MCP）转化成生产力的工程实践。
*   **Collaborative**: 愿意分享，经常在组内做 Tech Talk 分享新技术。

### Q3: 面对 Deadline 压力，你如何管理优先级？
**A:**
*   **Eisenhower Matrix**: 区分紧急和重要。
*   **Communication**: 及时向上管理。如果发现进度可能通过，第一时间同步 Risk，并给出 Plan B（比如砍掉非核心 Feature，保住 MVP 上线）。
*   **Focus**: 在 SkillLoop Hackathon 中就是典型的例子，砍掉所有非必要的 UI，只保留最核心的 Log 机制。
