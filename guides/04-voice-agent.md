# 🎙️ Build guide: a voice agent measured on what callers feel

> **Time:** 2-4 weeks · **Difficulty:** 🔴 Advanced · **Catalog entry:** [Multimodal](../catalog/multimodal.md)
> **Proves:** real-time pipelines, turn-taking, state updates under interruption, latency measured where the caller hears it, and task success checked against application state.

Most voice portfolio projects are a chatbot with a microphone: speech in, a model reply, speech out, and a demo video recorded in a quiet room. It sounds impressive and proves little. This guide builds a booking agent that has to get the booking right, keep up when the caller changes their mind halfway through a sentence, and answer fast enough that the call feels natural. Then it measures those things at the caller's end, not the server's.

The hard part is not the voice. It's updating state when the caller interrupts. EchoChain, a 2026 benchmark built around exactly this, reports that no real-time voice model it tested passed more than 50% of its interruption scenarios ([📄 EchoChain](https://arxiv.org/abs/2604.16456)). A portfolio project that measures corrections and interruptions is working on a problem the field hasn't solved.

---

## 1. Scope

**Build:** a phone or browser agent that books, changes and cancels appointments for a small business (a clinic, a salon, a restaurant) over a sandbox calendar you own.

**Tools (4-5):** `check_availability`, `create_booking`, `change_booking`, `cancel_booking`, `transfer_to_human`.

**The agent must:**

1. Collect what it needs (date, time, party size or service, name) and confirm before writing.
2. Handle corrections ("actually make that Thursday") and keep only the latest value.
3. Stop talking when the caller genuinely interrupts, and keep talking through "mm-hm" and "okay".
4. Hand off to a human when asked or when it's stuck.

**Use only synthetic audio or recordings from people who agreed to it.** Keep every write reversible.

**Leave out of the core:** voice cloning, many languages, a custom speech model. One language and one good pipeline, measured well, beats breadth.

## 2. Architecture

```
caller (WebRTC or phone) ─▶ noise filter + VAD ─▶ turn detector ─▶ speech-to-text ─▶ LLM + booking state machine ─▶ text-to-speech ─▶ playback
                                   │                                                       │
                          interruption handler                                       sandbox calendar
                                   └──────────▶ event log: audio timestamps at the client, transcript, tool calls, state changes
```

Or swap the middle for a realtime speech-to-speech model connected to the same tools. Build one, then try the other as a comparison.

Design rules:

- **State lives in your code, not the transcript.** Keep the booking as a typed object. Each correction updates a field; the model reads and writes that object through tools.
- **Confirm before writing.** Read back the booking and wait for a yes before `create_booking` runs.
- **Timestamp at the client.** Record when the caller stopped speaking and when the first agent audio actually played, on the caller's side.
- **Tell interruptions from backchannels.** LiveKit's turn handling separates genuine interruptions from conversational backchanneling and, by default, resumes speaking after a false interruption ([📘 LiveKit: turns](https://docs.livekit.io/agents/logic/turns)).
- **Don't assume realtime is faster.** LiveKit notes realtime models can give lower latency with fewer round trips but less reliable tool calling, and are not guaranteed to be faster than a well-tuned pipeline ([📘 LiveKit: voice agent latency](https://livekit.com/blog/understand-and-improve-agent-latency)).

## 3. Milestones

| Week | Ship | Done when |
|---|---|---|
| 1 | Sandbox calendar, booking state machine, a text-only version, scenario fixtures with expected end states | The text version passes its scenarios from a clean reset |
| 2 | Working voice loop with the booking tools, client-side timestamps, first latency numbers | You can replay any call and see where each second went |
| 3 | Noise, long pauses, corrections, interruptions, backchannels and handoff tests | Correction and interruption failures are measured, not assumed |
| 4 | Second pipeline configuration, turn-taking tuning, consented sample recordings, README | You have a pipeline A vs pipeline B table on the same scenarios |

**Two weeks only?** Stop after week 2 with one pipeline, 30 scripted calls and honest latency numbers. Say in the README that interruptions aren't tested yet.

## 4. The call set

Write 80-150 scenarios. Each has a caller goal, a script or a simulated caller persona, and the **required end state** of the calendar.

- **Mix:** plain bookings, changes, cancellations, impossible requests (fully booked, closed day), and handoff requests.
- **Corrections:** the caller changes a date, time or name, sometimes while the agent is mid-sentence.
- **Audio conditions:** background noise, accents, long pauses, people who think out loud.
- **Backchannels:** "mm-hm", "right", "okay" while the agent talks, labelled as not-interruptions.
- **Split by voice or recording session,** not by random lines, so the held-out set isn't near-copies of what you tuned on.

Text-first fixtures make week 1 fast. Simulated callers with text-to-speech scale well, but run a slice with real human speakers too, and report them separately.

## 5. What to measure

OpenAI's voice agent evaluation guide puts task completion first: did the run reach the expected application state, meet the mandatory requirements, and avoid unauthorised actions. It treats tool accuracy and semantic quality as secondary metrics that explain failures. In its own worked example, a run passes while tool accuracy is 50% ([📘 OpenAI: voice agent evaluation](https://developers.openai.com/cookbook/examples/audio/voice_agent_evaluation)). So publish the outcome and the components side by side:

| Metric | How to compute | Pipeline A | Pipeline B |
|---|---|---|---|
| Task success | Calls ending in the right calendar state with all required fields ÷ calls | | |
| Correction retention | Corrections reflected in the final state ÷ task-relevant corrections | | |
| Word error rate | (substitutions + deletions + insertions) ÷ reference words, on the labelled slice | | |
| False interruption rate | Backchannels that wrongly stopped the agent ÷ labelled backchannels | | |
| Response latency | p50 and p95 from the caller's end of speech to first audible agent reply, at the client | | |
| Stop latency | p50 and p95 from a genuine interruption starting to agent audio stopping | | |
| Cost | All speech, model, transport and phone costs ÷ call minutes; also ÷ successful calls | | |

Notes that make the table credible:

- **Word error rate is not task success.** A transcript can be perfect and the booking wrong, or the reverse.
- **Server time is not what the caller hears.** Time to first byte from your TTS leaves out buffering and network. Measure at the client.
- **Same scenarios, same voices, for both pipelines.** Change one thing at a time.
- **Report denominators** ("41 of 48 corrections kept"), and say which numbers came from simulated callers.

## 6. Cost and latency

Break each turn into stages: end-of-turn detection, transcription, model time to first token, tool calls, TTS time to first byte, and playback buffering. LiveKit's agents SDK records per-turn end-to-end latency, LLM time to first token and TTS time to first byte on each chat message, which covers most of this ([📘 LiveKit: voice agent latency](https://livekit.com/blog/understand-and-improve-agent-latency)). Add your client timestamps on top, because that's the number the caller feels.

Cost per call minute includes speech-to-text, model, text-to-speech, transport and phone charges. Use each provider's price list on the day you ran the calls, and write that date down. Count failed and retried calls in cost per successful call.

## 7. Common mistakes

- Reporting transcription accuracy as if it were task accuracy.
- Testing only clean, scripted speech in a quiet room.
- Ignoring silence, hesitations and backchannels.
- Calling server-side first-byte time "latency" without measuring playback.
- Keeping the booking only in the conversation history, so corrections get lost.
- Writing to the calendar before the caller confirms.
- Recording people without their permission.

## 8. Stretch goals

- **Correction benchmark:** callers change dates, quantities or names while the agent is speaking, at controlled points in its reply. Compare against the failure patterns EchoChain names.
- **Phone line:** connect a real number through SIP and report how phone audio changes word error rate and latency.
- **Pipeline vs realtime:** run the same call set on a realtime speech model with the same tools and compare success, tool accuracy and latency.
- **Regional latency:** move the agent closer to or further from the models and users, and chart the change.

## 9. References

- 💻 [livekit/agents](https://github.com/livekit/agents) · 15.3k ⭐ · Apache-2.0 (⚠️ its turn detection models use a separate LiveKit Model License) · a framework for real-time voice agents with turn detection, telephony and per-turn metrics.
- 💻 [pipecat-ai/pipecat](https://github.com/pipecat-ai/pipecat) · 17.3k ⭐ · BSD-2-Clause · an open-source framework for voice and multimodal agents; a good second pipeline to compare.
- 💻 [jitsi/jiwer](https://github.com/jitsi/jiwer) · 947 ⭐ · Apache-2.0 · word error rate and related measures for your speech-to-text slice.
- 📘 [OpenAI: voice agent evaluation](https://developers.openai.com/cookbook/examples/audio/voice_agent_evaluation) · outcome-first grading, with tool accuracy and latency as diagnostics.
- 📘 [LiveKit: understand and improve voice agent latency](https://livekit.com/blog/understand-and-improve-agent-latency) · where the seconds go, and pipeline vs realtime trade-offs.
- 📄 [EchoChain (2026)](https://arxiv.org/abs/2604.16456) · a benchmark for state updates when users interrupt mid-response.

## 10. Put it on your resume

Fill in your own numbers; these show the shape, not real results:

- *Built a voice booking agent over a sandbox calendar; 104 of 120 held-out calls ended in the correct booking, with 1.1 s p95 response latency measured at the caller's side.*
- *Moved booking state out of the transcript into a typed object; correction retention rose from 61% to 90% on 48 mid-sentence changes.*
- *Compared a speech-to-text/LLM/text-to-speech pipeline with a realtime model on the same calls; chose the pipeline for 12 points higher task success at similar latency.*

Next: write the README with the [project README template](../templates/project-readme.md), and see [presenting your project](../templates/presenting-your-project.md) for the interview.

---

<sub>Sources checked 17 Nov 2026. Star counts are a snapshot. Found something out of date? [Open an issue](https://github.com/landedjobs/ai-engineer-portfolio-projects/issues).</sub>
