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

---

## 系统位置速记

```text
User Query
  │
  ├── Exact / Phrase Match
  ├── Keyword Broad Match
  ├── MSM Semantic Recall  ← 你的项目
  └── Other Recall Sources
  │
  ▼
Candidate Merge / Dedup
  │
  ▼
Downstream Ranking + Auction
  │
  ▼
Ad Impression / Click / Conversion
```

一句话定位：MSM 控制的是「语义召回候选质量」，不是全局最终排序和拍卖。

## Query-Ad 图挖掘白板例子

```text
Q1: "cheap flight to paris"  -> Ad_A: travel booking
Q2: "paris vacation deals"   -> Ad_A: travel booking
Q2: "paris vacation deals"   -> Ad_B: hotel package

Transitive candidate:
Q1 -> Ad_A -> Q2 -> Ad_B
So Q1 may match Ad_B.
```

为什么有效：Q1 和 Q2 文本不完全一样，但共享了真实点击广告 Ad_A，说明用户商业意图相近。

为什么危险：如果 Ad_A 是 Amazon、YouTube、Weather 这类超高 degree 广告，就会把很多不相关 query 连起来。

解决：对 Ad_A 做 degree penalty，并要求 query intent 一致、边权足够高、多路径支持。

## L1 / L2 细节口径

### L1 做什么

- 输入：离线图挖掘和 rewriting 生成的大量候选。
- 目标：用轻量特征快速过滤明显差的候选，同时保留多目标多样性。
- 特征：edge weight、query/ad click、CTR、ad quality、relevance score、degree penalty、intent consistency。
- 方法：Pareto 或 scalarization，不只按 revenue 截断。

### L2 做什么

- 输入：L1 之后的较小候选集。
- 目标：更精细地判断 query-ad 相关性和商业价值。
- 特征：更复杂的文本相似、历史表现、广告质量、黑白名单、market/language 规则。
- 输出：给下游广告排序系统的一路候选。

### 关键边界

- L1/L2 是 MSM 内部质量漏斗。
- 下游 ranking/auction 会融合其他召回源，并结合 bid、quality、pCTR 决定展示。

## Pareto 面试讲法

先讲直觉：

> 如果只按 revenue，可能留下出价高但相关性差的广告；如果只按 relevance，商业收益不足。Pareto 的作用是在 L1 阶段保留没有被全面支配的候选，让 L2 和下游有更好的选择空间。

再讲数学：

```text
Candidate A dominates B if:
Revenue_A >= Revenue_B
CTR_A >= CTR_B
Relevance_A >= Relevance_B
and one dimension is strictly better.
```

最后讲工程：

- 真正线上不一定显式求完整三维 frontier，可能用归一化加权、阈值约束、分桶 topK 近似。
- Linear scalarization 快，但对非凸 frontier 有局限。
- 兜底是 relevance hard constraint、bad match blacklist、latency budget。

## 实验复盘口径

### RPM +1.8% 如何讲可信

1. A/B 随机分桶，而不是上线前后直接比。
2. 分 market、device、query bucket、commercial intent 看收益是否一致。
3. 看 guardrail：relevance、CTR、complaint、latency、advertiser ROI。
4. 看 diagnostic：MSM coverage、ad depth、downstream pass rate、bad match rate。
5. 成熟广告系统基线高，小幅收益也要统计显著，可用 CUPED 降方差。

### 如果面试官质疑 revenue 涨是季节性

回答：看 control/treatment 同期差异，并按历史 pre-period 校准；如果 treatment 相对 control 在多个市场和 query bucket 都提升，而且 guardrail 稳定，才认为是算法增量。

## 追问答案库

### Q1：为什么不用纯 embedding 召回？

Embedding 能捕捉文本语义，但搜索广告更关心商业意图。点击图来自真实用户和广告互动，能发现文本不相似但商业目的相近的关系。最稳的是图挖掘、rewriting、embedding 多路互补。

### Q2：Hot Ad 软降权为什么比硬删好？

硬删会伤害头部商业 query，因为热门广告对某些 query 确实相关。软降权只降低它作为桥节点传播语义的能力，保留真实高质量边。

### Q3：Query rewriting 怎么防止语义漂移？

离线生成高置信同义词，按 market/language/intent 分桶；上线前做人工抽检和 bad match 评估；在线用黑白名单、relevance threshold 和 guardrail 控制。

### Q4：L1 为什么要 Pareto，而不是把候选都交给 L2？

候选太多会拖慢下游，也会让低质量候选污染排序。L1 要做便宜但不能太粗暴的质量控制，Pareto 能避免单目标截断杀掉高潜候选。

### Q5：RPM 提升但 CTR 下降怎么办？

要看下降幅度、relevance、长期体验和广告主 ROI。若 CTR 下降来自低质曝光增多，不能上线；若 revenue 提升且 relevance/complaint/ROI 稳定，需要进一步分桶确认是否是 query mix 变化。

## 风险边界

- 不说 MSM 是完整广告排序系统；它是语义召回和质量筛选模块。
- 不把 L1/L2 说成全局粗排/精排；要强调是 MSM 内部漏斗。
- 不说图挖掘一定比 embedding 好；二者信号不同，最好互补。
- 不只报 RPM，要主动补 guardrail，显得更可信。
