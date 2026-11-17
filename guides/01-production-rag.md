# 🔎 Build guide: a production RAG assistant that knows its limits

> **Time:** 2-4 weeks · **Difficulty:** 🟡 Intermediate · **Catalog entry:** [RAG apps](../catalog/rag-apps.md)
> **Proves:** retrieval design, grounded answers with citations, abstention, access control, and the habit of measuring before you claim.

Most RAG portfolio projects are a chatbot over a PDF. Reviewers have seen hundreds. This guide builds the version that holds up in an interview: a support assistant that answers from a bounded set of documents, cites the passage it used, says "I don't know" when the documents don't cover the question, and never shows a user a document they aren't allowed to see. Then it proves each of those claims with numbers.

It mirrors what real hiring exercises ask for. Tucows' AI engineer take-home asks for a "production-minded" knowledge assistant with a RAG pipeline behind a REST API and an MCP tool, and weights RAG quality at 20% of the score, with API design, the MCP server and tests at 15% each ([📘 Tucows exercise](https://github.com/tucows/interview-exercise-ai)). So the plumbing around the model counts as much as the model.

---

## 1. Scope

**Build:** a support assistant for one product, over a corpus you control:

- A product manual (30-100 pages you write or adapt from an openly licensed source).
- A set of past support tickets or FAQ entries.
- Two user groups with different access, for example `customer` and `staff`, where some documents are staff-only.

**The assistant must:**

1. Answer questions with inline citations to the exact passage used.
2. Abstain ("The documentation doesn't cover this") when the answer isn't in the corpus.
3. Never retrieve or reveal a staff-only passage to a customer.
4. Expose the same capability through an API (and, as a stretch, an MCP tool).

**Leave out of the core:** web browsing, multi-agent setups, fine-tuning. They add surface area without making the core claims stronger.

## 2. Architecture

```
documents ─▶ parser ─▶ versioned chunks ─▶ ┌ keyword index (BM25)
                                            └ vector index
question + user ─▶ permission filter ─▶ retrieve (keyword | vector | hybrid) ─▶ optional reranker
                ─▶ answer generator ─▶ citation check ─▶ answer + cited passages ─▶ API / UI
```

Design rules that make it reviewable:

- **Filter by permission before retrieval, not in the prompt.** A prompt that says "don't reveal staff documents" is not access control. Put the user's group in the index query so forbidden chunks are never retrieved.
- **Return the retrieved passages with every answer.** Then your evals can tell a retrieval miss from a generation mistake.
- **Version the chunks.** Store a corpus version on every chunk and every eval run, so a re-index doesn't silently change your numbers.
- **Keep ingestion and query traces separate.** Ingestion cost is paid once per corpus version; query cost is paid every time. Report them apart.

## 3. Milestones

| Week | Ship | Done when |
|---|---|---|
| 1 | Corpus, labelled question set, keyword (BM25) baseline, one-command ingestion | `make ingest && make eval` runs end to end on the baseline |
| 2 | Vector retrieval, cited answers, a minimal UI or API, first held-out eval | You have a baseline-vs-vector table on held-out questions |
| 3 | Hybrid retrieval or a reranker, permission tests, unanswerable questions | Permission leakage is measured, not assumed |
| 4 | Re-index and deletion, a small load test, README, failure review | A stranger can run it and reproduce your table |

**Two weeks only?** Stop after week 2 with a clean baseline-vs-vector comparison. A finished small comparison beats an unfinished big system.

## 4. The question set

Your eval set is the most important file in the repo. Build it before tuning anything.

- **150-300 questions**, written by you, each with a reference answer and the ID of the passage(s) that support it.
- **Mix:** ordinary questions, hard ones (answer split across two passages, near-duplicate passages), **unanswerable** ones (about 15%), and **permission** ones (a customer asking about staff-only content).
- **Split:** a development set you tune on and a held-out set you only run at the end. If several questions come from the same passage, keep them in the same split so you're not testing on near-copies of your tuning data.

## 5. What to measure

LangSmith's RAG guide separates four questions that one "quality score" hides: is the answer correct against a reference, is it relevant to the question, is it grounded in the retrieved text, and did retrieval find the right text at all ([📘 LangSmith: evaluate a RAG app](https://docs.langchain.com/langsmith/evaluate-rag-tutorial)). Report them separately:

| Metric | How to compute | Baseline | Your system |
|---|---|---|---|
| Evidence recall@5 | Share of labelled supporting passages that appear in the top 5 retrieved, averaged over questions | | |
| Answer correctness | Answers that pass your reference-answer rubric ÷ all answerable questions | | |
| Citation support | Cited claims actually supported by the cited passage ÷ all cited claims | | |
| Abstention precision / recall | Correct abstentions ÷ all abstentions; correct abstentions ÷ unanswerable questions | | |
| Permission leakage | Staff-only facts shown to a customer ÷ permission tests (target: 0) | | |
| Latency | p50 and p95 end-to-end, measured at the client | | |
| Cost per correct answer | All query costs (including failed and retried calls) ÷ correct answers | | |

Notes that make the table credible:

- **Same held-out questions for every row.** Change one thing at a time.
- **Say how you graded.** Use code for exact checks (did the cited passage ID match?) and an LLM judge only for fuzzy ones, spot-checked by hand. Anthropic's eval guide warns that model graders are non-deterministic and need human calibration ([📘 Anthropic: demystifying evals for agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)).
- **Count the judge's cost separately** from the app's cost.
- **Report denominators** ("41 of 45 unanswerable questions"), not just percentages.

## 6. Cost and latency

Log, per query: retrieval time, reranker time, generation time, input and output tokens, and cache hits. Log, per corpus version: parsing and embedding time and cost, and index size. Use the provider's price list on the day you ran the experiment, and write that date down. Free credits are not zero cost; price what it would cost to run.

For latency definitions (end-to-end vs time to first token, and what queueing does to both), NVIDIA's benchmarking primer is a good reference ([📘 NVIDIA: LLM inference benchmarking](https://developer.nvidia.com/blog/llm-benchmarking-fundamental-concepts)).

## 7. Common mistakes

- Tuning and testing on the same questions, or on questions generated from the same chunks you tuned on.
- Grading fluency instead of correctness and grounding.
- Claiming access control because the prompt says so.
- Citations that exist but don't support the sentence they're attached to.
- Hiding ingestion cost, or comparing a cached system against an uncached baseline without saying so.
- A bigger corpus with no measurements instead of a smaller one with a clear table.

## 8. Stretch goals

- **Contextual retrieval:** prepend a short LLM-written context line to each chunk before embedding and report the recall change ([📄 Anthropic: contextual retrieval](https://www.anthropic.com/news/contextual-retrieval)).
- **Incremental updates:** add, edit and delete documents without a full re-index, with tests that deleted content stops appearing.
- **MCP tool:** expose `search_docs` and `answer` as an MCP server so an agent client can use the same pipeline.
- **Multi-tenant:** several customers' documents in one index with no cross-tenant leakage.

## 9. References

- 💻 [run-llama/llama_index](https://github.com/run-llama/llama_index) · 52.9k ⭐ · MIT · loaders, indexes and query engines; use it for plumbing, but your eval table is the evidence, not the framework.
- 💻 [vibrantlabsai/ragas](https://github.com/vibrantlabsai/ragas) · 16.4k ⭐ · Apache-2.0 · ready-made RAG metrics (faithfulness, context recall) to compare against your own.
- 💻 [langchain-ai/rag-from-scratch](https://github.com/langchain-ai/rag-from-scratch) · 10.1k ⭐ · ⚠️ no licence stated · notebooks that build each RAG step by hand; read and re-implement, don't copy.
- 📘 [LangSmith: evaluate a RAG application](https://docs.langchain.com/langsmith/evaluate-rag-tutorial) · the four separate RAG checks.
- 📘 [Tucows AI engineer interview exercise](https://github.com/tucows/interview-exercise-ai) · archived, still readable · a real take-home with its scoring rubric; the closest public picture of what reviewers grade.

## 10. Put it on your resume

Fill in your own numbers; these show the shape, not real results:

- *Built a cited support assistant over a 200-page product manual; raised held-out answer correctness from 68% to 84% (300 questions) by adding hybrid retrieval and a reranker, at $0.03 per query.*
- *Cut unsupported citations from 11% to 2% with a post-generation citation check; abstained correctly on 41 of 45 out-of-scope questions.*
- *Enforced document-level access control in retrieval; 0 staff-only passages shown across 60 permission tests.*

Next: write the README with the [project README template](../templates/project-readme.md), and see [presenting your project](../templates/presenting-your-project.md) for the interview.

---

<sub>Sources checked 17 Nov 2026. Star counts are a snapshot. Found something out of date? [Open an issue](https://github.com/landedjobs/ai-engineer-portfolio-projects/issues).</sub>
