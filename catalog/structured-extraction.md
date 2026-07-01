# 🧩 Structured Extraction

> Turning messy text into **validated, typed data** is the most quietly employable skill on this list — every enterprise AI feature depends on it. Pydantic/Zod schemas + retries + confidence scores read as pragmatic, anti-hallucination engineering.

**Skills legend:** `Pydantic / Zod schemas` · `function-calling` · `validation + retry` · `confidence scoring` · `constrained decoding` · `entity resolution`
**Difficulty:** 🟢 Beginner · 🟡 Intermediate · 🔴 Advanced
**Link types:** 📄 paper · 📘 docs · 🎬 video · 🧑‍🏫 course · 🛠️ tool · 💻 repo

---

### 1. Resume parser with confidence scores — 🟢 Beginner
Parse resumes into a strict schema (skills, roles, dates) with per-field confidence and a retry loop on validation failure. The perfect first structured-extraction project.
- **Skills proven:** Pydantic schemas, validation, confidence, retry.
- 💻 [567-labs/instructor](https://github.com/567-labs/instructor) — MIT · Pydantic-validated LLM outputs.
- 💻 [dottxt-ai/outlines](https://github.com/dottxt-ai/outlines) — 14.1k ⭐ · Apache-2.0 · constrained/structured generation.

### 2. Invoice extractor with retry + human-in-the-loop — 🟡 Intermediate
Extract line items, totals, and tax with validation; route low-confidence fields to a review queue. Mirrors a real enterprise workflow.
- **Skills proven:** nested schemas, confidence thresholds, review UX.
- 📘 [instructor — retries & validation](https://python.useinstructor.com/concepts/retrying/) — docs.

### 3. Entity extraction + disambiguation over news — 🟡 Intermediate
Pull people/orgs/places and resolve them to canonical IDs (e.g. Wikidata), handling "Apple the company vs the fruit." Extraction + entity resolution.
- **Skills proven:** NER, entity linking, disambiguation, canonicalization.
- 📘 [Outlines — structured generation docs](https://dottxt-ai.github.io/outlines/) — constrained decoding.

### 4. JSON-schema-locked agent output — 🟡 Intermediate
Force an agent's every response into a validated schema (tool calls, plans, final answers) so downstream code never parses free text. Reliability upgrade for any agent project.
- **Skills proven:** constrained decoding, schema-first design, function-calling.
- 💻 [dottxt-ai/outlines](https://github.com/dottxt-ai/outlines) — 14.1k ⭐ · Apache-2.0.
- 💻 [567-labs/instructor](https://github.com/567-labs/instructor) — MIT.

### 5. Text → knowledge-graph triples → Neo4j — 🔴 Advanced
Extract (subject, relation, object) triples and load them into a graph DB you can query — the ingestion half of a GraphRAG system.
- **Skills proven:** relation extraction, triple stores, graph loading, Cypher.
- 📄 [Microsoft GraphRAG paper](https://arxiv.org/abs/2404.16130) — graph-extraction reference.
- 🛠️ [Neo4j — LLM knowledge graph builder](https://neo4j.com/labs/genai-ecosystem/llm-graph-builder/) — reference tool.

### 6. Classification with taxonomy + calibrated confidence — 🟢 Beginner
Multi-label classify text against a taxonomy and calibrate the confidence so thresholds are meaningful. Small, sharp, and evaluable.
- **Skills proven:** multi-label classification, confidence calibration, thresholding.
- 💻 [567-labs/instructor](https://github.com/567-labs/instructor) — MIT · enum/classification patterns.

### 7. Web-page → structured records scraper — 🟡 Intermediate
Given a URL, extract clean typed records (products, jobs, papers) that validate against a schema, with graceful handling of missing fields. Practically useful and portfolio-ready.
- **Skills proven:** extraction-from-HTML, schema design, missing-data handling.
- 💻 [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) — 116k ⭐ · Apache-2.0 · scraping/extraction apps.

---

← Back to the [full catalog](../README.md) · Want the **12 that actually land jobs**? See [projects-to-land-an-ai-job](https://github.com/landedjobs/projects-to-land-an-ai-job).
