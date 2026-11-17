# 🎨 Build guide: a multimodal system that answers with page and timestamp evidence

> **Time:** 2-4 weeks · **Difficulty:** 🔴 Advanced · **Catalog entry:** [Multimodal](../catalog/multimodal.md)
> **Proves:** cross-modal retrieval, temporal grounding, evidence you can click, a text-only baseline, and honest ingestion and query costs.

Most multimodal portfolio projects caption an image or summarise a video's audio. That shows you can call a vision model, not that vision changed anything. This guide builds a training assistant over procedure videos and a manual that answers "where is this part handled?" or "when does this step happen?" with a video interval and a manual page. Then it compares itself against a transcript-only version on questions the audio alone can't answer. If the frames don't change the answers, the table will say so.

Open vision-language models now make this a bounded project. The Qwen3-VL technical report describes explicit textual timestamp alignment for more precise temporal grounding in video ([📄 Qwen3-VL technical report](https://arxiv.org/abs/2511.21631)). That is a capability claim about the model, not evidence that it works on your videos. Your eval set provides that.

---

## 1. Scope

**Build:** an assistant for one small, self-made domain, for example assembling a bike part or servicing a home appliance:

- 5-10 procedure videos you record yourself (or with people who consent in writing), 3-10 minutes each.
- A 20-40 page manual you write for the same procedures, with diagrams.
- Questions written to need the picture: parts identified by sight, silent actions, vague narration ("put this one here").

**The assistant must:**

1. Answer with a video interval (start and end time) and, where relevant, a manual page.
2. Open the player at the cited time and the manual at the cited page.
3. Abstain when neither the video nor the manual covers the question.
4. Run in two modes, transcript-only and multimodal, over the same questions.

**Leave out of the core:** live camera input, video generation, fine-tuning a vision model. They add cost without testing the core claim.

## 2. Architecture

```
video ─▶ audio ─▶ transcript with word timestamps ─┐
      └▶ sampled frames + timestamps ──────────────┼─▶ cross-modal index
manual ─▶ pages ─▶ text / layout chunks + page images ┘
question ─▶ retrieve (transcript only | transcript + frames + pages) ─▶ vision-language model
         ─▶ answer + interval + page ─▶ grounding check ─▶ player / manual viewer
```

Design rules that make it reviewable:

- **Timestamps travel with everything.** Every transcript segment and every frame carries its source time, so a retrieved frame can always become a clickable interval.
- **Keep the transcript-only path working.** It is your baseline. Same questions, same model where possible, only the visual input removed.
- **Record your sampling.** Frames per video minute and image resolution decide both cost and what the model can see. A step that lasts two seconds can fall between frames.
- **Return the evidence with the answer.** The cited interval and page are part of the output, so your eval can check them with code.

Whisper or WhisperX gives you transcripts with timestamps (WhisperX adds word-level alignment and speaker labels). Docling can parse the manual's pages, layout and tables. Qwen3-VL accepts images and video and runs locally under vLLM or SGLang.

## 3. Milestones

| Week | Ship | Done when |
|---|---|---|
| 1 | Consented recordings, the manual, questions with labelled answers, intervals and pages | Every question has a reference answer and evidence label |
| 2 | Transcription, page retrieval, the transcript-only baseline | `make ingest && make eval` produces the baseline column |
| 3 | Frame sampling, multimodal answering, interval output, grounding check | You have a transcript-vs-multimodal table on held-out questions |
| 4 | Sampling ablation, missing-evidence tests, cost report, an interactive player | A stranger can click any answer and land on the cited moment |

**Two weeks only?** Use 3 videos and skip the manual. A clean transcript-vs-multimodal comparison on video alone still makes the point.

## 4. The question set

Write 100-150 questions before building the multimodal path.

- **Each question** has a reference answer, the labelled time interval(s) and, where relevant, the manual page.
- **Mix:** questions the transcript answers (control), visual-only questions (a part never named aloud), silent actions, ambiguous narration, and **unanswerable** questions (about 15%).
- **Split by video, not by question.** Questions from the same recording stay together, so you never tune on one half of a video and test on the other.
- **Fix your tolerance rules first:** what interval overlap counts as a hit, and which frames show a given action.

## 5. What to measure

| Metric | How to compute | Transcript only | Multimodal |
|---|---|---|---|
| Answer correctness | Answers passing a frozen factual rubric ÷ questions | | |
| Temporal IoU | Length of overlap between predicted and labelled interval ÷ length of their union; mean over temporal questions | | |
| Grounding recall at IoU ≥ 0.5 | Temporal questions with a predicted interval at or above the threshold ÷ temporal questions | | |
| Page evidence precision | Cited manual pages that support the claim ÷ cited pages | | |
| Unsupported visual claims | Visual claims not backed by the labelled frames ÷ visual claims | | |
| Abstention | Correct abstentions ÷ unanswerable questions; wrong abstentions ÷ answerable ones | | |
| Latency and cost | Indexing time per video minute; p50 / p95 query time; cost per correct answer | | |

Notes that make the table credible:

- **Break the table down by question type.** The headline is the visual-only rows. If multimodal only helps on questions the transcript already answers, say so.
- **State the threshold** next to any grounding number. An IoU of 0.5 and 0.3 tell different stories.
- **Grade with code where you can.** Interval overlap and page matches are exact checks. Use a model judge only for answer wording, and calibrate it by hand ([📘 Anthropic: demystifying evals for agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)).
- **Report denominators** ("31 of 40 visual-only questions"), not just percentages.

## 6. Cost and latency

Log, per video: decoding, transcription, frame extraction, resizing and indexing time and cost. Log, per query: retrieval, image tokens, model inference and total time. Report frames per minute, image resolution and context length, plus cached and uncached costs. Keep ingestion cost (paid once per video) apart from query cost (paid every time). For latency terms such as time to first token and what queueing adds, see [📘 NVIDIA: LLM inference benchmarking](https://developer.nvidia.com/blog/llm-benchmarking-fundamental-concepts). A local GPU is not free; price its hours.

## 7. Common mistakes

- Running an image captioner and calling it a multimodal product.
- Testing only questions the transcript already answers.
- Sampling so few frames that the action in the question is never seen.
- Reporting timestamps without an overlap rule or tolerance.
- Splitting one video's questions across tuning and testing.
- Assuming a code licence covers the model weights, or your right to publish someone's recording.

## 8. Stretch goals

- **Point and ask:** let the user click a region of a frame and ask a follow-up, with spatial evidence and a clear abstention path.
- **Sampling ablation:** plot answer correctness and cost against frames per minute, and pick the point you'd ship.
- **Page images, not page text:** retrieve manual pages as images with a ColPali-style model and compare with text chunks on diagram questions.
- **Model swap:** run a smaller or hosted vision model on the same set and show where correctness drops.

## 9. References

- 💻 [QwenLM/Qwen3-VL](https://github.com/QwenLM/Qwen3-VL) · 20.2k ⭐ · Apache-2.0 · open vision-language models with video grounding and vLLM / SGLang serving; the [Qwen3-VL-4B-Instruct weights](https://huggingface.co/Qwen/Qwen3-VL-4B-Instruct) are also Apache-2.0, but check each checkpoint's card.
- 💻 [m-bain/whisperX](https://github.com/m-bain/whisperX) · 25.4k ⭐ · BSD-2-Clause · transcription with word-level timestamps and speaker labels.
- 💻 [openai/whisper](https://github.com/openai/whisper) · 112.9k ⭐ · MIT (code and weights) · the base speech recognition model.
- 💻 [docling-project/docling](https://github.com/docling-project/docling) · 70.7k ⭐ · MIT · manual parsing with layout, tables and OCR; check each model's own licence.
- 💻 [illuin-tech/colpali](https://github.com/illuin-tech/colpali) · 2.9k ⭐ · MIT (code) · page-image retrieval for the stretch goal; model checkpoints carry their own licences.
- 📄 [Qwen3-VL technical report](https://arxiv.org/abs/2511.21631) · how the model aligns text with video timestamps.
- 📄 [ColPali: efficient document retrieval with vision language models](https://arxiv.org/abs/2407.01449) · retrieving pages as images instead of extracted text.

## 10. Put it on your resume

Fill in your own numbers; these show the shape, not real results:

- *Built a video and manual assistant that answers with clickable timestamps and page citations; raised answer correctness from 61% (transcript only) to 79% on 100 held-out questions.*
- *Reached 0.62 mean temporal IoU and 71% grounding recall at IoU ≥ 0.5; on visual-only questions, multimodal answered 31 of 40 against 9 of 40 for the transcript baseline.*
- *Cut indexing cost per video minute by 60% by sampling 1 frame every 2 seconds instead of 2 per second, with a 2-point drop in correctness.*

Next: write the README with the [project README template](../templates/project-readme.md), and see [presenting your project](../templates/presenting-your-project.md) for the interview.

---

<sub>Sources checked 17 Nov 2026. Star counts are a snapshot. Found something out of date? [Open an issue](https://github.com/landedjobs/ai-engineer-portfolio-projects/issues).</sub>
