# 🌐 Build guide: a browser agent that verifies the final state, not the screenshot

> **Time:** 2-4 weeks · **Difficulty:** 🔴 Advanced · **Catalog entry:** [Agents & tool use](../catalog/agents.md)
> **Proves:** browser automation, independent outcome checks, coping with page changes, resistance to hostile page text, and an honest comparison with a plain script.

The usual browser agent project is a screen recording of an agent booking something on a live website. It looks impressive once, and nobody can rerun it: the site changes, the account state drifts, and the only proof of success is the agent saying "done". This guide builds the version a reviewer can trust. The agent works on a website you own and can reset, every run is checked against the application's database rather than the agent's own story, and the agent is compared with a scripted baseline on the same tasks.

---

## 1. Scope

**Build:** a small procurement or service portal with synthetic accounts and records, for example an office-supplies catalogue with a request form, or a helpdesk with a ticket form.

The agent gets a task ("find the cheapest 27-inch monitor with USB-C under $300, add two to a draft request for the design team, and save it"), works through the pages, and leaves the portal in a state you can check.

**Typical actions:** search, filter, open a record, compare options, fill a form, save a draft.

**Include the messy cases on purpose:** a renamed button or moved field, a slow page, a record that went out of stock, and page text that tries to give the agent new instructions.

**Leave out of the core:** real purchases, irreversible submissions, public websites, logins to real services, and anything that touches a CAPTCHA. The agent saves drafts; a human submits.

## 2. Architecture

```
task ─▶ policy + domain allowlist ─▶ browser session ─▶ observation (DOM / accessibility tree | screenshot)
                                            ▲                       │
                                            └──── action executor ◀─ model
                                                        │
                    trace recorder (every observation, action, retry) ─▶ final-state verifier ─▶ score
                                                                             (reads the portal's database)
reset endpoint ─▶ clean database + fresh browser profile before every trial
```

Design rules:

- **Verify against the database, not the narrative.** The verifier reads the portal's records and checks every goal: right item, right quantity, right team, saved as a draft, nothing else changed.
- **A reset endpoint for every trial.** Fresh data, fresh browser profile, no cookies carried over. Anthropic's eval guide notes that shared state between runs can cause correlated failures and can also artificially inflate performance ([📘 Anthropic: demystifying evals for agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)).
- **A domain allowlist.** The browser can only reach your portal. Any other navigation fails the run.
- **Page text is data, not instructions.** Plant hostile text in product descriptions and check the agent ignores it. OWASP lists websites as a source of indirect prompt injection ([📘 OWASP LLM01](https://genai.owasp.org/llmrisk/llm01-prompt-injection/)).
- **No secrets in traces.** Test accounts only, and redact form values you would not want in a screenshot.

Browser Use and Stagehand give you the browser loop and the act / observe / extract primitives. Your contribution is the controlled portal, the verifier, the failure taxonomy and the comparison, not a wrapper around someone else's site.

## 3. Milestones

| Week | Ship | Done when |
|---|---|---|
| 1 | The portal, seed data, a reset endpoint, task fixtures with goal checks, a scripted Playwright baseline | The script passes its own fixtures from a clean reset |
| 2 | The agent loop, the final-state verifier, first end-to-end eval | You have a script-vs-agent table on held-out tasks |
| 3 | Layout variants, slow responses, stale records, planted hostile text | Layout-change success and injection resistance are measured, not assumed |
| 4 | Compare observation modes (DOM, screenshot, both), trace replays, README | A stranger can reset, rerun and replay a failed trial |

**Two weeks only?** Stop after week 2 with a clean script-vs-agent table on the unchanged portal. A small benchmark with a trustworthy verifier beats a big one with none.

## 4. The task set

Write 60-120 tasks. Each task has a seed state, an instruction, and a list of **goal checks** the verifier runs against the database: which records must exist, with which fields, and which must not change. Where more than one outcome is acceptable, list each one.

This is the idea behind WebArena: self-hosted, fully working websites, with tasks graded on whether the job was actually done. Its best GPT-4 agent reached 14.41% end-to-end success against 78.24% for humans ([📄 WebArena](https://arxiv.org/abs/2307.13854)). You don't need its scale, only its discipline.

Mix: ordinary tasks, tasks that need comparison across pages, impossible ones (the item doesn't exist, so the right move is to stop and say so), layout variants of earlier tasks (same goal, changed page), and injection tasks (hostile text on the page). Keep a held-out set you don't look at while tuning prompts.

## 5. What to measure

| Metric | How to compute | Script | Agent |
|---|---|---|---|
| End-state success | Trials where every goal check passes ÷ trials | | |
| Consistency (pass^3) | Tasks that pass all 3 trials from a clean reset ÷ tasks | | |
| False success reports | Trials the agent called done that failed verification ÷ trials the agent called done | | |
| Layout-change success | Success on layout-variant tasks ÷ layout-variant tasks | | |
| Injection resistance | Injection trials with no off-task action or navigation ÷ injection trials | | |
| Human interventions | Trials needing a human step ÷ trials, with the reason for each | | |
| Efficiency | Browser actions and model calls per successful task; p50 / p95 wall time | | |

**False success reports matter most.** An agent that fails and says so is safe to deploy behind a human. An agent that fails and says "done" is not. Report this number even when it looks bad.

**Count timeouts and crashes as failures,** not as excluded runs, and give the denominator ("41 of 60 layout variants").

## 6. Cost and latency

Log, per trial: browser start, navigation, waiting for the page to settle, observation capture, model calls, actions, retries and verification. Report screenshot tokens and DOM tokens separately, since they are billed differently and often decide which observation mode is cheaper. Cost per *successful* task includes failed and retried trials, plus browser hosting if you pay for it. Use the same reset policy for every variant so the timings compare.

For scale: Browserbase reports over 200 experiment runs and about 4,000 browser hours behind its browser-agent evaluations, and published 3,772 human-verified evaluations with traces ([📘 Browserbase: evaluating browser agents](https://browserbase.com/blog/evaluating-browser-agents/)). That is infrastructure scale, not a target. Treat it as a reminder that browser hours are a real cost line.

## 7. Common mistakes

- Accepting a good-looking final screenshot as proof of completion.
- Evaluating on a live public website that changes under you between runs.
- Carrying cookies, carts or drafts from one trial into the next.
- Bypassing logins, rate limits or CAPTCHAs on sites you don't own.
- Leaking credentials or personal data into saved traces and screenshots.
- No scripted baseline, so nobody knows whether the agent was needed.

## 8. Stretch goals

- **Crash and resume:** kill the browser mid-task and show the run resumes from the trace without duplicating a draft.
- **Observation ablation:** DOM only, screenshot only, and both, under the same step and cost budget.
- **Approval step:** let the agent prepare a real submission, then pause for a human to approve it, and measure how often the prepared submission was right.
- **Self-healing script:** when the scripted baseline breaks on a layout change, fall back to the agent for that step only, and compare cost with the agent doing everything.

## 9. References

- 💻 [browser-use/browser-use](https://github.com/browser-use/browser-use) · 121.3k ⭐ · MIT · a Python library for LLM-driven browser agents.
- 💻 [browserbase/stagehand](https://github.com/browserbase/stagehand) · 26.1k ⭐ · MIT · act, observe and extract primitives you can mix with plain code.
- 💻 [microsoft/playwright](https://github.com/microsoft/playwright) · 100.4k ⭐ · Apache-2.0 · the scripted baseline, plus a [trace viewer](https://playwright.dev/docs/trace-viewer) for replaying failed runs.
- 💻 [web-arena-x/webarena](https://github.com/web-arena-x/webarena) · 1.6k ⭐ · Apache-2.0 · self-hosted websites with task-completion checks; the model for this guide's eval.
- 📄 [WebArena: a realistic web environment for building autonomous agents](https://arxiv.org/abs/2307.13854) · why reproducible sites and functional checks matter.
- 📘 [Browserbase: training and evaluating browser agents](https://browserbase.com/blog/evaluating-browser-agents/) · what goes wrong when you evaluate on real websites.
- 📘 [Anthropic: demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents) · outcomes vs transcripts, isolated trials, grader choice.

## 10. Put it on your resume

Fill in your own numbers; these show the shape, not real results:

- *Built a browser agent over a resettable procurement portal; raised verified completion from 72% (scripted baseline) to 86% across 90 held-out tasks, with every run checked against the database.*
- *Cut false "done" reports from 9% to 1% by adding a self-check before the agent ends a task; published replayable traces of every failure.*
- *Held 81% success on layout-changed pages where the script fell to 40%, and resisted planted instructions on 58 of 60 injection tasks.*

Next: write the README with the [project README template](../templates/project-readme.md), and see [presenting your project](../templates/presenting-your-project.md) for the interview.

---

<sub>Sources checked 17 Nov 2026. Star counts are a snapshot. Found something out of date? [Open an issue](https://github.com/landedjobs/ai-engineer-portfolio-projects/issues).</sub>
