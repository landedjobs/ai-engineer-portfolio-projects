# 🛠️ Build guides

The [catalog](../catalog/) tells you *what* to build. These guides tell you *how to build it so it gets you hired*: scope for a 2-4 week build, the architecture, week-by-week milestones, the exact eval table to publish, cost and latency to measure, the mistakes reviewers spot, and example resume lines.

| # | Guide | Difficulty | Proves |
|---|---|---|---|
| 1 | [Production RAG assistant that knows its limits](01-production-rag.md) | 🟡 Intermediate | Retrieval, citations, abstention, access control |
| 2 | [Tool-using agent that changes the right thing, safely](02-tool-using-agent.md) | 🔴 Advanced | Tool design, state-based evals, reliability, approvals |

More guides are on the way: MCP server, voice agent, browser agent, eval harness, fine-tuning, LLM serving, structured extraction and multimodal.

**Every guide uses the same reporting rules,** so your numbers are comparable and believable:

- Build the test set first, with a held-out split you only run at the end.
- Compare against a baseline on the same cases, changing one thing at a time.
- Report "passed / total", not just a percentage, and say how each case was graded.
- Cost per successful task includes failed and retried attempts; date your prices.
- Show real failures, not just the best run.

**Templates:** [project README](../templates/project-readme.md) · [eval report](../templates/eval-report.md) · [presenting your project](../templates/presenting-your-project.md)
