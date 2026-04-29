---
tags: [求职, 学习, Agent, P1]
aliases: [Agent学习]
created: 2026-04-13
related: "[[面试-BingViz Agents]], [[简历底稿]]"
---

# 学习材料 — AI Agent 🟡 (辅攻)

> 对应简历: BingViz Agents + SkillLoop, Hackathon 全球季军
> 精选最重要的材料，按优先级分层

---

## P0 必读

- [ ] [Building Effective Agents — Anthropic](https://www.anthropic.com/engineering/building-effective-agents) — ⭐⭐ **最重要**。Agent 架构模式与工程原则。
  - 核心结论：成功的 Agent 系统通常不是越复杂越好，而是从简单 LLM call、RAG、工具调用开始，只有当任务确实需要多步推理和外部行动时才升级为 workflow 或 autonomous agent。
  - 关键区分：workflow 是由代码预先编排路径，LLM 在固定步骤里执行；agent 是 LLM 动态决定步骤、工具和停止条件。你的 BingViz 更接近受控 workflow，而不是完全放权的 autonomous agent。
  - 基础模块：Augmented LLM = LLM + retrieval + tools + memory。工具描述、参数 schema、返回格式和错误处理都属于 Agent-Computer Interface，不是附属细节。
  - 六种模式：Prompt Chaining、Routing、Parallelization、Orchestrator-Workers、Evaluator-Optimizer、Autonomous Agent。
  - Prompt Chaining：把任务拆成顺序步骤，每一步输出给下一步。适合报告生成、翻译、结构化改写；缺点是路径固定，遇到新根因线索不够灵活。
  - Routing：先判断任务类型，再进专门链路。适合把 BingViz 问题路由到 RPM、coverage、query intent、bid/CTR 等不同分析模板。
  - Parallelization：多个子任务并行跑，再聚合结果。适合同时检查 market、device、query segment、ad coverage，降低单一路径误判。
  - Orchestrator-Workers：中心 LLM 动态拆任务并分派 worker。适合 coding agent 或信息搜集，但要注意 worker 结果合并和 trace 评估。
  - Evaluator-Optimizer：生成器给答案，评估器提出改进，多轮迭代。适合 SkillLoop 的 Validation Results，也适合 BingViz final answer 的证据完整性检查。
  - Autonomous Agent：LLM 长时间自主规划、调用工具、根据环境反馈推进。适合开放任务，但成本、延迟和错误累积风险最高，需要 sandbox、guardrails、stop condition。
  - 和简历连接：你可以说“BingViz 不是盲目做最自由的 agent，而是参考 agentic workflow 思路，把归因分析限制在可验证 DAG 中；Context Propagation 是为了获得 workflow 的稳定性，同时保留一定动态下钻能力”。
  - 面试一句话：Agent 工程的关键不是把模型放飞，而是选择合适复杂度；业务归因这种高风险任务，更适合有状态、有工具证据、有评估闭环的受控 workflow。
  - 必背风险：复杂 agent 会带来更高 token 成本、延迟、调试难度和错误累积；框架能加速开发，但会遮蔽底层 prompt/tool/trace，生产系统要能看见每一步。
- [ ] [ReAct: Reasoning and Acting](https://arxiv.org/abs/2210.03629) — Yao et al., 2022。你的 Context Propagation 改进的直接基线。
  - 核心问题：传统 LLM 只生成答案，缺少和外部环境互动；传统 RL/规划系统能行动但语言推理弱。ReAct 把 reasoning trace 和 action trace 交替起来。
  - 基本循环：Thought 负责推理下一步，Action 调用搜索/API/环境工具，Observation 接收外部反馈，然后继续 Thought。
  - 优点：可解释、能查资料、能根据新 observation 修正方向，比一次性回答更适合知识密集任务和交互环境。
  - 局限：每一步都让 LLM 自己决定下一步，长链路中容易发散、重复查同一类信息、遗忘早期发现、被无关 observation 带偏。
  - 对 BingViz 的启发：RPM 归因确实需要“推理 + 工具”，但不能完全依赖模型自由决定步骤。你保留工具反馈，替换开放式 Thought 决策，把下一步选择收敛到归因 DAG 和 Context Object。
  - Context Propagation 如何改进：父节点把 market、metric、period、finding、confidence、evidence 写成结构化 state；子节点直接继承这些约束，而不是从长对话历史里猜当前任务。
  - 面试一句话：ReAct 证明了 reasoning + acting 的价值，但业务归因需要更强确定性；我的改进是把 ReAct 的自由循环改成图约束下的工具执行和上下文传播。
  - 必背风险：不要说 ReAct 错了；要说它适合开放探索，但对企业数据归因这种高可信场景，需要 state、schema、guardrail 和 evaluation。
- [ ] [MCP 官方规范](https://modelcontextprotocol.io/) — FastMCP 工具标准化解耦的理论来源。
  - 核心定义：MCP 是连接 AI 应用和外部系统的开放协议，可以把本地文件、数据库、API、工作流、prompt template 暴露给 AI 应用。
  - 类比：MCP 像 AI 应用的 USB-C；AI Host 不需要为每个数据源写死集成，只要按协议连接 MCP Server。
  - 参与者：Host 是 VS Code/Claude Desktop/Agent App 这类 AI 应用；Client 是 Host 内部为每个 server 维护连接的组件；Server 是提供工具、资源和 prompt 的外部程序。
  - 三类核心原语：Tools 是可执行动作，如查数据库；Resources 是上下文数据，如 schema、文件、指标元数据；Prompts 是可复用交互模板，如 SQL few-shot 或归因分析模板。
  - 协议层：MCP 使用 JSON-RPC 2.0 做消息交换，包含 initialize、capability negotiation、tools/list、tools/call、resources/read、notifications 等流程。
  - Transport：stdio 适合本地工具和桌面集成，低开销；Streamable HTTP 适合远程服务、多客户端、企业认证和集中部署。
  - 和简历连接：BingViz 用 MCP/ FastMCP 把 CK、Databricks、Kusto/Titan API、semantic layer 从 Agent 主体里拆出去，新增数据工具不需要重写 Agent 推理逻辑。
  - 面试一句话：MCP 不只是 function calling 的另一种写法，而是工具发现、能力协商、schema、transport、通知和权限边界的一整套生态协议。
  - 必背风险：MCP 本身不自动保证安全；安全来自 server 端权限、只读工具、参数 schema、SQL allowlist、scan limit、审计日志和必要的人审。

---

## P1 推荐阅读

- [ ] [LangGraph 官方文档](https://langchain-ai.github.io/langgraph/) — BingViz 图导向执行的核心参考。
  - 核心内容：LangGraph 把 LLM 应用建模成 state graph，由节点处理任务、边决定流转、state 在节点间传递。
  - 为什么重要：普通 chain 是线性流程，适合固定步骤；业务归因需要条件分支、回退、重试、并行下钻和最终汇总，更像 graph。
  - 关键概念：State、Node、Edge、Conditional Edge、Checkpoint、Human-in-the-loop、Tool Node。
  - 和 Context Propagation 的关系：Context Object 可以作为 graph state；每个 drilldown node 读取父节点 state，写入新的 finding/evidence/confidence。
  - 面试一句话：我用 graph workflow 替代线性 chain，是因为归因分析天然是“发现异常 -> 选择下钻方向 -> 交叉验证 -> 汇总证据”的状态机。
- [ ] [OpenAI: Building Agents Guide](https://platform.openai.com/docs/guides/building-agents) — Guardrails、handoffs、tracing、tool use 的工程参考。
  - 核心内容：Agent 系统通常由模型、工具、指令、上下文、guardrails、handoffs、tracing/evaluation 组成。
  - Guardrails：限制输入输出、工具调用、权限范围和失败行为。对应 BingViz 的只读查询、表/字段校验、scan limit、dangerous SQL block。
  - Handoffs：一个 agent 或 workflow 把任务交给更专门的 agent/workflow。对应把泛化业务问题交给 RPM drilldown、query intent、ad coverage 等专门节点。
  - Tracing：记录每次模型调用、工具调用、输入输出、错误和耗时。对应 BingViz evaluation 和线上 debug。
  - 面试一句话：Agent 要上线，不能只看最终回答；要有 guardrails 管住动作，有 handoff 管住职责，有 tracing 管住可观测性。
- [ ] [Agentic Design Patterns — Andrew Ng](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/) — 4 种常见 Agent 能力模式。
  - Reflection：模型生成后自我批判和修改。对应 SkillLoop 的 Validation Results，但你的版本更强调外部测试结果，而不是纯自评。
  - Tool Use：模型调用外部工具获取事实或执行动作。对应 BingViz 查询 CK/Databricks/Kusto，而不是凭语言模型猜指标。
  - Planning：先拆解任务，再逐步执行。对应归因 DAG 和 Structured Task Log 的 Planned Steps。
  - Multi-Agent：多个角色协作。对应 orchestrator-workers，但你的面试口径应强调“能用多 Agent，但业务归因优先单 workflow + 多工具，降低协调成本”。
  - 面试一句话：我的项目覆盖 Tool Use、Planning 和 Reflection，但没有盲目堆多 Agent；核心选择标准是可控、可验证、可 debug。
- [ ] [MCP Architecture](https://modelcontextprotocol.io/docs/learn/architecture) — MCP client-server、data layer、transport layer 的细节。
  - 架构层次：Data layer 定义 JSON-RPC 消息、生命周期、tools/resources/prompts/notifications；Transport layer 负责 stdio 或 HTTP 通信、鉴权和连接管理。
  - 初始化流程：client/server 先做 protocol version negotiation、capability discovery、identity exchange；能力不兼容时应该终止连接。
  - 工具发现：client 调 `tools/list` 获取工具名、描述、inputSchema；模型后续只能按发现到的 schema 调 `tools/call`。
  - 动态更新：server 可通过 notification 告知工具变化，client 再刷新 tool registry。
  - 和简历连接：这支撑你说“工具标准化解耦”：不是把函数列表写在 prompt 里，而是通过协议发现、调用、更新、审计。

---

## P2 补充阅读

- [ ] [LLM Agent Survey](https://arxiv.org/abs/2308.11432) — Agent 综述，用来搭概念框架。
  - 核心内容：通常从 profile、memory、planning、action、evaluation 几个维度理解 Agent。
  - Profile：Agent 的角色和目标；BingViz 的 profile 是业务分析助手，不是通用聊天机器人。
  - Memory：短期上下文、长期记忆、外部状态；你的 Context Object 属于任务级外部状态，不是无边界聊天记忆。
  - Planning：任务拆解、子目标、反思；对应归因 DAG 和 SkillLoop Planned Steps。
  - Action：工具/API/环境交互；对应 MCP tools。
  - Evaluation：任务成功率、过程质量、安全性、人类接受度；对应 BingViz evaluator。
- [ ] [Toolformer](https://arxiv.org/abs/2302.04761) — 工具使用能力的经典论文。
  - 核心内容：模型可以学习在合适位置调用外部工具，例如计算器、搜索、翻译、日历等，以补足纯语言模型能力。
  - 对你的启发：企业指标归因不能靠模型“记忆”或猜测，必须调用真实数据工具；工具调用结果是 ground truth 的主要来源。
  - 面试用法：可以用它解释 Tool Use 的重要性，再转到 MCP：Toolformer 关注模型如何学会用工具，MCP 关注工程上如何标准化暴露工具。
- [ ] [AutoGen](https://arxiv.org/abs/2308.08155) — Microsoft 多智能体协作框架。
  - 核心内容：用多个可对话 agent 分工协作，例如 planner、executor、critic、user proxy。
  - 适用场景：任务角色清晰、可以通过对话协作、需要多个专家视角时有价值，比如代码生成、研究搜集、模拟评审。
  - 和你的边界：BingViz 不一定需要多个 agent 互相聊天；如果工具和状态已经清晰，单 orchestrated workflow 更稳定、更容易评估。
  - 面试一句话：AutoGen 提供多 agent 协作思路，但我的项目选择更受控的 graph workflow，是因为业务数据归因更重视确定性和证据链。
- [ ] [FastMCP GitHub](https://github.com/jlowin/fastmcp) — MCP server 快速开发实现。
  - 核心内容：用较少代码定义 MCP server、tool、resource，并支持本地或远程 transport。
  - 和项目连接：BingViz 的数据工具可以封装为 FastMCP tools，例如 `get_schema`、`run_readonly_query`、`get_metric_lineage`、`fetch_dashboard_metadata`。
  - 工程要点：工具名要清晰，参数要结构化，返回值要稳定；危险操作应从接口设计上变难，而不是只靠 prompt 告诉模型不要做。
  - 面试一句话：FastMCP 是落地层，MCP 是协议层；我关注的是把企业数据能力封装成模型容易正确调用、也容易审计的工具接口。

---

## 关键词自测

### 核心架构

> ReAct Loop (Thought → Action → Observation)
> 你的 Context Propagation 替代方案 — 减少冗余推理/降低 token/提高确定性
> Graph-based vs Chain-based Execution / LangGraph DAG
> Tool Use / Function Calling / Tool schema
> Agent Memory: Working Memory vs Long-term Memory

### 协议 & 工程

> MCP 三大原语: Tool / Resource / Prompt
> Context Engineering (超越 Prompt Engineering)
> Structured Output / Schema Validation
> Guardrails / Human-in-the-loop

### 高级模式

> Multi-Agent: Orchestrator-Workers / Debate / Delegation
> Handoff 机制 / Agent 间任务交接
> Evaluation & Observability / LangSmith trace
> Document-Driven Development (你的 SkillLoop 方法论)

---

## 4. 深化阅读地图：按 BingViz / SkillLoop 反推

> Agent 是辅攻，但你的项目很新。面试重点不是背框架名，而是讲清「为什么 ReAct 不稳、为什么 MCP 解耦、为什么 Task Log 能让长任务可控」。

### 4.1 Agent 架构模式：支撑「Context Propagation 替代 ReAct」

- [ ] [Plan-and-Solve Prompting](https://arxiv.org/abs/2305.04091) — ReAct 之外的规划范式，帮你解释「先规划再执行」。
  - 核心内容：把复杂问题先拆成 plan，再逐步 solve，避免模型直接跳到最终答案。
  - 和 ReAct 区别：ReAct 每轮边想边行动；Plan-and-Solve 先形成全局步骤，再执行，控制感更强。
  - 和 BingViz 连接：RPM 归因可以先规划 market drilldown、query intent、coverage、bid/CTR，而不是让模型每轮临时决定下一步。
  - 面试口径：我的 Context Propagation 更像工程化 Plan-and-Solve：计划不是一段自然语言，而是可执行 DAG 和结构化 state。
  - 局限：计划一开始可能不完整，所以需要允许动态扩展节点和交叉验证。
- [ ] [Tree of Thoughts](https://arxiv.org/abs/2305.10601) — 多路径搜索式推理，可用于对比你的树形归因分析。
  - 核心内容：不只生成一条 reasoning chain，而是展开多个 thought 分支，对中间状态评分、选择、回溯。
  - 和 BingViz 连接：业务归因经常有多个候选根因，例如 query mix、ad coverage、bid、CTR、market shift；可以把每个候选根因当成 thought branch。
  - 工程转译：不要真的无限展开 thought tree，而是把候选根因变成可执行分析节点，每个节点必须用数据工具验证。
  - 面试口径：Tree of Thoughts 给了“多假设搜索”的理论背景，但我在业务系统里把它约束成有成本预算、有证据要求的归因树。
  - 局限：分支越多成本越高，评分如果只靠 LLM 会不可靠；必须引入数据 evidence 和 stopping condition。
- [ ] [Reflexion](https://arxiv.org/abs/2303.11366) — 通过反思和记忆改善 Agent，适合和 Validation Results 对比。
  - 核心内容：Agent 在失败后生成反思，把经验写入 memory，用于下一次尝试。
  - 和 SkillLoop 连接：SkillLoop 的 Validation Results 类似反思触发器，但它更强调外部验证结果，比如测试失败、依赖缺失、接口报错。
  - 关键差异：Reflexion 可以是模型自我评价；SkillLoop 不能只靠自评，必须把真实运行结果写进 Task Log。
  - 面试口径：我借鉴 reflection 思路，但把“反思”落成可审计的工程状态，避免模型自己说自己对。
  - 局限：纯文本反思容易积累错误经验，需要和测试、trace、人工审核结合。
- [ ] [Voyager](https://arxiv.org/abs/2305.16291) — 长期技能库和自我改进，适合补「Agent memory」概念。
  - 核心内容：Agent 在 Minecraft 环境中持续探索，把成功技能沉淀为 skill library，后续复用。
  - 和 SkillLoop 连接：SkillLoop 也强调长周期任务中保存状态和经验，但不是游戏技能库，而是工程任务日志和决策记录。
  - 和 BingViz 连接：可类比为分析模板库，例如 RPM 归因、coverage 归因、query intent 归因都能沉淀为 reusable workflow。
  - 面试口径：长期 Agent 不能只依赖上下文窗口，需要外部化记忆；但企业业务场景里，记忆必须结构化、可审计、可删除。
  - 局限：自动积累技能可能引入错误或过时经验，需要版本管理和验证。
- [ ] [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/) — 当前主流 Agent SDK，重点看 handoffs、guardrails、tracing。
  - 核心内容：把 agent、tool、handoff、guardrail、trace 组织成可编程 SDK。
  - Handoff：适合把一个任务交给专门 agent 或 workflow，例如从总分析 agent 交给 SQL analysis agent。
  - Guardrail：适合限制输入输出、工具参数和安全策略，例如禁止写 SQL、限制扫描大表。
  - Tracing：适合记录每次 tool call、模型输入输出和错误，方便 evaluation。
  - 面试口径：无论用哪个 SDK，生产 Agent 的关键能力都是工具、状态、边界和 trace；框架只是实现载体。
- [ ] [Microsoft AutoGen](https://microsoft.github.io/autogen/) — 多 Agent 协作框架，和 orchestrator-workers 模式对齐。
  - 核心内容：多个可对话 agent 通过消息协作，每个 agent 可以有不同角色、工具和终止条件。
  - 典型角色：planner 负责拆解任务，executor 调工具或写代码，critic 检查输出，user proxy 引入人类反馈。
  - 和 SkillLoop 连接：适合解释 coding agent 的协作范式，但 SkillLoop 更强调单个或多个 agent 都必须共享外部任务日志。
  - 面试口径：多 Agent 不是越多越好；当任务天然分角色时有价值，但业务归因更需要统一状态和证据链。
  - 局限：多 Agent 会增加协调成本、消息噪声和评估难度。
- [ ] [Semantic Kernel](https://learn.microsoft.com/en-us/semantic-kernel/overview/) — 微软 Agent/插件编排框架，适合面微软生态问题。
  - 核心内容：用 plugins/functions、planners、memory/connectors 编排 LLM 应用。
  - 和 MCP 的关系：Semantic Kernel 偏应用编排框架，MCP 偏工具/上下文协议；两者可互补。
  - 和 BingViz 连接：如果面试官问微软生态，可以把 BingViz 的工具层类比为 plugin/function 层，把 Context Propagation 类比为 workflow state 管理。
  - 面试口径：我不把自己绑定到某个框架，核心能力是把业务工具、状态流和评估闭环搭起来。
  - 局限：框架抽象会降低透明度，关键路径要能看底层 prompt、tool schema 和 trace。

读完要能回答：

- ReAct、Plan-and-Execute、Graph-based workflow 的控制权分别在哪里？
- Context Propagation 是 memory、state 还是 workflow constraint？如何准确命名？
- 树形归因遇到新线索时，如何动态扩展节点而不是写死流程？
- 什么时候应该用多 Agent，什么时候单 Agent + 多工具更稳？

### 4.2 Tool Use / MCP / Schema：支撑「工具标准化解耦」

- [ ] [MCP Specification](https://modelcontextprotocol.io/specification/latest) — 重点看 lifecycle、capabilities、tools、resources。
  - 核心内容：定义 client/server 如何初始化、协商能力、发现工具、调用工具、读取资源、接收通知。
  - Lifecycle：连接不是一上来就调用工具，而是先 initialize，确认 protocol version 和 capabilities。
  - Tools：可执行动作，适合 database query、API call、metric fetch。
  - Resources：上下文数据，适合 schema、metric definition、dashboard lineage。
  - Prompts：可复用模板，适合 SQL few-shot、归因分析 SOP。
  - 面试口径：MCP 把工具能力从 prompt 里的自然语言描述，升级成可发现、可验证、可版本化的协议对象。
- [ ] [MCP Transports](https://modelcontextprotocol.io/docs/concepts/transports) — stdio、HTTP/SSE 的取舍，面试可能问部署形态。
  - Stdio：本地进程通信，无网络开销，适合桌面工具、文件系统、本地开发 server。
  - Streamable HTTP：适合远程服务、多用户、多客户端、企业鉴权、集中部署。
  - SSE/streaming：适合长任务进度、日志、增量结果。
  - 和 BingViz 连接：本地开发可用 stdio；团队共享的数据工具更适合 HTTP + bearer token/OAuth + server-side audit。
  - 面试口径：transport 的选择本质是部署边界和权限边界的选择，不只是通信方式。
- [ ] [FastMCP Docs](https://gofastmcp.com/) — 你项目相关实现，重点看 server、tool schema、client。
  - 核心内容：快速把 Python 函数封装成 MCP tools/resources，并支持不同 transport。
  - Tool 设计：函数名、参数名、docstring、类型标注都会影响模型是否正确调用。
  - 数据工具建议：拆成 `list_metrics`、`get_schema`、`run_readonly_query`、`get_lineage`，不要只暴露一个万能 SQL 工具。
  - 面试口径：FastMCP 降低了实现成本，但真正的难点是工具边界设计、参数约束、错误返回和审计。
  - 局限：工具太宽会危险，工具太碎会让模型选择困难；需要按业务任务设计粒度。
- [ ] [Gorilla: LLM Connected with Massive APIs](https://arxiv.org/abs/2305.15334) — Tool/API 调用经典论文。
  - 核心内容：让 LLM 根据自然语言选择正确 API，并生成正确参数，减少 hallucinated API calls。
  - 和 MCP 连接：Gorilla 偏模型如何学会选 API，MCP 偏工程系统如何暴露和调用 API。
  - 和 BingViz 连接：数据工具很多时，最怕模型编造不存在的工具或参数；需要工具检索、schema、示例和错误反馈。
  - 面试口径：企业 Agent 的 tool use 不是“能调用函数”这么简单，而是要防 API hallucination 和 argument hallucination。
  - 局限：API 选择准确不等于业务结论正确，仍需要数据验证和最终 answer evaluation。
- [ ] [ToolLLM](https://arxiv.org/abs/2307.16789) — 工具学习数据与评估，补充 Tool-use benchmark 视角。
  - 核心内容：构建大规模工具调用数据和评估方法，让模型学习多工具选择和组合。
  - 关键点：真实任务往往需要多步工具链，而不是一次调用；评估要看工具选择、参数、执行结果和最终答案。
  - 和 BingViz 连接：归因分析也是多工具链，可能先查 schema，再查 market，再查 query，再查 coverage。
  - 面试口径：Tool-use evaluation 要评估过程，不只评估最终文字；否则模型可能答案看似合理但工具路径错误。
  - 局限：benchmark 工具和企业内部工具差异大，落地时要自建场景集。
- [ ] [Pydantic](https://docs.pydantic.dev/latest/) — Schema validation 和 structured output 的工程基础。
  - 核心内容：用类型和 schema 校验输入输出，自动解析、报错和生成 JSON schema。
  - 和 MCP 连接：tool inputSchema 可由类型定义生成，帮助模型理解参数边界。
  - 和 BingViz 连接：Context Object、SQL request、metric query、evaluation result 都应该有 schema。
  - 面试口径：Schema validation 是拦截工具参数幻觉的第一道防线；模型输出不可信，必须经过结构化校验。
  - 局限：schema 只能保证结构合法，不能保证业务语义正确，所以还需要 metadata validation 和工具 dry run。

读完要能回答：

- MCP 的 Tool、Resource、Prompt 三类原语分别解决什么问题？
- MCP 和普通 function calling 的区别是什么？为什么说它是「生态协议」而不是「函数调用格式」？
- Tool Arguments Hallucination 怎么拦截？schema validation、dry run、permission boundary 怎么设计？
- 数据工具里最危险的操作是什么？如何做只读权限、限流、SQL allowlist？

### 4.3 Evaluation / Observability：支撑「Agent 能上线」

- [ ] [Ragas](https://docs.ragas.io/) — RAG/Agent 输出质量评估，重点看 faithfulness、answer relevance、context precision。
  - 核心内容：评估回答是否忠实于检索上下文、是否回答问题、上下文是否有用。
  - 和 BingViz 连接：最终 root cause 不能凭模型猜，必须能追溯到 SQL/metric/tool evidence。
  - 可迁移指标：faithfulness 对应结论是否被数据支持；answer relevance 对应是否回答业务问题；context precision 对应工具证据是否有效。
  - 面试口径：业务分析 Agent 的评估不能只看语言流畅度，要看结论和证据之间是否 faithful。
  - 局限：RAG 指标偏文本检索，数据分析还要额外看 SQL validity、metric correctness、business acceptance。
- [ ] [DeepEval](https://docs.confident-ai.com/) — LLM 单元测试框架，和 SkillLoop 的 Validation Results 很搭。
  - 核心内容：把 LLM 输出测试化，定义 test case、metric、threshold，让回归测试可重复。
  - 和 SkillLoop 连接：Validation Results 可以记录 DeepEval/单元测试/集成测试输出，防止 Agent 假装完成。
  - 和 BingViz 连接：可以为固定归因场景构造 golden set，测试工具选择、证据完整性和最终结论。
  - 面试口径：Agent 迭代必须有 regression tests，否则 prompt 或工具改动会悄悄破坏旧场景。
  - 局限：LLM-as-judge 也可能偏，需要和规则指标、人审和业务指标结合。
- [ ] [LangSmith](https://docs.smith.langchain.com/) — Trace、dataset、evaluation，适合讲 Agent observability。
  - 核心内容：记录每次链路执行的 prompt、model、tool call、latency、error、intermediate output。
  - 和 BingViz 连接：归因结论被质疑时，可以回放每一步查询和上下文传播。
  - 典型用法：构建 dataset，批量跑 agent，比较不同 prompt/model/tool 版本。
  - 面试口径：没有 trace 的 Agent 很难上线，因为出了错不知道是 prompt、模型、工具、数据还是状态传播的问题。
  - 局限：trace 里可能有敏感数据，需要脱敏、权限和保留周期。
- [ ] [OpenTelemetry GenAI Semantic Conventions](https://opentelemetry.io/docs/specs/semconv/gen-ai/) — 生产级 tracing 的标准化方向。
  - 核心内容：用统一字段记录 GenAI 请求、模型、token、工具调用、latency、错误等 telemetry。
  - 和 BingViz 连接：企业环境里不一定用某个 LLM 专属平台，OpenTelemetry 方便接入现有 observability stack。
  - 面试口径：生产 Agent 的监控应该进入标准 telemetry 系统，而不是只存在 notebook 或 debug log。
  - 局限：标准字段只能解决观测格式，业务质量指标仍要自己定义。
- [ ] [SWE-bench](https://www.swebench.com/) — 代码 Agent 评测基准，适合 SkillLoop/Vibe Coding 的方法论背景。
  - 核心内容：用真实 GitHub issue 和测试结果评估 coding agent 修 bug 的能力。
  - 和 SkillLoop 连接：代码任务有客观测试反馈，非常适合 Structured Task Log 的 Validation Results。
  - 面试口径：coding agent 的优势是环境反馈明确，测试能告诉它是否真的修好；SkillLoop 把这种反馈写入长期任务状态。
  - 局限：通过测试不等于代码设计好，仍需要人审、架构约束和安全检查。
- [ ] [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — Prompt injection、data leakage、tool misuse 安全材料。
  - 核心风险：prompt injection、sensitive information disclosure、insecure output handling、excessive agency、tool misuse、supply chain risk。
  - 和 BingViz 连接：数据表内容、dashboard 描述、用户输入都可能包含恶意指令；Agent 不能把外部数据当系统指令执行。
  - 防护：工具只读、最小权限、schema validation、输出脱敏、危险操作人审、prompt/data 分层。
  - 面试口径：工具越强，Agent 安全越重要；不能只靠 prompt 说“不要泄露数据”，必须靠权限和接口边界。
  - 局限：OWASP 是风险清单，不是完整架构方案，需要结合企业权限系统落地。

读完要能回答：

- Agent 归因分析怎么评估？是看最终答案、过程 trace，还是业务决策是否正确？
- Faithfulness、tool success rate、SQL validity、human acceptance rate 怎么定义？
- 线上 Agent 出错怎么定位：prompt、model、tool、schema、数据源、权限还是上下文传播？
- Prompt injection 进入数据表内容时，Agent 如何防御？

### 4.4 数据分析 Agent：支撑「BingViz 业务归因」

- [ ] [Text-to-SQL Survey](https://arxiv.org/abs/2208.13629) — SQL Agent 的基础综述，补 Text-to-SQL 局限。
  - 核心内容：Text-to-SQL 关注把自然语言问题转换成 SQL，涉及 schema linking、query generation、execution accuracy。
  - 和 BingViz 连接：BingViz 需要 SQL 能力，但目标不是“生成一条 SQL”，而是多步归因分析。
  - 关键局限：Text-to-SQL 回答 what happened 容易，回答 why happened 需要多轮 drilldown、指标定义、对照基线和业务假设。
  - 面试口径：我把 Text-to-SQL 放在工具层，而不是把整个业务分析简化成 SQL 生成问题。
  - 风险：SQL 正确不等于业务结论正确，可能用错指标、时间窗、过滤条件或 baseline。
- [ ] [Spider Text-to-SQL Benchmark](https://yale-lily.github.io/spider) — 了解 SQL 生成评测，不必深刷。
  - 核心内容：跨数据库、多表复杂 SQL 的 benchmark，用 exact match 和 execution accuracy 等方式评估。
  - 和 BingViz 连接：可以说明 SQL 生成有成熟评测，但 BingViz 还要评估归因路径和业务结论。
  - 面试口径：Spider 这类 benchmark 测 SQL 能不能写对，BingViz 需要进一步测分析步骤是否合理、证据是否完整。
  - 局限：benchmark schema 相对静态，企业数据仓库有权限、血缘、口径和成本问题。
- [ ] [Data Formulator](https://github.com/microsoft/data-formulator) — 微软数据分析与可视化 Agent 参考。
  - 核心内容：帮助用户通过自然语言和交互生成数据转换与可视化。
  - 和 BingViz 连接：同样是 AI-assisted data analysis，但 BingViz 更偏业务指标归因和自动下钻。
  - 可借鉴点：数据 transformation、chart intent、用户交互式修正、可视化解释。
  - 面试口径：数据分析 Agent 不只是 SQL，也包括数据变换、可视化、解释和交互式验证。
  - 局限：可视化生成不能替代根因分析，需要把 chart 背后的 metric lineage 和 evidence 讲清。
- [ ] [LIDA: Automatic Generation of Visualizations](https://arxiv.org/abs/2303.02927) — LLM 数据可视化/分析材料。
  - 核心内容：LLM 根据数据摘要、用户目标生成可视化和解释。
  - 和 BingViz 连接：最终业务归因可以用图表展示 market contribution、query mix shift、coverage drop。
  - 面试口径：可视化是让业务方信任结论的证据载体；但图表生成要建立在正确数据和指标口径上。
  - 局限：LLM 可能选择误导性图表或过度解释，需要 chart recommendation guardrail。
- [ ] [Databricks Mosaic AI Agent Framework](https://docs.databricks.com/aws/en/generative-ai/agent-framework/) — Databricks 生态 Agent 参考，和你 CK + Databricks 架构相关。
  - 核心内容：围绕企业数据、模型 serving、evaluation、deployment 构建数据/AI agent。
  - 和 BingViz 连接：Databricks 适合离线深度归因、大规模 query log join、长窗口分析；ClickHouse 适合实时扫描。
  - 面试口径：我的架构里 CK 和 Databricks 分工明确，前者负责快速发现哪里异常，后者负责大规模离线分析为什么异常。
  - 局限：Databricks 作业成本和延迟更高，需要把实时扫描和离线深挖分层。

读完要能回答：

- 为什么传统 Text-to-SQL 只能回答「是什么」，很难回答「为什么」？
- 归因分析的搜索空间如何定义：地域、query、广告覆盖、bid、CTR、商业意图？
- ClickHouse 和 Databricks 的分工是性能、成本还是数据新鲜度驱动？
- Agent 发现根因后，如何让业务方信任结论？需要哪些证据链？

---

## 5. BingViz / SkillLoop 专属追问

### Context Propagation

- Q: Context Object 里存什么？事实、假设、约束、指标、SQL 结果分别怎么表示？
- Q: 父节点结论如果错了，会不会把错误传播到所有子节点？怎么纠偏？
- Q: Context Propagation 和普通 memory 有什么区别？
- Q: 为什么不用一个超长 prompt 保存全历史？token、噪声、可控性分别有什么问题？

### MCP / 工具层

- Q: MCP Server 如何发现工具能力？schema 如何版本化？
- Q: Databricks/Kusto/ClickHouse 三类工具的权限和成本控制怎么做？
- Q: Tool 调用失败时，Agent 是重试、换工具、降级，还是回问用户？
- Q: 如何防止 Agent 生成危险 SQL 或扫描过大表？

### SkillLoop / Vibe Coding

- Q: Structured Task Log 和 Chain-of-Thought 有什么关系？是否会泄露敏感推理？
- Q: Task Log 字段为什么选 Current Goal、Planned Steps、Decision History、Known Dependencies、Validation Results？
- Q: 如果 Agent 把 Task Log 写错了怎么办？如何用 tests/trace 校正？
- Q: Document-Driven Development 和传统 TDD/ADR/PRD 有什么异同？

---

## 6. 实战练习

- [ ] 写一个最小 MCP Server：暴露 `get_schema` 和 `run_readonly_query` 两个工具，并用 schema validation 拦截非法字段。
- [ ] 用 LangGraph 画一个 RPM 归因 DAG：Root -> Market Drilldown -> Query Intent -> Ad Coverage -> Bid/CTR。
- [ ] 设计一个 Context Object JSON schema，包含 `finding`, `confidence`, `evidence`, `constraints`, `next_nodes`。
- [ ] 为一个 Agent trace 设计评估表：tool success、SQL validity、evidence completeness、human acceptance。
- [ ] 写一份 SkillLoop Task Log 模板，并用一个小功能开发任务跑一遍计划、执行、验证闭环。

---

## 7. 复习输出物

- [ ] 1 页纸：BingViz Agent 架构图，标出 LLM、LangGraph、MCP、CK、Databricks、Context Object。
- [ ] 1 页纸：ReAct vs Context Propagation 对比表。
- [ ] 1 页纸：MCP vs Function Calling vs 内置 Tool Registry。
- [ ] 5 个 30 秒答案：ReAct 不稳、Context Propagation、MCP、Schema Validation、Structured Task Log。
- [ ] 1 个 5 分钟答案：从零设计一个业务归因分析 Agent 平台。

---

## 8. 7 天冲刺路线

### Day 1：把 Agent 控制流讲清

- 读 Anthropic Building Effective Agents，区分 workflow 和 autonomous agent。
- 读 ReAct 摘要和方法部分，只抓住 Thought / Action / Observation 的控制权问题。
- 输出：一张 ReAct 失败模式表，列出发散、循环、遗忘、工具误用、token 膨胀。

### Day 2：把 Context Propagation 讲成工程方案

- 画 BingViz 归因 DAG：root、market drilldown、query intent、coverage、bid/CTR、final synthesis。
- 写 Context Object JSON schema，字段必须包含 `facts`、`hypotheses`、`constraints`、`evidence`、`confidence`、`next_actions`。
- 输出：30 秒版和 3 分钟版两套回答。

### Day 3：MCP 和工具安全

- 读 MCP lifecycle、tools、resources、transport。
- 对比 function calling、plugin registry、MCP server 三种模式。
- 输出：一个只读 SQL 工具的参数 schema、权限边界、失败重试策略。

### Day 4：Agent Evaluation

- 读 Ragas / DeepEval / LangSmith 的核心指标。
- 把 BingViz evaluation 拆成 tool-level、step-level、answer-level、business-level。
- 输出：一张 evaluation rubric，包含 success、smart-fail、understand-fail、tech-fail、no-response。

### Day 5：SkillLoop 方法论

- 对比 TDD、ADR、PRD、runbook、Structured Task Log。
- 输出：一个开发任务的 Task Log 样例，必须包含目标、计划、依赖、决策、验证结果。

### Day 6：安全与生产化

- 读 OWASP LLM Top 10 中 prompt injection、data leakage、excessive agency、tool misuse。
- 输出：BingViz 的安全边界：只读、SQL allowlist、scan limit、schema validation、human approval。

### Day 7：系统设计串讲

- 用 5 分钟讲完「从零设计业务归因 Agent 平台」。
- 必须覆盖数据源、语义层、工具层、workflow、context、evaluation、observability、deployment。

## 9. 论文与资料卡片模板

每篇材料读完只写 6 行，避免陷入全文摘抄：

```text
资料名：
解决的问题：
核心方法：
和我简历的连接：
面试可用一句话：
局限与风险：
```

建议优先做这些卡片：

| 资料 | 解决的问题 | 简历连接 | 一句话口径 |
| ---- | ---------- | -------- | ---------- |
| ReAct | 推理与行动交替 | Context Propagation 的基线 | ReAct 强在灵活，弱在长链路可控性 |
| Plan-and-Solve | 先规划再求解 | 归因 DAG 的 planning 层 | 规划把开放式搜索变成可检查步骤 |
| Tree of Thoughts | 多路径搜索 | 多根因假设探索 | 适合探索多个 hypothesis，但成本更高 |
| Reflexion | 失败后反思改进 | Validation Results | 反思要落到外部状态和验证结果，否则仍会漂移 |
| MCP Spec | 工具协议 | FastMCP 工具层 | MCP 是工具生态协议，不只是函数调用格式 |
| Ragas / DeepEval | 输出评估 | Agent evaluation | Agent 评估要看答案、证据、工具和业务接受度 |
| OWASP LLM Top 10 | 安全风险 | 企业数据 Agent | 工具越强，权限边界和审计越重要 |

## 10. BingViz 系统设计模板

### 需求定义

- 输入：业务问题、市场、时间窗口、指标、对照基线。
- 输出：根因候选、证据链、置信度、反例检查、建议动作。
- 约束：只读数据、低延迟探索、可回放 trace、不能泄露敏感数据。

### 模块拆分

| 模块 | 职责 | 关键风险 | 防护 |
| ---- | ---- | -------- | ---- |
| Query Planner | 把问题拆成分析 DAG | 过度拆分或漏拆 | 模板化 drilldown + 动态扩展 |
| Semantic Layer | 指标、表、dashboard 血缘 | 用错指标 | metadata validation |
| MCP Tools | CK/Databricks/Kusto 查询 | 危险 SQL、成本过高 | readonly、limit、allowlist |
| Context Store | 保存事实和假设 | 错误传播 | fact/hypothesis 分层、confidence |
| Evaluator | 评估过程和答案 | 只看最终文本 | trace + answer + business rubric |
| Observability | trace 和 debug | 出错不可定位 | tool spans、prompt version、dataset version |

### 5 分钟讲法骨架

1. 先定义业务问题和指标口径，避免 Agent 自己猜。
2. 通过 semantic layer 找到相关表、字段和 dashboard 血缘。
3. Planner 生成分析 DAG，不让模型每一步自由发散。
4. 每个节点调用 MCP 工具，只读查询并写入 Context Object。
5. Synthesis 节点汇总事实、假设、置信度和反例检查。
6. Evaluator 从工具成功率、SQL 合法性、证据完整性、人工接受度打分。

## 11. SkillLoop 深挖材料

SkillLoop 最好不要只讲「写文档」。更强的表达是：把文档变成 Agent 的外部状态机。

| 问题 | 普通 coding agent | SkillLoop |
| ---- | ----------------- | --------- |
| 目标漂移 | 依赖聊天历史 | `Current Goal` 每轮重锚定 |
| 决策反复 | 模型记忆不可靠 | `Decision History` 保留原因 |
| 依赖幻觉 | 直接假设库存在 | `Known Dependencies` 必须验证 |
| 假装完成 | 生成代码后停止 | `Validation Results` 记录测试输出 |
| 交接困难 | 只能看对话 | Task Log 可被人和 Agent 共同读取 |

面试可用类比：

> TDD 让代码通过测试，ADR 记录架构决策，PRD 记录产品意图；SkillLoop 把这些压缩成一个 Agent 可读写的执行协议，让长周期开发不依赖模型短期记忆。

## 12. 高频追问答案素材

- Q：为什么说 Context Propagation 不是简单 memory？
  - A：Memory 只回答「存了什么」，Context Propagation 还规定「哪些事实必须传给哪个子任务，以及子任务必须在什么约束下执行」。它更接近 workflow state 和 constraint passing。
- Q：如果数据源返回互相矛盾的结论怎么办？
  - A：Context Object 里把结论标为 hypothesis，触发 cross-check 节点；例如 RPM 下降同时看 coverage、CTR、bid、query mix、market mix，不能单路径定因。
- Q：Agent 如何处理工具失败？
  - A：先判断是参数错误、权限错误、数据为空、超时还是 schema mismatch。参数错误可修正重试，权限错误要降级或请求人审，数据为空要把 negative evidence 写进 trace。
- Q：为什么不用多 Agent 互相辩论？
  - A：业务归因更需要可控工具调用和证据链。多 Agent 适合角色分工明显的任务，但会增加协调成本和评估难度；单 Agent + 图工作流 + 多工具更稳。
- Q：SkillLoop 会不会把 CoT 暴露出来？
  - A：Task Log 不存模型私有推理，而存工程状态：目标、计划、决策依据、依赖、验证结果。它是可审计执行记录，不是无限制暴露思维链。
