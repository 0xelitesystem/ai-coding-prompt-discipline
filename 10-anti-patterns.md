# 10. Anti-patterns

User-side mistakes that reliably produce bad AI coding output. Avoid these.

## Anti-pattern 1: vague intent, specific code request

```
"Make this faster"
"Clean this up"
"Add error handling"
"Refactor this"
```

The AI guesses what "faster" / "cleaner" / "errors" / "refactor" means and produces something arbitrary.

**Fix:** Be specific about the target.

```
"This query is 800ms on a table with 2M rows. Reduce to under 100ms.
Acceptable changes: index changes, query rewrite, materialized view.
Not acceptable: caching, schema migration."
```

## Anti-pattern 2: the missing context drop

Pasting a code snippet with no project context, then expecting an idiomatic fix.

```
"Fix this:
[200-line file with no explanation]"
```

The AI infers the project from the snippet and gets it wrong.

**Fix:** Drop a context block first (see 07-context-priming.md). Then paste the code.

## Anti-pattern 3: the cumulative chat tax

By message 40, the AI has lost track of what's important. You keep referring to "that thing we built earlier" and the AI keeps confidently citing the wrong version.

**Fix:** Periodically refresh:

```
Re-read your last 3 messages. Tell me the current state of the code.
If your understanding differs from what I have on disk, tell me where.
```

Or just start a new session with the relevant files re-pasted.

## Anti-pattern 4: the rolling rewrite

You ask for one change. The AI changes one thing but also rewrites everything else. You don't notice. You ask for the next change. The AI rewrites again. Three messages in, the code is unrecognizable.

**Fix:** Diff every response. Reject anything that touches code outside the requested change.

```
You changed lines I did not ask you to change.
Revert all changes except [specific change requested].
```

## Anti-pattern 5: the false-progress trap

```
AI: "Done! I've implemented X."
You: [doesn't check]
You: "Great, now build Y on top."
AI: [builds Y on top of broken X]
```

By the time you notice X was incomplete, you have a tower of code resting on a placeholder.

**Fix:** Verify every "done" before moving on.

```
Show me the implementation, not a summary.
Show me a sample input and the output.
Show me what happens with an edge case.
```

## Anti-pattern 6: the agreement loop

```
You: "I think X is wrong because Y."
AI: "You are absolutely right! Let me fix that."
You: [accepts]
[X was not wrong; AI just agreed with you]
```

The AI agreeing with your hypothesis is not evidence the hypothesis is correct.

**Fix:** Test for sycophancy.

```
Before changing anything: explain in your own words why my hypothesis is correct.
If you cannot, tell me. Do not change code to match my guess if my guess is wrong.
```

## Anti-pattern 7: the credentialed user trap

You mention you are a senior engineer / expert in X / 20 years experience. The AI ramps down its caution because "you must know what you are doing."

**Fix:** Do not credential yourself. Or do, then explicitly request:

```
Ignore my background. Treat my request the same way you would treat a junior engineer asking the same thing.
Verify assumptions. Ask questions. Push back where you would push back on anyone else.
```

## Anti-pattern 8: the multi-question dump

```
"Hey can you refactor this to use the new API, also add tests, also update the docs, also there is a bug in the related module that you should look at?"
```

The AI does some of it, badly, in one big response.

**Fix:** One thing at a time.

```
We will do four things in order. Tell me when ready, I will give you the first.
1. Refactor to new API
2. Add tests
3. Update docs
4. Fix bug in related module (after I review)
```

## Anti-pattern 9: the no-revert habit

You let the AI make changes you don't fully understand, then discover later they broke something.

**Fix:** Use version control. Commit before any AI-assisted change. Revert if the change is bad. Do not try to "fix forward" through 5 more AI messages.

## Anti-pattern 10: the "explain it to me" trap

```
You: "Explain how X works."
AI: [long explanation that may be wrong]
You: [now trusts the explanation]
You: "Now build Y based on that."
AI: [builds Y based on a wrong explanation]
```

Explanations are not verification. The AI's explanation may be plausible and wrong.

**Fix:** When the explanation matters, verify against authoritative docs.

```
You told me X works like Y.
Cite the specific docs URL or source file that confirms this.
If you cannot, mark this as unverified and we will check before depending on it.
```

## Anti-pattern 11: the bored prompt

After a long session you start writing terse, vague prompts because you assume the AI "knows what I mean."

```
"now do the other thing"
"yeah do that"
"like before"
```

The AI fills in the blank wrong about a third of the time.

**Fix:** Even when tired, write one full sentence specifying what you want. The energy spent is much less than the energy spent unwinding a wrong inference.

## Anti-pattern 12: pasting AI output as new requirements

```
You: "Implement X"
AI: [implements X with some details you didn't specify]
You: "OK now also implement Y" (assuming the same details)
AI: [implements Y with different inferred details]
```

The implicit defaults the AI used for X are not carried into Y automatically.

**Fix:** When inferred decisions matter, surface them explicitly.

```
For X you assumed [decisions A, B, C].
For Y, use the same A, B, C.
```

## Anti-pattern 13: the runaway TODO

The AI writes `// TODO: implement this` and you ship it because the function "compiled." Six weeks later production crashes on the TODO.

**Fix:** Block TODOs from getting merged.

```
Grep for TODO before any commit.
Either implement, or convert to a tracked issue with a real owner and date.
"// TODO" alone in a diff is a blocker.
```

## Anti-pattern 14: the new-deps default

Whenever you ask "how do I do X", the AI's first answer is "install library Y."

**Fix:** Set a no-new-deps default.

```
Default to standard library and existing project dependencies.
If you want to recommend a new dependency, justify:
- What it solves that we cannot do in 30 lines of our own code
- Its size (bundled / installed)
- Its last commit date and weekly downloads
- Its security posture (any CVEs in the last 12 months)
- The cost of maintaining it ourselves vs depending on it

Then ask before adding.
```

## Anti-pattern 15: the lonely AI

Working with an AI for hours without human review. Your judgment drifts in directions you would catch if a colleague were watching.

**Fix:** Show AI-assisted output to a human at least once a day. Even just "read this and tell me if it looks weird." Fresh eyes catch drift.
