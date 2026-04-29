---
tags: [求职, 笔记, 复习, MSM]
aliases: [MSM复习笔记]
created: 2026-04-13
related: "[[面试-搜索广告 MSM]], [[简历底稿]]"
---

# 复习笔记 — 搜索广告 MSM

> 2026-04-13 复习整理

---

## 一句话总结

> 长尾 Query 变现效率低 → 做了**召回扩展 + 排序升级**两件事，核心方法是**图挖掘 + 帕累托优化**。

## 重要澄清 ⚠️

MSM 的 L1+L2 **不是**广告系统整体的粗排精排，而是 **MSM 召回模块内部的分层筛选**。MSM 是一路语义召回源，L1/L2 控制的是"给出去什么质量的候选"，最终排序是下游系统负责。

面试时定位：
> "MSM 是搜索广告的一路语义召回源。L1/L2 是 MSM 内部的分层筛选机制——先用 Pareto 多目标做候选集质量把控，再精筛后交给下游排序系统。我们控制的是给出去什么质量的候选，而不是最终的广告排序。"

---

## 痛点

搜索广告靠 Query 和广告主 Keyword 匹配投放。传统 Exact Match / Phrase Match 只覆盖头部流量。大量长尾 Query **没有广告可展示 = 白白浪费收入**。排序只看 eCPM 会让出价高但相关性差的广告霸屏，伤害用户体验。

---

## 第一条线：召回侧 — 图挖掘

### 召回方法

1. 用户点击日志天然构成 **Query-Ad 二部图**
2. **Transitive Join**: Q1 和 Q2 都点了 Ad X → Q1 和 Q2 语义相似 → Q1→Ad X→Q2→Ad Y → Q1 也可匹配 Ad Y
3. **Hot Ad 降噪**: Degree 归一化惩罚（类 TF-IDF），**软降权而非硬截断**。Ad 被越多 Query 共享，传递权重越低。只有共享多个稀疏 (Niche) 广告时才认为强相关
4. **NLP Rewriting**: Porter Stemmer 处理词形变化 + BERT 离线生成同义词词典（只用高置信度头部结果，防止漂移）

### 面试回答模式

> **问题本质 → 方法灵感来源 → 具体做法 → 为什么比暴力方法好**

Hot Ad 问题面试答法：
> "本质是 Hub 节点效应——热门广告被所有 Query 点过，Transitive Join 会把不相关 Query 连起来。做法不是硬截断，而是借鉴 TF-IDF 对 Degree 做归一化惩罚，软降权。既抑制噪声又不损失有效匹配关系。"

---

## 第二条线：排序侧 — Pareto 多目标

### 排序方法

1. 设计 **L1 粗筛 + L2 精筛** 漏斗（MSM 内部的，不是整体广告排序）
2. L1 不按 eCPM 单一截断，而是构建 **Revenue / CTR / Relevance** 三目标 **Pareto 优化空间**
3. 取 **Pareto Frontier 上的 Top K** — 在任意一个维度上足够优秀且不被完全支配就留下
4. 落地: **Linear Scalarization** `Score = w1 × pCTR × Bid + w2 × RelevanceScore`，权重通过 **PID 动态调参**或**离线 Grid Search**

### 为什么在 L1 做 Pareto？（三层逻辑）

1. **L1 杀错不可逆**: 如果 L1 只按 eCPM 截断，相关性好但出价低的广告直接被杀，L2 再好也选不出来
2. **Pareto 是多路归并**: 保留多样性候选集给 L2
3. **计算代价几乎为零**: 加权求和和算 eCPM 复杂度同量级

### Linear Scalarization 局限性

- **只能找凸 Pareto Front 上的点** — 几何上是用直线做切线扫描，非凸凹陷区域的 Pareto 最优解会被跳过
- 非凸怎么办？
  - **ε-Constraint**: 把一个目标当约束，滑动阈值扫非凸区域。简历中"保证 Relevance 不低于某个 Constraint"就是这个思想
  - **Tchebycheff**: 最小化与理想点的最大偏差，能保证扫到所有 Pareto 最优解
  - **工程务实**: 实际系统 Pareto Front 近似凸，PID 动态调权 + 离线 Grid Search 双保险已够用

面试 30 秒版：
> "局限是只能找凸 Pareto Front 上的解。理论上可用 ε-Constraint 或 Tchebycheff 方法。但实际系统 Pareto Front 近似凸，加上 PID 动态调权 + 离线 Grid Search 双保险，已经足够 cover 业务需求。"

---

## 结果

- 北美 RPM **+1.8%**（成熟广告系统 0.5% 就算显著）
- CN Revenue **+2%**
- 获 **Greatness Award**

---

## 骨架速记

```text
痛点: 长尾 Query 没广告 + eCPM 排序伤体验
        │
   ┌────┴────┐
   ▼         ▼
 召回侧      排序侧
 二部图       L1+L2 (MSM内部筛选)
 Transitive   Pareto 多目标
 Join         (Revenue/CTR/Relevance)
   │           │
   ▼           ▼
 Hot Ad       Linear Scalarization
 Degree降权    + PID 动态调参
   │           │
   ▼           ▼
 NLP增强      结果: RPM +1.8%
 Stemmer+BERT  Greatness Award
```
