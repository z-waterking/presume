---
aliases: [简历资料详解包, 简历证据包, resume evidence pack]
tags: [求职, 简历, 面试, 资料包]
created: 2026-04-29
related: "[[简历终稿]], [[简历底稿]], [[学习路线]]"
---

# 简历资料详解包 — Resume Evidence Pack

> 目标：把简历里的每一句核心经历，都扩展成可追溯的背景、技术细节、参考资料、面试回答和风险边界。
> 使用方式：面试前先读「快速定位矩阵」，再按目标岗位选择 Agent / 搜广 / 推荐三条线深挖。

---

## 0. 快速定位矩阵

| 简历模块 | 简历核心说法 | 可追溯资料 | 面试必须讲清 | 风险边界 |
| -------- | ------------ | ---------- | ------------ | -------- |
| BingViz Agents | LLM + MCP + LangGraph/图导向执行，做复杂业务归因 | `references/BingVizAgents/`, `interview/01_agent_bingviz.md`, `学习材料/学习-AI Agent.md` | 为什么 ReAct 不稳，Context Propagation 怎么让归因更确定，MCP 如何解耦工具 | 不把普通 Text-to-SQL 说成完全自主科学发现；强调有工具、schema、评估和人审 |
| SkillLoop | Document-Driven Development + Structured Task Log，解决长周期 Agent 编程上下文丢失 | `references/Hackathon2025/`, `笔记/复习-BingViz Agents.md` | Task Log 字段、为什么能降低幻觉、和 TDD/ADR/PRD 的关系 | 不夸大成生产系统；定位为 Hackathon 获奖 MVP 和方法论验证 |
| MSM 搜索广告 | Query-Ad 图挖掘 + NLP Rewriting + Pareto 多目标筛选 | `references/MSM DeepDive/`, `interview/02_msm_search_ads.md`, `学习材料/学习-计算广告.md` | 长尾 Query 为什么难，Transitive Join 怎么挖掘，Hot Ad 怎么降噪，L1/L2 是 MSM 内部筛选 | 明确 MSM 是语义召回源，不是全局广告最终排序系统 |
| 阿里推荐排序 | PID 深度控制 LTR，UV 价值 +12% | `A_resume/resume_draft.md`, `interview/03_alibaba_recsys.md`, `学习材料/学习-推荐系统.md` | PID 的 error、P/I/D 业务含义、调节点、安全机制、为什么不用 RL | 线上调权要讲 guardrail，不只讲公式 |
| 阿里召回融合 | Borda Count 多路召回融合，GMV +2.4% | `笔记/复习-阿里推荐系统.md`, `学习材料/学习-推荐系统.md` | 为什么用 rank 而非 score，权重怎么来，如何处理覆盖高但转化差的召回源 | 不能说 Borda 总优于模型融合；强调低延迟、可解释、召回源频繁变化 |
| 阿里全链路解耦 | Hitrate 评估、延迟反馈、Exploration Traffic 打破耦合 | `interview/03_alibaba_recsys.md`, `学习材料/学习-推荐系统.md` | 精排胜出日志带来的 selection bias，随机探索如何收集无偏样本 | 探索流量有成本，必须讲流量比例和保护指标 |

---

## 1. 微软 BingViz Agents & SkillLoop

### 1.1 简历原话

- BingViz Agents：基于 LLM + MCP 架构，构建专注于复杂归因的深度业务分析引擎。融合 ClickHouse(实时) 与 Databricks(离线) 数仓，利用 LangGraph 实现图导向的推理执行。设计 Automatic Context Propagation 机制，替代不稳定的 ReAct 循环，实现「大盘扫描 -> 下钻归因」的稳健思维链，成功自动归因欧洲市场 RPM 波动根因，直接支持业务战略决策。
- SkillLoop：在 Global Hackathon 中发起 SkillLoop 项目，首创 Document-Driven Development 范式。设计基于 Structured Task Log 的系统协议，强制 Agent 维护「自包含」的工程状态，解决长周期开发中的上下文丢失难题。项目荣获 Global 3rd Place，并作为 OPE 标杆向 Microsoft AI CEO Mustafa Suleyman 汇报。

### 1.2 业务背景

- Bing / 搜索广告业务每天产生大量查询、广告、点击、覆盖率、RPM、市场维度数据。
- 传统数据分析通常是「人提问题 -> 手写 SQL/Kusto -> 看结果 -> 再手工决定下一维度」。
- 传统 Text-to-SQL 只能回答「是什么」：例如德国 RPM 降了多少；但很难回答「为什么」：是 query intent 变了、广告覆盖率掉了、bid 变化了，还是用户结构变了。
- ReAct Agent 在这种长链路分析里容易出现三类问题：发散、死循环、遗忘早期发现。

### 1.3 架构资料与代码线索

| 资料路径 | 里面有什么 | 可用于支撑的简历点 |
| -------- | ---------- | ------------------ |
| `references/BingVizAgents/main.py` | Agent 入口、CLI 参数、交互模式、模型 provider 选择、异步运行 | 不是 demo prompt，而是完整 Agent 应用入口 |
| `references/BingVizAgents/core/agent/bingviz_agents.py` | BingVizAgents 类、OpenAI Agents SDK 的 Agent/Runner、会话管理、MCP server 集成 | 说明有会话态、工具态和运行器，而不是一次性脚本 |
| `references/BingVizAgents/interfaces/mcp/mcp_server.py` | FastMCP server、stdio/HTTP/SSE transport、日志处理、lazy-load、venv auto-activation | 支撑 MCP 标准化工具接入、不同客户端接入模式 |
| `references/BingVizAgents/runtime/tools/titan_api_tool.py` | MSAL token manager、SQL 执行、企业认证 | 支撑企业数据工具、权限认证、真实数据连接 |
| `references/BingVizAgents/semantic/semantic_interface.py` | NetworkX LineageGraph、table/metric/dashboard metadata、上下游依赖 | 支撑「语义层」「指标血缘」「归因路径」 |
| `references/BingVizAgents/evaluation/evaluator.py` | 成功/智能失败/理解失败/技术失败/无响应分类，多维评分 | 支撑 Agent evaluation，不只是主观看答案 |
| `references/BingVizAgents/evaluation/README.md` | Markdown/Excel 报告、数值误差容忍、批量场景评测 | 支撑可运营的评估闭环 |
| `references/BingVizAgents/deep_analytic/README.md` | 多步分析 pipeline、上下文累积、场景配置、模型 provider 灵活性 | 支撑深度分析和 context accumulation |
| `references/BingVizAgents/DOCKER-DEPLOYMENT.md` | interactive + MCP server 两服务、端口 8888、volume mount | 支撑工程部署能力 |

### 1.4 技术细节：Automatic Context Propagation

核心不是「把所有历史塞进 prompt」，而是把分析过程变成有状态的约束传播。

```text
Root: Analyze RPM Drop
  context.market = Europe
  context.metric = RPM
  context.period = current vs baseline
  context.finding = Europe RPM dropped
        |
        v
Market Drilldown
  output: Germany / France / UK contribution
        |
        v
Query Intent Drilldown
  input: context.market + metric + suspicious segment
  output: News / Weather / informational query mix increased
        |
        v
Ad Coverage / Monetization Check
  output: lower commercial intent -> fewer monetizable ads -> RPM drop
```

面试时可以这样定义：

- **Context Object**：结构化状态，不是聊天记录。存事实、约束、指标、置信度、证据、下一步候选。
- **Propagation**：父节点结论显式传给子节点，子节点不再从冗长历史里猜当前任务。
- **Guardrail**：Context 中的事实必须有工具结果或数据证据；低置信度结论标为 hypothesis，不直接变成 final answer。
- **优势**：降低 token、减少跑偏、支持回放和 debug、便于把每一步写进 trace。

### 1.5 技术细节：MCP 工具层

MCP 的面试表达不要停留在「我用了工具」。要讲出协议化解耦。

- **普通 function calling**：工具 schema 通常写死在 Agent 代码里，新增数据源需要改 Agent 配置或重新部署。
- **MCP**：数据源能力由独立 MCP Server 暴露，Agent 作为 client 握手后动态发现 tools/resources/prompts。
- **Transport**：stdio 适合本地/桌面集成，HTTP/SSE 适合服务化部署。
- **Schema Validation**：工具调用前验证参数、表名、字段名、SQL 范围，拦截 Tool Arguments Hallucination。
- **权限边界**：数据分析 Agent 默认只读、限制扫描量、限制危险 SQL、记录每次工具调用。

### 1.6 技术细节：ClickHouse / Databricks / Kusto 分工

| 数据系统 | 面试表达 | 适合任务 |
| -------- | -------- | -------- |
| ClickHouse | T+0 快速指标扫描，高并发聚合，适合「哪里出问题」 | market/query/device 维度快速 slice |
| Databricks | T+1 离线深度归因，复杂 join、大规模 query log 分析 | 复杂归因、长窗口、PySpark pipeline |
| Kusto / Titan API | 企业内部数据查询与认证接入 | 业务指标、日志、广告系统数据源 |
| Semantic Layer | 指标、表、dashboard 血缘 | 避免 Agent 用错表、错指标、错依赖 |

### 1.7 Evaluation 资料

可以把 Agent 评估拆成四层：

- **Tool-level**：工具调用是否成功、SQL 是否合法、字段是否存在、查询成本是否超限。
- **Step-level**：每一步是否回答了当前 drilldown 问题，是否保留了父节点 context。
- **Answer-level**：最终结论是否 faithful、是否有证据、是否解释了反例。
- **Business-level**：分析师是否接受、是否减少分析时间、是否支持决策。

`references/BingVizAgents/evaluation/evaluator.py` 的 5 类分类可以这样讲：

- success：答案正确且证据充分。
- smart-fail：识别到无法回答或数据不足，没有胡编。
- understand-fail：理解错问题。
- tech-fail：工具、数据源、执行失败。
- no-response：没有有效响应。

### 1.8 SkillLoop 资料展开

资料线索：

- `references/Hackathon2025/# SkillLoop 开发说明书.md`：Skill Profile、AI Copilot、Transaction Flow 三模块；time-coin economy；reputation system。
- `references/Hackathon2025/Dev.md`：组织内技能孤岛、培训成本高、跨团队连接弱；B2B SaaS / PUPM 模式。
- `references/Hackathon2025/SkillLoopV2/backend/`：Node.js + Express + TypeScript + better-sqlite3，mock SSO，user/skill/matching/transactions API。
- `references/Hackathon2025/SkillLoopV2/frontend/`：React + Vite + TypeScript。
- `references/Hackathon2025/SkillLoopV2/SkillLoopTaskLog.md`：任务拆解从 DB schema、API、matching、transaction flow 到 UI 和 seed data。
- `references/Hackathon2025/SkillLoop_开发流程追踪表.md`：账号、Copilot、交易、集成、测试、商业化、冷启动七阶段。

Structured Task Log 字段解释：

| 字段 | 解决的问题 | 面试说法 |
| ---- | ---------- | -------- |
| Current Goal | Agent 做着做着忘目标 | 每个周期都重新锚定当前目标 |
| Planned Steps | Agent 随机行动 | 先计划再执行，可审查、可回滚 |
| Decision History | 重复推翻旧决定 | 保留 ADR 式决策记录 |
| Known Dependencies | 捏造不存在依赖 | 依赖必须显式列出并被验证 |
| Validation Results | 假装完成 | 每一步有测试、运行结果或人工验收 |

### 1.9 推荐外部资料

P0：

- Building Effective Agents — Anthropic：Agent workflow / agent 区分，适合解释「不追求复杂，追求可控」。
- ReAct: Synergizing Reasoning and Acting：说明你改进的基线是什么。
- MCP Specification：Tool / Resource / Prompt，client-server lifecycle。
- LangGraph docs：state、node、edge、checkpoint、human-in-the-loop。

P1：

- Plan-and-Solve、Tree of Thoughts、Reflexion：用于扩展「规划、树搜索、反思」的理论背景。
- Ragas、DeepEval、LangSmith：用于 Agent evaluation / observability。
- OWASP Top 10 for LLM Applications：用于 prompt injection、tool misuse、data leakage 安全追问。

### 1.10 面试 30 秒答案

> BingViz 的核心不是把 SQL 自动生成出来，而是把业务归因流程做成受控 Agent workflow。传统 ReAct 每轮都让模型自己决定下一步，容易跑偏和遗忘。我把归因过程拆成树状节点，父节点的关键发现写入结构化 Context Object，子节点只在这个约束下执行 drilldown。工具层用 MCP 解耦 ClickHouse、Databricks 和 Kusto，调用前做 schema validation，最后用 evaluation framework 评估答案质量和工具成功率。

### 1.11 高频追问

- Q：Context Propagation 和 Memory 有什么区别？
  - A：Memory 是信息存储，Context Propagation 是工作流约束传递。它不仅保存内容，还规定子任务必须继承哪些事实、指标、市场、时间窗口和假设。
- Q：父节点错了会不会错上加错？
  - A：Context 里要区分 fact 和 hypothesis，并记录 confidence/evidence。低置信度发现不会直接终止分析，会触发 cross-check 节点，比如同时看 query intent、coverage、CTR、bid。
- Q：为什么 MCP 比 function calling 更适合企业数据？
  - A：企业数据源多、权限复杂、工具生命周期独立。MCP 把工具能力服务化，支持多 transport、动态发现、独立部署和权限边界。
- Q：SkillLoop 和普通项目管理文档有什么区别？
  - A：普通文档给人看；SkillLoop 的 Task Log 是 Agent 执行协议的一部分，Agent 必须读写它，并用 Validation Results 驱动下一步。

---

## 2. 微软 Meta Smart Match 搜索广告

### 2.1 简历原话

> 针对长尾流量变现效率低痛点，主导智能语义匹配系统 MSM 的召回与排序升级。构建 Query-Ad 点击二部图，利用 Transitive Join 挖掘非直观共现关系，并结合 NLP Rewriting 技术，突破仅靠 Query-Keyword 表面匹配的限制。设计 L1 粗排 + L2 精排漏斗，在 L1 阶段引入 Pareto Optimization，在 Revenue、CTR、Relevance 多目标空间中动态平衡变现与体验。北美市场 RPM +1.8%，CN 市场 Revenue +2%。

### 2.2 业务背景

- 搜索广告依赖用户 query 与广告主 keyword/ad 的匹配。
- Exact Match / Phrase Match 精度高但覆盖有限，长尾 query 很容易没有合适广告。
- Broad / semantic match 要扩展覆盖，但风险是引入相关性差、商业意图弱、投诉率高的广告。
- 成熟广告系统中 0.5% RPM 都很可观，1.8% 需要非常强的实验可信度和 guardrail。

### 2.3 资料线索

| 资料路径 | 内容 | 可支撑点 |
| -------- | ---- | -------- |
| `references/MSM DeepDive/DeepDive1` | MSM Query-Keyword matching pipeline、14+ processing stages、Porter Stemmer、transitive closure、L1/L2 scoring、threshold、top-300、30-day blacklist、decile bucketing | 支撑 MSM 不是单模型，而是多阶段召回/筛选 pipeline |
| `references/MSM DeepDive/DeepDive Rawsource` | 原始深挖材料 | 可补具体字段、pipeline 背景 |
| `interview/02_msm_search_ads.md` | 项目自述和 Q&A | 面试口径 |
| `学习材料/学习-计算广告.md` | 论文、博客、实战练习 | 理论补充 |
| `笔记/复习-搜索广告MSM.md` | 30 秒复习版、关键澄清 | 快速背诵 |

### 2.4 Pipeline 细节

可以把 MSM 讲成多阶段候选生成与质量控制：

```text
Raw Query / Keyword / Ad Logs
  -> term normalization: Porter Stemmer / tokenization
  -> graph construction: Query-Keyword / Query-Ad click graph
  -> transitive closure: Q1-K1, Q2-K1, Q2-K2 => Q1-K2
  -> feature engineering: click, CTR, rank, ad quality, relevance features
  -> L1 scoring: RankScore / CTRScore / ADScore / threshold gates
  -> quality filters: blacklist, whitelist, entity match pruning
  -> top-300 selection: controlled candidate size
  -> decile bucketing: query importance / quality segmentation
  -> downstream ranking / serving
```

### 2.5 Query-Ad 图挖掘

核心直觉：用户点击行为是弱监督语义信号。

- 如果 `Q1 -> AdA`，`Q2 -> AdA`，说明 Q1 和 Q2 可能共享商业意图。
- 如果 `Q2 -> AdB`，则可以推导 `Q1 -> AdB` 是一个候选匹配。
- 这类关系有时比 embedding 更稳，因为它来自真实商业点击行为，而不是纯文本相似度。

候选公式可以这样讲：

```text
score(Q, Ad2) = sum over paths Q -> Ad1 -> Q2 -> Ad2
                edge_weight(Q, Ad1)
              * edge_weight(Q2, Ad1)
              * edge_weight(Q2, Ad2)
              * degree_penalty(Ad1)
              * intent_filter(Q, Q2)
```

Hot Ad 降噪：

- 问题：热门广告像 hub，会连接大量不相关 query。
- 方案：借鉴 IDF，对高 degree ad 做软惩罚，而不是硬删。
- 可选形式：`1 / log(1 + degree)`、`1 / sqrt(degree)`、按 ad vertical 分桶归一化。
- 为什么软惩罚：热门广告仍可能对头部商业词有效，硬删会损失覆盖。

### 2.6 NLP Rewriting

| 技术 | 作用 | 风险 | 保护 |
| ---- | ---- | ---- | ---- |
| Porter Stemmer | 处理英文单复数、时态、派生词 | 过度 stemming 引入 bad match | 黑白名单、query class filter |
| BERT / semantic expansion | 离线生成高置信同义词和改写 | 语义漂移、商业意图漂移 | 只保留高置信头部、离线字典、人工抽检 |
| Query intent classifier | 区分 informational / transactional | 误杀潜在商业 query | 阈值分层、保留探索流量 |

### 2.7 L1 / L2 与 Pareto

重要澄清：MSM 的 L1/L2 是语义召回模块内部的分层筛选，不等于整个广告系统最终排序。

多目标：

- Revenue：预期收入，通常与 bid、pCTR、pCVR、coverage 有关。
- CTR：用户点击倾向，代表用户吸引力。
- Relevance：query-ad 相关性，保护体验和长期信任。

Pareto 说法：

- 单按 revenue 排序会杀掉低 bid 但高度相关的广告。
- 单按 relevance 排序会损失商业化收益。
- Pareto frontier 保留「至少在某个维度足够优秀，且没有被其他候选全面支配」的候选。
- 工程上可用 linear scalarization 快速落地，再用 grid search / PID 调权扫描 trade-off。

### 2.8 实验与指标

Primary metrics：

- RPM：每千次搜索/展示收入。
- Revenue：总收入或市场级收入。
- Query coverage：有广告可匹配的 query 比例。
- Ad coverage / depth：可用广告候选数。

Guardrail metrics：

- CTR / long click / quick back。
- Relevance label / complaint rate。
- Latency / serving cost。
- Advertiser ROI / CPA / conversion quality。

实验可信度：

- 按 market、query bucket、device、commercial intent 分层看。
- 使用历史同期和 A/B 对照排除季节性。
- 小收益用 CUPED 或 pre-period covariates 降低方差。
- 关注 novelty effect 和竞价环境变化。

### 2.9 推荐外部资料

P0：

- DSSM / CDSSM / Duet：微软语义检索传统。
- SimRank / Personalized PageRank / Mining of Massive Datasets：图挖掘与 hub 降噪。
- Ad Click Prediction: A View from the Trenches：广告 CTR 工程基础。
- GSP / Position Auctions：搜索广告拍卖机制。
- Trustworthy Online Controlled Experiments / CUPED：实验验证。

P1：

- ColBERT / ColBERTv2：late interaction，解释双塔和 cross encoder 的 trade-off。
- ANCE：hard negative dense retrieval。
- Optimal RTB / pacing equilibria：预算与竞价系统。

### 2.10 面试 30 秒答案

> MSM 的核心是解决搜索广告长尾 query 没有合适广告的问题。我用点击日志构建 Query-Ad/Query-Keyword 图，通过 Transitive Join 挖掘非直观商业意图关系，同时用 Porter Stemmer 和离线 BERT 同义词做 rewriting 约束。为了避免扩展召回伤害体验，MSM 内部做了 L1/L2 分层筛选，在 L1 用 Revenue、CTR、Relevance 的 Pareto 多目标保留候选多样性，并通过 degree penalty 抑制 Hot Ad 噪声，最终带来北美 RPM +1.8%。

### 2.11 高频追问

- Q：Embedding 召回也能做语义匹配，为什么还要图挖掘？
  - A：Embedding 学文本语义，图挖掘学真实商业点击共现。很多广告匹配文本不相似但商业意图一致，点击图能补这部分。
- Q：Transitive Join 怎么防止语义漂移？
  - A：限制路径长度、边权阈值、Hot Ad degree penalty、query intent filter、多路径一致性验证。
- Q：Linear Scalarization 的局限是什么？
  - A：只能稳定扫到凸 Pareto frontier。非凸区域可用 epsilon-constraint 或 Tchebycheff，但工程上常用 grid search + guardrail 足够。
- Q：RPM +1.8% 如何证明是算法收益？
  - A：A/B 随机分桶，分 market/query bucket 看一致性，观察 guardrail，不只看总 revenue，还看 coverage、relevance、latency、投诉率。

---

## 3. 阿里巴巴推荐系统

### 3.1 简历原话

> 分别负责 AE 推荐业务中排序及召回模块，从全链路视角进行迭代升级，取得加收转化率 +7%、UV 价值 +12%。排序侧提出实时深度控制模块，通过 PID 动态调整各 Label 对应 Loss 融合权重；召回侧提出个性化多路召回融合，基于 Borda Count 和离线/在线评估闭环；全链路侧搭建 Hitrate 评估流程，研究召回与排序漏斗耦合问题。

### 3.2 业务背景

- 电商推荐目标天然多目标：点击、加购、收藏、成交、GMV、UV value、用户体验、商家生态。
- 推荐系统是漏斗结构：召回、粗排、精排、重排、混排，每一层都可能放大上一层偏差。
- 大促、运营活动、新品扶持会让业务目标变化很快，人工调权响应慢。
- 多路召回上线后，固定 TopK 配额容易让低效召回占坑，高效召回进不来。

### 3.3 PID 深度控制 LTR

核心公式：

```text
error_t = target_metric_t - observed_metric_t
control_t = Kp * error_t
          + Ki * sum(error_0...error_t)
          + Kd * (error_t - error_{t-1})

ranking_score = base_score + control_t * business_weight
或
loss = sum_i dynamic_weight_i * loss_i
```

面试要讲清的不是公式，而是调节点：

- 调 Loss 权重：影响模型训练，适合周期稍长、稳定调整。
- 调 Ranking Score 权重：影响在线策略，响应快，但更需要 clipping 和 guardrail。
- 调业务阈值：例如类目/场景 quota、rerank 规则，解释性强但表达能力有限。

安全机制：

- Clipping：限制每次权重变化幅度。
- Anti-windup：积分项达到边界后停止继续累积，避免回调过冲。
- Low-pass filter：对 D 项或指标输入做平滑，降低噪声。
- KL / PSI：监控打分分布或曝光分布偏移。
- AUC / GAUC guardrail：离线模型质量过线才放量。
- A/B guardrail：GMV、CTR、转化、投诉、延迟同时看。

### 3.4 全场域优化

样本侧：

- 延迟 Label：曝光当天未购买不一定是负样本，可能 1-7 天后转化。
- 缓存区：样本先暂存，等待归因窗口结束或转化信号回流。
- 正负样本缓冲队列：避免样本比例因活动、流量波动突然变化。
- Fake negative 修正：减少把未来转化样本提前标负。

特征侧：

- Drop Rank：删除某个特征后看排序变化或指标下降，评估特征重要性。
- Top 10% 特征蒸馏：把大模型中最有价值特征给轻量 LTR，平衡效果与线上延迟。
- 训练/服务一致性：离线特征、实时特征、回填特征要避免穿越和延迟错配。

工程侧：

- 数据源配置化。
- 样本构造配置化。
- 模型训练配置化。
- 在线生效配置化。
- 支持多场域横向复制。

### 3.5 Borda Count 多路召回融合

基本公式：

```text
final_score(item) = sum over source s:
                    source_weight_s * rank_score_s(item)

rank_score_s(item) = max_score - rank_position_s(item)
```

为什么不用原始分：

- I2I、U2I、Embedding、热门、规则召回分数分布不同。
- 有的分数是相似度，有的是概率，有的是业务规则分。
- 直接加会被某一路分数尺度主导。
- Rank 天然归一化，且在线计算是整数加权，稳定、快、可解释。

权重来源：

- 离线：根据 Hitrate、转化、覆盖、互补性生成候选权重。
- 在线：小流量评估不同权重组合。
- 反馈：把在线结果回写给生成器，更新下一轮权重。

Borda vs RRF vs 学习式融合：

| 方法 | 优点 | 缺点 | 适合场景 |
| ---- | ---- | ---- | -------- |
| Borda Count | 简单、低延迟、可解释、分布无关 | 可能对长列表覆盖源偏友好 | 召回源多且频繁变化 |
| RRF | 更强调头部 rank，抗噪 | 对尾部信息利用少 | 多检索源融合、搜索场景 |
| 分数归一化 | 保留原始置信度 | 归一化难，漂移风险高 | 分数含义接近的召回源 |
| Learning-to-rank 融合 | 表达能力强 | 训练/特征/延迟/可解释成本高 | 召回源稳定、样本充足 |

### 3.6 Hitrate 与全链路解耦

经典耦合问题：

```text
精排偏好 A 类商品 -> A 曝光更多 -> 召回训练日志里 A 正样本更多
                    -> 召回更偏好 A -> 精排看到更多 A
                    -> B 类商品永远没有机会被看见
```

怎么证明：

- 看各召回源曝光占比与转化效率是否不匹配。
- 看高 Hitrate 召回源是否只是命中了精排偏好的旧分布。
- 看探索流量中被主链路低估的 item 是否有更高真实转化。
- 对比随机/探索样本和生产日志样本的 label 分布。

怎么解：

- Exploration Traffic：少量随机或策略探索，收集无偏/低偏样本。
- Label correction：修正召回训练样本。
- 召回来源泛化：让召回不只学习精排胜出 item。
- Offline-online gap 诊断：Hitrate、转化、精排一致性一起看。

### 3.7 推荐外部资料

P0：

- ESMM：CVR 样本选择偏差和延迟转化基础。
- MMoE / PLE：多目标推荐建模，解释目标冲突和 seesaw effect。
- DIN / DIEN / SIM：阿里电商兴趣建模经典链路。
- YouTube DNN：工业推荐召回 + 排序双阶段架构。
- Delayed Feedback / ES-DFM：转化延迟、fake negative。

P1：

- COLD / Privileged Features Distillation：粗排蒸馏。
- Monolith：实时推荐训练架构。
- Counterfactual Learning / IPW / Doubly Robust：无偏学习和离线评估。
- Trustworthy Online Controlled Experiments：线上实验方法。

### 3.8 面试 30 秒答案

> 我在阿里主要从排序、召回和全链路三个层面提升推荐效率。排序侧，业务目标经常变化，人工调权慢，所以我把 LTR 后链路做成 PID 闭环控制，根据线上指标和目标的 error 动态调权，并用 clipping、KL、AUC guardrail 保证稳定。召回侧，十几路召回固定 TopK 会导致低效源占坑，我用 Borda Count 把多路召回融合成 rank voting 问题，用离线/在线评估闭环动态更新权重。全链路侧，我发现召回样本来自精排胜出日志会形成马太效应，所以通过 Hitrate、曝光转化和探索流量做解耦。

### 3.9 高频追问

- Q：为什么 PID 比 RL 更适合这里？
  - A：PID 可解释、收敛快、探索成本低，适合成熟业务线小幅稳定调控。RL 需要探索，可能伤害用户和业务指标。
- Q：积分项会不会过冲？
  - A：会，所以要 anti-windup、clipping、指标平滑和回滚阈值。
- Q：Borda 会不会偏向覆盖大的召回源？
  - A：可能，所以 source weight 不能只看覆盖，要看转化、互补性、去重后贡献和在线效果。
- Q：Hitrate 高为什么线上不涨？
  - A：可能只是命中精排旧偏好，缺少增量；也可能召回 item 进来后被粗排/精排杀掉，或者召回和最终目标不一致。

---

## 4. 论文、教育与奖项支撑材料

### 4.1 发表论文

1. Social weather: A review of crowdsourcing-assisted meteorological knowledge services through social cyberspace
   - 可讲能力：文献综述、众包数据、知识服务、跨源信息组织。
   - 与简历主线关系：早期训练了数据挖掘、信息组织、知识服务视角，和后来的数据分析 Agent / semantic layer 有弱关联。
2. Academic rising star prediction via scholar's evaluation model and machine learning techniques
   - 可讲能力：预测建模、特征工程、评价体系、机器学习应用。
   - 与简历主线关系：和推荐/排序里的评价模型、特征工程、预测任务有方法论连续性。

### 4.2 奖项

- Microsoft Global Hackathon Global 3rd Place：支撑 SkillLoop 的影响力和跨团队认可。
- Microsoft Greatness Award：支撑 MSM 搜索广告项目的业务影响。
- 北京市优秀毕业生、创业团队二等奖、工信创新奖学金：用于证明学习能力、长期稳定性和创业/产品意识。

---

## 5. 面试前硬证据补齐清单

这些材料如果后续能补到 `references/` 或 `docs/materials/`，会让简历说服力更强：

- [ ] MSM A/B 实验截图或脱敏 summary：RPM +1.8%、CN Revenue +2%、guardrail 指标。
- [ ] MSM pipeline 的脱敏架构图：Raw source -> transitive join -> L1/L2 -> filters -> output。
- [ ] BingViz 自动归因 case 的脱敏 trace：问题、工具调用、context、结论、业务采纳。
- [ ] BingViz evaluation report 示例：success/smart-fail 分类、accuracy、explanation quality。
- [ ] SkillLoop Hackathon 提交材料、demo 截图、评审反馈、获奖证明。
- [ ] 阿里 PID 调权的脱敏实验记录：目标、error、权重曲线、guardrail、UV value 收益。
- [ ] 阿里 Borda 融合实验记录：召回源权重、Hitrate、GMV +2.4%。
- [ ] 阿里全链路解耦分析：召回曝光占比、转化效率、exploration traffic 结论。

---

## 6. 目标岗位讲法

### 6.1 AI Agent / AI Infra 岗

主线：

> 我不是只会调 prompt，而是把 Agent 当工程系统做：工具协议、状态传播、schema validation、评估、部署和可观测性都考虑了。

重点材料：BingViz Agents、SkillLoop、MCP、evaluation、OWASP、Ragas/DeepEval。

### 6.2 搜广 / 推荐 / 算法岗

主线：

> 我有成熟商业系统里的召回、排序、多目标优化、实验验证经验，也能把 Agent 新技术和业务分析结合起来。

重点材料：MSM、阿里推荐、ESMM/MMoE/PLE、DSSM/GSP/FTRL、A/B 实验。

### 6.3 AI 创业公司 / Full-stack Agent 岗

主线：

> 我既能做 LLM Agent 架构，也能把产品闭环、数据闭环和工程落地一起推进；SkillLoop 是这种能力的压缩样例。

重点材料：SkillLoop 产品文档、Node/React MVP、Agent workflow、Document-Driven Development。

---

## 7. 三套 3 分钟自述素材

### 7.1 Agent 方向

我最近在微软重点做 AI Agent 和业务分析自动化。一个代表项目是 BingViz Agents，目标是把分析师多天的 RPM 归因分析变成可控的 Agent workflow。传统 Text-to-SQL 只能回答「是什么」，但业务真正关心「为什么」。我把归因过程拆成图/树状任务，并设计 Automatic Context Propagation，让父节点发现的市场、指标、时间窗口和假设显式传给子节点。这样模型不是每轮自由发散，而是在约束下调用 ClickHouse、Databricks、Kusto 等工具做 drilldown。工具层我用 MCP 做标准化解耦，并通过 schema validation 防止字段幻觉。这个系统曾自动定位欧洲 RPM 波动和 News/Weather 等低商业意图 query 的关系，帮助业务纠正判断。另一个项目 SkillLoop 则把这种经验迁移到 coding agent，通过 Structured Task Log 让 Agent 维护目标、计划、决策、依赖和验证结果，解决长任务上下文丢失，最终拿到 Hackathon 全球季军。

### 7.2 搜广方向

我在微软 Meta Smart Match 做搜索广告语义匹配，核心问题是长尾 query 变现效率低。广告主 keyword 覆盖有限，传统 exact/phrase match 精度高但覆盖不足；如果盲目 broad match，又会牺牲相关性。我负责的方向是用点击日志构建 Query-Ad/Query-Keyword 图，通过 Transitive Join 挖掘非直观商业意图关系，并结合 Porter Stemmer 和离线 BERT rewriting 做语义约束。为了控制召回质量，我在 MSM 内部设计 L1/L2 分层筛选，在 L1 引入 Revenue、CTR、Relevance 的 Pareto 多目标，不只取收入最高，而是保留多目标 frontier 上有价值的候选。同时对 Hot Ad 做 degree penalty，避免 hub 噪声污染长尾 query。最终北美 RPM 提升 1.8%，CN revenue 提升 2%，也因为这个项目拿到了 Greatness Award。

### 7.3 推荐方向

我在阿里海外电商主要做推荐系统排序、召回和全链路优化。排序侧，业务目标经常因为大促、新品扶持和场景变化而变化，人工调权周期长，所以我借鉴 PID 控制，把 LTR 后链路做成闭环系统，根据线上指标和目标的 error 动态调整 loss 或 ranking score 权重，并用 clipping、KL、AUC 等 guardrail 保证稳定，带来 UV value +12%。召回侧，十几路召回固定 TopK 会导致低效召回源占坑，我把融合建模为投票问题，用 Borda Count 基于 rank 而不是原始分做融合，再通过离线生成器和在线评估器更新权重，带来 GMV +2.4%。全链路侧，我搭建 Hitrate 评估并研究召回和精排耦合，识别出精排胜出日志带来的马太效应，用探索流量和召回来源泛化做解耦。

---
