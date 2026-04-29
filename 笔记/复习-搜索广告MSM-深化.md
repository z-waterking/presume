---
tags: [求职, 笔记, 复习, MSM, 深化]
aliases: [MSM复习深化]
created: 2026-04-29
related: "[[复习-搜索广告MSM]], [[学习-计算广告-深化]]"
---

# 复习笔记 — 搜索广告 MSM 深化

> 配套 `复习-搜索广告MSM.md`，把每个面试可能被追问的点都写成可以直接讲的段落。

---

## 1. 三套自我介绍

**30 秒**：
> 我在微软搜索广告做 MSM —— Meta Smart Match，一路语义召回源 + 内部质量漏斗。两条线：召回侧用 Query-Ad 二部图 + Transitive Join 挖语义，Hot Ad 用 degree penalty 软降权；排序侧 L1 用 Pareto 多目标（Revenue / CTR / Relevance），L2 精筛后交下游 ranking。北美 RPM +1.8%，CN +2%，拿了 Greatness Award。

**1 分钟**：加：
- 痛点：长尾 query 没广告 = 浪费收入；纯 eCPM 排序伤体验。
- Rewriting：Porter Stemmer + BERT 离线同义词字典，按 market/lang/intent 分桶。
- 关键澄清：L1/L2 是 MSM 内部漏斗，不是全局粗排/精排。

**3 分钟**：加：
- 二部图边权定义、degree penalty 三种公式、intent gate。
- Pareto 数学定义 + Linear Scalarization 局限 + ε-Constraint。
- RPM +1.8% 可信度：同期 A/B + 分桶切片 + guardrail 全绿 + CUPED + 分阶段 ramp。

---

## 2. 系统位置（必背）

```text
User Query
 │
 ├── Query Understanding (intent / lang / vertical)
 │
 ├── Matching Layer
 │     ├── Exact / Phrase Match
 │     ├── Keyword Broad Match
 │     ├── MSM Semantic Recall ← 我
 │     │     ├── L1: Pareto 多目标粗筛
 │     │     └── L2: 精筛
 │     └── Other Recall Sources
 │
 ├── Candidate Merge / Dedup / Quality Filter
 │
 ├── Ranking (pCTR · bid · quality)
 │
 ├── Auction (GSP, reserve, pacing)
 │
 └── Impression / Click / Feedback
```

**澄清话术**：
> MSM 控制的是"语义召回候选质量"。我们 L1/L2 是 MSM 内部分层漏斗，不是全局粗排/精排；最终广告排序由下游 ranking + auction 决定。

---

## 3. Query-Ad 图候选公式（背）

```text
score(q_src, ad_tgt) = Σ_{path: q_src → ad_pivot → q_pivot → ad_tgt}
        w(q_src, ad_pivot)
      · w(q_pivot, ad_pivot)
      · w(q_pivot, ad_tgt)
      · degree_penalty(ad_pivot)
      · intent_consistency(q_src, q_pivot)
      · time_decay(t)
```

边权 `w(q,a)`：`α·click + β·CTR + γ·conv + δ·revenue`，归一化后组合。

Degree penalty 三种：

```text
penalty_log  = 1 / log(1 + deg(a))
penalty_sqrt = 1 / sqrt(deg(a))
penalty_idf  = log(N / (1 + deg(a)))
```

讲法：
> degree penalty 不是惩罚热门广告本身，而是降低它"作为桥节点"传播语义的能力。头部 query 不能硬过滤 hot ad，否则会丢真实商业覆盖。

---

## 4. Hot Ad 白板例子

```text
Q1: "cheap flight to paris" -> Ad_A (travel booking)
Q2: "paris vacation deals"  -> Ad_A (travel booking)
Q2: "paris vacation deals"  -> Ad_B (hotel package)

Transitive: Q1 -> Ad_A -> Q2 -> Ad_B
=> Q1 may match Ad_B
```

**为什么有效**：Q1 和 Q2 文本不同，但共享真实点击 Ad_A，说明商业意图相近。

**为什么危险**：若 Ad_A 是 Amazon / YouTube / Weather 这类超高 degree 广告，会把大量不相关 query 串起来。

**怎么解**：degree penalty + intent gate + 多路径支持要求 + 边权阈值。

---

## 5. NLP Rewriting 完整 pipeline

```text
1) raw query log
2) lang detect / market 分桶
3) tokenize (lang-specific)
4) lemmatize / stem (Porter for en, lang-specific 其他)
5) candidate rewrites:
     a) BERT MLM 同义词
     b) co-click query-query
     c) translation memory（多语言市场）
6) score: intent + click-graph + market frequency
7) precision filter (≥0.85)
8) bad-match audit (sample + manual)
9) blacklist (brand / pharma / political / minor)
10) freeze 字典 + 版本号上线
```

上线时只查字典，不在线跑大模型；按 `lang × market × intent` 分桶；每个 rewrite 带 confidence。

---

## 6. L1 Pareto：完整推导

### 6.1 支配关系

```text
A ≻ B  iff  A.r ≥ B.r ∧ A.ctr ≥ B.ctr ∧ A.rel ≥ B.rel
        ∧ (∃i 严格 >)
```

### 6.2 Linear Scalarization

```text
score = w_r · r + w_ctr · ctr + w_rel · rel
```

几何上是用超平面切凸 frontier，**非凸点扫不到**。

### 6.3 ε-Constraint

```text
maximize  revenue
s.t.      relevance ≥ ε_rel
          ctr       ≥ ε_ctr
```

简历里"保证 Relevance 不低于某 Constraint"就是这个思想。

### 6.4 Tchebycheff

```text
minimize max_i { λ_i · |x_i - x_i*| }
```

理论上能扫到所有 Pareto 点，包括非凸。

### 6.5 30 秒口径

> Linear Scalarization 局限是只能找凸 frontier 上的解。理论上可用 ε-Constraint 或 Tchebycheff。但实际线上 frontier 近似凸，我们用 Linear Scalarization + relevance hard constraint 兜底 + PID 动态调权 + 离线 grid search，已足够 cover 业务。

---

## 7. 为什么 L1 必须用 Pareto（三层逻辑）

1. **L1 杀错不可逆**：单按 eCPM 截断，低 bid 高相关候选直接被杀，L2 救不回来。
2. **保多目标多样性**：让 L2 / 下游 ranking 有非退化候选集。
3. **几乎零额外算力**：小候选集上 O(n²)；线上常用近似（按维度 sweep）做到 O(n log n)。

---

## 8. 拍卖 / 排序 / 召回 三层职责（必澄清）

| 层 | 职责 | 我的贡献 |
| -- | ---- | -------- |
| 召回 | 候选生成 | MSM 一路 |
| 排序 | pCTR · bid · quality | 不归我，但 MSM 候选质量影响 ranking 表现 |
| 拍卖 | GSP / VCG 价格机制 | 不归我；但召回扩展会改变竞争密度 → 收入 |

GSP 实际 CPC：
```text
cpc_i = bid_{i+1} · qs_{i+1} / qs_i
```

讲法：
> 召回扩展提升收入有两个机制：(1) 长尾 query 从无广告变有广告；(2) 头部 query 候选池更深 → 拍卖竞争更激烈 → CPC 升。但前提是候选质量过关，否则 CTR / relevance / advertiser ROI 全下来，长期反噬。

---

## 9. RPM +1.8% 可信度回答骨架（背）

1. **同期 A/B**：control / treatment 同时跑，避免季节性。
2. **分桶切片**：market、device、query bucket、commercial intent，全部一致才认。
3. **Guardrail 全绿**：relevance、CTR、complaint、advertiser ROI、latency。
4. **方差控制**：CUPED 用 pre-period 协变量降方差。
5. **Hold-out / ramp**：novelty effect、carryover、SRM 检查；5%→25%→50%→100%。

反驳"季节性"标准答案：
> control 和 treatment 同期同流量随机分桶，季节性同时影响两组；treatment 相对 control 在多市场、多设备、多 query bucket 都正向，且 guardrail 稳定，方为算法增量。CUPED 还能进一步把方差压低，1.8% 在 99% 置信下显著。

---

## 10. 实验指标六层表

| 层级 | 指标 | 作用 | 异常 |
| ---- | ---- | ---- | ---- |
| Primary | RPM / Revenue | 商业收益 | 季节性 / 竞价环境 |
| User guardrail | CTR / quick back / complaint | 体验 | CTR 升但 relevance 降 |
| Advertiser guardrail | CPA / ROI / conv quality | 广告主 | 低质点击增加 |
| Supply diag. | query coverage / ad depth | 召回扩展 | coverage 升但质量差 |
| Quality diag. | relevance label / bad match rate | 语义质量 | 长尾漂移 |
| System | P95 latency / timeout / cost | 服务 | TopK 大 / 模型重 |

---

## 11. MSM 收益归因方法（追问标配）

不能只比"开/关 MSM"前后的整体 RPM，要：

1. 召回源打标，每个 candidate 知道是谁召回的。
2. 统计 MSM 候选的曝光、点击、收入。
3. 统计被下游 ranking 保留的比例。
4. 去重后净增量（只算"其他源没召回但 MSM 召回"的部分）。
5. 按 query bucket / market 切片，看哪类 query 上 MSM 真正贡献。

---

## 12. 高频追问标准答案段（背 8 段）

1. **为什么不用纯 embedding 召回？** Embedding 学文本语义，但搜索广告关心商业意图。点击图来自真实广告交互，能发现文本不相似但商业相近的关系。最稳是图 + rewriting + embedding 多路互补。

2. **Hot Ad 软降权 vs 硬过滤？** 硬过滤伤害头部商业 query。软降权只削弱"做桥节点"传播语义的能力，保留高质量直接边。

3. **Query rewriting 怎么防漂移？** 离线高置信 + 按 market/lang/intent 分桶 + 人工抽检 + 在线黑白名单 + relevance threshold + bad match guardrail。

4. **L1 为什么 Pareto？** 杀错不可逆 + 保多样性 + 算力近似零开销。

5. **Linear Scalarization 局限？** 只能扫凸 frontier。非凸用 ε-Constraint 或 Tchebycheff；线上 frontier 近似凸 + relevance hard constraint 兜底。

6. **RPM +1.8% 季节性？** 同期 A/B + 分桶一致 + guardrail 全绿 + CUPED + 分阶段 ramp。

7. **Relevance 降但 RPM 升能上线吗？** 不一定。看 relevance 下降幅度、complaint、长期 CTR、advertiser ROI；hard relevance 下限作 guardrail。

8. **MSM 收益归因？** 召回源打标 + 曝光/点击/收入 + 下游保留比例 + 去重净增量 + 分 query bucket 切片。

---

## 13. 边界声明

- 不说 MSM 是完整广告排序系统；它是语义召回 + 内部质量漏斗。
- 不说 L1/L2 是全局粗排/精排；强调 MSM 内部漏斗。
- 不说图挖掘一定优于 embedding；二者信号不同，互补最稳。
- 不只报 RPM；主动补 guardrail，显得更可信。

---

## 14. 最后 20 秒收束

> MSM 的核心不是某个单点模型，而是把"长尾 query 召回"和"多目标候选筛选"做成可解释、可灰度、可回滚的工程闭环。图挖掘补 embedding 的商业意图盲区，Pareto 替代单目标截断，rewriting 在离线管控好 precision，最终把语义召回从"实验性方法"做成稳定的一路生产召回源。
