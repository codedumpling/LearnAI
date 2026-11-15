# Appendix C: Further reading

A short list, chosen for durability. Search for titles if a link has moved; these
resources are revised and relocated often.

## Primary documentation (keep these open while building)

Provider documentation is the only reliable source for API details, limits and prices.

- **Anthropic documentation**: docs.claude.com. Prompt engineering guide, tool use,
  structured outputs, prompt caching, agent SDK.
- **OpenAI documentation**: platform.openai.com/docs. Prompting guides, structured
  outputs, function calling, agents.
- **Google Gemini API documentation**: ai.google.dev. Prompting strategies, function
  calling, context caching.
- **Model Context Protocol**: modelcontextprotocol.io. Specification and SDKs.

Read at least two providers' prompting guides. Where they agree, you have found
something durable.

## Prompting and context

- Anthropic, *Prompt engineering overview* and the interactive prompt engineering
  tutorial (in the Anthropic documentation and GitHub).
- Anthropic engineering blog, *Effective context engineering for AI agents*.
- OpenAI, *Prompt engineering* guide and the OpenAI Cookbook.

## Architectures and agents

- Anthropic, *Building effective agents*. The workflow-versus-agent distinction and
  the pattern catalogue used in chapter 5.
- Anthropic engineering blog, *Writing effective tools for agents*.
- OpenAI, *A practical guide to building agents*.
- *12-factor agents* (community project on GitHub). Principles for reliable agent software.

## Retrieval

- Anthropic, *Introducing Contextual Retrieval*. Adding context to chunks, with
  measured effects of hybrid search and reranking.
- Pinecone learning center and the LlamaIndex documentation. Clear explanations of
  retrieval techniques, readable regardless of which tools you use.

## Evals

- Hamel Husain, *Your AI product needs evals* and related posts. Practical,
  opinionated, widely cited.
- Eugene Yan, writing on LLM evaluation and *Patterns for building LLM-based systems
  and products*.
- Anthropic documentation, *Define success criteria and build evaluations*.

## Production practice

- Yan, Bischof, Frye, Husain, Liu and Shankar, *What we learned from a year of
  building with LLMs*. A broad collection of hard-won lessons.
- Chip Huyen, *AI Engineering* (O'Reilly). A book-length treatment of the whole field.
- Jay Alammar and Maarten Grootendorst, *Hands-On Large Language Models* (O'Reilly).

## Security

- OWASP, *Top 10 for Large Language Model Applications*.
- Simon Willison's blog, the prompt injection series. The clearest ongoing account of
  the problem, including the "lethal trifecta" framing behind chapter 11's
  three-factor diagram.
- NIST, *AI Risk Management Framework* and its generative AI profile.

## Understanding the models

- Andrej Karpathy, *Intro to Large Language Models* and *Let's build GPT* (videos).
- Jay Alammar, *The Illustrated Transformer*.
- 3Blue1Brown, the neural networks and transformers video series.

You do not need this depth to build applications. It helps your intuition about why
models behave as they do.

## Staying current

The field moves monthly. A sustainable habit:

- Read the release notes of the one or two providers you use.
- Follow two or three practitioners who publish what they learned from building, not
  just announcements.
- Re-run your eval suite when a new model appears. Your own numbers beat any headline.
- Revisit your tool choices twice a year, not weekly.

---
Previous: [Appendix B: Glossary](appendix-b-glossary.md) · Back to [the table of contents](../README.md)
