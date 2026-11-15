# 2. Prompting fundamentals

## After this chapter you will be able to

- Write a prompt with all the parts a model needs.
- Diagnose a failing prompt by asking what is missing.
- Use examples, structure and output formats deliberately.

## The core idea: brief a smart colleague

Imagine handing your prompt to a capable contractor who has never seen your project,
does not know your users, and cannot ask you a question. Would they have what they need?

That test catches most prompt problems. A model is not short of intelligence. It is
short of **your context**: the goal, the audience, the constraints, what you already
tried, and what "good" looks like.

## Anatomy of a prompt

Not every prompt needs every part, but a failing prompt is almost always missing one.

| Part | Question it answers | Example |
|---|---|---|
| **Goal** | What is this for? | "This summary goes to on-call engineers deciding whether to page someone." |
| **Task** | What exactly should be done? | "Summarize the incident log below." |
| **Context** | What do you need to know? | The log, the service's normal behavior, the audience's expertise |
| **Constraints** | What are the limits? | "Under 120 words. Do not speculate about cause." |
| **Examples** | What does good look like? | One or more sample input/output pairs |
| **Output format** | What shape should the answer take? | "Three bullets: impact, current status, next action." |
| **Escape hatch** | What if it cannot be done? | "If the log does not show the impact, say so." |

### Goal: explain why, not only what

Models make hundreds of small decisions while writing. If they know the purpose, they
make those decisions the way you would.

```text
Weak:   Keep it short.
Better: Keep it under 100 words. It is read on a phone lock screen, so anything past
        the first two lines is usually not seen.
```

The second version also tells the model to put the important thing first, without you
having to say so.

### Context: put the material in

If the answer depends on a document, a schema, a stack trace or a style guide, include
it. Wrap long material in clear delimiters so instructions and data do not blur:

```text
<incident_log>
...
</incident_log>

<runbook>
...
</runbook>

Using the runbook, summarize the incident log for the on-call engineer.
```

XML-style tags work well because they are unambiguous and you can refer to them by
name. Markdown headings also work. Pick one and be consistent.

For long documents, put the documents first and the question at the end.

### Constraints: say what to do

Positive instructions work better than prohibitions.

```text
Weak:   Don't use jargon.
Better: Write for a reader who has never worked in finance. If you must use a
        technical term, define it in the same sentence.
```

When you do need a prohibition, give the reason. "Never mention competitor names,
because legal reviews every message that does and it delays the send" is followed more
reliably, and more sensibly at the edges, than a bare rule.

Avoid shouting. Modern models are attentive to the system prompt; ALL-CAPS "CRITICAL"
warnings tend to make them over-apply the rule everywhere.

### Examples: powerful and easy to overdo

Examples ("few-shot prompting") are the most reliable way to convey a format or tone.
They are also the easiest way to get near-copies.

- Give **several varied** examples, not one.
- Say what the examples have in common, so the model learns the pattern, not the surface.
- Make sure every detail of an example is something you want copied. A model will
  reproduce a typo.

### Output format: decide before you ask

If code will parse the output, say exactly what shape it should have (chapter 4). If a
person will read it, say where it will be shown: a chat bubble, an email, a terminal, a
PDF. Format follows destination.

### Escape hatch: allow "I can't"

A model with no way to decline will produce something anyway. Give it a sanctioned way out:

```text
If the contract does not state a termination date, return "not stated".
Do not infer one from other dates.
```

## System prompt vs user message

- **System prompt:** standing instructions that hold for the whole conversation. Role,
  audience, rules, tools, tone.
- **User message:** this turn's request and its data.

Put stable things in the system prompt and variable things in the messages. This also
makes prompt caching work (chapter 10), because the stable prefix can be reused.

> **Provider notes**
> Anthropic takes the system prompt as a separate top-level parameter. OpenAI uses a
> message with a `system` or `developer` role. Gemini calls it a system instruction.
> Same concept, different field.

## Roles: useful when specific

"You are a helpful assistant" adds nothing. A role helps when it carries real
information about standards and audience:

```text
You are reviewing pull requests for a payments team. The reviewers care most about
correctness under concurrency and about never logging card data.
```

That is less a costume than a compressed set of priorities.

## Let the model think

For anything with multiple steps, ask for the reasoning before the answer, or use the
model's reasoning mode. An answer written first and justified afterwards is a guess with
a rationalization attached.

```text
First work through the calculation step by step inside <working> tags.
Then give the final figure inside <answer> tags.
```

Separating working from answer also lets your code discard the working.

## Worked example

A product manager wants release notes from a list of merged pull requests.

**First attempt**

```text
Write release notes for these PRs: [list]
```

The output is a flat list restating each PR title, including refactors no user cares
about, in a tone that matches nothing the company has published.

**Diagnosis using the anatomy table**

- Goal: missing. Who reads these, and why?
- Context: missing. No past notes, no product description.
- Constraints: missing. What to include and leave out?
- Format: missing.
- Escape hatch: missing. What about PRs with unclear user impact?

**Rewrite**

```text
You are writing release notes for Ledgerly, an invoicing app used by freelancers.
Readers are customers, not developers. They skim these to learn whether anything
changed that affects how they work.

<merged_prs>
[list with titles and descriptions]
</merged_prs>

<past_release_notes>
[two previous editions]
</past_release_notes>

Write this release's notes:
- Group changes under "New", "Improved" and "Fixed".
- Describe each change by what the customer can now do, not by what the code does.
- Leave out internal changes (refactors, dependency bumps, test changes).
- Match the voice of the past release notes.
- If you cannot tell whether a PR affects customers, list it at the end under
  "Needs review" with a one-line reason, rather than guessing.
```

Every added line answers a question the model would otherwise have guessed at.

## An iteration loop

1. Write the prompt as a brief.
2. Run it on **at least five varied inputs**, including an awkward one.
3. Read every output. For each problem, ask: *what did the model not know?*
4. Fix the gap in the prompt, generally, not with a patch for that one input.
5. Re-run on all inputs. Check you did not break the ones that worked.
6. Save the inputs. They are the start of your eval set (chapter 9).

## Common mistakes

- **Too short.** Twelve words for a task that would need a page to brief a human.
- **Patching.** Adding "also don't do X" after each failure, until the prompt is a pile
  of special cases. Find the underlying misunderstanding instead.
- **One example.** The model copies it.
- **Testing on one input.** It worked once; that is not evidence.
- **Contradictions.** "Be thorough" and "be brief" with no priority between them.
- **Burying the question** in the middle of a long document.
- **Asking the model to grade its own homework** in the same call that produced it.

## Exercises

1. Take a prompt you have used recently. Mark which parts of the anatomy table it has.
   Add the missing ones and compare outputs on three inputs.
   *Good answer: a before/after with a specific statement of what improved.*
2. Rewrite these three prohibitions as positive instructions with a reason:
   "Don't be verbose." "Don't make things up." "Don't use markdown."
   *Good answer: each says what to do, and why, e.g. "Answer only from the provided
   document, and quote the sentence you relied on, because readers verify these."*
3. Write a classification prompt with one example, then with four varied examples.
   Run both on ten inputs.
   *Good answer: you can show the one-example version skewing towards that example's label or phrasing.*
4. Give the same prompt to a colleague and ask what they would need to ask you before
   starting. Add those answers to the prompt.
   *Good answer: at least two additions you would not have thought of alone.*

---
Previous: [1. How LLMs work](01-how-llms-work.md) · Next: [3. Good vs bad prompts](03-good-vs-bad-prompts.md)
