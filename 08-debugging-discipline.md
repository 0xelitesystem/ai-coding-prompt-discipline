# 08. Debugging discipline

Structured debugging with AI without falling into a rabbit hole of attempted fixes.

## The debugging spiral

The default AI debugging behavior:

1. You report a bug.
2. AI proposes a fix without reading enough code.
3. Fix does not work.
4. AI proposes another fix, more invasive.
5. Fix does not work; introduces a new bug.
6. AI rewrites the function.
7. Function works but does something different now.
8. Original bug still there.

The discipline below stops this at step 2.

## Standing instruction

```
For any debugging task:
1. Read the error message exactly as reported. Do not paraphrase.
2. Identify the file and line number the error points to.
3. Read that file. Read the function containing the error.
4. State, in one paragraph, what you believe is happening before proposing any fix.
5. List the assumptions you are making. Mark each as "verified" or "unverified."
6. Verify the unverified assumptions before writing code.
7. Propose ONE change. Show the diff. Wait for me to apply and report results.
```

## The "what the error actually says" prompt

```
Read the error message I just pasted. Tell me, word by word:
- The exception type
- The message text
- The file and line
- The stack trace, in order from innermost to outermost

Do not interpret yet. Just transcribe what is there.
```

This catches misreading. Half of bad fixes start with "I think the error is about X" when the error is actually about Y.

## The "isolate before fixing" prompt

```
Before proposing a fix, reproduce the bug in the smallest possible test case.
Either:
- Write a failing test that demonstrates it, OR
- Describe the exact sequence of inputs that triggers it.

Do not write a fix without an isolated reproduction.
```

## The "one variable at a time" rule

```
Change exactly one thing per attempt.
After each change:
- I run it and report the result.
- You either commit the change or revert it.
- Do not bundle multiple speculative changes in one diff.
```

## The "stop guessing" prompt

When the AI is on attempt 3+ without progress:

```
You are guessing. Stop.

List:
- Every assumption you are making about the failing system.
- Which assumptions you have actually verified (by reading code or testing).
- Which you have not verified.

Verify all unverified assumptions before proposing the next change.
```

## Specific debugging prompts

### When the error message is generic

```
The error message is generic ("Something went wrong").
We need a better signal. Either:
- Add logging at specific points to narrow down where it fails
- Use a debugger breakpoint
- Read the source of the library to find what actually throws this message

Pick one. Tell me which and why.
```

### When the bug is intermittent

```
This bug is intermittent. Possible categories:
- Race condition (concurrent access)
- Time-of-day effect (cron, midnight rollover)
- Environment-dependent (one machine vs another)
- Cache-dependent (works after cache warm, fails cold)
- Data-dependent (specific row triggers it)

Before proposing a fix, narrow down which category.
```

### When the bug is "it works on my machine"

```
Bug reproduces in [env A], not in [env B].
Compare:
- Versions of all relevant tools and libraries
- Environment variables that affect behavior
- File system case sensitivity
- Timezone
- Locale
- Available memory/CPU
- Network conditions

List the differences. The bug is in the smallest difference.
```

### When the bug appeared after an "unrelated" change

```
The bug appeared after [change X], even though X looks unrelated.
Possible mechanisms:
- X changed an import order, affecting initialization
- X changed a transitive dependency version
- X changed a global state setup
- X exposed a pre-existing bug by changing timing

Look at the actual diff. Walk through what each line could affect.
```

### When the bug is in a library

```
This bug looks like it is in [library].
Before assuming the library is broken:
- Check the library's issue tracker for similar reports
- Check whether we are using the library correctly per the docs
- Construct a minimal repro using only the library, without our code
- Confirm the bug is in the library, not in our usage

If it is genuinely a library bug, decide: pin to a working version, patch with a fork, or work around in our code.
```

## The "post-mortem" prompt

After fixing a bug:

```
Now that the bug is fixed:
- What was the actual cause?
- What was my first hypothesis, and why was it wrong?
- What would have helped catch this earlier (test, type, assertion, logging)?
- Is there a class of similar bugs likely lurking elsewhere?

One paragraph each.
```

This is the only time you want the AI to be expansive in debugging. Use it to extract durable lessons.

## Common debugging anti-patterns to refuse

- **"Let me add some logging and try again"** without saying what specific log line will discriminate between hypotheses.
- **"It might be a race condition"** without evidence.
- **"Let me try a different library"** without confirming the current library is the cause.
- **"This sometimes happens"** as a non-explanation.
- **"I'll add a retry"** to mask the underlying error.

When you see these, respond with the "stop guessing" prompt.
