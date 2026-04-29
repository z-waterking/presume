---
tags: [求职, 笔记, 复习, Agent]
aliases: [Agent复习笔记]
created: 2026-04-13
related: "[[面试-BingViz Agents]], [[简历底稿]]"
---

# 复习笔记 — BingViz Agents + SkillLoop

> 2026-04-13 复习整理

---

## 一句话总结

> Agent 做业务分析不靠谱（ReAct 乱跑、幻觉、丢上下文）→ 做了两件事：**BingViz 用 Context Propagation 替代 ReAct 做稳健归因**，**SkillLoop 用文档驱动解决长周期开发中的上下文丢失**。

---

## 痛点

1. **业务分析效率低**: Bing 广告海量数据，分析师回答"为什么德国 RPM 掉了"要手写 SQL 花好几天
2. **ReAct 循环不稳定**: LLM 经常跑偏、死循环、到第 5 轮忘了第 1 轮发现的东西
3. **长任务上下文丢失**: 任务一长 LLM 就开始捏造事实（幻觉）

---

## BingViz — 解决"分析不稳定"

### 核心创新: Automatic Context Propagation

不让 LLM 自己决定下一步（ReAct），而是把分析过程**建模为树**：

```text
        RPM分析 (根节点)
       ╱          ╲
  地域下钻        品类下钻
  发现:德国异常    发现:无异常
      │
   Query分析
   发现:News/Weather
   无商业意图 ← 根因！
```

关键机制：

- 父节点产出的关键信息**强制写入全局 Context Object**
- 子节点**直接读取约束条件**，不需要从对话历史翻找
- 相比 ReAct: Token 消耗低、逻辑一致、不会跑偏

### ReAct 具体哪里不稳定？

ReAct 循环 `Thought → Action → Observation → Thought → ...` 三大问题：

1. **发散**: 第 3 轮发现"德国异常"，第 5 轮 LLM 突然去查日本——忘了在查什么
2. **死循环**: 查 A 没结果 → 查 B → 又回去查 A
3. **Token 爆炸**: 每轮把前面所有历史塞进 prompt，第 10 轮已经几万 token

### Context Propagation 替代了什么？

替代的是 **Thought 环节——"LLM 自己决定下一步做什么"**。

- ReAct: LLM 每轮自主推理下一步 = **开放式决策**，不可控
- CP: 决策结构预定义为树，LLM 只在每个节点内执行，节点跳转由系统控制

> 比喻: ReAct = 让实习生自己决定查什么；CP = 给他一份分析 SOP

面试 30 秒版：
> "ReAct 核心问题是每轮依赖 LLM 自主决策下一步，容易发散、死循环、Token 爆炸。我的 Context Propagation 把分析结构预定义为树，父节点关键发现强制写入全局 Context，子节点直接读取约束执行。LLM 从'开放式决策者'变成'受约束的执行者'，确定性大幅提高。"

### 技术栈

- **LangGraph**: 图导向执行（支持条件分支、回退重试，不是线性 Chain）
  - 选 LangGraph 而非 LangChain Chain: Chain 是 DAG 一条路走到黑，LangGraph 支持循环、条件分支、状态管理
- **MCP + FastMCP**: Tool 标准化解耦
- **ClickHouse + Databricks**: Lambda 架构变体

### MCP 的作用

没有 MCP:

```python
# Tool 硬编码在 Agent 里
tools = [{"name": "query_ck", "handler": ck_func}, ...]
# 加数据源 = 改代码重部署
```

有 MCP:

```text
Agent (MCP Client)
├── 握手 → Databricks MCP Server（独立进程）
├── 握手 → Kusto MCP Server（独立进程）
└── 握手 → ClickHouse MCP Server（独立进程）
# 新增数据源 = 部署新 MCP Server，Agent 零修改
```

面试 30 秒版：
> "MCP 实现 Tool 标准化解耦。之前 Tool 硬编码在 Agent 里，加数据源就得改代码重部署。用 MCP 后各数据源独立为 MCP Server，Agent 通过协议握手动态获取工具能力。新增数据源零侵入，扩展性从 O(n) 改动变成 O(1)。"

### CK + Databricks 分工

Lambda 架构变体：

- **ClickHouse**: T+0 实时扫描，发现突发指标异动（"哪里出了问题"）
- **Databricks**: T+1 离线深度归因，复杂 Join 和大规模 Query Log 分析（"为什么出问题"）

---

## SkillLoop — 解决"长任务幻觉"

### 核心创新: Document-Driven Development + Structured Task Log

强制 Agent 在执行任何操作前，维护一份结构化文档：

```text
Current Goal:       实现用户登录模块
Planned Steps:      1.设计API → 2.写前端 → 3.联调
Decision History:   选了JWT而非Session，因为...
Known Dependencies: 依赖auth库v2.0
Validation Results: 单元测试通过 ✅，集成测试待做
```

### 为什么能降低幻觉？

本质是 **Externalized CoT** — 把 LLM 脑子里的推理强制写到外部文档：

- `Known Dependencies: auth库v2.0` → 不会捏造不存在的 v3.0
- `Decision History: 选了JWT因为xxx` → 不会突然改成 Session 还假装之前就这么决定的
- `Validation Results: 测试X失败` → 不会跳过错误假装成功

> 类比: 口头汇报容易信口开河，书面报告得有据可查。Task Log = 强制 AI "写报告"

### 如果 Log 写了错误信息怎么办？三层防御

1. **Validation Results 字段**: 代码运行结果不会骗人，即使之前 Decision 有误，验证环节会暴露矛盾
2. **Plan vs 实际执行对比**: Log 里写了"计划做 A→B→C"，实际到 B 就报错，Agent 对比 Log 自动发现偏差
3. **Human-in-the-loop**: 关键决策节点暂停让人审核，Log 本身是可读文档

面试 30 秒版：
> "Task Log 把 LLM 内部推理外部化成结构化文档，每步决策和结果有据可查，幻觉无处藏身。如果 Log 写了错误信息，靠 Validation Results 兜底——代码运行结果不骗人，Agent 对比 Plan 和实际结果自动发现偏差。关键节点还支持 Human-in-the-loop 审核。"

### 结果

- Hackathon **全球季军 (Global 3rd Place)**
- 作为 OPE 标杆向 **Mustafa Suleyman** 汇报

---

## 骨架速记

```text
痛点: 业务分析靠人写SQL太慢 + ReAct做Agent不稳定
        │
   ┌────┴────┐
   ▼         ▼
 BingViz     SkillLoop
 分析引擎    开发范式
   │           │
   ▼           ▼
 Context     Document-Driven
 Propagation  Development
 (树形结构     (Structured
  替代ReAct)    Task Log)
   │           │
   ▼           ▼
 技术栈:      本质:
 LangGraph    Externalized CoT
 MCP+FastMCP  强制AI"写报告"
 CK+Databricks  降低幻觉
   │           │
   ▼           ▼
 成果:        成果:
 自动归因      Hackathon全球季军
 欧洲RPM根因   向Mustafa汇报
```
