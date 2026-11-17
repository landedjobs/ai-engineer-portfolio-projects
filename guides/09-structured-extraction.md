# 🧩 Build guide: structured extraction where valid JSON is only the start

> **Time:** 2-4 weeks · **Difficulty:** 🟡 Intermediate · **Catalog entry:** [Structured extraction](../catalog/structured-extraction.md)
> **Proves:** schema design, field-level evaluation, source evidence for every value, null handling, and a review queue that knows when the model is unsure.

Most extraction projects stop when the model returns JSON that parses. That is the easy part. A schema can't tell you that the amount is the subtotal instead of the total, that the currency is wrong, or that a line item belongs to a different invoice. This guide builds an invoice extractor that ties every value to the place on the page it came from, refuses to invent missing fields, and sends uncertain documents to a person. Then it proves how often the auto-accepted documents are fully correct.

OpenAI's own Structured Outputs guide makes the point: the feature keeps the model's output inside your JSON Schema, but your application must still handle refusals, incomplete responses and API errors, and must check any business rules the schema can't express ([📘 OpenAI: Structured Outputs](https://developers.openai.com/api/docs/guides/structured-outputs)). Those business rules are where this project lives.

---

## 1. Scope

**Build:** an extractor for one fixed document type, invoices or purchase orders, over documents you generate yourself:

- A synthetic generator that renders invoices from several layout templates, with known ground truth for every field.
- Noisy variants: scanned or photographed pages, skewed text, low contrast, a second page of line items.
- Hard cases on purpose: missing fields, totals that don't add up, two dates that look alike, unfamiliar layouts.

**The extractor must:**

1. Fill a fixed schema (vendor, invoice number, dates, currency, line items, tax, total).
2. Attach source evidence to every non-null value: a text span, page number and bounding box.
3. Return `null` for a field that isn't on the page, never a guess that happens to fit the type.
4. Route a document to a review queue when validation fails or evidence is weak, and export accepted records as versioned JSON.

**Leave out of the core:** many document types, payment decisions, fine-tuning. One document type measured well beats five measured loosely.

## 2. Architecture

```
document ─▶ OCR / layout parser ─▶ pages, text blocks, boxes
        ─▶ schema-constrained extraction ─▶ type + business-rule validation ─▶ ┌ pass ─▶ provenance check ─▶ auto-accept
                                                     │                         └ fail ─▶ retry (bounded) ─▶ review queue
                                                     └──────▶ event log ─▶ versioned JSON export
```

Design rules that make it reviewable:

- **Separate parsing from extraction.** Store the parser's output (text, boxes, tables) on its own, so you can tell an OCR miss from an extraction mistake.
- **Business rules in code, not in the prompt.** Line items sum to the subtotal, subtotal plus tax equals the total, the due date is on or after the issue date. A failed rule is a signal, not something to hide with a retry.
- **Evidence or null.** A value with no matching span on the page is flagged as unsupported, even when it is the right type.
- **Count every retry.** Retries can raise accuracy; they also cost money and time. Log each one.

Instructor gives you Pydantic-validated output with automatic retries on validation errors, and Docling gives you layout, reading order, tables and OCR for scanned pages. They are good building blocks; neither tells you whether your extracted amounts are right.

## 3. Milestones

| Week | Ship | Done when |
|---|---|---|
| 1 | Schema, synthetic generator, labels, a regex or single-prompt baseline | `make generate && make eval` produces a baseline field table |
| 2 | Layout parsing, validated extraction, source evidence per field | You have a baseline-vs-pipeline table on held-out layouts |
| 3 | Unseen-layout tests, null handling, line-item matching, the review queue | Unsupported fields and auto-accept precision are measured, not assumed |
| 4 | Throughput and cost benchmark, audit trail, export, README | A stranger can regenerate the data and reproduce your table |

**Two weeks only?** Stop after week 2 with clean field-level numbers and evidence on every value. A narrow, honest table beats a review UI with no measurements.

## 4. The document set

Your labelled documents are the most important part of the repo. Build them before tuning prompts.

- **300-500 documents**, each with ground truth for every field and the box where each value appears.
- **Mix:** clean digital PDFs, noisy scans, missing fields (the right answer is `null`), inconsistent totals (the right move is review), and multi-page line items.
- **Split by layout template, not at random.** If the same template appears in tuning and testing, you are testing memory of the layout. Hold out whole templates you never look at while tuning.
- **Freeze your matching rules** before the first run: how dates and amounts are normalised, and how predicted line items are paired with labelled ones.

## 5. What to measure

| Metric | How to compute | Baseline | Your system |
|---|---|---|---|
| Schema validity | Outputs that validate against the schema ÷ attempts | | |
| Field micro-F1 | Pool true positives, false positives and false negatives across all labelled fields; F1 = 2PR / (P + R) | | |
| Exact document accuracy | Documents with every required field correct ÷ documents | | |
| Unsupported-field rate | Non-null values with no matching source span ÷ non-null values | | |
| Auto-accept precision | Fully correct auto-accepted documents ÷ auto-accepted documents | | |
| Auto-accept coverage | Auto-accepted documents ÷ all documents (report next to precision) | | |
| Latency and cost | p50 / p95 per document and by page count; total cost ÷ correct documents | | |

Notes that make the table credible:

- **Schema validity is not accuracy.** Report it, but expect it near 100% with constrained output, and don't let it carry the story.
- **Precision and coverage go together.** Auto-accepting everything gives full coverage and poor precision. Show the trade-off at two or three thresholds.
- **Model confidence is not a probability** until you check it. If you use a confidence score to route documents, show how often high-confidence documents are actually correct.
- **Grade with code.** Field matches, sums and spans are exact checks. Keep an LLM judge out of this project unless a field is truly free text, and spot-check it by hand ([📘 Anthropic: demystifying evals for agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)).
- **Report denominators** ("388 of 400 documents"), not just percentages.

## 6. Cost and latency

Log, per document: OCR and layout time, extraction time, each validation retry, and input and output tokens. Report cost per page and per document separately, because a 6-page invoice is not a 1-page one. Add review minutes per document sent to the queue; that human time is part of the system's cost. Use the provider's price list on the day you ran the experiment and write the date down.

## 7. Common mistakes

- Treating valid JSON as correct extraction.
- Letting the same layout template leak into tuning and testing.
- Fuzzy matching that counts a wrong amount as close enough.
- Ignoring line-item alignment, so a swapped quantity and price still scores.
- Losing the source span, so nobody can check a value without re-reading the document.
- Calling a model's self-reported confidence calibrated without testing it.

## 8. Stretch goals

- **Three-way match:** reconcile each invoice against a purchase order and a receipt, and output a discrepancy report for a person to review, not a payment decision.
- **New layout in an hour:** show how long it takes to support an unseen template, and what accuracy it reaches before and after.
- **Constrained decoding with an open model:** run the same schema through Outlines on a local model and compare field F1 and cost.
- **Review UI:** show each flagged field with its box drawn on the page image, and log how long each review takes.

## 9. References

- 💻 [567-labs/instructor](https://github.com/567-labs/instructor) · 14.1k ⭐ · MIT · Pydantic-validated structured output with automatic retries on validation failure.
- 💻 [docling-project/docling](https://github.com/docling-project/docling) · 70.7k ⭐ · MIT · document parsing with layout, reading order, tables and OCR; its README says to check each model's own licence separately.
- 💻 [dottxt-ai/outlines](https://github.com/dottxt-ai/outlines) · 16.0k ⭐ · Apache-2.0 · constrained generation for open models, for the stretch goal.
- 📘 [OpenAI: Structured Outputs](https://developers.openai.com/api/docs/guides/structured-outputs) · what schema adherence covers and what your code still has to check.
- 📘 [Instructor: retrying](https://python.useinstructor.com/concepts/retrying/) · how validation retries work, so you can count them.
- 📘 [Docling documentation](https://docling-project.github.io/docling/) · formats, OCR options and the document representation you'll store.

## 10. Put it on your resume

Fill in your own numbers; these show the shape, not real results. Say the documents are synthetic:

- *Built an invoice extraction pipeline with source evidence for every field; raised field micro-F1 from 0.89 to 0.96 on 400 synthetic documents from held-out layouts.*
- *Added business-rule validation and a review queue; reached 97% auto-accept precision at 70% coverage, with every accepted value linked to a page and box.*
- *Cut unsupported (invented) field values from 6% to under 1% by returning null when no source span matched, at $0.004 per page.*

Next: write the README with the [project README template](../templates/project-readme.md), and see [presenting your project](../templates/presenting-your-project.md) for the interview.

---

<sub>Sources checked 17 Nov 2026. Star counts are a snapshot. Found something out of date? [Open an issue](https://github.com/landedjobs/ai-engineer-portfolio-projects/issues).</sub>
