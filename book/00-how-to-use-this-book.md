# 0. How to use this book

## After this chapter you will be able to

- Navigate the book and its conventions.
- Set up a minimal environment for the exercises.
- Practice in a way that actually builds skill.

## What "AI engineering" means here

AI engineering is building software where a language model does part of the work. You
are not training models. You are deciding what to ask a model, what information to give
it, what it is allowed to do, and how you will know whether the result is good.

Most of the skill is ordinary engineering applied to an unusual component: one that is
flexible, capable, non-deterministic, and that reads instructions written in English.

Three ideas run through every chapter:

1. **The model only knows what is in its context.** Most failures are missing-information
   failures, not intelligence failures.
2. **Start with the simplest thing that could work.** One good model call beats a clever
   pipeline that nobody can debug.
3. **If you cannot measure it, you cannot improve it.** Evals are to AI engineering what
   tests are to software engineering.

## Conventions

**Chapter shape.** Each chapter runs: concept → worked example → common mistakes → exercises.

**Bad/good pairs.** Prompt comparisons always use the same four parts:

> **Bad prompt**: what people usually write.
> **Why it fails**: the specific gap.
> **Better prompt**: a rewrite.
> **What changed**: the transferable lesson.

The "bad" prompts are not stupid. They are what a busy person writes when they forget
the model cannot read their mind.

**Code.** Examples are short Python or pseudo-code using a made-up neutral client:

```python
response = llm.generate(
    system="...",          # standing instructions
    messages=[...],        # the conversation
    tools=[...],           # optional
    max_tokens=1024,
)
```

Every major provider's SDK has this shape with different names. Translating is a
five-minute job with the provider's quickstart open.

**Provider notes.** Boxes like this flag real differences:

> **Provider notes**
> Where APIs differ in a way that changes your design, it is called out here.

**Diagrams** are Mermaid and render on GitHub.

## Setup for the exercises

You need very little:

- An API key for at least one model provider, or a local model runner.
- Python 3.10+ or Node 20+, whichever you prefer.
- A folder for experiments, under version control.
- A plain file or spreadsheet for recording what you tried and what happened.

That last item matters more than it looks. Prompt work without notes turns into
re-discovering the same thing every week.

## How to practice

- **Do the exercises against a real model.** Reading about prompts is like reading about
  swimming.
- **Read the outputs.** All of them, not a sample that confirms what you hoped.
- **Change one thing at a time**, and run each variant on several inputs, not one.
- **Keep your failures.** Every bad output is a future test case (chapter 9).
- **Try a second model.** A prompt that only works on one model is usually leaning on
  luck rather than clarity.

## Exercises

1. Make a call to any model API that returns the text "ok". Record how long it took and
   how many input and output tokens it used.
   *Good answer: you know where your SDK reports usage, and you have a number for latency.*
2. Ask the same open question five times with default settings. Note what varies.
   *Good answer: you can say which parts of the answer are stable and which are not.*
3. Create your experiment log with columns: date, goal, prompt version, inputs tried,
   what happened, what you learned.
   *Good answer: it exists and has its first three rows from exercises 1 and 2.*

---
Next: [1. How LLMs work](01-how-llms-work.md)
