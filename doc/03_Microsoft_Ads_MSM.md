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
