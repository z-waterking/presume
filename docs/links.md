# 📚 面试学习材料 & 链接收集

> 基于简历内容和 references 整理。**阿里推荐 & 微软广告**为主，**AI Agent** 为辅。
> 阅读完毕后打勾 `[x]` 并记录心得，方便回顾。

---

# 一、推荐系统 (阿里 — 主攻) 🔴

> 对应简历: 阿里巴巴海外电商 AE 推荐算法工程师 (2020-2022)
> 核心经验: PID 调权排序 / 多路召回 Borda 融合 / 全链路评估解耦

## 1.1 多目标排序 / LTR / PID 控制

### 📄 论文

- [ ] [ESMM: Entire Space Multi-Task Model](https://arxiv.org/abs/1804.07931) — 阿里妈妈, SIGIR 2018。多目标排序开山之作，解决 CVR 样本选择偏差，和 UV 价值优化直接相关
- [ ] [MMoE: Multi-gate Mixture-of-Experts](https://dl.acm.org/doi/10.1145/3219819.3220007) — Google, KDD 2018。多目标排序最经典网络结构，**面试必问**，理解 Gate 机制
- [ ] [PLE: Progressive Layered Extraction](https://dl.acm.org/doi/10.1145/3383313.3412236) — 腾讯, RecSys 2020。解决 MMoE 跷跷板问题 (seesaw effect)，多目标进阶方案
- [ ] [Controllable Multi-Objective Re-ranking](https://arxiv.org/abs/2307.09526) — 阿里。PID 控制器动态调权思路，和你的核心经验直接对应
- [ ] [From RankNet to LambdaRank to LambdaMART](https://www.microsoft.com/en-us/research/publication/from-ranknet-to-lambdarank-to-lambdamart-an-overview/) — Microsoft Research, 2010。LTR 三大范式 (Pointwise/Pairwise/Listwise) 最经典综述
- [ ] [Personalized Re-ranking for Recommendation](https://arxiv.org/abs/1904.06813) — 阿里, RecSys 2019。重排序阶段个性化，理解精排→重排衔接逻辑
- [ ] [Pareto-Optimal Multi-Objective Optimization 综述](https://arxiv.org/abs/2108.04690) — 理解 Pareto 最优理论基础，面试常问"多目标冲突怎么办"

### 🔑 关键词自测

```
LTR 三大范式: Pointwise / Pairwise (RankNet, LambdaRank) / Listwise (LambdaMART)
多目标建模: ESMM / MMoE / PLE / DBMTL / Seesaw Effect
多目标融合: score = w1*pCTR + w2*pCVR*price / 动态调权 / 约束优化
PID 控制器: 比例-积分-微分 / 在线动态调整目标权重 / 保底约束
深度排序模型演进: LR → GBDT+LR → Wide&Deep → DeepFM → DIN → DIEN → SIM
Calibration(校准): Platt Scaling / Isotonic Regression
Position Bias: PAL / 训练加 Position 特征, 推理去掉
```

---

## 1.2 多路召回融合 / 召回评估

### 📄 论文

- [ ] [Deep Neural Networks for YouTube Recommendations](https://dl.acm.org/doi/10.1145/2959100.2959190) — Google, RecSys 2016。⭐ **面试必读 No.1**，"召回+排序"双塔架构奠基之作
- [ ] [EBR: Embedding-based Retrieval in Facebook Search](https://arxiv.org/abs/2006.11632) — Meta, KDD 2020。工业级向量召回完整实践，Hard Negative Mining
- [ ] [Sampling-Bias-Corrected Neural Modeling](https://dl.acm.org/doi/10.1145/3298689.3346996) — Google, RecSys 2019。双塔召回采样纠偏，In-batch Negative Sampling
- [ ] [PDN: Path-based Deep Network for Multi-Channel Recall](https://arxiv.org/abs/2105.08571) — 阿里, 2021。多路召回融合，和你"10+路召回合并"经验直接对应
- [ ] [Deep Retrieval](https://arxiv.org/abs/2007.07203) — 字节, 2020。非向量化的端到端召回方案，对比理解不同召回范式

### 🔑 关键词自测

```
多路召回: 协同过滤CF / 热门 / 标签 / 向量Embedding / 图 / 行为序列
双塔模型 (Two-Tower): 用户塔+物品塔 / 内积 / ANN检索 / FAISS / ScaNN
负采样: 随机 / In-batch Negatives / Hard Negative Mining / 采样纠偏
多路融合: Borda Count(投票法) / 倒数排名融合(RRF) / 分数归一化
Hitrate 评估: 各路召回命中率对比 / UV维度转化率 / 与精排一致性
向量索引: FAISS (IVF/PQ/HNSW) / 近似最近邻
```

---

## 1.3 样本构造 / 延迟反馈 / 无偏学习

### 📄 论文

- [ ] [Modeling Delayed Feedback in Display Advertising](https://dl.acm.org/doi/10.1145/2623330.2623634) — Chapelle, KDD 2014。延迟反馈建模开山论文，你做"缓存区归因延迟 Label"的理论基础
- [ ] [ES-DFM: 延迟反馈建模](https://arxiv.org/abs/2104.01160) — 阿里, AAAI 2021。用 Elapsed-Time 信息建模转化延迟，和你经验高度匹配
- [ ] [Unbiased Learning to Rank](https://arxiv.org/abs/1804.05938) — Ai et al., SIGIR 2018。IPW 消除位置偏差经典方法，无偏学习基础
- [ ] [PAL: Position-bias Aware Learning](https://dl.acm.org/doi/10.1145/3298689.3347033) — 华为, RecSys 2019。位置偏差处理，训练加 Position, 推理去掉

### 🔑 关键词自测

```
样本构造: 曝光点击=正 / 曝光未点击=负 / 全空间样本 / 随机流量样本
延迟反馈: 归因窗口 / Fake Negative / DFM / ES-DFM / 缓存区回填
无偏学习: IPW(逆倾向加权) / Doubly Robust / Selection Bias
Exploration: ε-greedy / Thompson Sampling / EE权衡 / 无偏数据收集
特征工程: 实时特征 vs 离线特征 / 特征穿越问题
```

---

## 1.4 粗排 / 蒸馏 / 全链路架构

### 📄 论文

- [ ] [COLD: 阿里粗排系统](https://arxiv.org/abs/2007.16122) — 阿里, DLP-KDD 2020。从精排蒸馏到粗排，和你"Drop Rank 模型蒸馏思想"直接相关
- [ ] [Privileged Features Distillation](https://arxiv.org/abs/2007.07646) — 阿里, KDD 2020。特权特征蒸馏，粗排无法用精排实时特征，蒸馏弥补信息差
- [ ] [Monolith: Real Time Recommendation System](https://arxiv.org/abs/2209.07663) — 字节, 2022。实时训练架构，在线学习与 Embedding 表设计

### 🔑 关键词自测

```
四级漏斗: 召回(万级) → 粗排(千级) → 精排(百级) → 重排(最终展示)
粗排 COLD: 蒸馏轻量模型 / 特征裁剪 / 算力与效果权衡
重排: 多样性打散 / MMR / DPP / 业务规则 / 个性化重排
模型蒸馏: Teacher-Student / Soft Label / Privileged Features
Drop Rank 特征选取: 去掉某特征后排序变化 → 特征重要度评估
配置化引擎: ABTest框架 / 流量分桶 / Feature Flag
```

---

## 1.5 技术博客 & 工程实践

- [ ] [王喆《深度学习推荐系统》知乎专栏](https://www.zhihu.com/column/wangzhenotes) — ⭐ 中文推荐系统最佳入门/面试参考
- [ ] [美团技术团队博客](https://tech.meituan.com/) — 搜索"推荐"，多目标优化和特征工程文章质量极高
- [ ] [阿里云开发者社区](https://developer.aliyun.com/) — 搜索"推荐系统"，排序/召回/全链路优化
- [ ] [GitHub: DeepCTR](https://github.com/shenweichen/DeepCTR) — 模型代码实现参考，面试前手撕代码用
- [ ] [GitHub: fun-rec](https://github.com/datawhalechina/fun-rec) — 推荐系统学习路线和代码实践

---

## 1.6 面试高频追问 (必须准备答案)

```
Q: 多目标冲突怎么办?        → PID调权 / Pareto / 约束优化 / 分层目标
Q: 召回怎么评估好不好?       → Hitrate / UV维度转化率 / 与精排一致性
Q: 样本有偏怎么处理?         → IPW / 随机流量 / 全空间建模 / Doubly Robust
Q: 怎么做延迟反馈?           → 归因窗口 / 延迟建模 / 缓存区回填 / Fake Negative纠正
Q: 粗排和精排区别?           → 候选集大小 / 特征完整性 / 模型复杂度 / 蒸馏对齐
Q: 冷启动怎么做?             → 探索流量 / Bandit / 内容特征 / 迁移学习
Q: 推荐系统设计题?           → 画四级漏斗 / 数据流 / 特征系统 / 在线/近线/离线架构
```

---

# 二、计算广告 / 搜索广告 (微软 — 主攻) 🔴

> 对应简历: 微软 Meta Smart Match 搜索广告匹配系统 (2022-2024)
> 核心经验: Query-Ad 二部图召回 / Pareto 多目标排序 / NLP Query Rewriting

## 2.1 搜索广告匹配 / Query-Ad Matching

### 📄 论文

- [ ] [DSSM: Deep Structured Semantic Models](https://www.microsoft.com/en-us/research/publication/learning-deep-structured-semantic-models-for-web-search-using-clickthrough-data/) — ⭐ Microsoft Research, CIKM 2013。微软经典双塔语义匹配，Query-Ad 语义召回基石
- [ ] [SimRank: Structural-Context Similarity](https://dl.acm.org/doi/10.1145/775047.775126) — KDD 2002。二部图相似度度量，对应你的 Query-Ad 点击二部图和 Transitive Join
- [ ] [Sponsored Search Auction Design Beyond Keyword Matching](https://dl.acm.org/doi/10.1145/1242572.1242682) — Broder, Yahoo Research。搜索广告从关键词到语义匹配的演进经典
- [ ] [A Taxonomy of Web Search](https://dl.acm.org/doi/10.1145/792550.792552) — Broder, SIGIR 2002。Query 意图分类 (Navigational/Informational/Transactional)，Query Understanding 基础
- [ ] [ColBERT: Late Interaction over BERT](https://arxiv.org/abs/2004.12832) — SIGIR 2020。BERT 在检索匹配中的高效应用

### 🔑 关键词自测

```
DSSM / 双塔模型 (Two-Tower Model)
Query-Ad 点击二部图 (Click Bipartite Graph)
Transitive Join / 高阶关系挖掘 / Random Walk
语义召回 / ANN近似最近邻 (FAISS/HNSW) / Embedding / 向量检索
Broad Match / Phrase Match / Exact Match (匹配类型)
```

---

## 2.2 Query Rewriting / Query Understanding

### 📄 论文

- [ ] [BERT: Pre-training of Deep Bidirectional Transformers](https://arxiv.org/abs/1810.04805) — Google, NAACL 2019。你用 BERT 做同义词生成，面试必须能讲清楚原理
- [ ] [Porter Stemming Algorithm](https://tartarus.org/martin/PorterStemmer/) — 简历提到 Porter Stemmer，面试可能追问 stemming vs lemmatization

### 🔑 关键词自测

```
Porter Stemmer / Lemmatization / Tokenization
BERT Fine-tuning / Sentence Embedding
同义词挖掘 (Synonym Mining) / 同义词词典
Query Rewriting / Query Expansion / Query Suggestion
```

---

## 2.3 广告排序 / CTR 预估 / 多目标优化

### 📄 论文

- [ ] [Ad Click Prediction: A View from the Trenches](https://research.google/pubs/pub41159/) — ⭐ Google, KDD 2013。**面试必读**，大规模 CTR 预估工程实践总结
- [ ] [Practical Lessons from Predicting Clicks on Ads at Facebook](https://research.facebook.com/publications/practical-lessons-from-predicting-clicks-on-ads-at-facebook/) — ⭐ Meta, ADKDD 2014。GBDT+LR 经典组合，工业界 CTR 预估演进史
- [ ] [Wide & Deep Learning](https://arxiv.org/abs/1606.07792) — Google, 2016。Memorization + Generalization，面试必考
- [ ] [DeepFM](https://arxiv.org/abs/1703.04247) — IJCAI 2017。FM + DNN，CTR 预估面试高频考点
- [ ] [DIN: Deep Interest Network](https://arxiv.org/abs/1706.06978) — 阿里, KDD 2018。注意力机制用于 CTR 预估
- [ ] [DCN V2: Improved Deep & Cross Network](https://arxiv.org/abs/2008.13535) — Google, WWW 2021。特征交叉自动化
- [ ] [Pareto-Efficient Multi-Objective in E-Commerce](https://dl.acm.org/doi/10.1145/3298689.3346998) — RecSys 2019。Pareto 效率在多目标优化中的应用，与你经验高度匹配

### 🔑 关键词自测

```
CTR 预估模型链: LR → GBDT+LR → FM/FFM → Wide&Deep → DeepFM → DCN → DIN
eCPM = bid × pCTR (× pCVR)
Pareto Optimality / Pareto Front / Linear Scalarization
Revenue / CTR / Relevance 三目标权衡
PID Controller 动态调参 / Constrained Optimization / Lagrangian Relaxation
Calibration / Logloss / AUC / GAUC
```

---

## 2.4 Auction 机制 & Bid 策略

### 📄 论文

- [ ] [GSP: Generalized Second-Price Auction](https://www.benedelman.org/publications/gsp-060801.pdf) — Edelman et al., AER 2007。⭐ GSP 拍卖机制奠基论文，**面试必问**
- [ ] [Position Auctions](https://people.ischool.berkeley.edu/~hal/Papers/2006/position.pdf) — Varian (Google 首席经济学家), 2007。位置拍卖经典分析
- [ ] [RTB with Multi-Agent RL](https://arxiv.org/abs/1802.09756) — CIKM 2018。实时竞价中的强化学习出价策略

### 🔑 关键词自测

```
GSP (广义第二价格拍卖) / VCG 拍卖
First-Price vs Second-Price Auction
Reserve Price (底价) / Quality Score (质量分)
Pacing (预算节奏控制) / Budget Smoothing
RTB / DSP / SSP / Ad Exchange
Smart Bidding / Auto Bidding / ROI约束 / CPA约束
Nash Equilibrium / Incentive Compatibility
```

---

## 2.5 经典书籍 & 课程

- [ ] **《计算广告：互联网商业变现的市场与技术》— 刘鹏、王超** — ⭐⭐⭐ 中文计算广告最经典教材，面试前必通读
- [ ] **《深度学习推荐系统》— 王喆** — CTR 预估、Embedding、特征工程章节与广告高度相关
- [ ] [Introduction to Information Retrieval (免费在线)](https://nlp.stanford.edu/IR-book/) — 信息检索经典教材，倒排索引/TF-IDF/BM25
- [ ] [Stanford CS364A: Algorithmic Game Theory](http://timroughgarden.org/f13/f13.html) — 拍卖机制设计理论基础，GSP/VCG 必考
- [ ] [Stanford CS246: Mining Massive Datasets](http://web.stanford.edu/class/cs246/) — 推荐/广告/大规模数据挖掘

## 2.6 技术博客

- [ ] [Microsoft Bing Ads Blog](https://about.ads.microsoft.com/en-us/blog) — 微软广告官方博客
- [ ] [Google Research — Ads & Commerce](https://research.google/pubs/?area=advertising-and-commerce) — Google 广告论文列表
- [ ] [知乎 — "计算广告" 话题精华](https://www.zhihu.com/topic/19727681) — 面试知识点和系统设计讨论

## 2.7 面试高频追问 (必须准备答案)

```
Q: Query-Ad 二部图怎么做的? Transitive Join 怎么挖掘高阶关系?
Q: Hot Ad 节点 Degree 归一化惩罚解决什么问题? 怎么做?
Q: Porter Stemmer 和 Lemmatization 区别? 为什么选 Stemmer?
Q: BERT 离线同义词生成 pipeline? 怎么评估质量?
Q: L1 粗排和 L2 精排分别用什么模型? 为什么分层?
Q: Pareto Optimization 怎么平衡 Revenue/CTR/Relevance?
Q: Linear Scalarization 局限性? 怎么处理非凸 Pareto 前沿?
Q: PID 调参调的什么参数? P/I/D 分别什么作用?
Q: RPM +1.8% 怎么衡量? 怎么排除季节性 confounding?
Q: 从头设计一个搜索广告匹配系统, 你怎么设计?
```

---

# 三、AI Agent (微软 — 辅攻) 🟡

> 对应简历: BingViz Agents + SkillLoop, Hackathon 全球季军
> 精选最重要的材料即可，不需要太深入

## 3.1 必读 (P0)

- [ ] [Building Effective Agents — Anthropic](https://www.anthropic.com/engineering/building-effective-agents) — ⭐⭐ **最重要的一篇**。6 种 Agent 架构模式，核心观点: "构建最适合的系统而非最复杂的"
- [ ] [ReAct: Synergizing Reasoning and Acting](https://arxiv.org/abs/2210.03629) — Yao et al., 2022。Agent 最经典论文。你的 Context Propagation 改进了 ReAct，面试必须能对比
- [ ] [MCP (Model Context Protocol) 官方规范](https://modelcontextprotocol.io/) — Anthropic 主导的开放标准，你用 FastMCP 做 Tool 标准化解耦

## 3.2 推荐阅读 (P1)

- [ ] [LangGraph 官方文档](https://langchain-ai.github.io/langgraph/) — 你的 BingViz 项目核心框架，Graph-based stateful execution
- [ ] [OpenAI: A Practical Guide to Building Agents](https://platform.openai.com/docs/guides/building-agents) — Agent 构建实战指南，Guardrails / Handoffs / Tool 设计
- [ ] [Agentic Design Patterns — Andrew Ng](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/) — 4 种设计模式 (Reflection / Tool Use / Planning / Multi-Agent)，面试易于表达
- [ ] [MCP Architecture 详解](https://modelcontextprotocol.io/docs/learn/architecture) — Client-Server / Transport / Tool-Resource-Prompt 三大原语

## 3.3 补充阅读 (P2)

- [ ] [LLM Agent Survey 综述](https://arxiv.org/abs/2308.11432) — Wang et al., 2023。最全面 Agent 综述，通读摘要和结论即可
- [ ] [Toolformer](https://arxiv.org/abs/2302.04761) — Schick et al., 2023。LLM 自主学习使用工具
- [ ] [AutoGen: Multi-Agent Conversation](https://arxiv.org/abs/2308.08155) — Microsoft, 2023。多智能体协作框架
- [ ] [FastMCP GitHub](https://github.com/jlowin/fastmcp) — 你项目中使用的 MCP 实现库

## 3.4 关键词自测

```
核心架构:
  ReAct Loop (Thought → Action → Observation) / 你的 Context Propagation 替代方案
  Graph-based vs Chain-based Execution / LangGraph 的 DAG 执行
  Tool Use / Function Calling / Tool schema
  Agent Memory: Working Memory vs Long-term Memory

协议 & 工程:
  MCP 三大原语: Tool / Resource / Prompt
  Context Engineering (超越 Prompt Engineering)
  Structured Output / Schema Validation
  Guardrails / Human-in-the-loop

高级模式:
  Multi-Agent: Orchestrator-Workers / Debate / Delegation
  Handoff 机制 / Agent 间任务交接
  Evaluation & Observability / LangSmith trace
  Document-Driven Development (你的 SkillLoop 方法论)
```

---

# 四、学习优先级总览 📋

| 优先级 | 方向 | 内容 | 预计时间 |
|--------|------|------|----------|
| 🔴 P0 | 推荐+广告 | 关键词自测清单 — 每个概念都能讲清原理和实践 | 4-6h |
| 🔴 P0 | 推荐+广告 | 面试高频追问 (1.6 + 2.7) — 准备好完整答案 | 3-4h |
| 🔴 P0 | 推荐 | YouTube DNN + ESMM + MMoE — 三篇奠基论文 | 3h |
| 🔴 P0 | 广告 | Google "Trenches" + Facebook "Practical Lessons" + GSP | 3h |
| 🟠 P1 | 推荐 | 延迟反馈 + 无偏学习 + 粗排蒸馏 (对应你的项目) | 3h |
| 🟠 P1 | 广告 | DSSM + Pareto 优化 + 《计算广告》通读 | 4h |
| 🟠 P1 | Agent | Anthropic "Building Effective Agents" + ReAct 论文 | 2h |
| 🟡 P2 | 推荐 | 美团/字节技术博客 + DeepCTR 代码 | 2h |
| 🟡 P2 | 广告 | Stanford CS364A 拍卖理论 + Auction 关键词 | 2h |
| 🟡 P2 | Agent | MCP 规范 + LangGraph 文档 + 关键词自测 | 2h |

> **总预计时间: 26-30h**。建议每天 3-4h，一周内过完 P0，两周内全部覆盖。
