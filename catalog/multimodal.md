# 🎨 Multimodal

> Voice and vision projects demo *incredibly* well live and show full-stack range (streaming, latency, media handling). One polished multimodal app is a memorable interview artifact.

**Skills legend:** `STT / TTS` · `vision LLMs (VLMs)` · `real-time streaming` · `latency budgets` · `image-text retrieval` · `structured vision output`
**Difficulty:** 🟢 Beginner · 🟡 Intermediate · 🔴 Advanced
**Link types:** 📄 paper · 📘 docs · 🎬 video · 🧑‍🏫 course · 🛠️ tool · 💻 repo

---

### 1. Whisper transcription + speaker diarization — 🟡 Intermediate
Transcribe long audio and label *who* spoke, with timestamps and a searchable transcript. A practically useful tool you can ship.
- **Skills proven:** ASR, diarization, chunking long audio, timestamp alignment.
- 💻 [openai/whisper](https://github.com/openai/whisper) — 104k ⭐ · MIT · speech-to-text.
- 💻 [m-bain/whisperX](https://github.com/m-bain/whisperX) — BSD-4 · word-level timestamps + diarization.

### 2. Blog-to-podcast agent — 🟡 Intermediate
Turn an article or docs page into a two-host conversational audio episode (script + TTS). NotebookLM-style, and a shareable artifact.
- **Skills proven:** long-form summarization, dialogue generation, multi-voice TTS.
- 💻 [souzatharsis/podcastfy](https://github.com/souzatharsis/podcastfy) — 6.4k ⭐ · Apache-2.0 · blog→podcast.

### 3. Visual chatbot over your own images (LLaVA-style) — 🔴 Advanced
A chat UI that answers questions about uploaded images using an open vision-language model. Shows you can run VLMs, not just call an API.
- **Skills proven:** VLM serving, image preprocessing, multimodal prompting.
- 💻 [haotian-liu/LLaVA](https://github.com/haotian-liu/LLaVA) — 24.9k ⭐ · Apache-2.0 · visual instruction tuning.

### 4. CLIP-powered semantic image search — 🟢 Beginner
Embed an image library with CLIP and search it by text (or by image). The clean, classic multimodal-retrieval starter.
- **Skills proven:** joint image-text embeddings, vector search, retrieval UX.
- 💻 [openai/CLIP](https://github.com/openai/CLIP) — 33.8k ⭐ · MIT · image-text retrieval.

### 5. Lightweight image captioning + VQA (MiniGPT-4) — 🟡 Intermediate
Caption images and answer questions about them with a small VLM you can run locally. A cheaper alternative to #3.
- **Skills proven:** captioning, visual question answering, lightweight VLMs.
- 💻 [Vision-CAIR/MiniGPT-4](https://github.com/Vision-CAIR/MiniGPT-4) — 25.7k ⭐ · BSD-3 · lightweight VLM.

### 6. Chart / receipt digitizer (vision → structured JSON) — 🟡 Intermediate
Extract clean, validated JSON from charts, receipts, or invoices with a vision model + schema. Vision + structured output end-to-end — very hireable.
- **Skills proven:** vision extraction, schema validation, retry-on-invalid.
- 💻 [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps) — 116k ⭐ · Apache-2.0 · vision-extraction apps.
- 🛠️ [instructor — vision + Pydantic](https://python.useinstructor.com/concepts/multimodal/) — structured vision docs.

### 7. Real-time voice bot (WebRTC + STT + agent + TTS) — 🔴 Advanced
A sub-second, barge-in-capable voice assistant over WebRTC. The most impressive thing you can demo in a live interview.
- **Skills proven:** real-time media, latency optimization, turn-taking, streaming.
- 🛠️ [pipecat-ai/pipecat](https://github.com/pipecat-ai/pipecat) — BSD-2 · real-time voice/multimodal pipelines.
- 💻 [patchy631/ai-engineering-hub](https://github.com/patchy631/ai-engineering-hub) — 36.2k ⭐ · MIT · voice-bot builds.

### 8. Text-to-image pipeline with control + eval — 🟡 Intermediate
A generation app (SDXL/FLUX) with prompt templates, ControlNet-style conditioning, and a small human-preference eval. Shows the generative side of multimodal.
- **Skills proven:** diffusion pipelines, conditioning, preference eval, prompt design.
- 💻 [huggingface/diffusers](https://github.com/huggingface/diffusers) — Apache-2.0 · diffusion pipelines.

---

← Back to the [full catalog](../README.md) · Want the **12 that actually land jobs**? See [projects-to-land-an-ai-job](https://github.com/landedjobs/projects-to-land-an-ai-job).
