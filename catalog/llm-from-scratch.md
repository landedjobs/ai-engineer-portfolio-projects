# 🔬 LLM From-Scratch & Internals

> These projects don't ship products — they buy you **credibility in the interview**. When you can explain attention, tokenization, and a training loop from memory because you *built* them, whiteboard questions become easy.

**Skills legend:** `tokenization (BPE)` · `transformer internals` · `training loops` · `CUDA / low-level` · `pretraining vs fine-tuning` · `PyTorch`
**Difficulty:** 🟢 Beginner · 🟡 Intermediate · 🔴 Advanced
**Link types:** 📄 paper · 📘 docs · 🎬 video · 🧑‍🏫 course · 🛠️ tool · 💻 repo

> [!TIP]
> Pair each build with a short write-up ("what I learned building X from scratch"). The blog post is often more interview-useful than the code.

---

### 1. BPE tokenizer from scratch — 🟢 Beginner
Implement byte-pair encoding: train merges, encode/decode, handle special tokens. The perfect weekend "I understand tokenization" project.
- **Skills proven:** BPE, vocab construction, encode/decode round-trips.
- 💻 [karpathy/minbpe](https://github.com/karpathy/minbpe) — 10.6k ⭐ · MIT · minimal BPE reference.
- 🎬 [Karpathy — Let's build the GPT Tokenizer](https://www.youtube.com/watch?v=zduSFxRajkE) — companion video.

### 2. GPT-2 (124M) reproduction with nanoGPT — 🟡 Intermediate
Train a small GPT and reproduce GPT-2-scale loss curves. The reference "I can pretrain a transformer" project.
- **Skills proven:** transformer blocks, training loop, LR schedules, checkpointing.
- 💻 [karpathy/nanoGPT](https://github.com/karpathy/nanoGPT) — 60.3k ⭐ · MIT · small-scale training.
- 🎬 [Karpathy — Let's reproduce GPT-2](https://www.youtube.com/watch?v=l8pRSuU81PU) — companion video.

### 3. GPT-2 in raw C / CUDA (llm.c-style) — 🔴 Advanced
Implement training in C/CUDA to understand what the framework hides — kernels, memory, and the forward/backward at the metal. Deep-systems credibility.
- **Skills proven:** CUDA kernels, memory layout, low-level autograd, performance.
- 💻 [karpathy/llm.c](https://github.com/karpathy/llm.c) — 30.4k ⭐ · MIT · GPT-2/3 in C/CUDA.

### 4. Build a ChatGPT-like LLM in PyTorch (Raschka) — 🟡 Intermediate
Follow the "Build a Large Language Model (From Scratch)" path: implement attention, pretraining, then instruction fine-tuning. The most structured way in.
- **Skills proven:** attention, pretraining, SFT, end-to-end LLM assembly.
- 💻 [rasbt/LLMs-from-scratch](https://github.com/rasbt/LLMs-from-scratch) — 97.6k ⭐ · Apache-2.0 (code) · book companion repo.

### 5. Tiny Llama-architecture reproduction — 🔴 Advanced
Implement RoPE, RMSNorm, SwiGLU, and GQA — the modern-LLM building blocks GPT-2 lacks — and train a tiny model. Shows you know *current* architectures.
- **Skills proven:** RoPE, RMSNorm, SwiGLU, grouped-query attention.
- 💻 [jzhang38/TinyLlama](https://github.com/jzhang38/TinyLlama) — Apache-2.0 · tiny-scale Llama pretraining.

### 6. Attention mechanism visualizer — 🟡 Intermediate
Build an interactive tool that renders attention weights across heads/layers for a given input. Teaches internals *and* makes a great shareable artifact.
- **Skills proven:** attention internals, hooks, visualization, interpretability basics.
- 💻 [jessevig/bertviz](https://github.com/jessevig/bertviz) — Apache-2.0 · attention visualization reference.

### 7. KV-cache + speculative decoding from scratch — 🔴 Advanced
Implement a KV cache and a simple speculative-decoding loop, then measure the tokens/sec speedup. Bridges internals and serving performance.
- **Skills proven:** KV caching, speculative decoding, inference optimization.
- 📄 [Speculative Decoding](https://arxiv.org/abs/2211.17192) — the method.

---

← Back to the [full catalog](../README.md) · Want the **12 that actually land jobs**? See [projects-to-land-an-ai-job](https://github.com/landedjobs/projects-to-land-an-ai-job).
