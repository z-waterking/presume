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

- [ ] [Building Effective Agents — Anthropic](https://www.anthropic.com/engineering/building-effective-agents) — ⭐⭐ **最重要**。6 种 Agent 架构模式
- [ ] [ReAct: Reasoning and Acting](https://arxiv.org/abs/2210.03629) — Yao et al., 2022。你的 Context Propagation 改进了 ReAct
- [ ] [MCP 官方规范](https://modelcontextprotocol.io/) — 你用 FastMCP 做 Tool 标准化解耦

---

## P1 推荐阅读

- [ ] [LangGraph 官方文档](https://langchain-ai.github.io/langgraph/) — BingViz 核心框架
- [ ] [OpenAI: Building Agents Guide](https://platform.openai.com/docs/guides/building-agents) — Guardrails / Handoffs
- [ ] [Agentic Design Patterns — Andrew Ng](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/) — 4 种模式 (Reflection / Tool Use / Planning / Multi-Agent)
- [ ] [MCP Architecture](https://modelcontextprotocol.io/docs/learn/architecture) — Client-Server / Tool-Resource-Prompt

---

## P2 补充阅读

- [ ] [LLM Agent Survey](https://arxiv.org/abs/2308.11432) — Wang et al., 2023。最全面综述
- [ ] [Toolformer](https://arxiv.org/abs/2302.04761) — Schick et al., 2023
- [ ] [AutoGen](https://arxiv.org/abs/2308.08155) — Microsoft, 2023。多智能体框架
- [ ] [FastMCP GitHub](https://github.com/jlowin/fastmcp)

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
