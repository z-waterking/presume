---
aliases: [简历底稿, resume_draft]
tags: [求职, 简历]
---

# 张思凡 Sifan Zhang — 深度底稿 / Knowledge Base
> 此文件是所有简历信息的原始素材库，包含详尽技术细节、修改记录和待优化点。
> 对外简历请见 `resume_final.md`。

13260151230 | zhangsifanbj@163.com | 北京市海淀区

---

## 个人简介
拥有 5 年大厂核心算法与工程落地经验（阿里 + 微软）。深耕推荐系统、搜索引擎与计算广告领域。近期专注于 Generative AI 与 Agent 方向，探索 "Vibe Coding" 与 "Doc-First" 开发范式。

**核心技术栈**: LLM, LangGraph, MCP (Model Context Protocol), ClickHouse, Databricks, RAG, Multi-Agent, PID Control, Pareto Optimization, GBDT, Deep Learning

---

## 工作经历
| 公司 | 职位 | 时间 |
| :--- | :--- | :--- |
| **微软中国** | **SDE 2** | **2022.11-至今** |
| **阿里巴巴 海外电商** | **推荐算法工程师** | **2020.07-2022.10** |
| **阿里巴巴 & 亚马逊AWS** | **算法工程师(实习)** | **2019.05-2019.09** |

---

## 核心项目深度解析

### 1. 微软 — AI Agent & Vibe Coding (2024-至今)

**项目**: BingViz Agents (业务分析) & SkillLoop (Vibe Coding)

**核心职责**: 负责 Agent 平台及开发范式探索的核心研发。

#### 1.1 深度分析引擎 (BingViz Agents)
- **架构**: LLM + MCP 架构，LangGraph 图导向执行
- **数据层**: ClickHouse (实时 T+0) + Databricks (离线 T+1) — Lambda 架构变体
- **核心创新**: Automatic Context Propagation 机制
  - 将分析过程建模为树，父节点产生的关键信息强制写入全局 Context Object
  - 子节点直接读取约束条件，无需从对话历史翻找
  - 替代不稳定的 ReAct 循环，减少 Token 消耗，保证逻辑一致性
- **业务落地**: 自动归因欧洲市场 RPM 波动根因（发现用户搜索 News/Weather 等无商业意图词），结论直接纠正业务战略方向
- **工程细节**:
  - FastMCP 实现 Tool 标准化解耦（Databricks/Kusto 封装为独立 MCP Server）
  - Schema Validation 层防止 Tool Arguments Hallucination
  - 先用 CK 快速定位"哪里出了问题"，再生成 PySpark 推送 Databricks 跑"为什么出问题"

#### 1.2 开发范式探索 (SkillLoop)
- **方法论**: Document-Driven Development (文档驱动开发)
- **核心设计**: Structured Task Log 协议
  - 字段: `Current Goal`, `Planned Steps`, `Decision History`, `Known Dependencies`, `Validation Results`
  - 强制 Agent 维护"自包含"的工程状态，实现 Externalized Chain-of-Thought
  - 解决长周期开发中的上下文丢失与幻觉问题
- **成果**: Global Hackathon **全球季军 (Global 3rd Place)**
- **影响力**: 作为 OPE 标杆向 Microsoft AI CEO (Mustafa Suleyman) 汇报

---

### 2. 微软 — 搜索广告 Meta Smart Match (2022.11-2024)

**痛点**: 长尾流量变现效率低。传统 Exact/Phrase Match 仅覆盖头部流量，排序仅看 eCPM 伤害用户体验。

#### 2.1 语义召回体系
- **方法**: 基于二部图 (Bipartite Graph) 的图挖掘
  - 构建 Query-Ad 点击二部图
  - Transitive Join: Q1→Ad→Q2→Ad2 → Q1-Ad2 的高阶关系
  - 对 Hot Ad 节点做 Degree 归一化惩罚 (类 TF-IDF 思想)
- **NLP 增强**: Porter Stemmer (词干提取) + BERT 离线同义词生成 (高置信度头部结果作为离线字典)
- **工程**: 内部 Scope (类 Spark/MapReduce) 大数据处理

#### 2.2 分层排序架构
- **L1 粗排 + L2 精排** 漏斗
- **L1 创新**: Pareto Optimization
  - 多目标空间: Revenue, CTR, Relevance
  - 不取 Top K by Revenue，而是取 Top K from Pareto Frontier
  - 落地方式: Linear Scalarization + PID 动态调参 / 离线 Grid Search
- **成果**: 北美市场 RPM +1.8%, CN 市场 Revenue +2%
- **荣誉**: Greatness Award

---

### 3. 阿里巴巴 — 推荐系统 (2020.07-2022.10)

**总成果**: 加收转化率+7%, UV价值+12%

#### 3.1 排序: 实时深度控制模块
**深度控制 LTR (PID Control)**
- 借鉴自动控制 PID 思想，以"线上实际指标与期望目标的差值"为 Error
- PID 输出动态调整 LTR 模型 Loss 权重 / Ranking Score 加权
- 安全机制: Clipping 截断 + KL 散度监控 + AUC 阈值降级
- **成果**: UV 价值 +12%

**全场域优化**
- 样本侧: 缓存区归因延迟 Label + 缓冲队列保持正负样本比例
- 特征侧: Drop Rank 选取大模型 Top 10% 特征用于 LTR (模型蒸馏思想)
- 构建从数据源→样本→训练→线上生效的全配置化流程
- **成果**: 各场域 IPV +2%, UV 价值 +2%+

#### 3.2 召回: 个性化多路召回融合
- **痛点**: 10+ 路召回合并时，固定截断导致马太效应
- **方法**: Borda Count (投票理论)
  - 每路召回作为"投票者"，Item 作为"候选人"
  - 基于 Rank 位置打分 (整数运算，线上几十毫秒)
  - 离线生成器动态生成融合权重，在线评估器评估效果并反馈
- **成果**: GMV +2.4%

#### 3.3 全链路: 离线评估与解耦
- **Hitrate 评估**: 20+ 种召回源统一评估，发现互补召回源
- **解耦**: 评估召回曝光占比与转化效率，验证耦合现象；与精排联动泛化召回来源
- **核心洞察**: 召回模型样本来自"精排胜出"日志 → 马太效应 Loop。用 Exploration Traffic 收集无偏数据修正

---

## 教育背景
| 学校 | 学历 | 专业 | 时间 |
| :--- | :--- | :--- | :--- |
| 北京理工大学(985) | 硕士 | 计算机学院 计算机科学与技术 | 2017.09-2020.07 |
| 暨南大学(211) | 本科 | 电气信息学院 包装工程 | 2012.09-2016.07 |

## 获奖情况
- Microsoft Global Hackathon 2025 **Global 3rd Place (全球季军)** (2025.09)
- Microsoft Greatness Award (2024.12)
- 北京市2020年优秀毕业生 (2020.07)
- 北京大学生优秀创业团队二等奖，创立了智法互动创业团队，已投入运营 (2019.09)
- 工信创新奖学金 (2019.04)

## 发表论文
1. Yifan Zhu, Sifan Zhang, etc. Social weather: A review of crowdsourcing-assisted meteorological knowledge services through social cyberspace[J]. Geoscience Data Journal, 2020 (SCI-2区)
2. Yubing Nie, Yifan Zhu, Sifan Zhang, etc. Academic rising star prediction via scholar's evaluation model and machine learning techniques[J]. Scientometrics, 2019. (SCI-2区)

---

## 修改记录 & 待优化项

### 已采纳的 Review 建议 (来自 AI 简历顾问)
- ✅ 技术栈显性化: 明确列出 LangGraph, ClickHouse, Databricks, FastMCP
- ✅ 方法论强化: "Automatic Context Propagation" 替代笼统的 "Workflow-based"
- ✅ SkillLoop 升级: 明确 Structured Task Log 字段，强调 Externalized CoT
- ✅ MSM NLP 补充: 加入 Porter Stemmer / Query Rewriting 技术
- ✅ Scope → "分布式数据流系统" (面试非微软公司时通用化)

### 待优化
- [ ] 考虑添加"专业技能"栏，集中展示技术栈
- [ ] 英文版简历 (投硅谷公司时需要)
- [ ] 根据不同目标公司定制简历侧重点 (Agent 方向 vs 搜推方向)
