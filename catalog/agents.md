# 🤖 Agents & Tool-Use

> Agents are the theme with the widest quality gap: most portfolios ship a toy loop with no error handling. **Retry, backoff, cost caps, guardrails, and tracing** are what separate a demo from a shipped agent. Add those and one agent project outshines ten.

**Skills legend:** `tool-use / function-calling` · `orchestration (ReAct / plan-execute / supervisor)` · `state & memory` · `retries + backoff + cost tracking` · `guardrails` · `MCP`
**Difficulty:** 🟢 Beginner · 🟡 Intermediate · 🔴 Advanced
**Link types:** 📄 paper · 📘 docs · 🎬 video · 🧑‍🏫 course · 🛠️ tool · 💻 repo

---

### 1. Multi-role trip planner (CrewAI) — 🟢 Beginner
A crew of role-specialized agents (researcher, budgeter, itinerary writer) that collaborate to produce a plan. Great first taste of multi-agent orchestration.
- **Skills proven:** role decomposition, task hand-off, tool-use basics.
- 💻 [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) — 54.6k ⭐ · MIT · role-based multi-agent framework.

### 2. Multi-agent research team (LangGraph supervisor) — 🟡 Intermediate
A supervisor routes sub-tasks to specialist agents (search, read, synthesize) and merges results into a cited report. The most portfolio-relevant agent pattern.
- **Skills proven:** supervisor routing, shared state, cited synthesis, LangGraph.
- 💻 [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) — 36.1k ⭐ · MIT · stateful graph agents.
- 💻 [patchy631/ai-engineering-hub](https://github.com/patchy631/ai-engineering-hub) — 36.2k ⭐ · MIT · multi-agent tutorials.

### 3. ReAct agent with 5+ real tools — 🟡 Intermediate
A reasoning-and-acting loop wired to genuinely useful tools (web, calculator, code exec, a private API, a DB). Emphasize tool schemas and error recovery.
- **Skills proven:** ReAct loop, tool schema design, observation parsing, recovery.
- 📄 [ReAct — Synergizing Reasoning and Acting](https://arxiv.org/abs/2210.03629) — the paper.
- 📘 [LlamaIndex — ReAct agent](https://docs.llamaindex.ai/en/stable/examples/agent/react_agent/) — tutorial.

### 4. Hierarchical supervisor agent (teams of teams) — 🔴 Advanced
A top-level supervisor coordinating multiple sub-crews, each with its own supervisor. Tests state management and cost control at scale.
- **Skills proven:** hierarchical orchestration, budget enforcement, deadlock avoidance.
- 💻 [ashishpatel26/500-AI-Agents-Projects](https://github.com/ashishpatel26/500-AI-Agents-Projects) — 33.2k ⭐ · MIT · 500+ agent patterns + links.

### 5. Plan-and-execute agent (BabyAGI-style) — 🟡 Intermediate
Generate a plan, execute steps, re-plan on failure. Contrast with a pure ReAct agent on the same task and report steps/cost/success.
- **Skills proven:** planning, re-planning, task decomposition, comparison eval.
- 💻 [ashishpatel26/500-AI-Agents-Projects](https://github.com/ashishpatel26/500-AI-Agents-Projects) — 33.2k ⭐ · MIT.
- 📘 [LangGraph — plan-and-execute](https://langchain-ai.github.io/langgraph/tutorials/plan-and-execute/plan-and-execute/) — reference.

### 6. Autonomous game-playing agent — 🟢 Beginner
An agent that plays a text game (Wordle, 2048, a maze) via tool calls, with a scoreboard over N runs. Fun, self-contained, and shows a clean action/observation loop.
- **Skills proven:** action loops, state tracking, deterministic eval over runs.
- 💻 [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) — 116k ⭐ · Apache-2.0 · game-agent examples.

### 7. MCP-powered coding agent (GitHub / browser MCP) — 🔴 Advanced
An agent that uses Model Context Protocol servers (GitHub, filesystem, browser) to read issues, edit code, and open PRs. On-trend and highly differentiating for 2026.
- **Skills proven:** MCP client/servers, tool discovery, sandboxed execution.
- 📘 [Model Context Protocol — spec + servers](https://modelcontextprotocol.io/) — official docs.
- 💻 [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) — 116k ⭐ · Apache-2.0 · MCP agent examples.

### 8. Agent with persistent memory (MemGPT-style) — 🔴 Advanced
Long-term + working memory with summarization and retrieval, so the agent recalls facts across sessions. Show memory hits/misses in the trace.
- **Skills proven:** memory hierarchy, summarization, retrieval-augmented memory.
- 📄 [MemGPT — LLMs as Operating Systems](https://arxiv.org/abs/2310.08560) — the paper.
- 💻 [letta-ai/letta](https://github.com/letta-ai/letta) — Apache-2.0 · MemGPT successor (stateful agents).

### 9. Voice support agent — 🔴 Advanced
Real-time voice loop: STT → agent + tools (order lookup, refunds) → TTS, with barge-in and latency budgets. Full-stack and impressive live.
- **Skills proven:** streaming STT/TTS, tool-use under latency, turn-taking.
- 💻 [patchy631/ai-engineering-hub](https://github.com/patchy631/ai-engineering-hub) — 36.2k ⭐ · MIT · voice-agent builds.
- 🛠️ [LiveKit Agents](https://github.com/livekit/agents) — Apache-2.0 · real-time voice/agent framework.

### 10. Always-on briefing agent (HN / news / inbox) — 🟡 Intermediate
Runs on a schedule, pulls sources, dedupes, ranks, and delivers a personalized digest to Slack/email. A genuinely useful thing you'll keep running.
- **Skills proven:** scheduling, dedup/ranking, summarization, delivery integrations.
- 💻 [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) — 116k ⭐ · Apache-2.0 · briefing/news agents.

### 11. Computer-use / browser agent — 🔴 Advanced
An agent that drives a real browser to complete a task (fill a form, extract data, book something) with screenshots as observations. Bleeding-edge, high-signal.
- **Skills proven:** vision-grounded action, browser automation, failure recovery.
- 💻 [browser-use/browser-use](https://github.com/browser-use/browser-use) — MIT · LLM browser automation.
- 📄 [Anthropic — Computer Use](https://docs.anthropic.com/en/docs/build-with-claude/computer-use) — API + patterns.

### 12. Self-improving agent with reflection (Reflexion) — 🟡 Intermediate
The agent critiques its own output, stores lessons, and retries — measure success-rate lift across attempts. Cheap to build, strong eval story.
- **Skills proven:** self-critique, reflection memory, iterative improvement metrics.
- 📄 [Reflexion — Verbal Reinforcement Learning](https://arxiv.org/abs/2303.11366) — the paper.
- 💻 [ashishpatel26/500-AI-Agents-Projects](https://github.com/ashishpatel26/500-AI-Agents-Projects) — 33.2k ⭐ · MIT.

---

← Back to the [full catalog](../README.md) · Want the **12 that actually land jobs**? See [projects-to-land-an-ai-job](https://github.com/landedjobs/projects-to-land-an-ai-job).
