# ⚙️ Production & Serving

> This theme is where "I built a demo" becomes "I can run this in production." Quantization, throughput, p50/p95 latency, and $/1k-tokens are the vocabulary of infra maturity — put the numbers in your README.

**Skills legend:** `serving (vLLM / llama.cpp)` · `quantization` · `throughput & latency` · `streaming (SSE)` · `containers / K8s` · `cost per token`
**Difficulty:** 🟢 Beginner · 🟡 Intermediate · 🔴 Advanced
**Link types:** 📄 paper · 📘 docs · 🎬 video · 🧑‍🏫 course · 🛠️ tool · 💻 repo

---

### 1. Self-hosted OpenAI-compatible API with vLLM — 🟡 Intermediate
Serve an open model behind an OpenAI-compatible endpoint and benchmark throughput vs a hosted API. Shows you can run inference, not just call it.
- **Skills proven:** paged-attention serving, batching, throughput benchmarking.
- 💻 [vllm-project/vllm](https://github.com/vllm-project/vllm) — 84.9k ⭐ · Apache-2.0 · high-throughput serving.

### 2. Local / edge deployment with llama.cpp (GGUF) — 🟡 Intermediate
Run a quantized GGUF model on a laptop/Mac/Raspberry Pi and report tokens/sec at each quant level. Great "runs anywhere" story.
- **Skills proven:** GGUF quantization, CPU/Metal inference, quality-vs-size tradeoffs.
- 💻 [ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp) — 119k ⭐ · MIT · edge/local inference.

### 3. Quantized inference on Apple Silicon (MLX) — 🟡 Intermediate
Run and benchmark quantized models with MLX on an M-series Mac. A clean, self-contained perf study on hardware you already own.
- **Skills proven:** MLX, Apple-Silicon inference, quantization benchmarking.
- 💻 [ml-explore/mlx-examples](https://github.com/ml-explore/mlx-examples) — MIT · MLX inference examples.

### 4. FastAPI chat backend with SSE streaming — 🟡 Intermediate
A production-shaped API: streaming responses, auth, rate limits, request logging, and graceful errors. The backend every AI product needs.
- **Skills proven:** SSE streaming, auth, rate limiting, structured logging.
- 📘 [FastAPI — streaming responses](https://fastapi.tiangolo.com/advanced/custom-response/#streamingresponse) — docs.

### 5. One-click deploy of a RAG + LLM app (Modal / Railway) — 🟡 Intermediate
Take any project from this catalog and give it a real live URL with autoscaling and secrets management. **Reviewers rarely clone — a live URL is worth ten READMEs.**
- **Skills proven:** serverless GPU deploy, secrets, autoscaling, live-URL delivery.
- 📘 [Modal — LLM deployment docs](https://modal.com/docs/examples/vllm_inference) — reference.

### 6. vLLM production stack on Kubernetes — 🔴 Advanced
Deploy vLLM with request routing, autoscaling, and observability on K8s. The most "senior infra" project on the list.
- **Skills proven:** K8s, autoscaling, load balancing, GPU scheduling, monitoring.
- 💻 [vllm-project/production-stack](https://github.com/vllm-project/production-stack) — Apache-2.0 · reference K8s stack.

### 7. Semantic caching + cost-control gateway — 🟡 Intermediate
A proxy in front of LLM calls that does semantic caching, rate limiting, fallback routing, and budget caps — measure the cache hit-rate and $ saved.
- **Skills proven:** semantic cache, gateway routing, budget enforcement, fallbacks.
- 💻 [BerriAI/litellm](https://github.com/BerriAI/litellm) — MIT · LLM gateway/proxy.

### 8. Batch inference pipeline for large jobs — 🟢 Beginner
Process a large dataset through an LLM efficiently: batching, checkpointing, resumability, and a cost estimate up front. Unglamorous but very real.
- **Skills proven:** batching, checkpoint/resume, throughput, cost estimation.
- 💻 [vllm-project/vllm](https://github.com/vllm-project/vllm) — 84.9k ⭐ · Apache-2.0 · offline batched inference.

---

← Back to the [full catalog](../README.md) · Want the **12 that actually land jobs**? See [projects-to-land-an-ai-job](https://github.com/landedjobs/projects-to-land-an-ai-job).
