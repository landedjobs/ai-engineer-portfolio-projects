# 📏 Build guide: an eval harness that can block a bad release

> **Time:** 2-4 weeks · **Difficulty:** 🟡 Intermediate · **Catalog entry:** [Evals & LLMOps](../catalog/evals-llmops.md)
> **Proves:** test-set design, code and model graders, grader calibration against human labels, regression detection in CI, and a release decision you can defend.

Most eval projects are a dashboard: run a library's metrics over a few examples, get a column of scores, ship a chart. Nobody can tell whether a 0.82 is good, whether the judge is right, or whether the number would have caught a real regression. This guide builds an eval harness that earns its place in a release process. It runs versioned cases against two configurations of an app, grades them with checks you have tested, and produces a report that says "ship" or "don't ship" with reasons. Then it proves the harness works by planting regressions and showing it catches them.

---

## 1. Scope

**Build:** an evaluation harness for one real application, ideally one you already built (the [RAG assistant](01-production-rag.md) or the [tool-using agent](02-tool-using-agent.md) are good targets).

**The harness must:**

1. Run a versioned case bank against two configurations (old prompt vs new, model A vs model B) on the same cases.
2. Store every output and trace, so you can regrade without rerunning the app.
3. Grade with code where an exact check exists, and with a model grader only where judgement is needed.
4. Measure the model grader against human labels before trusting it.
5. Run in CI on every pull request and fail the check when a gated metric drops.

**Leave out of the core:** a hosted dashboard, many apps at once, online monitoring of live traffic. One app, one honest release report.

## 2. Architecture

```
versioned case bank ─▶ runner (isolated trials, config A | config B) ─▶ trace + output store
                                                                              │
                         ┌──────────────── graders ◀──────────────────────────┘
                         ├ code graders (exact state, schema, citation IDs)
                         ├ model grader (rubric, calibrated on human labels)
                         └ human review queue (disputed and sampled cases)
                                        │
                   paired comparison (same cases, A vs B) ─▶ CI gate ─▶ release report
```

Design rules:

- **Keep running and grading apart.** Grading reads stored outputs. A grader fix should never force you to pay for every model call again.
- **Version everything.** Cases, rubrics, grader prompts and app config each carry a version, and every report lists them.
- **Pair the comparison.** A and B run on the same cases, and the report shows which cases flipped, not only the two averages.
- **Code graders first.** Anthropic's eval guide describes code graders as fast, cheap and reproducible but brittle, and model graders as flexible but non-deterministic and in need of human calibration ([📘 Anthropic: demystifying evals for agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)).
- **Keep separate scores separate.** OpenAI's voice-agent eval example has a run where task completion passes while tool accuracy is 50% ([📘 OpenAI: voice agent evaluation](https://developers.openai.com/cookbook/examples/audio/voice_agent_evaluation)). A report that averaged those would hide the problem.

Promptfoo and Inspect both handle running and grading. Use one, but the case bank, the calibration and the release rule are yours.

## 3. Milestones

| Week | Ship | Done when |
|---|---|---|
| 1 | Case schema, runner, code graders, a baseline report for the current app | `make eval` produces a versioned report from a clean checkout |
| 2 | Trace viewer, repeated trials, paired A-vs-B report, CI job | A pull request shows a pass or fail check with a link to the report |
| 3 | 100-200 human-labelled outputs, the model grader, its error analysis | Grader false acceptance and false rejection are measured, not assumed |
| 4 | Seed known regressions, measure detection, write one release recommendation | A stranger can rerun the suite and get the same decision |

**Two weeks only?** Stop after week 2 with code graders, a paired report and a working CI gate. Add the model grader later.

## 4. The case bank and the label set

**Cases:** 150-300 inputs for the target app, each with an expected outcome or a rubric. Mix ordinary cases, hard ones, out-of-scope ones and adversarial ones. Tag every case with a slice (topic, difficulty, case type) so the report can say *where* a change helped or hurt. Keep a held-out set for the final release decision.

**Labels for the grader:** have a person (you, or better, someone who knows the domain) mark 100-200 real outputs pass or fail, with a one-line reason. Include plenty of failures; a label set of easy passes tells you nothing about the judge. Hamel Husain's guide to LLM judges recommends simple pass/fail labels with written critiques, and notes that raw agreement misleads when classes are imbalanced, so report true positive and true negative rates separately ([📘 Hamel Husain: LLM-as-a-judge guide](https://hamel.dev/blog/posts/llm-judge/)).

**Seeded regressions:** build 10-20 broken versions of the app on purpose: a worse prompt, a retriever returning the wrong chunk, a tool with a bug, a smaller model. These are your ground truth for whether the harness works.

## 5. What to measure

| Metric | How to compute | Baseline | Your system |
|---|---|---|---|
| Grader agreement | Model-grader decisions matching human labels ÷ labelled outputs | | |
| False acceptance | Known-bad outputs the grader passed ÷ known-bad outputs | | |
| False rejection | Known-good outputs the grader failed ÷ known-good outputs | | |
| Regression detection | Seeded regressions the CI gate blocked ÷ seeded regressions | | |
| Alert precision | Gate failures that were real regressions ÷ all gate failures | | |
| Run stability | Range of the headline score across 3 repeated runs of the same config | | |
| Suite cost and time | Wall time and total cost per full run, app and grading reported apart | | |

Here "Baseline" is the simplest harness (code graders only, or an untuned judge) and "Your system" is the calibrated one.

**Judge biases are known.** The MT-Bench paper found strong LLM judges reached over 80% agreement with human preferences, and also documented position, verbosity and self-enhancement biases ([📄 Judging LLM-as-a-judge](https://arxiv.org/abs/2306.05685)). Test for them: swap answer order, pad a wrong answer, and see whether the score moves.

**A saturated suite proves little.** Anthropic's guide warns that an eval at 100% tracks regressions but gives no signal for improvement. If everything passes, add harder cases.

## 6. Cost and latency

Log app inference, grader inference, orchestration time and human review time separately. Report the cost of one full suite run, and the extra cost of adding one case. If CI runs a cheaper subset, say which slices it skips and how many seeded regressions it misses compared with the full suite. Use the provider's price list on the day you ran it, and write that date down.

## 7. Common mistakes

- Letting the same model write the labels and grade against them, with no human review.
- Measuring judge agreement only on easy passes.
- Changing the rubric between config A and config B.
- Treating a high pass rate as proof the eval is useful.
- Reporting one averaged score that hides a failing component.
- A CI gate on a metric that swings more between repeated runs than the drop it is meant to catch.

## 8. Stretch goals

- **Slice diff:** a tool that names the case slices that explain most of a release change.
- **Disagreement workflow:** route cases where the grader and a human disagree to a second reviewer, and track how the label set changes.
- **Confidence intervals:** bootstrap the paired difference between A and B and gate on the interval, not the point estimate.
- **Grader drift:** rerun the calibration set when the judge model changes and report the shift.

## 9. References

- 💻 [promptfoo/promptfoo](https://github.com/promptfoo/promptfoo) · 27.4k ⭐ · MIT · declarative test configs and a [GitHub Action](https://www.promptfoo.dev/docs/integrations/github-action/) for before-vs-after evals on pull requests.
- 💻 [UKGovernmentBEIS/inspect_ai](https://github.com/UKGovernmentBEIS/inspect_ai) · 3.3k ⭐ · MIT · the UK AI Security Institute's evaluation framework, with model-graded scoring and tool use.
- 💻 [confident-ai/deepeval](https://github.com/confident-ai/deepeval) · 19.5k ⭐ · Apache-2.0 · pytest-style LLM tests with ready-made metrics to compare against your own graders.
- 📄 [Judging LLM-as-a-judge with MT-Bench and Chatbot Arena](https://arxiv.org/abs/2306.05685) · judge agreement and judge biases.
- 📘 [Anthropic: demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents) · harness, tasks, trials, graders, and reading transcripts.
- 📘 [Hamel Husain: using LLM-as-a-judge for evaluation](https://hamel.dev/blog/posts/llm-judge/) · pass/fail labels, critiques and calibrating a judge.

## 10. Put it on your resume

Fill in your own numbers; these show the shape, not real results:

- *Built a 220-case CI eval harness for a RAG assistant; it blocked 16 of 18 seeded regressions with 2 false alarms across 40 pull requests.*
- *Calibrated an LLM grader on 150 human-labelled outputs to 92% agreement, cutting false acceptance of bad answers from 18% to 5%.*
- *Separated app and grading cost in every report; a full suite run costs $1.40 and 6 minutes, and a 60-case CI subset catches 15 of the 16.*

Next: write the README with the [project README template](../templates/project-readme.md), and see [presenting your project](../templates/presenting-your-project.md) for the interview.

---

<sub>Sources checked 17 Nov 2026. Star counts are a snapshot. Found something out of date? [Open an issue](https://github.com/landedjobs/ai-engineer-portfolio-projects/issues).</sub>
