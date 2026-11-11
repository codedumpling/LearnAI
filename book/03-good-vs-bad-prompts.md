# 3. Good vs bad prompts

## After this chapter you will be able to

- Recognize the recurring ways prompts fail.
- Rewrite a weak prompt quickly using a small set of moves.
- Apply the same moves to coding, extraction, writing, classification and agent prompts.

## How to read the gallery

Twenty pairs, grouped by task. Each has the same four parts. Read **What changed**
most carefully: it is the bit that transfers to your own work.

The same handful of moves recur:

| Move | Short name |
|---|---|
| Say what the result is for | **Goal** |
| Put the needed material in the prompt | **Context** |
| Define what "done" or "good" means | **Criteria** |
| Specify the shape of the output | **Format** |
| Show varied examples | **Examples** |
| Give a way to decline or flag uncertainty | **Escape hatch** |
| Say what is in and out of scope | **Scope** |

---

## A. Coding

### 1. The bug report

**Bad prompt**
```text
My login is broken, fix it.
```
**Why it fails**: No code, no error, no definition of "broken". The model will invent a
plausible login bug and fix that.

**Better prompt**
```text
Logging in with a correct password returns HTTP 500 instead of 200. It started after
yesterday's upgrade of our session library from v3 to v4.

<error>
TypeError: Cannot read properties of undefined (reading 'regenerate')
    at login (src/auth/login.ts:42)
</error>

<file path="src/auth/login.ts">
...
</file>

Find the cause. Explain it in two or three sentences before changing anything, then
propose the smallest fix. Do not refactor unrelated code.
```
**What changed**: Context (symptom, error, code, what recently changed) and Scope
(smallest fix). "What changed recently" is the most useful single line in a bug report,
for models and for humans.

### 2. The feature request

**Bad prompt**
```text
Add caching to the API.
```
**Why it fails**: Which endpoints? What store? How stale may data be? What invalidates it?
Every answer is a guess.

**Better prompt**
```text
Add caching to GET /products/:id in src/routes/products.ts.

Why: this endpoint is 70% of our traffic and each call makes three database queries.
Product data changes a few times a day.

Requirements:
- Use the existing Redis client in src/lib/redis.ts.
- Cache for 5 minutes. Invalidate the entry when the product is updated
  (see PUT /products/:id in the same file).
- If Redis is unavailable, fall back to the database and log a warning. A cache
  failure must never fail the request.
- Add tests alongside the existing ones in tests/routes/products.test.ts.

Before writing code, tell me your plan in a few bullets and flag anything ambiguous.
```
**What changed**: Goal, Scope, Criteria. The failure-mode requirement is the kind of
thing a senior engineer knows to specify and a vague prompt silently omits.

### 3. The code review

**Bad prompt**
```text
Review this code.
```
**Why it fails**: You get a mix of style nitpicks and generic advice, with real problems
buried among them.

**Better prompt**
```text
Review this diff for a payments service. It will be deployed to production today.

Look for, in this order:
1. Correctness bugs, especially under concurrent requests.
2. Anything that could log or expose card data.
3. Missing error handling on the external calls.

Ignore style and naming; a linter handles those.

For each finding give: file and line, what goes wrong and with what input, and a
suggested fix. If you find nothing in a category, say so explicitly.

<diff>
...
</diff>
```
**What changed**: Criteria and Format. "What goes wrong and with what input" forces
concrete findings over vague worry. "Say so explicitly" distinguishes "looked, found
nothing" from "did not look".

### 4. The explanation

**Bad prompt**
```text
Explain this function.
```
**Why it fails**: The explanation's level is a guess. It often restates the code line
by line, which you could already read.

**Better prompt**
```text
I'm a backend developer comfortable with Python but new to this codebase and to
asyncio. Explain what this function is for and why it is written this way.

I can read the syntax, so skip line-by-line narration. I want to know: what problem it
solves, what would break if I made it synchronous, and anything surprising.

<code>
...
</code>
```
**What changed**: Audience. Telling the model what you already know is as valuable as
telling it what you don't.

### 5. The test request

**Bad prompt**
```text
Write tests for this.
```
**Why it fails**: You get happy-path tests that mirror the implementation, including
its bugs.

**Better prompt**
```text
Write tests for parse_invoice() below using pytest, matching the style in
tests/test_parser.py (included).

Derive the cases from the docstring and the spec excerpt, not from the implementation,
since the implementation may be wrong. Cover: a normal invoice, missing optional
fields, a negative amount, a date in each of the three accepted formats, and an empty
file.

If the spec is ambiguous about what should happen for a case, write the test with your
best reading and add a comment starting "SPEC QUESTION:".

<spec>...</spec>
<code>...</code>
<existing_tests>...</existing_tests>
```
**What changed**: Context (the spec as the source of truth) and Escape hatch. Tests
derived from code confirm the code. Tests derived from a spec check it.

---

## B. Extraction and classification

### 6. The extraction

**Bad prompt**
```text
Get the important info from this email.
```
**Why it fails**: "Important" is undefined, and the output shape will differ every time.

**Better prompt**
```text
Extract the following from the customer email below. Return JSON only.

{
  "customer_name": string or null,
  "order_id": string or null,       // format "ORD-" followed by 6 digits
  "issue_type": "billing" | "shipping" | "product_defect" | "other",
  "requested_action": string,       // one sentence, in the customer's terms
  "urgency": "low" | "normal" | "high"
}

Use null when a field is not stated; do not guess. Mark urgency "high" only if the
customer mentions a deadline, a safety issue, or a repeated failed contact.

<email>
...
</email>
```
**What changed**: Format and Criteria. Every field has a type, and the subjective one
(urgency) has a rule.

### 7. The classifier

**Bad prompt**
```text
Is this review positive or negative?
```
**Why it fails**: Mixed and neutral reviews exist. You will get essays, hedges and
inconsistent labels.

**Better prompt**
```text
Classify the product review into exactly one label:

- positive: the reviewer would recommend the product overall
- negative: the reviewer would not recommend it overall
- mixed: clear praise and clear complaints with no overall verdict
- not_a_review: spam, questions, or text unrelated to the product

Judge by the overall verdict, not by how many good or bad points are listed.

<examples>
"Battery is weak but I'd still buy it again." -> positive
"Looks great. Broke in a week. Returning it." -> negative
"Fast shipping. Screen is fine, speakers are poor." -> mixed
"Does this come in blue?" -> not_a_review
</examples>

Reply with the label only.

<review>
...
</review>
```
**What changed**: Criteria (labels defined, tie-break rule stated) and Examples chosen
to sit on the boundaries, which is where classifiers fail.

### 8. The document question

**Bad prompt**
```text
What does the contract say about termination?
```
(with no contract attached, or a 90-page one pasted before it)

**Why it fails**: Without the document the model answers from general knowledge of
contracts. With it, nothing stops paraphrase drifting from what the text says.

**Better prompt**
```text
<contract>
...
</contract>

Using only the contract above, answer: under what conditions can either party
terminate, and what notice is required?

First quote the relevant clauses word for word with their section numbers. Then give
your answer in plain English based on those quotes. If the contract is silent on
something I asked about, say that it is silent rather than describing what is typical.
```
**What changed**: Grounding. Quote-then-answer makes the model find the evidence first
and makes the answer checkable.

### 9. The data cleanup

**Bad prompt**
```text
Clean up this CSV.
```
**Why it fails**: "Clean" could mean anything from trimming whitespace to deleting rows.

**Better prompt**
```text
Normalize the CSV below. Rules:
- Dates to ISO 8601 (YYYY-MM-DD). Input dates are US-style (MM/DD/YYYY).
- Phone numbers to E.164, assuming US (+1) when no country code is given.
- Trim whitespace; collapse internal runs of spaces to one.
- Do not delete, merge or reorder rows.

If a value cannot be normalized confidently, leave it unchanged and add its row number
and reason to a list after the CSV under the heading "UNRESOLVED".

Return the CSV in a code block, then the UNRESOLVED list.
```
**What changed**: Criteria and Escape hatch. The rule "leave it unchanged and report it"
turns silent corruption into a review queue.

---

## C. Writing and summarizing

### 10. The summary

**Bad prompt**
```text
Summarize this meeting transcript.
```
**Why it fails**: A summary for whom, to do what? You get a chronological retelling.

**Better prompt**
```text
Summarize this meeting for the engineers who were not there. They need to know what
was decided and what they personally have to do, and will not read more than a screen.

Structure:
1. Decisions (each with who decided and the reason, in one line)
2. Action items (owner, task, due date if stated)
3. Open questions

Leave out discussion that did not lead to a decision. If an action item has no clear
owner, write "owner: unassigned" rather than picking someone.

<transcript>
...
</transcript>
```
**What changed**: Goal and Format. The reader's need dictates what the summary keeps.

### 11. The email

**Bad prompt**
```text
Write an email to the client about the delay.
```
**Why it fails**: The model knows neither the facts nor the relationship, so it writes a
generic apology with invented details.

**Better prompt**
```text
Draft an email from me (project lead) to Dana, our contact at Northwind. We've worked
together for two years; the tone between us is direct and friendly, first names.

Facts:
- The data migration will finish on 21 March, not 7 March.
- Cause: their legacy export had inconsistent customer IDs, which we found on Monday.
- We've already fixed 80% with a script; the rest need manual review.
- Nothing is needed from her team.
- Go-live moves from 10 March to 24 March.

Lead with the new date. Explain the cause without blaming their team. Under 150 words.
No "I hope this finds you well".
```
**What changed**: Context (facts and relationship). The model cannot know facts you
have not told it, and will invent them if the email needs some.

### 12. The rewrite

**Bad prompt**
```text
Make this better.
```
**Why it fails**: Better how? The model will change everything a little, including what
you liked.

**Better prompt**
```text
Edit this paragraph for a technical blog read by working engineers.

Problems I see: the main point arrives in the last sentence, and the middle repeats
itself. Fix those. Keep my wording where it already works, keep the two code terms
exactly as written, and keep it at roughly the same length.

After the edited version, list the changes you made in three bullets or fewer.

<paragraph>
...
</paragraph>
```
**What changed**: Criteria and Scope. Saying what to preserve matters as much as saying
what to fix.

### 13. The brainstorm

**Bad prompt**
```text
Give me some names for my app.
```
**Why it fails**: You get the most average names possible, because nothing pushes away
from the average.

**Better prompt**
```text
I need name candidates for a budgeting app aimed at people in their twenties who find
finance apps intimidating. Tone: calm, a bit playful, not corporate.

Constraints: one or two words, easy to say aloud, no "fin", "pay", "wallet" or "ly".

Give 20 candidates across four different directions (for example: nature, everyday
objects, invented words, verbs), five per direction. One line each on why it fits.
Then pick your top three and say what worries you about each.
```
**What changed**: Context, plus explicitly asking for **variety across directions** and
for self-critique. Asking for many options in distinct groups is how you escape the
average.

### 14. The tone request

**Bad prompt**
```text
Write it in our brand voice.
```
**Why it fails**: The model has never seen your brand voice.

**Better prompt**
```text
Write the announcement in our brand voice. Here are three things we have published
that capture it:

<samples>
...
</samples>

What they share: short sentences, second person, concrete numbers instead of
adjectives, and one dry joke at most. Match those qualities. Do not reuse phrases
from the samples.
```
**What changed**: Examples, with the common thread named and a guard against copying.

---

## D. System prompts and agents

### 15. The system prompt

**Bad prompt**
```text
You are a helpful customer support assistant. Be nice and answer questions.
```
**Why it fails**: It gives no information the model did not already have. Every real
question (what product, what policies, when to escalate) is unanswered.

**Better prompt**
```text
You handle customer support chat for Ledgerly, an invoicing app for freelancers.

Who you are talking to: mostly non-technical self-employed people, often stressed
because an invoice or payment is involved. Be warm, plain and brief.

What you can do: answer questions from the help articles provided in each turn, look
up an account with the lookup_account tool, and open a ticket with create_ticket.

What you cannot do: issue refunds, change billing, or give tax advice. For those,
open a ticket and tell the customer a human will reply within one business day. We
route these to people because they involve money and legal exposure.

If the help articles do not answer the question, say you are not sure and open a
ticket. A wrong answer about someone's invoices is worse than a slower right one.

Reply in the customer's language. Use short paragraphs, no headings, since replies
appear in a small chat widget.
```
**What changed**: Everything it now contains is situational: audience, capabilities,
limits with reasons, the uncertainty policy, and the display surface.

### 16. The tool description

**Bad prompt**
```text
name: search
description: Searches.
parameters: { q: string }
```
**Why it fails**: The model must guess what is searched, what query style works and
what comes back. It will call it at the wrong times with the wrong inputs.

**Better prompt**
```text
name: search_help_articles
description: Full-text search over Ledgerly's public help articles. Use this whenever
  the customer asks how to do something in the product. Returns up to 5 articles with
  title, URL and a 200-word excerpt. Does not search customer accounts or invoices;
  use lookup_account for those. Works best with a few keywords ("recurring invoice
  schedule"), not a full question.
parameters:
  query: string   // 2-6 keywords
  product_area: "invoices" | "payments" | "account" | "integrations" (optional)
```
**What changed**: A tool description is a prompt. It needs what the tool does, when to
use it, when not to, what it returns, and how to call it well.

### 17. The agent task

**Bad prompt**
```text
Clean up the repo.
```
**Why it fails**: An agent with file access and this instruction might delete things
you needed. There is no boundary and no stopping point.

**Better prompt**
```text
Remove dead code from src/billing/. Goal: we're about to onboard two engineers to
this module and want it easier to read.

In scope: unused functions, unused imports, commented-out blocks.
Out of scope: anything outside src/billing/, public API signatures, and the
legacy_export module (still used by a cron job that static analysis won't see).

Before deleting a function, search the whole repo for references, including strings
and config files. If you're unsure whether something is used, leave it and list it.

Done when: the test suite passes and you've given me a list of what was removed and
what you left because you were unsure. Don't commit; I'll review the diff.
```
**What changed**: Scope, Criteria (a definition of done) and what requires human
sign-off. Agents need boundaries more than chat prompts do, because they act.

### 18. The delegation to a sub-agent

**Bad prompt**
```text
Research the auth bug.
```
**Why it fails**: The sub-agent starts from zero. It does not know what you have
already ruled out and will repeat that work, then return an unusable essay.

**Better prompt**
```text
Users are intermittently logged out after about 10 minutes. I've ruled out token
expiry (tokens last 24h, verified in the config) and the load balancer (sticky
sessions are on).

Investigate whether the session store could be evicting entries. Look at
src/session/store.ts and the Redis config in infra/redis.conf. You only need to
read; don't change anything.

Report back: (1) yes/no on whether eviction can explain it, (2) the evidence with
file and line, (3) anything you could not verify. Mark clearly what you confirmed by
reading versus what you're inferring.
```
**What changed**: Context (what is ruled out), Scope (read only) and Format for the
reply. A sub-agent's brief is its entire world.

---

## E. Getting better answers

### 19. The leading question

**Bad prompt**
```text
Don't you think microservices would fix our scaling problems?
```
**Why it fails**: The question contains its answer. Models tend to agree with the
premise they are handed.

**Better prompt**
```text
We have a Django monolith serving 2,000 requests/second at peak. The bottleneck is
report generation, which blocks web workers for up to 30 seconds. The team is five
engineers.

What are our options for fixing this? Include options that keep the monolith. For
each, give the main cost and the main risk. Then tell me which you'd pick for a team
our size and what would change your mind.
```
**What changed**: Neutral framing with the real facts, and an explicit request for
alternatives and for the conditions under which the recommendation flips.

### 20. The everything prompt

**Bad prompt**
```text
Read this 40-page spec, find the inconsistencies, fix them, write the implementation
plan, estimate it, and draft the kickoff email.
```
**Why it fails**: Five tasks with different success criteria. Errors in step one
silently flow into the other four, and you cannot check any stage.

**Better prompt**
```text
Step 1 of 4. Read the spec below and list every place where two sections contradict
each other or where a requirement is ambiguous enough that two engineers would build
it differently.

For each: quote both passages with section numbers, and say what the conflict is in
one sentence. Don't propose fixes yet; I'll decide those with the product owner.

<spec>
...
</spec>
```
**What changed**: One task per prompt, with a human checkpoint between. Chains of small
verified steps beat one large unverified leap (chapter 5).

---

## Common mistakes

- Reading these as templates to paste. They are illustrations of moves; your prompt
  needs your context.
- Making prompts longer without making them more informative. Length is a side effect
  of supplying context, not the goal.
- Fixing a prompt for the one input in front of you. Re-test on the others.

## Exercises

1. For pairs 2, 6 and 15, name which of the seven moves each added line belongs to.
   *Good answer: every line mapped; you notice Criteria and Context do most of the work.*
2. Rewrite each of these: "Translate this." / "Is this code secure?" / "Write a SQL
   query for the sales report." State the assumptions you had to invent.
   *Good answer: your list of invented assumptions is exactly what the bad prompt was missing.*
3. Find three prompts from your own history that gave disappointing results. Diagnose
   and rewrite each, then run old and new on the same inputs.
   *Good answer: side-by-side outputs and one sentence per pair on what made the difference.*
4. Take pair 7 and remove the examples. Run both versions on 20 reviews you label
   yourself first.
   *Good answer: an accuracy number for each, and the specific reviews where they disagreed.*
5. Write a bad prompt on purpose for a task you know well, then hand it to a colleague
   to rewrite without talking to you.
   *Good answer: the questions they wished they could ask you become your checklist.*

---
Previous: [2. Prompting fundamentals](02-prompting-fundamentals.md) · Next: [4. Structured output](04-structured-output.md)
