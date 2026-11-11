# The AI Engineer's Handbook

A practical book for working developers who want to get good at building with large
language models: writing prompts that work, choosing the right application architecture,
picking tools, and shipping systems that stay reliable.

It is provider-neutral. Concepts come first; where the Claude, OpenAI, Gemini and
open-model APIs differ, a **Provider notes** box says how.

## Who this is for

You already write software. You do not need a machine learning background. You want to
stop guessing and start engineering.

## How to read it

- **In a hurry:** chapters 2, 3 and 5, then [the cheat sheets](book/appendix-a-cheatsheets.md).
- **Building a feature this week:** 2 → 4 → 5 → 9.
- **Building an agent:** 5 → 8 → 11 → 9.
- **Want to use coding agents better:** 2 → 3 → 12.
- **Cover to cover:** each chapter ends with exercises; chapter 14 has projects that tie them together.

## Contents

### Part I: Foundations
| # | Chapter | What you get |
|---|---------|--------------|
| 0 | [How to use this book](book/00-how-to-use-this-book.md) | Conventions, setup, how to practice |
| 1 | [How LLMs work (enough to engineer with)](book/01-how-llms-work.md) | Tokens, context windows, sampling, limits |
| 2 | [Prompting fundamentals](book/02-prompting-fundamentals.md) | The anatomy of a prompt that works |
| 3 | [Good vs bad prompts](book/03-good-vs-bad-prompts.md) | 20 paired rewrites with the reasons |
| 4 | [Structured output](book/04-structured-output.md) | JSON, schemas, validation, retries |

### Part II: Architecture
| # | Chapter | What you get |
|---|---------|--------------|
| 5 | [Application architectures](book/05-application-architectures.md) | Single call to multi-agent, and when to use which |
| 6 | [The AI stack](book/06-the-ai-stack.md) | The layers of a real application and three reference stacks |
| 7 | [RAG and data](book/07-rag-and-data.md) | Chunking, embeddings, hybrid search, reranking |
| 8 | [Agents and tool use](book/08-agents-and-tool-use.md) | The agent loop, tool design, MCP, context engineering |

### Part III: Shipping
| # | Chapter | What you get |
|---|---------|--------------|
| 9 | [Evals](book/09-evals.md) | Measuring quality so you can change things safely |
| 10 | [Production](book/10-production.md) | Cost, latency, caching, fallbacks, observability |
| 11 | [Security and safety](book/11-security-and-safety.md) | Prompt injection, data leakage, least privilege |

### Part IV: Practice
| # | Chapter | What you get |
|---|---------|--------------|
| 12 | [AI-assisted coding](book/12-ai-assisted-coding.md) | Working well with coding agents day to day |
| 13 | [The tools landscape](book/13-tools-landscape.md) | A categorized map with "pick this when" guidance |
| 14 | [Projects](book/14-projects.md) | Six graded projects with acceptance criteria |

### Appendices
- [A: Cheat sheets](book/appendix-a-cheatsheets.md)
- [B: Glossary](book/appendix-b-glossary.md)
- [C: Further reading](book/appendix-c-further-reading.md)

## A note on freshness

Principles in this book age slowly; product names age fast. Tool and API details were
written in November 2026. Treat any specific product claim as a pointer to go and check
the current documentation, not as a fact to rely on.
