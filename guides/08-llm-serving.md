# ⚙️ Build guide: LLM serving tuned for useful throughput under a latency limit

> **Time:** 2-4 weeks · **Difficulty:** 🔴 Advanced · **Catalog entry:** [Production & serving](../catalog/production-serving.md)
> **Proves:** load testing, latency percentiles, controlled optimization, quality checks under load, and a serving cost you can defend.

The usual serving project is "I ran vLLM and got N tokens per second". That number says little: it was probably measured at saturation, with one output length, and with no latency limit or quality check. This guide builds the version an infra reviewer respects: one open model behind a gateway, a fixed and reproducible workload, a declared latency limit, and a table showing how much correct work each configuration delivers inside that limit, and at what cost.

The engineering trade-off is simple to state. More concurrency raises throughput but also raises latency. NVIDIA's cost-benchmarking guide rules out every configuration that misses the latency constraint, then picks the highest-throughput one that remains ([📘 NVIDIA: how much does your LLM inference cost?](https://developer.nvidia.com/blog/benchmarking-llm-inference-costs-for-smarter-scaling-and-deployment)). Reproduce that choice with your own workload.

---

## 1. Scope

**Build:** a serving stack for one open-weight model sized to your hardware, for example a 1-4B model on one GPU.

- A **gateway** in front of the inference server with timeouts, a request quota and streaming.
- A **fixed workload**: real or realistic prompts with a known input and output length distribution, and a seed.
- A **quality check**: 100-200 task cases with a scorer, run at each configuration, so a faster but worse setup can't win.
- A **declared latency limit** before you start, for example p95 time to first token under 500 ms and p95 end-to-end under 4 s.

**Then test one optimization at a time:** batch and concurrency limits, prefix caching, chunked prefill, quantization, or routing short requests to a smaller model.

**Leave out of the core:** Kubernetes, multi-node serving, custom kernels. One machine and a clean experiment beats a cluster with no numbers.

## 2. Architecture

```
workload (seeded prompts) ─▶ load generator ─▶ gateway (quotas, timeouts, streaming) ─▶ inference server ─▶ model / GPU
                                  │                                │                            │
                          client timestamps              request + queue logs            token + GPU metrics
                                  └──────────────▶ results store ◀─┴────────────────────────────┘ ─▶ report
quality cases ─▶ same gateway ─▶ scorer ─▶ quality per configuration
```

Design rules:

- **Measure at the client.** Server-side timings miss network and gateway time. Users feel client-side latency.
- **Pin the setup.** Store model revision, server version, launch flags, GPU model and count, and the workload seed with every result.
- **Reset caches between runs.** vLLM's benchmark docs warn that repeating a run against the same server can reuse prompts left in the prefix cache and inflate throughput ([📘 vLLM benchmark CLI](https://docs.vllm.ai/en/latest/benchmarking/cli.html)).
- **Change one thing per run,** and keep the output length limits identical across runs.
- **Code licence is not model licence.** vLLM is Apache-2.0; the weights you serve have their own licence. [Qwen3-4B-Instruct-2507](https://huggingface.co/Qwen/Qwen3-4B-Instruct-2507) is Apache-2.0, which keeps this simple.

## 3. Milestones

| Week | Ship | Done when |
|---|---|---|
| 1 | Pinned server, gateway, seeded workload, quality cases and scorer | One command starts the stack and runs a small load test plus the quality check |
| 2 | Load sweep across arrival rates or concurrency, client-side latency report | You can name the highest rate that meets your latency limit for the baseline |
| 3 | One optimization at a time, each with a quality check | You have a baseline-vs-optimized table with quality regressions shown |
| 4 | Routing or fallback, a cost model, a reproducibility guide | A stranger can rerun the sweep and get your operating point |

**Two weeks only?** Stop after week 2 with a clean load sweep and a stated operating point.

## 4. The workload

The workload is your test set. A benchmark on random 128-token prompts tells you little about a real product.

- **Use realistic lengths.** Record the input and output token distribution you test with, and publish it.
- **Mix request types** if your product has them: short chat turns, long-context requests, requests with a shared system prompt (where prefix caching helps).
- **Fix the arrival pattern.** A steady rate, a burst, and a ramp. Seed everything so a rerun sends the same requests.
- **Warm up, then measure.** Say whether warm-up and cold starts are in or out of the numbers.

## 5. What to measure

NVIDIA's benchmarking primer separates time to first token, end-to-end latency, intertoken latency and system throughput, and notes that time to first token includes queueing, prefill and network time ([📘 NVIDIA: LLM inference benchmarking](https://developer.nvidia.com/blog/llm-benchmarking-fundamental-concepts)). Tools compute these differently, so write down your formulas:

| Metric | How to compute | Baseline | Your system |
|---|---|---|---|
| Output throughput | Total generated output tokens ÷ measurement-window seconds | | |
| Goodput | Requests that pass the quality check and meet the latency limit ÷ measurement-window seconds | | |
| Time to first token | First streamed token time − request send time; p50 / p95 | | |
| Time per output token | (last token time − first token time) ÷ (output tokens − 1), for responses over one token; report the distribution | | |
| Limit violations and errors | Requests over the latency limit or ending in error ÷ attempted requests | | |
| Cost per useful request | Billed serving cost for the window ÷ requests counted in goodput | | |

Notes that make the table credible:

- **Saturation is not an operating point.** Report the best throughput *within* the limit, not the peak.
- **Normalize per GPU** when configurations use different GPU counts, as the NVIDIA cost guide recommends.
- **Report denominators** ("1,812 of 1,900 requests within limit") and the arrival rate for each row.
- **Quantization needs a quality row.** A faster model that fails more task cases has not improved goodput.

## 6. Cost and latency

Price the GPU by billed hour on the day you ran the test, and include idle time: a GPU that waits for traffic still costs money. Log queue time, cold starts, network time, cache-hit rate, precision, and failed requests. Report cost per useful request and cost per million output tokens side by side.

You don't need a big budget. Develop on a free notebook GPU or a laptop, then run the final sweeps on one rented GPU for a few hours so the numbers come from stable hardware.

## 7. Common mistakes

- Comparing configurations with different output lengths.
- Reporting server-side time as user latency.
- Treating saturation throughput as a usable operating point.
- Rerunning against a warm prefix cache and calling it a speedup.
- Leaving idle GPU time out of the cost.
- Claiming quantization gains without a task-quality check.

## 8. Stretch goals

- **Local or remote routing:** a gateway that sends easy requests to the small local model and hard ones to a stronger hosted model, with the fallback cost and quality measured.
- **Compare engines:** run the same workload on vLLM and SGLang with matched settings.
- **Autoscaling simulation:** replay a day of traffic and show how many replicas the latency limit needs per hour.
- **Prefill and decode on separate GPUs:** read DistServe ([📄 arXiv](https://arxiv.org/abs/2401.09670)), which targets the request rate served within both TTFT and time-per-token limits on each GPU, and test whether the idea helps your workload.

## 9. References

- 💻 [vllm-project/vllm](https://github.com/vllm-project/vllm) · 97.1k ⭐ · Apache-2.0 · serving engine with PagedAttention, continuous batching, chunked prefill, prefix caching and an OpenAI-compatible server.
- 💻 [sgl-project/sglang](https://github.com/sgl-project/sglang) · 39.0k ⭐ · Apache-2.0 · a second serving engine for the engine-comparison stretch goal.
- 💻 [vllm-project/guidellm](https://github.com/vllm-project/guidellm) · 1.8k ⭐ · Apache-2.0 · SLO-aware load testing with rate sweeps and TTFT / intertoken latency distributions.
- 📘 [vLLM benchmark CLI](https://docs.vllm.ai/en/latest/benchmarking/cli.html) · `vllm bench serve`, client-side latency definitions and the prefix-cache warning.
- 📘 [NVIDIA: LLM inference benchmarking, fundamental concepts](https://developer.nvidia.com/blog/llm-benchmarking-fundamental-concepts) · metric definitions and where they differ across tools.
- 📘 [NVIDIA: how much does your LLM inference cost?](https://developer.nvidia.com/blog/benchmarking-llm-inference-costs-for-smarter-scaling-and-deployment) · choosing a configuration under a latency constraint, per-GPU normalization.
- 📄 [PagedAttention (vLLM paper)](https://arxiv.org/abs/2309.06180) · why KV-cache memory management limits batch size.

## 10. Put it on your resume

Fill in your own numbers; these show the shape, not real results:

- *Served a 4B open model behind a gateway on one GPU; raised goodput from 6 to 9 requests/s on a fixed workload while keeping p95 time to first token under a declared 400 ms limit.*
- *Enabled prefix caching for a shared system prompt; cut p95 time to first token by 35% with no change in task accuracy across 200 quality cases.*
- *Built a cost model including idle GPU time; showed 8-bit quantization lowered cost per useful request by 22% for a 1-point accuracy drop, and documented the trade-off.*

Next: write the README with the [project README template](../templates/project-readme.md), and see [presenting your project](../templates/presenting-your-project.md) for the interview.

---

<sub>Sources checked 17 Nov 2026. Star counts are a snapshot. Found something out of date? [Open an issue](https://github.com/landedjobs/ai-engineer-portfolio-projects/issues).</sub>
