# 14. Projects

## After this chapter you will be able to

- Build six small systems that together exercise every chapter.
- Judge your own work against explicit acceptance criteria.
- Show a portfolio of evidence, not just a list of topics read.

## How to use these

The projects go in order of difficulty. Each lists the chapters it draws on, what to
build, acceptance criteria, stretch goals, and the lesson people usually learn the
hard way.

Rules for all of them:

- **Write the eval set before the second version of the prompt.**
- **Log every model call.**
- **Keep a short write-up:** what you built, what failed, what you changed, final numbers.

The write-up is the most valuable artifact. It is what separates "followed a tutorial"
from "can engineer this".

---

## Project 1: Ticket triage

*Chapters 2, 3, 4, 9 · Architecture: single call · About half a day*

**Build.** A function that takes a support message and returns
`{category, urgency, summary, needs_human}` using schema-constrained output.

**Acceptance criteria**

- [ ] 50 labeled test messages, including at least 8 edge cases (multi-issue, other
      language, empty, abusive, off-topic)
- [ ] Category accuracy of 90% or better on the set
- [ ] Zero schema failures across 3 full runs
- [ ] Every remaining failure has a written explanation
- [ ] Cost and p95 latency per message are measured and recorded

**Stretch.** Compare three model tiers on accuracy, cost and latency. Add a held-out
set of 20 and report the gap.

**What people learn.** The label definitions were ambiguous, and fixing the definitions
improved accuracy more than any prompt trick.

---

## Project 2: Meeting notes pipeline

*Chapters 2, 5, 9 · Architecture: prompt chain with gates · About one day*

**Build.** Transcript in; out come decisions, action items with owners, and a
follow-up email draft. Three steps: extract, verify, write.

**Acceptance criteria**

- [ ] 10 test transcripts (synthetic is fine; include one with no decisions and one
      with an action item that has no owner)
- [ ] A code gate confirms every owner named in the output appears in the transcript
- [ ] A model grader checks that no action item is invented, calibrated against your
      own labels on at least 20 items
- [ ] The no-decisions transcript yields an explicit "no decisions" result, not a made-up one
- [ ] The independent steps run in parallel where possible

**Stretch.** Add a generate-then-critique step for the email and measure whether it
helps or only costs more.

**What people learn.** Gates between steps catch errors that would otherwise surface
three steps later with no clue to the cause.

---

## Project 3: Docs Q&A with citations

*Chapters 7, 9, 10 · Architecture: RAG · Two to three days*

**Build.** A question-answering system over a real documentation set of at least 100
pages (an open-source project's docs work well), with cited answers.

**Acceptance criteria**

- [ ] A baseline first: answer 20 questions with as much of the corpus as fits in the
      prompt, recording accuracy and cost
- [ ] Hybrid retrieval (keyword and vector) with a reranking step
- [ ] 40 questions with known source passages; recall@5 reported
- [ ] Answer correctness and groundedness graded separately from retrieval
- [ ] At least 5 unanswerable questions, all correctly declined
- [ ] For 5 wrong answers, a diagnosis naming the failing stage
- [ ] Prompt caching or response caching in place, with a measured effect

**Stretch.** Add query rewriting for follow-up questions. Add contextual headers to
chunks and measure the change in recall.

**What people learn.** Most wrong answers were retrieval failures, and parsing quality
mattered more than the choice of vector database.

---

## Project 4: Repository helper agent

*Chapters 8, 9, 11 · Architecture: single agent · Three to four days*

**Build.** An agent with `list_directory`, `read_file`, `search_text` and `run_tests`
that answers questions about a codebase and can fix a failing test. Write the loop
yourself before trying an agent SDK.

**Acceptance criteria**

- [ ] The loop has step, token and time limits enforced in code
- [ ] File tools cannot escape the project directory (tested with `../` paths)
- [ ] `run_tests` executes in a sandbox or container without credentials
- [ ] 10 tasks with end-state graders (the right answer was found; the test now passes
      and no test was deleted or weakened)
- [ ] Each task run 3 times; success rate, mean steps and mean cost reported
- [ ] One impossible task; the agent reports failure rather than claiming success
- [ ] A full trace is saved for every run

**Stretch.** Add context compaction and show a task that only succeeds with it. Port
the agent to an agent SDK and compare code size and behavior.

**What people learn.** Tool descriptions and tool output formats moved the success rate
more than the system prompt did.

---

## Project 5: Hardening

*Chapters 10, 11 · Applies to project 3 or 4 · Two days*

**Build.** Take project 3 or 4 and make it production-shaped.

**Acceptance criteria**

- [ ] A written threat review using the six questions in chapter 11
- [ ] 10 adversarial eval cases (direct and indirect injection) with code-checked
      "must not" conditions
- [ ] At least one risk removed by design (a capability taken away or gated), not by prompt wording
- [ ] Retries with backoff and a cap; a fallback model; a test that simulates provider failure
- [ ] Per-user rate limit and a spend cap
- [ ] A dashboard or report: cost per request, p50 and p95 latency, error rate, cache hit rate
- [ ] A documented rollback procedure for a prompt change

**Stretch.** Run the eval suite in CI and block merges on regression.

**What people learn.** The injection defenses that held were the structural ones.

---

## Project 6: Capstone, a small real product

*All chapters · One to two weeks*

**Build.** Something a real person will use. Suggestions:

- A pull-request reviewer for one of your own repositories
- A research assistant that produces a cited brief on a topic from the web
- A personal knowledge assistant over your own notes
- An operations assistant that triages alerts and drafts incident summaries

**Acceptance criteria**

- [ ] A one-page design: users, architecture diagram, why this rung of the ladder and
      not the one below
- [ ] Used by at least 3 people who are not you, with their feedback recorded
- [ ] An eval set of 50 or more cases, including cases that came from real usage
- [ ] Targets for quality, latency and cost stated up front, and measured at the end
- [ ] A threat review and its controls
- [ ] Tracing in place; you can explain any output a user questions
- [ ] A repository context file, so an agent or a new contributor can work in it
- [ ] A final write-up: what you would do differently

**What people learn.** Real users ask things the test set never imagined, and feeding
those back into the evals is the actual job.

---

## Self-assessment

After the projects, you should be able to say yes to these without hedging:

| Skill | Evidence |
|---|---|
| I can turn a vague request into a prompt that works across varied inputs | Projects 1, 2 |
| I can get reliable structured output and handle the failures | Project 1 |
| I can choose an architecture and defend not choosing a more complex one | Projects 2, 6 |
| I can build retrieval and tell a retrieval failure from a generation failure | Project 3 |
| I can build an agent with bounded cost and risk | Project 4 |
| I can measure quality and detect regressions | All |
| I can state a system's worst-case failure and what prevents it | Project 5 |
| I can delegate coding work and review the result responsibly | Throughout |

---
Previous: [13. The tools landscape](13-tools-landscape.md) · Next: [Appendix A: Cheat sheets](appendix-a-cheatsheets.md)
