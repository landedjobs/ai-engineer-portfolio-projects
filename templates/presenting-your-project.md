# Presenting your project: resume, links and the interview

A finished project only helps if a reviewer sees what it proves. This page covers the three places that happens: the resume line, the link they click, and the conversation.

## What hiring teams say they look for

| Who | What they said | What it means for you |
|---|---|---|
| Boris Cherny, creator of Claude Code ([📘 interview, Dec 2025](https://www.developing.dev/p/boris-cherny-creator-of-claude-code)) | "When I hire engineers, this is definitely something I look for. I want people with side quests, like cool weekend projects." | A project you chose yourself, with a reason it exists, counts. Be ready to say what surprised you. |
| Juno, AI Founding Engineer posting ([📘 YC job page](https://www.ycombinator.com/companies/juno-chat/jobs/TPnzNRU-ai-founding-engineer-at-juno)) | Asks for "an AI project or system you've built and are proud of" and "your GitHub, portfolio, or a demo". | One link they can open in a few seconds beats three half-finished repos. |
| Tucows, AI engineer take-home ([📘 exercise](https://github.com/tucows/interview-exercise-ai)) | Scores RAG quality at 20%, and API design, the MCP server and tests at 15% each. | Plumbing, tests and docs are graded, not just the model's answers. |
| Studio65, take-home ([📘 assignment](https://github.com/Studio65-AI/interview-assignment)) | "We're more interested in your improvement process and thought process than in hitting specific performance metrics." | Show how you improved it, not only where it ended up. Not every team wants a big platform. |

No source we found sets a minimum star count, a required number of projects, or a framework that helps more than another. What comes up again and again is **ownership you can inspect**: a demo shows the outcome, the repo shows your choices, and the eval shows the improvement is real.

## The resume line

Formula: **built what + for whom → measured result on a stated test set → how, in a few words → cost or scale.**

| Weak | Strong (with your own numbers) |
|---|---|
| Built a RAG chatbot using LangChain and Pinecone | Built a cited support assistant over a 200-page manual; raised held-out answer correctness from 68% to 84% (300 questions) with hybrid retrieval, at $0.03 per query |
| Developed a multi-agent system with CrewAI | Built an order-desk agent that applies approved changes in a sandboxed database; 89% final-state success on 120 held-out tasks, 0 duplicate writes across 400 runs |
| Fine-tuned Llama on custom data | Fine-tuned an 8B model for ticket triage; matched the hosted model's accuracy (91% vs 92%, 1,000 tickets) at a tenth of the cost per ticket |

Rules: one project, one or two lines. Lead with the result. Name the test set size. Leave tool names for the end, or the skills section.

For more bullet examples and full resumes, see [ai-engineer-resume-examples](https://github.com/landedjobs/ai-engineer-resume-examples).

## The link

- Link straight to the repo or demo, not to your GitHub profile.
- Pin the project on your GitHub profile and put the results table in the first screen of the README ([template](project-readme.md)).
- If the live demo costs money to keep running, record a 90-second video and offer a seeded local demo.

## The interview

Expect to be asked to go deep on one project. Prepare these five answers:

1. **Why this project?** The problem, and why you picked it over something easier.
2. **Walk me through the architecture.** Draw it in under two minutes, data flow first.
3. **How do you know it works?** Your test set, how you graded, the baseline, the numbers.
4. **What broke?** Two real failures, how you found them and what you changed.
5. **What would you do with more time / at 10x the traffic?** Concrete next steps and where it would break first.

The AI system design round often builds on the same ideas; see [ai-system-design-interview](https://github.com/landedjobs/ai-system-design-interview) and the question bank in [awesome-ai-engineer-interview](https://github.com/landedjobs/awesome-ai-engineer-interview).

See also: [project README template](project-readme.md) · [eval report template](eval-report.md)
