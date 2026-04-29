---
aliases: [学习路线, study guide]
tags: [求职, 学习]
---

# 学习资源与练习路线 (Study Guide)

> 针对 AI 方向面试的全维度准备资源，涵盖框架、算法练习、深度研究和系统设计。

---

## 零、深化版材料索引

> 三份主题学习材料已经按简历项目深化，复习时优先从这里进入。

| 方向 | 对应简历项目 | 主文件 | 复习重点 |
| ---- | ------------ | ------ | -------- |
| 总资料包 | 全部简历经历 | [[简历资料详解包]] | 证据来源、项目细节、追问口径、3 分钟自述 |
| 推荐系统 | 阿里推荐：PID 排序 / Borda 召回 / 全链路解耦 | [[学习-推荐系统]] | 多目标排序、召回融合、延迟反馈、无偏评估 |
| 搜索广告 | 微软 MSM：Query-Ad 图召回 / Pareto 筛选 | [[学习-计算广告]] | 搜索广告匹配、图挖掘、拍卖机制、A/B 实验 |
| AI Agent | BingViz Agents / SkillLoop | [[学习-AI Agent]] | ReAct 改进、MCP、Context Propagation、Agent 评估 |

优先级建议：

- P0：先把每个项目的「30 秒答案」和「1 页纸架构图」做出来。
- P1：再补论文和博客，重点读能支撑你简历亮点的材料。
- P2：最后扩展到系统设计、开源框架和 benchmark。

---

## 一、核心框架与协议 (工程架构底层)

### Model Context Protocol (MCP) 官方文档

- **重点**: Core Specification。作为 MCP 架构的早期实践者，需深入理解其如何通过标准化接口解决 Tool-use 的生态碎片化问题。

### LangGraph 进阶架构指南

- **重点**: Persistence (持久化)、Human-in-the-loop (人在回路)、Multi-agent patterns。

### OpenAI Cookbook (生产级案例)

- **重点**: Techniques to improve reliability 和 Retrieval Augmented Generation。

### Pydantic AI (新一代 Agent 框架)

- **重点**: 类型安全 (Type-safe)，适合有微软 SDE 背景、追求工程严谨性的开发者。

### Anthropic Contextual Retrieval

- **重点**: 通过添加上下文摘要（Contextual BM25）提升 RAG 性能。

---

## 二、算法与大模型"力扣式"练习平台

### DeepML (ML 版力扣)

- **价值**: 手写代码实现算法。
- **必刷题**: Implement Multi-Head Attention, Adam Optimizer from scratch, Positional Encoding。

### Interview Query (LLM & Data Science)

- **价值**: 大厂 LLM 系统真题，包括模型评估、Prompt Engineering 策略题。

### Hugging Face NLP Course

- **价值**: 交互式练习，快速补齐 Tokenizer 到 Dataset 处理的底层工程细节。

### LMSYS Chatbot Arena (评测研究)

- **价值**: 顶尖模型对比数据，理解为什么某些模型在 Reasoning 上更强，有助于 Agent 模型选型。

---

## 三、针对简历项目的深度研究材料

### 1. 自动控制与模型调优 (对应阿里 PID 项目)

- **CVPR 论文**: A PID Controller Approach for Stochastic Optimization
- **理由**: 从数学层面解释为什么 PID 能平衡多目标 Loss，面试中可升华项目深度。

### 2. 多目标优化与搜推 (对应微软 MSM 项目)

- **Google Research**: Multi-Task Learning for Recommender Systems
  - 探讨 Revenue 与 User Experience 的权衡，契合 Pareto 优化实践。
- **WSDM 2024**: LLMs for Recommender Systems Tutorial
  - 了解业界如何将 LLM 作为搜推系统的"大脑"。

### 3. Agent 评估与归因 (对应 BingViz 归因分析项目)

- **RAGAS** (RAG Evaluation Framework)
  - 通过 Faithfulness, Answer Relevance 量化 Agent 输出质量。
- **DeepEval** (GitHub)
  - 工业级 LLM 单元测试框架，符合"文档驱动开发"工程范式。

---

## 四、行业深度博文 (系统设计面试必备)

### Chip Huyen: Building LLM applications for production

- **地位**: LLM 工程领域的"圣经"级文章。

### Eugene Yan: Patterns for Building LLM-based Systems

- **地位**: 从预训练、微调到 RAG 的各种模式总结。

### ByteByteGo (LLM System Design)

- **地位**: 精美图解，适合复习高并发、高可用的 AI 系统架构。

---

## 五、推荐练习路线

### 第一阶段: 手感恢复 (1-2 周)

在 DeepML 上手写一遍 Transformer 核心组件（Attention, MLP, LayerNorm）。

### 第二阶段: 架构深挖 (2-3 周)

精读 MCP 协议，尝试写一个简单的 MCP Server 连接你做过的小工具。

### 第三阶段: 方法论总结 (持续)

结合 Chip Huyen 博客，总结一套你自己的 "Agent 评估与归因分析框架"。你在微软做的归因分析是极大的亮点，要把这套逻辑抽象成通用的方法论——这是面试资深岗位 (SDE 2 / Senior) 的关键。

---

## 六、系统设计 (System Design) 专项

> SDE 2 / Senior 级别的面试重头戏，必须准备。

### 必练题目 (结合自身项目)

1. **Design a Recommendation System** — 直接映射阿里项目，重点讲召回→粗排→精排→重排的漏斗架构
2. **Design an Ad Serving System** — 直接映射 MSM 项目，重点讲 Auction、Budget Pacing、Real-time Bidding
3. **Design an LLM Agent Platform** — 直接映射 BingViz，重点讲 Tool Orchestration、Context Management、Evaluation
4. **Design a Real-time Feature Store** — 覆盖你在阿里做的实时样本流、延迟 Label 归因

### 推荐资源

- **ByteByteGo**: System Design Interview 系列（图解清晰）
- **Alex Xu**: System Design Interview Vol 1 & 2
- **ML System Design**: Chip Huyen — Designing ML Systems (O'Reilly)

---

## 七、Coding 面试准备

> 即使是 AI 方向，大厂通常也有 1-2 轮 Coding。

### 刷题策略

- **LeetCode 精选 150 题** (Neetcode 150)：覆盖高频数据结构与算法
- **重点**: Array/String、Graph (映射你的二部图经验)、Tree (映射 Agent 的决策树)、DP
- **每日目标**: 2-3 题，Medium 为主

### ML Coding (手写实现)

- DeepML 上手写: Attention、Adam、LayerNorm、Beam Search
- 手写: AUC 计算、NDCG 计算、TF-IDF（这些和你的项目直接相关）

---

## 八、四周深化路线

### Week 1：把项目讲稳

- [ ] 每个项目写 1 个「一句话总结」和 1 个「3 分钟自述」。
- [ ] 画三张图：阿里推荐漏斗、MSM 语义召回、BingViz Agent 架构。
- [ ] 准备 15 个 30 秒答案：每个项目 5 个高频追问。

### Week 2：补理论地基

- [ ] 推荐系统：ESMM、MMoE、PLE、DIN、MIND、延迟反馈、无偏学习。
- [ ] 搜索广告：DSSM、Duet、SimRank、GSP、FTRL、在线实验。
- [ ] Agent：ReAct、Plan-and-Solve、MCP、Ragas/DeepEval、OWASP LLM Top 10。

### Week 3：做工程化练习

- [ ] 手写 AUC、NDCG、Hitrate、Borda Count、RRF。
- [ ] 做一个 PID toy simulator，展示 clipping、anti-windup、指标噪声平滑。
- [ ] 写一个最小 MCP Server，并设计 schema validation 和只读 SQL guardrail。

### Week 4：面试压测

- [ ] 每天随机抽 1 个项目，做 10 分钟技术深挖自问自答。
- [ ] 做 2 次系统设计：推荐系统、搜索广告匹配系统、业务归因 Agent 三选二。
- [ ] 每次模拟后更新 [[面试复盘]]：记录卡住的问题、补充材料、改进版回答。

---

## 九、最终输出物清单

- [ ] `阿里推荐系统_1页纸.md`：PID、Borda、全链路解耦。
- [ ] `MSM搜索广告_1页纸.md`：Query-Ad 图、Hot Ad 降噪、Pareto 筛选。
- [ ] `BingVizAgent_1页纸.md`：LangGraph、MCP、Context Object、评估。
- [ ] `简历资料详解包.md`：所有项目的证据、资料、口径和风险边界。
- [ ] `高频追问_30秒答案.md`：至少 30 个短答。
- [ ] `系统设计_白板稿.md`：推荐系统、搜索广告、Agent 平台三套。
- [ ] `指标与实验.md`：A/B、显著性、guardrail、offline-online gap。
