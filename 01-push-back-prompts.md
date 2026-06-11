# 01. Push-back prompts

How to get the AI to disagree with you when you're wrong, instead of building the wrong thing.

## The standing instruction

Add this to your AGENTS.md / CLAUDE.md / system prompt at the project level:

```
Disagree when you have a specific technical reason to disagree.
Do not soften disagreement with "you could also" or "another option might be."
State the reason in one sentence, propose the alternative in one sentence,
ask which I want before writing code.

If I am wrong about how a tool, API, or library behaves, say so directly.
If I am wrong about the problem itself (the right fix is upstream), say so.
If I push back without a technical reason, hold your position with the reason.
```

## When to use the explicit push-back prompt mid-task

When you've asked for something and want to know if the AI would actually do it differently. Drop this into chat:

```
Before you implement this, tell me:
1. The one thing in my request that is most likely to be wrong.
2. The decision I made without realizing I made one.
3. What you would do differently if it were your project, in one sentence.
Wait for my answer before writing code.
```

## Specific push-back prompts by domain

### Database schema design

```
Before you write the migration, identify:
- Any field I made nullable that shouldn't be.
- Any field I made non-nullable that should be.
- Any composite uniqueness constraint I forgot.
- Any indexing decision I am implicitly making by column order.
Then propose the schema you would actually use.
```

### API design

```
Before you write the endpoint, tell me:
- The HTTP method I picked, and whether it's the right one for this operation.
- The status code I implicitly want for success and error cases.
- The auth model (or lack of one) I am assuming.
- The pagination/filtering strategy I have not specified.
Push back if my framing is wrong.
```

### Frontend component design

```
Before you write this component, point out:
- Where I am putting business logic in a UI component that belongs in a hook or service.
- Where I am prop-drilling something that should be context.
- Where I am making this controlled when it should be uncontrolled (or vice versa).
- Where I am about to re-implement something the framework already does.
```

### Refactoring requests

```
Before you refactor, answer:
- Is this refactor solving a real problem or making it pretty?
- Does the refactor change behavior, even subtly?
- Which existing tests would catch a behavior regression? If none, stop and write tests first.
Refuse to refactor if the answer to question 1 is "making it pretty."
```

## The single-line push-back trigger

When you want a quick gut-check without changing the workflow, drop this into any prompt:

```
What's wrong with what I just asked for?
```

If the AI's first response is "Nothing, that looks good!" without a real reason, repeat:

```
Are you sure? What's the second-order problem?
```

## When push-back is correct vs sycophantic

Correct push-back has a specific technical reason. Sycophantic agreement-disguised-as-push-back looks like:

> "Great question! There are a few ways to think about this..."

That's not push-back. That's the AI buying time.

Correct push-back looks like:

> "Your `users.email` field is nullable but you have a UNIQUE constraint on it. That means two NULL rows will conflict in MySQL and not in Postgres. Make it NOT NULL or drop the constraint."

Train the AI to do the second by rejecting the first explicitly.
