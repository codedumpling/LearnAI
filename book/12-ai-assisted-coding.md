# 12. AI-assisted coding

## After this chapter you will be able to

- Delegate coding tasks to an agent so that the result is usable the first time.
- Set up a repository so agents work well in it.
- Review agent-written code efficiently, and recognize the typical failure modes.

## The shift

A coding agent reads your repository, runs commands, edits files and iterates on test
results. That changes your job from writing every line to **specifying, steering and
verifying**. The engineers who get the most from these tools are not the ones who type
the cleverest prompts. They are the ones who are clearest about what they want and most
disciplined about checking what they got.

Everything from chapters 2 and 3 applies. This chapter adds what is specific to code.

## Levels of delegation

| Mode | You do | The agent does | Good for |
|---|---|---|---|
| **Autocomplete** | Write code | Finish the line or block | Boilerplate, familiar patterns |
| **Chat** | Ask, copy, adapt | Explain, sketch, draft | Learning, design discussion, snippets |
| **Supervised agent** | Brief, watch, approve steps | Read, edit, run in your working tree | Features and fixes you will review closely |
| **Autonomous agent** | Brief, then review the result | Work alone in an isolated branch or sandbox, possibly for hours | Well-specified tasks with a clear check |
| **Parallel agents** | Split work, review several results | Several tasks at once in separate worktrees | Independent tasks; migrations; wide refactors |

Move down the table as the task becomes better specified and the verification becomes
more automatic. A task with a failing test that must pass is far safer to delegate
fully than "make the dashboard nicer".

## Briefing a coding task

A complete brief has five parts:

```text
1. Goal and why      What this is for and who benefits.
2. Starting points   Files, functions, docs, the error, the failing test.
3. Constraints       What must not change; conventions; dependencies allowed.
4. Definition of done  The command that proves it works.
5. What to hand back  Summary, open questions, what was not verified.
```

**Bad prompt**
```text
Add dark mode.
```
**Why it fails**: Unknown scope, unknown existing theming approach, no check. You will
get a plausible implementation that ignores the design system you already have.

**Better prompt**
```text
Add a dark theme to the settings page as the first step of a wider rollout. Users
have asked for it for night-time use.

Starting points: colors are CSS variables in src/styles/tokens.css; components should
already use them. The theme toggle goes in src/pages/Settings.tsx next to the
language picker.

Constraints: don't add dependencies. Don't hard-code colors in components; if you
find any, replace them with tokens and list them for me. Persist the choice in the
existing user-preferences store, not localStorage. Respect the OS setting by default.

Done when: `npm test` and `npm run lint` pass, and the settings page renders
correctly in both themes (take a screenshot of each).

Tell me which components you had to fix and anything that looked wrong but was out
of scope.
```
**What changed**: The agent knows where things are, what it must not do, and how to
prove completion.

### Plan first for anything non-trivial

Ask for a plan before code: "Read the relevant files and propose an approach. Don't
edit yet." Reading a ten-line plan takes a minute and can save an hour of work in the
wrong direction. Correct the plan, then let it run.

### Give it a way to check itself

The single biggest improvement to agent output is a feedback loop: tests, a type
checker, a linter, a build, a script that exercises the feature, a screenshot. An agent
that can run a check will iterate to green. An agent that cannot will hand you
something that merely looks right.

For a bug: first have it write a failing test that reproduces the bug, then fix it.

## Preparing the repository

Agents begin each session knowing nothing about your project. A **context file** at the
repository root fixes that. Different tools read different filenames (`CLAUDE.md`,
`AGENTS.md`, and tool-specific rule files); the content is what matters.

```markdown
# Project: payments-api

## Commands
- Install: `pnpm install`
- Test one file: `pnpm test path/to/file.test.ts`   (prefer this over the full suite; it's slow)
- Lint + types: `pnpm check`

## Layout
- src/routes/   HTTP handlers, thin
- src/domain/   business logic, no I/O
- src/adapters/ database and external services

## Conventions
- Money is integer cents, in a `Money` type. Never floats.
- Errors: throw `DomainError` subclasses; routes map them to HTTP codes.
- New endpoints need a test in tests/routes/ and an entry in docs/api.md.

## Gotchas
- `legacy_export/` is used by a cron job outside this repo. Don't delete "unused" code there.
- Tests need the local database: `pnpm db:up` first.
```

Guidelines:

- **Short and specific.** Include what an agent could not work out from the code, and
  what it would get wrong. Leave out what it can read for itself.
- **Commands first.** How to build, test and lint is the highest-value content.
- **Maintain it.** When an agent makes a mistake a note would have prevented, add the
  note. When a note is stale, delete it.
- **Explain reasons.** "Don't delete code in legacy_export, because a cron job uses it"
  generalizes; a bare prohibition does not.

Beyond the context file, the things that help agents are the things that help new
human engineers: fast tests, one-command setup, clear module boundaries, consistent
naming, accurate docs.

## Working patterns

- **Small steps, frequent commits.** Commit after each working step so you can inspect
  and revert cleanly.
- **Fresh context for fresh tasks.** Long sessions accumulate irrelevant history. Start
  a new session for a new task, and carry state forward in files (a plan, a notes file).
- **Correct early.** If the first steps are heading the wrong way, stop it and
  redirect. Do not wait to see how it turns out.
- **Reset when it loops.** After two failed corrections, start fresh with a better
  brief that includes what you learned. Arguing with a confused context rarely works.
- **Explore, then build.** Use the agent to map unfamiliar code ("how does auth flow
  through this service? cite files") before asking for changes.
- **Parallelize independent work** in separate worktrees or sandboxes. Watch for tasks
  that touch the same files.
- **Use a second reviewer.** A fresh agent that sees only the diff and the requirement
  catches things the author-agent misses.

## Reviewing agent code

Review is now where most of your attention goes. You remain responsible for what you merge.

**Check the claim first.** Did it do what was asked? Run the check yourself. "Tests
pass" should be a thing you saw.

**Then look for the characteristic failures:**

| Failure mode | What it looks like |
|---|---|
| **Gaming the check** | Test weakened, skipped, or expectation edited to match buggy output; special-casing the test input |
| **Over-engineering** | New abstractions, config options and helpers nobody asked for |
| **Scope creep** | Unrelated files reformatted or "improved" |
| **Invented APIs** | A method or package that does not exist; verify unfamiliar calls |
| **Defensive clutter** | try/except around everything; fallbacks that hide real errors |
| **Ignored conventions** | A second way of doing something the codebase already does one way |
| **Stale assumptions** | Deprecated library usage from older training data |
| **Unverified success** | "This should work now" without having run anything |
| **Security slips** | String-built SQL, secrets in code, missing authorization checks |

**Read the diff, not the summary.** Summaries are written by the same process that
wrote the code and inherit its blind spots.

**Size the review to the risk.** A one-line copy fix needs a glance. A change to
authentication needs you to understand every line.

**Ask the agent to explain** anything you do not follow. If the explanation does not
hold together, neither does the code.

## When not to delegate

- You cannot say what "done" means. Clarify first; the agent cannot do that for you.
- You could not evaluate the result. Delegating what you do not understand produces
  code nobody understands.
- The change is irreversible or touches production data, without a review step.
- Describing the task takes longer than doing it.
- You are trying to learn the thing. Use the agent as a tutor, not a ghostwriter.

## Keeping your own skills

Using agents well depends on judgment that comes from having written and debugged code
yourself. To keep building it:

- Predict what the fix will be before reading the agent's.
- Ask "why" until you could have written it.
- Do some work by hand deliberately, especially in areas you are weak in.
- Treat every surprising agent mistake as a question: what did it not know, and should
  that be in the context file?

## Common mistakes

- One-line briefs for multi-file work.
- No runnable check, so "done" means "looks done".
- Merging on the strength of the summary.
- A context file that is either empty or two thousand lines of everything.
- Letting a session run for hours across unrelated tasks.
- Accepting a passing test suite without looking at whether the tests changed.
- Blaming the tool for a brief a human could not have followed either.

## Exercises

1. Write a context file for a repository you work in. Keep it under 60 lines.
   *Good answer: a new teammate could build, test and avoid the top three pitfalls from it alone.*
2. Take a task from your backlog and write the five-part brief. Have a colleague read
   it and list their questions.
   *Good answer: their questions become additions to the brief.*
3. Give an agent a bug with the instruction to reproduce it in a failing test first.
   *Good answer: a commit history showing red test, then fix, then green.*
4. Review an agent-produced diff against the failure-mode table. Record what you find.
   *Good answer: at least one concrete finding or a justified "none", with the check you ran yourself.*
5. Run the same task twice: once with a one-line prompt, once with a full brief.
   Compare the diffs and the time you spent reviewing.
   *Good answer: a specific account of what the brief prevented.*

---
Previous: [11. Security and safety](11-security-and-safety.md) · Next: [13. The tools landscape](13-tools-landscape.md)
