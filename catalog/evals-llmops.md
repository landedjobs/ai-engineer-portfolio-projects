# 📊 Evals & LLMOps

> **"Eval is the new system design."** This is the single most under-built theme relative to how much interviewers care. A rigorous eval harness attached to any other project is often the thing that gets you the callback. Numbers beat vibes.

**Skills legend:** `golden sets` · `LLM-as-judge` · `faithfulness / precision / relevance` · `A/B + statistical significance` · `tracing (tokens/latency/cost)` · `CI regression gates` · `drift monitoring`
**Difficulty:** 🟢 Beginner · 🟡 Intermediate · 🔴 Advanced
**Link types:** 📄 paper · 📘 docs · 🎬 video · 🧑‍🏫 course · 🛠️ tool · 💻 repo

> [!WARNING]
> **Eval gaming is a red flag.** Report seeds, run counts, and confidence intervals — not the best of five runs. Reviewers who know evals will spot a cherry-picked number instantly.

---

### 1. RAGAS-style RAG eval harness — 🟡 Intermediate
Build a golden set and score faithfulness, context precision/recall, and answer relevance on every change. The flagship eval project — pair it with any RAG app above.
- **Skills proven:** golden-set curation, RAG metrics, regression tracking.
- 💻 [explodinggradients/ragas](https://github.com/explodinggradients/ragas) — 14.5k ⭐ · Apache-2.0 · RAG eval metrics.

### 2. LLM-as-judge with a rubric + human spot-check — 🟡 Intermediate
Design a scoring rubric, run an LLM judge, and validate it against human labels (report agreement / Cohen's κ). Shows you don't trust the judge blindly.
- **Skills proven:** rubric design, judge prompting, human-agreement measurement.
- 💻 [Arize-ai/phoenix](https://github.com/Arize-ai/phoenix) — 10.3k ⭐ · Apache-2.0 · OpenTelemetry-based eval.
- 📄 [Judging LLM-as-a-Judge (MT-Bench)](https://arxiv.org/abs/2306.05685) — the reference paper.

### 3. A/B prompt evaluation with statistical significance — 🟡 Intermediate
Compare two prompts/models on a task set with a proper significance test and effect size — not "it looks better." Deploy the winner behind a flag.
- **Skills proven:** experiment design, significance testing, effect sizes.
- 💻 [langfuse/langfuse](https://github.com/langfuse/langfuse) — 30.1k ⭐ · MIT · experiments + prompt management.

### 4. CI gate that fails a PR on eval regression — 🟡 Intermediate
A GitHub Action that runs the eval suite on every PR and blocks merge if faithfulness/accuracy drops below threshold. The most "production" thing on this page.
- **Skills proven:** CI/CD for LLMs, thresholds, quality gates.
- 📘 [Langfuse — CI/CD evals](https://langfuse.com/docs/evaluation) — reference setup.
- 💻 [promptfoo/promptfoo](https://github.com/promptfoo/promptfoo) — MIT · CLI eval + CI integration.

### 5. Trace-based cost & latency dashboard — 🟡 Intermediate
Instrument an app with tracing and surface p50/p95 latency, tokens, and $/1k-tokens per route. The "production mindset" signal reviewers scan for.
- **Skills proven:** tracing, cost accounting, percentile latency, dashboards.
- 💻 [langfuse/langfuse](https://github.com/langfuse/langfuse) — 30.1k ⭐ · MIT · tracing + cost dashboards.

### 6. Hallucination audit with citation verifiability — 🔴 Advanced
For each answer, verify every claim is supported by a retrieved source and compute a hallucination rate. Publish the failure taxonomy.
- **Skills proven:** claim decomposition, source attribution, failure analysis.
- 💻 [Arize-ai/phoenix](https://github.com/Arize-ai/phoenix) — 10.3k ⭐ · Apache-2.0 · hallucination evals.

### 7. Adversarial / red-team eval suite — 🔴 Advanced
Generate jailbreaks, prompt-injections, and edge cases; measure attack success rate and harden with guardrails. Security-flavored and increasingly asked-for.
- **Skills proven:** red-teaming, prompt-injection defense, safety metrics.
- 💻 [promptfoo/promptfoo](https://github.com/promptfoo/promptfoo) — MIT · red-team / injection testing.
- 💻 [Arize-ai/phoenix](https://github.com/Arize-ai/phoenix) — 10.3k ⭐ · Apache-2.0.

### 8. Production drift monitor over time — 🔴 Advanced
Track output-quality and input-distribution drift on live traffic, with alerts when metrics slip. Closes the loop from "shipped" to "monitored."
- **Skills proven:** online monitoring, drift detection, alerting.
- 📘 [Arize Phoenix — monitoring docs](https://arize.com/docs/phoenix) — drift + monitoring guide.

### 9. Golden-dataset builder + labeling workflow — 🟢 Beginner
A small tool to curate, version, and label an eval set (with inter-annotator agreement). The unglamorous foundation every other eval depends on.
- **Skills proven:** dataset versioning, labeling UX, annotator agreement.
- 💻 [langfuse/langfuse](https://github.com/langfuse/langfuse) — 30.1k ⭐ · MIT · datasets + annotation.

### 10. Model/prompt regression leaderboard — 🟡 Intermediate
Run a fixed suite across models (and over time) and publish a living leaderboard with cost/quality Pareto fronts. Great artifact to pin.
- **Skills proven:** benchmarking, Pareto analysis, reproducible runs.
- 💻 [promptfoo/promptfoo](https://github.com/promptfoo/promptfoo) — MIT · multi-model comparison.

---

← Back to the [full catalog](../README.md) · Want the **12 that actually land jobs**? See [projects-to-land-an-ai-job](https://github.com/landedjobs/projects-to-land-an-ai-job).
