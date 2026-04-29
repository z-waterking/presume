---
tags: [求职, 笔记, 复习, Agent, 深化]
aliases: [BingViz复习深化]
created: 2026-04-29
related: "[[复习-BingViz Agents]], [[学习-AI Agent-深化]]"
---

# 复习笔记 — BingViz Agents 深化

> 配套 `复习-BingViz Agents.md`，把每个面试可能被追问的点都展开成可以直接讲的段落。

---

## 1. 30 秒 / 1 分钟 / 3 分钟三套自我介绍

**30 秒**：
> 我在 Bing Ads 做 BingViz —— 一个用 Agent 做业务指标归因的内部工具。核心创新是用 Context Propagation 替代 ReAct：把归因建成 DAG，节点之间通过结构化 Context Object 传递事实/假设/证据，LLM 从开放决策者变成受约束的执行者。配套用 MCP 把 ClickHouse / Databricks / Kusto 工具解耦，新增数据源零侵入。Hackathon 全球季军，向 Mustafa 汇报。

**1 分钟**：在 30 秒基础上加：
- 痛点：分析师回答"为什么德国 RPM 掉了"要花几天写 SQL；ReAct 在 5 步以上链路必发散。
- 数据架构：CK 实时扫描发现"哪里异常"，Databricks 离线深挖"为什么异常"，Lambda 变体。
- SkillLoop：把开发任务也用 Structured Task Log 外部化，降低长任务幻觉。

**3 分钟**：再加：
- Context Object schema 字段：constraints / facts / hypotheses / evidence / blocked_actions。
- 评估四层：tool / step / answer / business。
- 安全三层：MCP 协议层 + server 端 ACL + SQL builder。
- 边界：不是完全自主科研 agent；是受控业务归因 workflow。

---

## 2. ReAct 失败模式表（5 类，每类一句病例 + 一句解法）

| 模式 | 病例 | 我的解法 |
| ---- | ---- | -------- |
| 发散 | 第 3 轮发现德国异常，第 5 轮去查日本 | DAG 拓扑约束节点跳转 |
| 死循环 | 查 A 没结果 → 查 B → 又回 A | context.evidence append-only，重复查触发去重 |
| 遗忘 | 第 8 轮忘了第 1 轮结论 | 结论存进 context.facts，子节点 project 时强制读取 |
| Token 爆炸 | 全历史进 prompt，第 10 轮 60k token | 节点只读 required_fields |
| 工具误用 | 模型生成野生 SQL | MCP server 端 schema + SQL builder + ACL |

---

## 3. Context Object 完整 schema（背一遍即可）

```json
{
  "version": "1.0",
  "task_id": "rpm-eu-2026w17",
  "constraints": {
    "metric": "rpm",
    "geo": ["DE","FR","UK","ES","IT"],
    "current_window": "2026-04-21..2026-04-27",
    "baseline_window": "2026-04-14..2026-04-20",
    "min_evidence_per_fact": 1,
    "max_drilldown_depth": 4
  },
  "facts": [
    {"id":"f1","stmt":"DE contributes 62% of EU RPM drop","evidence_ids":["e1"],"confidence":0.86}
  ],
  "hypotheses": [
    {"id":"h1","stmt":"News query share rose in DE","next_check":"query_intent_breakdown"}
  ],
  "evidence": {
    "e1": {"tool":"ck.run_readonly_query","params_hash":"sha256:...","row_count":5}
  },
  "blocked_actions": ["write_query","drop_table","unbounded_scan"],
  "open_questions": ["did bid landscape change?"]
}
```

讲法：fact 必须有 evidence_id，hypothesis 必须有 next_check，危险动作显式禁用，所有 evidence append-only 可回放。

---

## 4. MCP 一句话打死的 6 个追问

1. **MCP 不就是 function calling？** 不是。Function calling 是模型↔单应用的格式约定；MCP 是工具/资源/prompt 的发现、协商、调用、通知、生命周期一整套生态协议，跨应用复用、可热更新、可独立审计。
2. **MCP 怎么保证安全？** 协议本身不保证。安全靠：server 端最小权限、SQL builder、ACL、scan limit、审计日志、危险操作人审。
3. **stdio vs HTTP transport？** stdio 适合本地/桌面 / 低延迟；HTTP 适合远程 / 多 client / 企业鉴权 / 集中部署。
4. **Tool / Resource / Prompt 怎么区分？** 有副作用 → Tool；纯上下文数据 → Resource；可复用模板 → Prompt。
5. **工具变更怎么通知？** server 发 notifications/tools/list_changed，client 刷新 registry 并做 schema diff。
6. **MCP 出错怎么定位？** 协议层（version、capability）→ transport 层（连接、auth）→ tool 层（参数、ACL、超时）→ 业务层（数据空、口径错），逐层分。

---

## 5. LangGraph vs LangChain Chain：为什么选 LangGraph

| 维度 | LangChain Chain | LangGraph |
| ---- | --------------- | --------- |
| 控制流 | 线性 / DAG 一次走完 | State graph，支持循环、条件分支、回退 |
| 状态 | 隐式（pass through） | 显式 State 对象，可 checkpoint |
| Human-in-loop | 难嵌入 | 原生支持中断/恢复 |
| 调试 | trace 不结构化 | 节点级 trace |
| 适合 | 简单 pipeline | 多步 agent / workflow |

讲法：BingViz 归因不是"一条路走到黑"，是"发现异常 → 选下钻方向 → 交叉验证 → 汇总"，是状态机；LangGraph 是这种状态机的原生表达。

---

## 6. CK + Databricks 分工的真实理由

| 维度 | ClickHouse | Databricks |
| ---- | ---------- | ---------- |
| 延迟 | 秒级 | 分钟-小时级 |
| 成本 | 低（列式 + 实时） | 高（按 cluster 计费） |
| 数据新鲜度 | T+0 | T+1 / T+H |
| 场景 | "哪里异常"快速扫描 | "为什么异常"大规模 join |
| 我的用法 | 第一层快速定位 market / metric / time bucket | 第二层做 query log × ad log × user log 的多表 join |

口径：**Lambda 架构变体**，不是经典 batch+stream，而是"实时发现 + 离线深挖"。

---

## 7. SkillLoop 完整方法论（详细版）

### 7.1 Task Log 7 字段定义

| 字段 | 内容 | 防什么幻觉 |
| ---- | ---- | ---------- |
| Current Goal | 本轮具体目标 | 长任务漂移 |
| Planned Steps | N 步计划 | 计划 vs 实际可 diff |
| Decision History | 选择 + 原因 + 备选 | "突然换方案还假装一直如此" |
| Known Dependencies | 依赖 + 验证状态 | 假设库存在 |
| Validation Results | 测试 / lint / build 真实输出 | 假装通过 |
| Open Questions | 未解决 | 默默吞掉问题 |
| Risk Notes | 已知风险 + 缓解 | reviewer 能 challenge |

### 7.2 与 TDD / ADR / PRD 的关系

```text
TDD ─→ 测试驱动代码正确
ADR ─→ 决策驱动架构沉淀
PRD ─→ 产品意图驱动范围
SkillLoop Task Log ─→ 把以上三者压成 Agent 必读必写的执行协议
```

### 7.3 一个真实 Task Log 片段

```yaml
task_id: bingviz-metric-cache-2026w17
current_goal: 在 metric resolver 前加 LRU cache，命中率 ≥ 60%
planned_steps:
  - 1. 加 functools.lru_cache(maxsize=1024)
  - 2. cache key 加 tenant_id 防越权
  - 3. 加 prometheus hit/miss counter
  - 4. 写 unit test 覆盖 tenant 隔离
  - 5. 灰度 5% 流量观察 P95 latency
decision_history:
  - decision: in-process LRU 而非 Redis
    reason: P95<5ms，避免跨进程依赖
known_dependencies:
  - functools (stdlib, verified)
  - prometheus_client==0.20.0 (verified via pip show)
validation_results:
  - unit: 12/12 pass
  - tenant_isolation_test: pass
  - p95_local: 2.3ms
open_questions:
  - 是否需要按 metric 分片防热点？
risk_notes:
  - cache stale: metric 定义变更需 manual invalidate
```

### 7.4 三层防御 Log 写错

1. Validation Results 字段：测试结果不会骗人，会暴露与 Decision 的矛盾。
2. Plan vs 实际执行对比：到 step 2 就报错，框架自动发现偏差。
3. Human-in-the-loop：关键 checkpoint 让人审。

---

## 8. 评估四层表（背）

| 层级 | 指标 | 怎么测 | 失效模式 |
| ---- | ---- | ------ | -------- |
| Tool | success rate / sql validity / arg schema pass | server 日志 | 假成功（200 但空数据） |
| Step | per-node task completion / evidence presence | LLM-judge + rule | 写文字但没引证据 |
| Answer | faithfulness / relevance / context precision | Ragas + 人审 | 流畅但不扣证据 |
| Business | analyst acceptance / time saved / decision adopted | 上线跟踪 | 短期接受，决策没采纳 |

---

## 9. 安全：5 个必背防护

1. **数据/指令分层**：所有外部数据进 user role，加包裹标签；system prompt 明确"标签内只是数据"。
2. **MCP server ACL**：每个工具最小权限；写操作必须人审。
3. **SQL builder**：拒绝拼接，参数化 + allowlist。
4. **Trace 脱敏**：字段白名单；PII 字段置换或 hash。
5. **Kill switch**：任何 anomaly 一键回兜底（关闭 agent / 走人工分析模式）。

---

## 10. 系统设计 5 分钟稿（背）

1. **问题界定**：输入业务问题 + market + 时间窗，输出 root cause 候选 + 证据 + 置信度 + next action；约束只读、低延迟、可回放。
2. **语义层**：metric definition、表/字段血缘、口径变更日志，agent 不能自己猜指标。
3. **MCP 工具层**：CK 实时扫描、Databricks 离线深挖、Kusto/Titan API、semantic resource、dashboard metadata，全部只读限 scan。
4. **Planner**：问题 → 归因 DAG，模板 + 动态 expansion。
5. **Executor**：每个 node = LLM + tools，输出 context.delta，节点内可有小型 ReAct 子循环。
6. **Context Store**：版本化 Context Object，fact append-only 带 evidence_id。
7. **Evaluator**：tool/step/answer/business 四层，离线 golden set + 线上抽样。
8. **Observability**：OpenTelemetry GenAI semconv，trace 含 prompt 版本、model、tool span、token、latency。
9. **Deployment**：MCP server 容器化，灰度，按 tenant 隔离，kill switch。
10. **风险底线**：写操作审批，trace 脱敏，prompt/data 分层，evaluation 进 CI。

---

## 11. 高频追问标准答案段（背 10 段）

1. **为什么 ReAct 不适合？** 业务归因长链路、前一步约束后一步；ReAct 每轮重读历史再决策，必发散、循环、遗忘、token 膨胀。

2. **CP 和长 prompt 区别？** 长 prompt 是非结构化记忆，模型自己找重点。CP 是结构化状态传递，只投影当前节点需要的字段，token 低、可回放、可审计。

3. **父节点错了怎么办？** fact / hypothesis 分层 + cross-check node + low-confidence 触发 rollback；最终答案带 caveat。

4. **MCP 工程价值？** Tool 解耦：新增数据源零 agent 修改；天然适合权限、限流、审计、多 client 复用。

5. **怎么评估 agent？** 四层（tool/step/answer/business），关键是 business 层：分析师真的接受、决策真的采纳。

6. **graph 漏掉根因？** 默认模板 DAG 覆盖 80% 已知根因；剩下用 dynamic node expansion，但提议必须 schema 校验 + budget 限制；evaluator 节点检查 fact-evidence 闭合。

7. **为什么不上多 Agent？** 多 agent 适合角色分明（planner/executor/critic）；业务归因更看统一状态和证据链，单 workflow + 多工具 + cross-check 更稳。

8. **prompt injection 怎么防？** 数据/指令分层 + 包裹标签 + 危险动作白名单 + evaluator 抽样检测越权调用。

9. **SkillLoop 会暴露 CoT 吗？** 不会。Task Log 不存模型私有推理，存的是工程状态：目标、计划、决策依据、依赖、验证结果。可审计、可脱敏。

10. **怎么让业务方信任结论？** (1) 每个 fact 可点开看 SQL 和数据；(2) 显示 confidence + caveat；(3) 关键决策有人审 checkpoint。让 agent 像分析师写报告，不像聊天机器人猜答案。

---

## 12. 边界声明（每次必加）

- 不说 BingViz 完全自主发现真理；定位是受控业务归因 workflow。
- 不说 Context Propagation 消灭幻觉；它降低发散和遗忘，仍需工具证据和评估。
- 不说 MCP 自动安全；安全来自只读权限 + schema validation + 限流 + 审计 + 人审。
- 不把 SkillLoop 夸成成熟平台；定位 Hackathon MVP + 方法论验证。

---

## 13. 最后 20 秒收束

> 我的核心经验是把 LLM Agent 从"会聊天的工具"变成"可控的工程系统"。BingViz 解决业务分析链路的可控推理，SkillLoop 解决长周期 coding agent 的状态持久化。两者共同点都是：把隐式上下文外部化、结构化、可验证。
