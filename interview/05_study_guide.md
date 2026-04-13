---
aliases: [学习路线, study guide]
tags: [求职, 学习]
---

# 学习资源与练习路线 (Study Guide)

> 针对 AI 方向面试的全维度准备资源，涵盖框架、算法练习、深度研究和系统设计。

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
