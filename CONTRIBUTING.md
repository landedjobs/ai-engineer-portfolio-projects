# Contributing to AI Engineer Portfolio Projects

Thanks for helping build the most comprehensive catalog of AI engineering portfolio projects! This is a curated list — the bar is **quality, not quantity.**

## What makes a good project entry

Every project you add must clear all four checks:

1. **Buildable.** A motivated engineer can actually ship it — not a research-only idea, not a $50k-of-GPU idea. If it can't be built on a laptop, a free-tier GPU, or a cheap API budget, note the requirement explicitly.
2. **Skills-tagged.** State the concrete skills it *proves* (e.g. "hybrid retrieval, re-ranking, faithfulness evals") — the things a hiring manager would credit.
3. **Difficulty-rated.** One of 🟢 Beginner · 🟡 Intermediate · 🔴 Advanced.
4. **Reference-backed.** At least one **typed, annotated, license-noted** reference (a repo, paper, docs page, or course) that helps someone actually build it.

## Entry format

Match the existing format in the `catalog/<theme>.md` files exactly:

```markdown
### N. Project title — 🟡 Intermediate
One or two sentences on what you build and why it's a strong portfolio signal.
- **Skills proven:** skill, skill, skill.
- 💻 [owner/repo](https://github.com/owner/repo) — 12k ⭐ · MIT · one-line annotation.
- 📄 [Paper title](https://arxiv.org/abs/...) — what it's the reference for.
```

**Link type tags:** 📄 paper · 📘 docs · 🎬 video · 🧑‍🏫 course · 🛠️ tool · 💻 repo.

## License discipline (important)

This repo is MIT. When you reference or suggest adapting code:

- **Only recommend adapting MIT / Apache-2.0 / CC0 code**, and always with attribution (`License · Source · Authors`).
- **Flag non-commercial or copyleft licenses** (e.g. `RAG_Techniques` is non-commercial) so people **re-implement the technique** rather than copy notebooks into a repo they publish.
- Note the license inline on every referenced repo (`· MIT`, `· Apache-2.0`, `· ⚠️ Non-commercial`).

## How to contribute

1. Fork the repo.
2. Add or edit the entry in the relevant `catalog/<theme>.md` file (keep entries ordered logically by difficulty within a theme).
3. If you add a project, update the theme count in `README.md` (the Contents table and the total).
4. Keep annotations **specific and genuinely useful** — no thin promo, no bare link dumps.
5. Open a PR using the template, with a one-line description of what you added.

Prefer not to edit Markdown? Open an issue with the **Suggest a project** or **Add a resource** template and we'll add it.

## Style

- One idea per entry; lead with the concrete value.
- Cite a source for any stat or benchmark claim.
- Keep links live — we periodically run a link checker and prune dead ones.
- Tables over prose wherever things are comparable.

## Code of conduct

Be kind and helpful. This community exists to get people hired. 💜
