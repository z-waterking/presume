# 张思凡面试准备全集 (Full Interview Preparation)

---

## 目录 (Table of Contents)
1. [自我介绍 (Self Introduction)](#自我介绍-self-introduction)
2. [项目深度解析：微软 BingViz Agents & SkillLoop](#项目深度解析微软-bingviz-agents--skillloop)
3. [项目深度解析：微软搜索广告 (MSM)](#项目深度解析微软搜索广告-msm---召回与排序)
4. [项目深度解析：阿里巴巴推荐系统](#项目深度解析阿里巴巴推荐系统---深度控制与全链路)
5. [综合素质与行为面试 (Behavioral)](#综合素质与行为面试-behavioral--soft-skills)

---

# 自我介绍 (Self Introduction)

## 中文主要自述 (2-3分钟版本)

面试官好，我是张思凡。

我拥有5年的核心算法与工程落地经验，目前在微软中国担任 SDE 2。我的职业生涯主要分为两个阶段：

**第一阶段是在阿里巴巴（2年+）**，我专注于**推荐系统**的全链路优化。
在阿里海外电商部门，我负责了从召回、排序到全链路评估的核心模块。当时面临的主要挑战是如何在多路召回和复杂业务目标下提升效率。我主导了**实时深度控制模块（PID LTR）**和**个性化召回融合**项目，通过控制论和博弈论的方法，解决了人工调参的低效问题，最终实现了 UV 价值提升 12% 的显著成果。这段经历让我对搜推系统的底层架构和业务指标有了深刻的理解。

**第二阶段是在微软（3年+）**，我将技术栈扩展到了**搜索广告**与 **AI Agent** 领域，并更加注重工程架构与算法的结合。
在搜索广告组，针对长尾流量变现难的问题，我主导了 **Meta Smart Match (MSM)** 系统升级。我利用图挖掘不仅做到了语义召回，还设计了基于 **Pareto Optimization** 的多目标排序架构，平衡了收入与用户体验，使得北美市场 RPM 提升了 1.8%。
最近一年，我将重心利用转向了 **Generative AI**。我负责了内部 Agent 平台 **BingViz** 的核心研发，构建了基于 **LLM + MCP** 的深度分析引擎，成功实现了自动化的业务归因。此外，我在 Global Hackathon 中发起的 **SkillLoop** 项目，探索了 "Document-Driven Development" 的新范式，获得了全球季军，并向 AI CEO 做了汇报。

**总结来说**，不管是传统的搜推算法，还是前沿的 LLM 应用，我都能结合业务痛点，设计出具备高度工程可行性的解决方案。我非常期待能将我在复杂系统架构和 AI 落地方面的经验带到新的岗位上。

---

## 英文关键词版 (English Highlights)

*   **Experience**: 5 years (Alibaba + Microsoft).
*   **Domain**: Recommendation Systems, Search Ads, Generative AI (LLM & Agents).
*   **Key Achievement (MS)**: Improved Ads Revenue (RPM +1.8%) via Graph mining & Pareto Optimization. Built LLM Agent framework for automated analytics.
*   **Key Achievement (Alibaba)**: Recommendation efficiency (UV Value +12%) via PID control & Personalized Fusion.
*   **Passion**: Solve complex engineering problems with interdisciplinary approaches (Control Theory, Voting Theory).

---

## 模拟问答 (Q&A)

### Q1: 你从阿里跳槽到微软，技术栈上有什么最大的转变？你是如何适应的？
**A:**
*   **转变**: 从纯粹的“算法优化”（阿里时期更侧重模型效果、特征工程）转向了“算法+架构”的双重驱动（微软更看重 System Design 和 Engineering Excellence）。同时，领域从推荐系统扩展到了搜索广告，最近又进入了 LLM Agent 开发。
*   **适应**: 我将阿里时期积累的全链路视角带到了微软。例如在做广告 MSM 时，我没有只盯着模型 AUC，而是从数据源头（图挖掘）和架构分层（L1/L2）去解决问题。在 Agent 开发中，我利用我在搜推系统中对“召回-排序”的理解，设计了 Agent 的 Tool Selection 机制，这其实是另一种形式的“召回”。

### Q2: 为什么现在选择看新的机会？
**A:**
*   （根据实际情况回答，建议方向：）
*   寻求更大的业务 Impact，希望在一个更具创业氛围或核心业务场景中落地 LLM 应用。
*   希望将我在搜推与 Agent 结合的独特经验（RAG, Tool Use, Planning）应用到更垂直的领域。

### Q3: 你觉得自己最大的优势是什么？
**A:**
*   **跨界思维**: 不仅仅用 CS 的方法解决问题，还会引入控制论（PID做流控）、经济学（帕累托优化）、政治学（Borda 计数做融合）的理论。
*   **Full-stack AI 能力**: 既懂传统的搜推算法（Deep Learning, Ranking），又精通现在的 LLM Stack（LangGraph, MCP, RAG），这让我能设计出更稳健的混合系统。

---

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

---

# 项目深度解析：微软搜索广告 (MSM) - 召回与排序

## 项目背景 (Context)
搜索广告（Search Ads）的核心挑战在于：用户搜索的 Query 极其长尾且多样，而广告主的 Keyword 往往比较缺乏。
传统的 Exact Match (精确匹配) 和 Phrase Match (短语匹配) 只能覆盖头部流量。不仅漏掉了大量长尾查询（变现效率低），而且在排序阶段往往只看 eCPM（预估收入），导致展示了一些相关性差但出价高的广告，伤害了用户体验。

---

## 详细自述 (Script)

### 1. 语义召回体系 (Semantic Recall)
“为了解决长尾匹配问题，我构建了一套基于**图挖掘**的语义召回系统。
传统的 Embedding 召回虽然泛化性好，但经常匹配出‘形似神不似’的 Bad Case。
我设计了基于 **二部图 (Bipartite Graph)** 的挖掘算法。我利用用户的点击日志，构建了 Query-Ad 的点击图。利用 **Transitive Join (传递连接)** 思想：如果 Query A 点击了 Ad X，且 Query B 也点击了 Ad X，那么 Query A 和 B 在语义上大概率是相似的。
通过挖掘这种 A->Ad->B 的高阶关系，我们挖掘出了大量非直观的共现关系。配合 NLP Rewriting 技术做语义约束，显著提升了长尾 Query 的覆盖率。”

### 2. 分层排序架构 (Tiered Ranking with Pareto)
“召回多了，排序压力就大了。我设计了 **L1 粗排 + L2 精排** 的漏斗架构。
核心创新在于 L1 阶段引入了 **Pareto Optimization (帕累托优化)**。
以前的公式简单粗暴，就是 `Bid * pCTR`。但这会导致高出价的‘烂广告’霸屏。
我构建了一个多目标优化空间：Revenue, CTR, Relevance。我不寻求单一如 Revenue 最大化，而是在保证 Relevance 不低于某个约束（Constraint）的前提下，去使得 Revenue 的边界最大化（Pareto Frontier）。
最终在北美市场实现了 RPM +1.8% 的同时，用户投诉率没有上升，甚至通过更高的相关性提升了 CTR。”

---

## 模拟问答 (Q&A)

### Q1: 你的二部图挖掘中，如何处理热门广告（Hot Ad）带来的噪声？
**A:**
这是一个经典问题。像 "Amazon App" 这种热门广告，会被无数不相关的 Query 点击。如果直接用 A->Ad->B，会导致所有 Query 都关联在一起。
*   **因应策略**: 我引入了 **TF-IDF** 类似的思想降低热门 Ad 的权重，或者使用 **SimRank** 算法。具体实践中，我对 Ad 节点的 Degree 做了归一化惩罚。只有当两个 Query 共享了多个、且相对稀疏（Niche）的广告时，我们才认为它们强相关。

### Q2: 为什么在 L1 做 Pareto？L1 不应该主要关注性能吗？
**A:**
正因为 L1 数据量大、模型简单，所以更容易“杀错”好广告。
*   如果 L1 只按 eCPM 截断，很多相关性极好但出价稍低的广告直接就被过滤了，根本进不了 L2。
*   在 L1 做 Pareto 其实是一种**多路归并**的思想。我们不是取 Top K by Revenue，而是取 Top K from the Pareto Frontier（即：要么很赚钱，要么极度相关，要么用户极爱点）。这样为 L2 保留了更多样性的候选集。

### Q3: 你的 NLP Rewriting 具体用了什么技术？
**A:**
我们结合了传统的 **Porter Stemmer** (词干提取) 和基于 **BERT** 的离线生成。
*   Stemmer 解决单复数、时态等简单的 mismatch。
*   BERT 模型用于生成同义词改写（Paraphrasing）。为了防止漂移，我们只用高置信度的头部结果作为离线字典，而不是在线实时 Inference（为了由性能考虑）。

### Q4: Multi-Objective Optimization 具体怎么落地的？是 Scalarization 吗？
**A:**
是的，最工程化的落地方式是 **Linear Scalarization** (线性加权)。
*   公式类似于：$Score = w_1 \cdot pCTR \cdot Bid + w_2 \cdot RelevanceScore$。
*   这里的 $w$ 参数不是拍脑袋定的，而是通过 **PID 控制器** 动态调整的，或者通过离线 Grid Search 在 Pareto 曲线上找到的最佳 Trade-off 点。

### Q5: 1.8% 的 RPM 提升对于你们组来说是什么水平？
**A:**
在成熟的搜索广告系统中，通常 0.5% 的提升就算显著（Significant）了。1.8% 是一个非常大的 Step Change，因为它不仅是算法调优，而是引入了全新的召回路径（Graph）和全新的排序范式（Pareto）。这也正是我获得 Greatness Award 的原因。

---

# 项目深度解析：阿里巴巴推荐系统 - 深度控制与全链路

## 项目背景 (Context)
在阿里海外电商（AliExpress），推荐系统面临两个独特挑战：
1.  **运营干预频繁**: 经常有大促或特定的扶持任务，需要实时调整流量分发，传统重新训练模型太慢。
2.  **多路召回内卷**: 线上挂了十几路召回（I2I, U2I, Embedding等），截断数量（TopK）通常是人工拍的，导致效率低下的召回源占了坑位，高效的进不来。

---

## 详细自述 (Script)

### 1. 深度控制 LTR (PID Control)
“针对排序阶段难以实时响应业务目标的问题，我提出了**实时深度控制模块**。
通常 LTR（Learning to Rank）模型发布后，参数就固定了。但业务目标（如要推新品、要冲 GMV）是动态的。
我借鉴了自动控制领域的 **PID (比例-积分-微分)** 思想。
我们将‘线上实际指标（如新品曝光率）’与‘期望目标’的差值作为 Error，输入 PID 控制器。PID 的输出会实时通过一个系数作用在 LTR 模型的 Loss Function 权重上，或者直接作用在 Ranking Score 的加权上。
这实现了一个**闭环反馈系统**。运营只需要设定目标，算法自动寻找当前约束下的最优参数，不再需要人工反复修改规则。这带来了 UV 价值 +12% 的稳定性提升。”

### 2. 个性化召回融合 (Pooling with Voting Theory)
“针对多路召回抢占配额的问题，我设计了**个性化融合模块**。
以前是固定配额：I2I取50个，U2I取50个。这很僵化。
我引入了 **Borda Count (波达计数法)** 这种投票理论。我们把每一路召回源看作一个‘投票者’，Item 是‘候选人’。
根据 Item 在各路召回中的相对排名（Rank）以及该路召回的历史权重（由离线评估器生成），计算一个加权综合得分。
这样，如果一个 Item 同时出现在多个高效召回源的头部，它的总分就会很高，自然排在前面。这实现了**基于 Item 质量的动态截断**，而不是基于渠道的固定截断。GMV 提升了 2.4%。”

---

## 模拟问答 (Q&A)

### Q1: 在 LTR 中引入 PID，会不会导致模型震荡？
**A:**
非常有洞察力。PID 调参确实是个坑。
*   **解决**: 我对 PID 的输出幅度做了 **Clipping (截断)**，限制权重的最大变化率，防止剧烈波动。
*   同时，我引入了 **Safety Guard**，利用离线 AUC 监控。如果实时调整导致排序打分的分布发生剧烈 偏移（KL 散度过大），会自动降级回兜底策略。

### Q2: Borda Count 这种方法计算复杂度高吗？线上RT（响应时间）怎么保证？
**A:**
Borda Count 本身只是简单的加权求和，计算量很小。
*   主要的开销在于**归一化**。不同召回源的分数（Score）分布不同，不能直接加。但 Rank 是分布无关的。
*   我们直接用 Rank 位置进行打分（比如第1名得100分，第2名99分），这是纯整数运算，非常快。在线上几十毫秒内处理几千个 Candidate 完全没问题。

### Q3: 你的“全链路优化”中，提到“解除耦合”，具体是指什么？
**A:**
推荐系统有个经典痛点：**召回模型的样本通常来自“精排胜出”的日志**。
*   如果精排不喜欢某一类 Item，召回模型就学不到这类 Item 为正样本，以后就更不召回，形成了**马太效应/Loop**。
*   **解耦**: 我在离线评估时，不只看 Hitrate（是否命中精排），而是看**全库的有效性**。同时，我们在随机流量（Exploration Traffic）上收集无偏数据，修正召回模型的 Label，让召回层能“看到”那些被精排误杀的潜在好商品。

### Q4: 为什么要用 Drop Rank 选取 Top 10% 特征？
**A:**
这是为了**工程性能与效果的平衡**。
*   大模型（深度模型）特征成千上万，在线推理慢。
*   LTR (Learning to Rank) 通常用 GBDT 或简单线性模型，速度快但容量小。
*   我们利用 **Drop Rank**（一种特征重要性评估方法）从大模型里蒸馏出最重要的 10% 特征给 LTR。这其实是一种**模型蒸馏 (Distillation)** 的思想，用轻量级模型逼近大模型的效果。

---

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
