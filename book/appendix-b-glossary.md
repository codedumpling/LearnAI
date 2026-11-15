# Appendix B: Glossary

Terms as this book uses them. Chapter numbers point to the main discussion.

**Agent.** A model in a loop with tools, deciding its own next step toward a goal. (8)

**Agent harness.** The code around the model that runs the loop, executes tools,
manages context and enforces limits. (8)

**Batch API.** An interface for submitting many requests for asynchronous processing
at a lower price. (10)

**BM25.** A standard keyword-ranking algorithm used in full-text search. (7)

**Chain (prompt chain).** A fixed sequence of model calls where each uses the previous
output. (5)

**Chunk.** A passage of a document, split out for indexing and retrieval. (7)

**Code grader.** An eval check implemented in ordinary code. (9)

**Compaction.** Summarizing earlier conversation history to free context space. (8)

**Context engineering.** Deciding what information is in the context window at each
step of a task. (8)

**Context file.** A file in a repository (for example `CLAUDE.md` or `AGENTS.md`) that
tells coding agents how the project works. (12)

**Context window.** Everything the model can see for one call, measured in tokens,
including its own output. (1)

**Embedding.** A vector representing the meaning of a piece of text, used for
similarity search. (7)

**Escape hatch.** An explicit, sanctioned way for the model to say it cannot answer. (2)

**Eval.** A repeatable test of output quality: dataset, system under test, graders. (9)

**Few-shot prompting.** Including examples of the desired input and output in the
prompt. (2)

**Fine-tuning.** Further training a model on your own examples. (13)

**Gate.** A code check between steps of a chain that can stop or redirect the flow. (5)

**Gateway.** A service between your applications and model providers that handles
routing, keys, limits and logging. (6)

**Grounding.** Tying an answer to supplied source material, usually with quotes or
citations. (7)

**Guardrail.** A check on input or output that blocks or flags policy violations. (11)

**Hallucination.** Output that is fluent and confident but false or unsupported. (1)

**Held-out set.** Eval cases you do not tune against, used to detect overfitting. (9)

**Hybrid search.** Combining keyword and vector search results. (7)

**Idempotent.** Safe to repeat without additional effect; needed for retried tool
calls. (10)

**Indirect prompt injection.** Injection delivered through content the model processes
instead of through the user's message. (11)

**Least privilege.** Granting only the access a task needs. (11)

**LLM.** Large language model.

**Max tokens.** The cap on output length for one call. (1)

**MCP (Model Context Protocol).** An open standard for connecting models to tools and
data sources. (8)

**Model grader (LLM as judge).** A model call that evaluates another model's output
against a rubric. (9)

**Multi-agent system.** Several agents with separate contexts, usually coordinated by
an orchestrator. (5, 8)

**Orchestration.** The logic that decides which model calls and tools run, in what
order. (6)

**Prompt caching.** Reusing a provider-side cache of a repeated prompt prefix to cut
cost and latency. (10)

**Prompt injection.** Untrusted text causing a model to act against its operator's
intent. (11)

**RAG (retrieval-augmented generation).** Retrieving relevant material at request time
and including it in the prompt. (7)

**Reasoning mode (extended thinking).** A model feature that spends tokens on
intermediate reasoning before the answer. (1)

**Recall@k.** The share of queries for which a relevant passage appears in the top k
retrieved results. (7)

**Reranker.** A model that rescores retrieved candidates against the query for better
ordering. (7)

**Router.** A step that classifies an input and dispatches it to a specialized
handler. (5)

**Sandbox.** An isolated environment for running untrusted or generated code. (11)

**Schema-constrained output (structured output).** Generation restricted so that the
response conforms to a supplied schema. (4)

**Stop reason.** The API field saying why generation ended: finished, hit the token
limit, made a tool call, and so on. (1)

**Streaming.** Receiving output incrementally as it is generated. (10)

**Sub-agent.** An agent started by another agent to handle a delegated task in its own
context. (8)

**System prompt.** Standing instructions that apply to the whole conversation. (2)

**Temperature.** A sampling setting controlling how varied the output is. (1)

**Token.** The unit of text a model reads and writes; roughly three quarters of an
English word. (1)

**Tool (function) calling.** The model returning a structured request to invoke a
function your code provides. (4, 8)

**Trace.** A linked record of every step in handling one request. (10)

**Vector store.** A database or index for nearest-neighbor search over embeddings. (7)

**Workflow.** A system where code decides the sequence of steps and models fill them
in; contrast with agent. (5)

---
Previous: [Appendix A: Cheat sheets](appendix-a-cheatsheets.md) · Next: [Appendix C: Further reading](appendix-c-further-reading.md)
