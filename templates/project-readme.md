# Project README template

Copy this into your project's `README.md` and fill it in. The order matters: a reviewer reads the top, looks for numbers, and decides in about 90 seconds whether to keep going.

Tucows' AI engineer take-home spells out what a reviewer expects to find in a README: "setup instructions, architecture overview, design decisions, patterns used, known limitations, and what you'd improve with more time", plus an architecture diagram ([📘 Tucows exercise](https://github.com/tucows/interview-exercise-ai)). This template covers all of it.

---

```markdown
# <Project name>: <what it does, in one line>

<One sentence on who it's for and the problem it solves.>

**Demo:** <live URL> · <90-second video or GIF> · <seeded local demo: `make demo`>

## Results

| Metric | Baseline | This project | How measured |
|---|---|---|---|
| <main quality metric> | <x%> | <y%> | <n held-out cases, grader> |
| <reliability or safety metric> | | | |
| p50 / p95 latency | | | <client-side, n requests> |
| Cost per successful task | | | <prices as of YYYY-MM-DD> |

<Two sentences: what changed between baseline and this project, and what it cost.>

## Quick start

    git clone <repo> && cd <repo>
    cp .env.example .env        # add your API key
    make setup && make demo      # or: docker compose up

## How it works

<Architecture diagram: ASCII or an image.>

<Three to five bullets: the main components and how data moves through them.>

## Design decisions

| Decision | Options considered | Why this one |
|---|---|---|
| <retrieval method / agent vs workflow / model choice> | | |

## Evaluation

- **Test set:** <how many cases, how they were written, dev vs held-out split>
- **Grading:** <what's checked by code, what by an LLM judge, how the judge was checked by hand>
- **Reproduce:** `make eval` (about <n> minutes, about $<x>)

## Failures and limits

<Three real failure cases from your eval, with what went wrong and why.>
<What the system does not handle. Where it would break at 10x the load.>

## Security and data

<What data it touches, how access is controlled, what is logged.>

## What I'd do next

<Two or three concrete next steps, with the metric each should move.>

## License

<Your licence. Note any third-party code and its licence.>
```

---

**Checklist before you share it**

- [ ] The first screen shows what it does, a demo link and the results table.
- [ ] Every number has a denominator and says how it was measured.
- [ ] `make demo` (or equivalent) works from a fresh clone, without your machine's state.
- [ ] There's a baseline, so the numbers mean something.
- [ ] At least three real failures are described.
- [ ] Costs include failed and retried calls, and the price date.
- [ ] No API keys, personal data or copied code with an incompatible licence.

See also: [eval report template](eval-report.md) · [presenting your project](presenting-your-project.md)
