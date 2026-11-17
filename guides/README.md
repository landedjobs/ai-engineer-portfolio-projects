# 🛠️ Build guides

The [catalog](../catalog/) tells you *what* to build. These guides tell you *how to build it so it gets you hired*: scope for a 2-4 week build, the architecture, week-by-week milestones, the exact eval table to publish, cost and latency to measure, the mistakes reviewers spot, and example resume lines.

| # | Guide | Difficulty | Proves |
|---|---|---|---|
| 1 | [Production RAG assistant that knows its limits](01-production-rag.md) | 🟡 Intermediate | Retrieval, citations, abstention, access control |
| 2 | [Tool-using agent that changes the right thing, safely](02-tool-using-agent.md) | 🔴 Advanced | Tool design, state-based evals, reliability, approvals |
| 3 | [MCP server that works in real clients, with proper auth](03-mcp-server.md) | 🔴 Advanced | Protocol design, OAuth, tenant isolation, client compatibility |
| 4 | [Voice agent measured on what callers feel](04-voice-agent.md) | 🔴 Advanced | Turn-taking, interruptions, latency, tool accuracy |
| 5 | [Browser agent that verifies the final state](05-browser-agent.md) | 🔴 Advanced | Web automation, state checks, prompt-injection defence |
| 6 | [Eval harness that can block a bad release](06-eval-harness.md) | 🟡 Intermediate | Test sets, graders, judge calibration, CI gates |
| 7 | [Fine-tuning or distillation you can justify](07-fine-tuning.md) | 🟡 Intermediate | LoRA/QLoRA, distillation, held-out comparison, cost |
| 8 | [LLM serving tuned for useful throughput](08-llm-serving.md) | 🔴 Advanced | vLLM/SGLang, load tests, latency limits, cost per request |
| 9 | [Structured extraction where valid JSON is only the start](09-structured-extraction.md) | 🟡 Intermediate | Schemas, validation, field-level accuracy, review routing |
| 10 | [Multimodal system that answers with page and timestamp evidence](10-multimodal.md) | 🔴 Advanced | Vision-language models, transcripts, grounded citations |

**Every guide uses the same reporting rules,** so your numbers are comparable and believable:

- Build the test set first, with a held-out split you only run at the end.
- Compare against a baseline on the same cases, changing one thing at a time.
- Report "passed / total", not just a percentage, and say how each case was graded.
- Cost per successful task includes failed and retried attempts; date your prices.
- Show real failures, not just the best run.

**Templates:** [project README](../templates/project-readme.md) · [eval report](../templates/eval-report.md) · [presenting your project](../templates/presenting-your-project.md)
