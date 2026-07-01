# 🎛️ Fine-Tuning & Training

> Fine-tuning differentiates you from the prompt-only crowd — but only if you **evaluate the tuned model against the base** and report the delta. A LoRA run with no comparison is noise; a LoRA run with a win/loss table is a portfolio piece.

**Skills legend:** `dataset curation` · `PEFT (LoRA / QLoRA)` · `preference tuning (DPO)` · `eval vs base` · `quantization / VRAM management` · `distillation`
**Difficulty:** 🟢 Beginner · 🟡 Intermediate · 🔴 Advanced
**Link types:** 📄 paper · 📘 docs · 🎬 video · 🧑‍🏫 course · 🛠️ tool · 💻 repo

> [!TIP]
> Use Unsloth or a QLoRA config to avoid OOM on a single consumer/free-tier GPU — VRAM blowups are the #1 reason these projects stall.

---

### 1. LoRA fine-tune a small Llama vs base — 🟡 Intermediate
Fine-tune Llama 3.2 1B/3B on a focused task, then run a head-to-head eval against the base model. The canonical "I can actually tune models" project.
- **Skills proven:** LoRA, dataset formatting, base-vs-tuned eval.
- 💻 [unslothai/unsloth](https://github.com/unslothai/unsloth) — 67.7k ⭐ · Apache-2.0 core (AGPL Studio UI) · 2× faster tuning.
- 📘 [HF PEFT — LoRA](https://huggingface.co/docs/peft) — official docs.

### 2. QLoRA on a consumer / free-tier GPU — 🟡 Intermediate
4-bit quantized fine-tuning that fits a 7B model on a single 16GB GPU (or a free Colab). Document the memory footprint and throughput.
- **Skills proven:** 4-bit quantization, memory budgeting, QLoRA.
- 📄 [QLoRA — Efficient Finetuning of Quantized LLMs](https://arxiv.org/abs/2305.14314) — the paper.
- 💻 [unslothai/unsloth](https://github.com/unslothai/unsloth) — 67.7k ⭐ · Apache-2.0 · QLoRA notebooks.

### 3. DPO preference tuning — 🔴 Advanced
Build a preferred/rejected pairs dataset and align a model with Direct Preference Optimization; measure win-rate vs the SFT baseline.
- **Skills proven:** preference data, DPO, win-rate eval.
- 📄 [DPO — Direct Preference Optimization](https://arxiv.org/abs/2305.18290) — the paper.
- 📘 [HF TRL — DPOTrainer](https://huggingface.co/docs/trl/dpo_trainer) — implementation docs.

### 4. Instruction-tune a small model (Axolotl / Llama-Factory) — 🟡 Intermediate
Run a full SFT pipeline with a config-driven trainer on a curated instruction set; publish the config so it's reproducible.
- **Skills proven:** SFT pipelines, config-driven training, reproducibility.
- 💻 [axolotl-ai-cloud/axolotl](https://github.com/axolotl-ai-cloud/axolotl) — Apache-2.0 · config-first fine-tuning.
- 💻 [hiyouga/LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory) — Apache-2.0 · unified tuning UI/CLI.

### 5. Fine-tune a sentence-transformer for RAG embeddings — 🟡 Intermediate
Tune an embedding model on in-domain pairs and show retrieval recall@k improving on *your* corpus. Directly boosts a RAG project — a great pairing.
- **Skills proven:** contrastive/triplet training, embedding eval, domain adaptation.
- 📘 [Sentence Transformers — training](https://www.sbert.net/docs/sentence_transformer/training_overview.html) — official docs.

### 6. Reasoning-model fine-tune (distill chain-of-thought) — 🔴 Advanced
Distill CoT traces from a strong reasoning model into a small student, then measure the reasoning-benchmark lift. On-trend for 2026's reasoning wave.
- **Skills proven:** distillation, CoT data generation, reasoning eval.
- 📄 [DeepSeek-R1 — reasoning via RL + distillation](https://arxiv.org/abs/2501.12948) — the paper.
- 💻 [patchy631/ai-engineering-hub](https://github.com/patchy631/ai-engineering-hub) — 36.2k ⭐ · MIT · distillation walkthroughs.

### 7. Synthetic dataset generation + quality filtering — 🟡 Intermediate
Generate a training set with an LLM, then dedupe and quality-filter it (perplexity / rubric / dedup). Data quality is where most tuning wins actually come from.
- **Skills proven:** synthetic data, filtering/dedup, dataset curation.
- 💻 [argilla-io/distilabel](https://github.com/argilla-io/distilabel) — Apache-2.0 · synthetic-data pipelines.

### 8. GRPO / RL fine-tune on a verifiable task — 🔴 Advanced
Use a reward you can *check* (math, code tests, JSON validity) to run GRPO-style RL on a small model. Bleeding-edge and very differentiating.
- **Skills proven:** RL fine-tuning, reward design, verifiable rewards.
- 📘 [HF TRL — GRPO](https://huggingface.co/docs/trl/grpo_trainer) — implementation docs.
- 💻 [unslothai/unsloth](https://github.com/unslothai/unsloth) — 67.7k ⭐ · Apache-2.0 · GRPO examples.

---

← Back to the [full catalog](../README.md) · Want the **12 that actually land jobs**? See [projects-to-land-an-ai-job](https://github.com/landedjobs/projects-to-land-an-ai-job).
