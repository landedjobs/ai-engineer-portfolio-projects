# 🤖 Build guide: a tool-using agent that changes the right thing, safely

> **Time:** 2-4 weeks · **Difficulty:** 🔴 Advanced · **Catalog entry:** [Agents & tool use](../catalog/agents.md)
> **Proves:** tool design, state-based evaluation, reliability across repeated runs, recovery from failures, and human approval for risky actions.

"I built a multi-agent system" is the most common line on AI engineering resumes, and one of the weakest when there's nothing behind it. This guide builds an agent whose success you can check without reading its transcript: it either left the system in the right state or it didn't. Then it compares the agent against a plain fixed workflow over the same tools, because sometimes the workflow wins, and knowing that is the engineering judgement reviewers look for.

---

## 1. Scope

**Build:** an operations assistant for a small sandboxed business system, for example a mock order desk or IT helpdesk with a database you own.

It reads a request ("customer 4821 wants to change the shipping address on their last order"), looks up records, proposes an action, and applies it after approval.

**Keep the tool set small and typed (4-6 tools):** `search_records`, `get_record`, `validate_change`, `apply_change`, `ask_user`, `request_approval`.

**Include the messy cases on purpose:** missing records, ambiguous requests ("their last order" when there are two the same day), tool timeouts, and duplicate submissions.

**Leave out of the core:** several cooperating agents, browsing, long-term memory. Start with one agent and a baseline. Add roles only if a measured failure calls for them.

## 2. Architecture

```
request ─▶ router / policy ─▶ ┌ fixed workflow (baseline)
                              └ agent loop ─▶ typed tool layer ─▶ sandbox database
                                     │                 │
                              approval gate      event log + idempotency keys
                                     └──────▶ outcome verifier (checks the database, not the transcript)
```

Design rules:

- **Typed tools with validation.** Every tool has a schema and rejects bad arguments with a clear error the agent can recover from.
- **Idempotency keys on every write.** Retrying `apply_change` must never apply it twice.
- **Approval before consequential writes**, shown to the user as a plain diff of what will change.
- **Bounded loops.** A step limit and a cost limit per task, with a clean failure when hit.
- **An event log** of every model call, tool call, retry and approval, so a reviewer can replay a run.

LangGraph's durable execution and human-in-the-loop support, or the OpenAI Agents SDK, are reasonable ways to build this, but the recovery behaviour is something you design and test, not something a framework gives you for free.

## 3. Milestones

| Week | Ship | Done when |
|---|---|---|
| 1 | Sandbox service, seed data, task fixtures with the expected end state, the fixed workflow | The workflow passes its own fixtures from a clean reset |
| 2 | Typed tools, the agent loop, first end-to-end eval | You have a workflow-vs-agent table on held-out tasks |
| 3 | Approval gate, idempotency, retry and restart tests, injected tool failures | Duplicate writes and unapproved writes are measured, not assumed |
| 4 | Adversarial requests, cost comparison, a trace viewer, README | A stranger can run the eval and replay a failed run |

## 4. The task set

Write 100-150 tasks. Each task has a starting database state, a request, and the **required end state** (which rows must change, which must not). Where more than one outcome is acceptable, list all acceptable end states.

This is how τ-bench evaluates agents: it compares the final database state with an annotated goal state, and checks whether the agent succeeds reliably across repeated trials, not just once ([📄 τ-bench, ICLR 2025](https://proceedings.iclr.cc/paper_files/paper/2025/hash/1b126cc38b8638e07bef37e7b2bb72bf-Abstract-Conference.html); [💻 sierra-research/tau-bench](https://github.com/sierra-research/tau-bench) · 1.5k ⭐ · MIT).

Mix: ordinary tasks, ambiguous ones (the right move is `ask_user`), impossible ones (the right move is a clear refusal), policy traps (a request that breaks a rule), and failure injections (a tool times out once). Keep a held-out set you don't look at while tuning prompts.

## 5. What to measure

| Metric | How to compute | Workflow | Agent |
|---|---|---|---|
| Final-state success | Runs whose database matches an acceptable end state ÷ runs | | |
| Consistency (pass^3) | Tasks that pass all 3 independent runs from a clean reset ÷ tasks | | |
| Policy violations | Runs with a forbidden action (unapproved write, rule broken) ÷ runs | | |
| Tool-call correctness | Calls with the right tool, valid arguments and met preconditions ÷ calls | | |
| Recovery rate | Injected failures followed by a correct finish ÷ recoverable injected failures | | |
| Efficiency | Mean tool calls; p50 / p95 time to finish; total cost ÷ successful tasks | | |

**pass@k vs pass^k.** Anthropic's eval guide separates *pass@k* (at least one of k tries succeeds) from *pass^k* (all k tries succeed), and notes pass^k falls as k grows because consistency is harder ([📘 Anthropic: demystifying evals for agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)). A user gets one try, so report pass^k and don't present pass@k as reliability.

**Human wait time.** Report time spent waiting for approval separately from automated time, but keep human steps in the story: a task that needs approval every time is a different product from one that doesn't.

## 6. Cost and latency

Log every model round, tool wait, retry, approval wait and resumed run. Cost per *successful* task includes the failed and retried attempts. If the fixed workflow gets 90% of the agent's success at a fifth of the cost, say so: that finding is worth more in an interview than a bigger agent.

## 7. Common mistakes

- Building several agents before a single-agent baseline exists.
- Trusting the agent's own "done, I've updated it" instead of checking the database.
- Retrying writes without idempotency.
- Testing only the happy path where every tool succeeds.
- No step or cost limit, so one confused run loops forever.
- Reporting the best run, not the consistency across runs.

## 8. Stretch goals

- **Crash and resume:** kill the process mid-task and show the run resumes from the log without repeating writes.
- **Clarifying questions:** measure how often the agent asks when it should and doesn't ask when it shouldn't.
- **MCP:** serve the tool layer as an MCP server and run the same eval from a different client.
- **Cheaper model ablation:** swap the model for a smaller one and show where success drops.

## 9. References

- 💻 [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) · 45.1k ⭐ · MIT · graph-based agents with durable execution and human-in-the-loop.
- 💻 [openai/openai-agents-python](https://github.com/openai/openai-agents-python) · 30.7k ⭐ · MIT · a lightweight agent SDK with tools, handoffs and tracing.
- 💻 [sierra-research/tau-bench](https://github.com/sierra-research/tau-bench) · 1.5k ⭐ · MIT · the state-based agent benchmark this guide's eval is modelled on.
- 📄 [τ-bench (ICLR 2025)](https://proceedings.iclr.cc/paper_files/paper/2025/hash/1b126cc38b8638e07bef37e7b2bb72bf-Abstract-Conference.html) · final-state checking and repeated-trial reliability.
- 📘 [Anthropic: demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents) · transcripts vs outcomes, pass@k vs pass^k, grader choice.

## 10. Put it on your resume

Fill in your own numbers; these show the shape, not real results:

- *Built a tool-using operations agent over a sandboxed order system; raised final-state success from 72% (fixed workflow) to 89% on 120 held-out tasks, with pass^3 of 81%.*
- *Added approval gates and idempotency keys; 0 duplicate or unapproved writes across 400 runs with injected tool failures.*
- *Showed a fixed workflow matched the agent on 70% of task types at 20% of the cost, and routed those types to it.*

Next: write the README with the [project README template](../templates/project-readme.md), and see [presenting your project](../templates/presenting-your-project.md) for the interview.

---

<sub>Sources checked 17 Nov 2026. Star counts are a snapshot. Found something out of date? [Open an issue](https://github.com/landedjobs/ai-engineer-portfolio-projects/issues).</sub>
