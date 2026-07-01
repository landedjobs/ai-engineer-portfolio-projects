# 🧠 Prompt Engineering & DSPy

> The 2026 shift: stop hand-tuning prompt strings, start **programming and optimizing** them. DSPy-style projects — where prompts are compiled against a metric — signal that you treat prompting as engineering, not vibes.

**Skills legend:** `prompt programming (DSPy)` · `metric-driven optimization` · `few-shot selection` · `prompt versioning` · `context engineering` · `caching`
**Difficulty:** 🟢 Beginner · 🟡 Intermediate · 🔴 Advanced
**Link types:** 📄 paper · 📘 docs · 🎬 video · 🧑‍🏫 course · 🛠️ tool · 💻 repo

---

### 1. DSPy pipeline optimized against a metric — 🟡 Intermediate
Define a task as DSPy modules and let the optimizer compile the prompts/few-shots to maximize your metric. Then show the before/after score. The flagship of this theme.
- **Skills proven:** DSPy modules, metric-driven compilation, few-shot optimization.
- 💻 [stanfordnlp/dspy](https://github.com/stanfordnlp/dspy) — MIT · programming (not prompting) framework.
- 📄 [DSPy paper](https://arxiv.org/abs/2310.03714) — the method.

### 2. Prompt optimizer that beats a hand-written baseline — 🟡 Intermediate
Automatically search prompt variants (or use DSPy/APE) and prove a lift over your best manual prompt on a held-out set. Quantified prompt engineering.
- **Skills proven:** automated prompt search, held-out eval, baseline comparison.
- 💻 [stanfordnlp/dspy](https://github.com/stanfordnlp/dspy) — MIT · optimizers (MIPRO/BootstrapFewShot).

### 3. Context-engineering study (what to put in the window) — 🟡 Intermediate
Systematically vary retrieved context, ordering, and compression; measure quality and cost. "Context engineering" is the 2026 replacement for prompt-fiddling.
- **Skills proven:** context selection/ordering, compression, cost/quality tradeoffs.
- 📘 [Anthropic — effective context engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) — reference.

### 4. Prompt-versioning + registry with rollback — 🟢 Beginner
A small system to version prompts, A/B them, and roll back — like Git for prompts, wired to an eval. Production hygiene most portfolios skip.
- **Skills proven:** prompt versioning, A/B routing, rollback, eval hookup.
- 💻 [langfuse/langfuse](https://github.com/langfuse/langfuse) — 30.1k ⭐ · MIT · prompt management + versioning.

### 5. Prompt-injection defense playground — 🟡 Intermediate
A test-bed of injection attacks against a prompted app, plus layered defenses (delimiting, allow-lists, output checks) with a measured attack-success rate.
- **Skills proven:** prompt-injection, defense-in-depth, security eval.
- 💻 [promptfoo/promptfoo](https://github.com/promptfoo/promptfoo) — MIT · injection/red-team testing.

### 6. Chain-of-thought vs. structured-reasoning benchmark — 🟢 Beginner
Compare zero-shot, few-shot CoT, and self-consistency on a reasoning set; publish the accuracy/cost table. Simple, clean, and eval-driven.
- **Skills proven:** CoT, self-consistency, reasoning eval, cost accounting.
- 📄 [Self-Consistency Improves CoT](https://arxiv.org/abs/2203.11171) — the method.

---

← Back to the [full catalog](../README.md) · Want the **12 that actually land jobs**? See [projects-to-land-an-ai-job](https://github.com/landedjobs/projects-to-land-an-ai-job).
