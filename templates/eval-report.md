# Eval report template

The results table in your README is the summary. This file is the evidence behind it: what you tested, how you graded, and what you found. Put it in your project as `EVALS.md`.

A reviewer should be able to answer three questions from it without trusting your prose: **what counts as success, what improved, and what that improvement cost.**

---

```markdown
# Evaluation

Run date: YYYY-MM-DD · Commit: <sha> · Corpus / data version: <v>

## What success means

<One paragraph per metric: the exact rule for pass and fail.
Example: "Correct = the answer contains every fact in the reference answer and no contradicting fact.">

## Test set

| Split | Cases | How made | Used for |
|---|---|---|---|
| Development | | | tuning prompts and settings |
| Held-out | | | final numbers only |

Case types: <ordinary / hard / unanswerable / adversarial / failure-injected, with counts>.
Cases that share a source document or template are kept in the same split.

## Graders

| Check | Graded by | Calibration |
|---|---|---|
| <exact match, schema, database state> | code | n/a |
| <correctness, groundedness> | LLM judge (<model>) | <n> cases also graded by hand; agreement <x%> |

## Results (held-out)

| System | <metric 1> | <metric 2> | p50 / p95 latency | Cost per success | Runs |
|---|---|---|---|---|---|
| Baseline | | | | | |
| Variant A | | | | | |
| Final | | | | | |

- Every row uses the same held-out cases.
- Numbers are "passed / total" first, then the percentage.
- Repeated runs: <report the spread, or pass^k for agents>.

## Cost

| Item | Units | Price (date) | Total |
|---|---|---|---|
| Model input / output tokens | | | |
| Embeddings / tools / other APIs | | | |
| Judge (eval only) | | | |

## Failure review

<The 5-10 most instructive failures: input, what happened, why, and whether you fixed it.>

## What I would test next

<Gaps in this eval you know about.>
```

---

**How to compute the common metrics**

| Metric | Formula | Watch out for |
|---|---|---|
| Success rate | passed ÷ eligible attempts | Count timeouts and errors as failures |
| pass^k (consistency) | tasks passing **all** k runs ÷ tasks | Not the same as pass@k (any of k) |
| Precision / recall / F1 | TP ÷ (TP+FP) · TP ÷ (TP+FN) · 2PR ÷ (P+R) | Say how abstentions are counted |
| recall@k (retrieval) | relevant items in top k ÷ all relevant items | Freeze the relevance labels before tuning |
| Latency | client-observed finish − request time; p50 and p95 | Report failed requests separately |
| Cost per success | total cost of all attempts ÷ successes | Include retries; keep judge cost separate |

Further reading: [📘 Anthropic: demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents) · [📘 LangSmith: evaluate a RAG app](https://docs.langchain.com/langsmith/evaluate-rag-tutorial) · [📘 NVIDIA: LLM inference benchmarking](https://developer.nvidia.com/blog/llm-benchmarking-fundamental-concepts)

See also: [project README template](project-readme.md) · [presenting your project](presenting-your-project.md)
