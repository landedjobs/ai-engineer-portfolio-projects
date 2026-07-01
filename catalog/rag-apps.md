# 🔎 RAG Apps

> Retrieval-Augmented Generation is the **single highest-signal theme** on this list. "One production RAG project with proper evals beats five tutorial projects." Ship at least one from this file with a live URL, an eval table, and citations in the output.

**Skills legend:** `chunking` · `hybrid search (BM25 + dense)` · `re-ranking` · `retrieval evals (recall@k, MRR, nDCG, faithfulness)` · `vector DBs (pgvector/Chroma/Weaviate/Qdrant)` · `citations & grounding`
**Difficulty:** 🟢 Beginner · 🟡 Intermediate · 🔴 Advanced
**Link types:** 📄 paper · 📘 docs · 🎬 video · 🧑‍🏫 course · 🛠️ tool · 💻 repo

> [!TIP]
> The 90-second scan interviewers run: is there a **live URL**, an **eval table with numbers**, **citations** in the answers, and **cost/latency** figures? Build for that scan.

---

### 1. Document Q&A RAG (PDF / Slack / Notion) — 🟢 Beginner
Ingest a corpus (your own PDFs, a Slack export, a Notion workspace), chunk + embed, retrieve, and answer with inline citations. The canonical first RAG project — make it *hybrid* (BM25 + dense) to stand out from the tutorial crowd.
- **Skills proven:** chunking strategy, embeddings, top-k retrieval, prompt grounding, citations.
- 💻 [NirDiamant/RAG_Techniques](https://github.com/NirDiamant/RAG_Techniques) — 28.2k ⭐ · custom **non-commercial** license (re-implement, don't redistribute the notebooks). The single best techniques reference.
- 💻 [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) — 116k ⭐ · Apache-2.0 · dozens of runnable RAG starters.

### 2. Contextual-chunk-headers RAG (Anthropic Contextual Retrieval) — 🟡 Intermediate
Prepend an LLM-generated context sentence to each chunk before embedding so retrieval survives fragmentation. Report recall@k before/after — a clean, quantified win.
- **Skills proven:** contextual chunking, embedding-cost tradeoffs, retrieval eval deltas.
- 📄 [Anthropic — Contextual Retrieval](https://www.anthropic.com/news/contextual-retrieval) — the method + benchmark numbers to reproduce.
- 💻 [NirDiamant/RAG_Techniques](https://github.com/NirDiamant/RAG_Techniques) — 28.2k ⭐ · non-commercial · CHC notebook.

### 3. Query-rewriting RAG (HyDE / HyPE / multi-query) — 🟡 Intermediate
Rewrite or hypothetically-expand the user query before retrieval. Compare raw-query vs HyDE vs multi-query on the same eval set.
- **Skills proven:** query transformation, retrieval ablation, HyDE/HyPE.
- 📄 [HyDE — Precise Zero-Shot Dense Retrieval](https://arxiv.org/abs/2212.10496) — the original paper.
- 💻 [NirDiamant/RAG_Techniques](https://github.com/NirDiamant/RAG_Techniques) — 28.2k ⭐ · non-commercial · query-transform notebooks.

### 4. Agentic RAG (retrieve-decide-retrieve loop) — 🔴 Advanced
An agent that decides *whether* and *what* to retrieve, iterates on insufficient context, and knows when to stop. Pairs a router/planner with the retriever.
- **Skills proven:** tool-use routing, iterative retrieval, stopping criteria, EmbeddingGemma / local embeddings.
- 💻 [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) — 116k ⭐ · Apache-2.0 · agentic-RAG examples.
- 📘 [LangGraph docs — Agentic RAG](https://langchain-ai.github.io/langgraph/tutorials/rag/langgraph_agentic_rag/) — reference architecture.

### 5. GraphRAG (entity graph + community summaries) — 🔴 Advanced
Extract entities/relations into a graph, summarize communities, and answer global "sensemaking" questions a vanilla vector store can't. High-effort, high-differentiation.
- **Skills proven:** graph extraction, community detection, hierarchical summarization, Neo4j/networkx.
- 📄 [Microsoft — From Local to Global (GraphRAG)](https://arxiv.org/abs/2404.16130) — the paper.
- 💻 [microsoft/graphrag](https://github.com/microsoft/graphrag) — MIT · reference implementation.

### 6. Corrective / trustworthy RAG with hallucination checker — 🟡 Intermediate
Grade retrieved docs for relevance, fall back to web search when the corpus is thin, and verify each claim against sources before answering (CRAG / self-RAG).
- **Skills proven:** relevance grading, fallback routing, claim verification, guardrails.
- 📄 [Corrective RAG (CRAG)](https://arxiv.org/abs/2401.15884) — the method.
- 💻 [patchy631/ai-engineering-hub](https://github.com/patchy631/ai-engineering-hub) — 36.2k ⭐ · MIT · corrective-RAG walkthroughs.

### 7. Text-to-SQL RAG over a real database — 🟡 Intermediate
Retrieve schema + few-shot examples, generate SQL, execute, and self-correct on error. Ground answers in query results, not the model's memory.
- **Skills proven:** schema retrieval, SQL generation, execution-feedback loops, safety (read-only).
- 📘 [LangChain — SQL Q&A tutorial](https://python.langchain.com/docs/tutorials/sql_qa/) — end-to-end reference.
- 💻 [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) — 116k ⭐ · Apache-2.0 · text-to-SQL app.

### 8. Multi-modal RAG over PDFs + images + tables (ColPali / ColQwen) — 🔴 Advanced
Retrieve over page *images* with a vision retriever (ColPali) instead of brittle OCR + text chunks. Answers questions about charts and layout-heavy docs.
- **Skills proven:** visual document retrieval, late-interaction embeddings, VLM answering.
- 📄 [ColPali — Efficient Document Retrieval with Vision LMs](https://arxiv.org/abs/2407.01449) — the paper.
- 💻 [illuin-tech/colpali](https://github.com/illuin-tech/colpali) — MIT · reference code + weights.

### 9. Long-context RAG with a chunking-strategy benchmark — 🟡 Intermediate
Hold everything constant and vary only chunk size / overlap / splitter; publish a table of faithfulness + recall per config. Shows evaluation discipline more than novelty.
- **Skills proven:** controlled ablation, chunking, eval harness design.
- 💻 [NirDiamant/RAG_Techniques](https://github.com/NirDiamant/RAG_Techniques) — 28.2k ⭐ · non-commercial · chunking notebooks.
- 📘 [Chroma — chunking research](https://research.trychroma.com/evaluating-chunking) — empirical chunking study to reproduce.

### 10. Production NotebookLM clone (source-grounded study assistant) — 🔴 Advanced
Upload sources, chat grounded to them with citations, and generate an audio/podcast overview. A full product, not a demo — deploy it.
- **Skills proven:** multi-doc ingestion, grounded chat, citation UX, optional TTS overview.
- 💻 [patchy631/ai-engineering-hub](https://github.com/patchy631/ai-engineering-hub) — 36.2k ⭐ · MIT · NotebookLM-style builds.
- 📘 [Google NotebookLM](https://notebooklm.google/) — the product to benchmark against.

### 11. Re-ranking pipeline (cross-encoder / Cohere / bge-reranker) — 🟡 Intermediate
Add a re-ranker after first-stage retrieval and quantify the nDCG / MRR lift. The cheapest high-impact upgrade to any RAG stack — a great standalone study.
- **Skills proven:** two-stage retrieval, cross-encoders, ranking metrics.
- 🛠️ [bge-reranker (FlagEmbedding)](https://github.com/FlagOpen/FlagEmbedding) — MIT · open re-ranker models.
- 📘 [Pinecone — rerankers guide](https://www.pinecone.io/learn/series/rag/rerankers/) — concepts + code.

### 12. Hybrid search from scratch (BM25 + dense + RRF fusion) — 🟡 Intermediate
Implement lexical + semantic retrieval and fuse with Reciprocal Rank Fusion; show where each wins (acronyms/IDs vs paraphrase). The technique interviewers most expect.
- **Skills proven:** BM25, dense retrieval, RRF, retrieval evaluation.
- 📘 [Weaviate — hybrid search](https://weaviate.io/blog/hybrid-search-explained) — concepts + fusion.
- 💻 [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) — 116k ⭐ · Apache-2.0 · hybrid-search examples.

---

← Back to the [full catalog](../README.md) · Want the **12 that actually land jobs**? See [projects-to-land-an-ai-job](https://github.com/landedjobs/projects-to-land-an-ai-job).
