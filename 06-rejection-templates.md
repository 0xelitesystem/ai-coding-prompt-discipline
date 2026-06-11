# 06. Rejection templates

How to reject AI suggestions without making the next response worse. The wrong rejection makes the AI flail; the right rejection re-anchors.

## The wrong way to reject

> "No that's wrong, try again"

Result: the AI rewrites everything, often making the same mistake in a new shape, with apologies stacked on top.

## The right way to reject

Be specific about what was wrong and what you want instead.

```
The [specific thing] is wrong because [specific reason].
The right approach is [specific direction].
Try again, fix only that, leave the rest alone.
```

## Template: rejecting an over-engineered solution

```
This is over-engineered. Specifically:
- The [Class/Factory/Manager] is wrapping logic that can be 3 lines inline.
- The [Options object] has one field that's never varied.
- The [Abstraction] has one concrete user.

Rewrite as procedural code with no abstractions.
We can extract helpers later when a second use case shows up.
```

## Template: rejecting a wrong technical choice

```
You chose [X]. That's wrong because [specific reason].
Use [Y] instead.

Specifically: [one sentence explaining the choice].

Do not apologize. Do not restart from scratch. Change [X] to [Y] and adjust surrounding code only as needed.
```

## Template: rejecting a "but here is also" addition

```
I asked for [exactly this]. You also added [extra thing].
Remove [extra thing]. Keep only [exactly this].
Do not add anything else.
```

## Template: rejecting a hallucinated API

```
The function/method/option you used does not exist.

Specifically: [name] does not exist in [library/framework/version].
The actual API is [correct name].

Check the docs URL [URL] before responding.
If you cannot verify the API exists, say so and stop.
Do not invent an alternative that "should work like that."
```

## Template: rejecting an over-cautious response

When the AI adds defensive code, error handling, and validation that nobody asked for:

```
Remove the defensive code:
- The null check on [X] (it cannot be null here).
- The try/catch around [Y] (it does not throw).
- The input validation on [Z] (the type system covers it).

Show me the minimal version.
```

## Template: rejecting an explanation when you want code

```
I asked for code, not an explanation. Give me the code only.
No preamble, no postscript, no "Here is how this works."
Just the code.
```

## Template: rejecting fake progress

When the AI says "Done!" but did not actually do the thing:

```
You said this is done but [specific thing] is not implemented.
You wrote a TODO comment where the implementation should be.
Either implement [specific thing] or say "I cannot implement [specific thing] because [reason]."
Do not claim done when there are TODOs.
```

## Template: rejecting apology stacking

When the AI starts every response with "You're absolutely right, I apologize..."

```
Skip apologies. Just fix it.
```

If this happens 3+ times in a row, escalate:

```
Stop apologizing. Apologies waste tokens and signal you might be agreeing without understanding.
Respond with the fix and no preamble.
```

## Template: when the AI is right and you were wrong

The AI sometimes pushes back correctly. Acknowledge it:

```
You're right. [Brief acknowledgment of what you missed.]
Go ahead with your suggested approach.
```

This trains the model (in long contexts and in your memory file) that push-back is welcome.

## Template: rejecting a half-fix

```
Your fix addresses the symptom but not the cause.
The cause is [actual cause].
Fix the cause. The symptom should go away as a result.
If fixing the cause is out of scope, say so and we will patch the symptom explicitly with a note linking to the cause.
```

## Template: rejecting a "modernization"

When the AI rewrites old-but-working code in a newer style:

```
The old code works. Do not rewrite it in a new style for the sake of it.
Change only what is necessary to solve the actual problem.
If you want to recommend a future refactor, do that in a one-line comment, not in the diff.
```

## Template: stopping a debugging spiral

When the AI is making 5+ attempts to fix something and each attempt makes it worse:

```
Stop. Revert to the state before the first attempt.
Read the original code and the original error message.
Tell me what the error message actually says.
Tell me what you have been assuming about the error.
Do not write code until we agree on what is happening.
```
