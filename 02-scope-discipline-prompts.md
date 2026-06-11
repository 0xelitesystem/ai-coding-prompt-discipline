# 02. Scope discipline prompts

Preventing feature creep, scope expansion, and "while we're at it" rewrites.

## Standing instruction

```
Do only what I ask. Do not add features I did not request.
Do not refactor adjacent code unless I asked for refactoring.
Do not rename variables, restructure files, or change imports unless that is the task.
If you see a real bug in adjacent code, mention it in one sentence and ask before fixing.
```

## The "do less" prompt

When the AI is about to overdeliver:

```
Stop. Re-read what I asked for. Write only the code that satisfies that exact request.
No additional features, no defensive checks I did not request, no logging,
no error handling beyond what is necessary for the happy path,
no comments explaining what the code does.
```

## The "minimal viable change" prompt

For changes to existing code:

```
Show me the smallest possible diff that solves this problem.
Do not refactor the function I am editing.
Do not change the function signature unless required.
Do not rename anything.
Do not reformat anything.
Just the change.
```

## The scope boundary prompt

Before starting a multi-step task:

```
Here is the scope of this task: [LIST]
Here is what is explicitly NOT in scope: [LIST]
If you encounter something that seems related to NOT-IN-SCOPE, stop and ask.
Do not assume "while we're here, let me also..." is welcome.
```

## Specific anti-creep prompts

### When adding a function

```
Add this function. Do not create a new file unless the existing file is over 500 lines.
Do not add a class wrapper if a function is enough.
Do not export it unless I will use it elsewhere.
Do not add JSDoc beyond a one-line summary.
```

### When fixing a bug

```
Fix this bug only. Do not rewrite the buggy function.
Do not add tests for unrelated edge cases.
Do not refactor the surrounding code.
The diff should be 1 to 10 lines.
If the bug needs more than 10 lines to fix, stop and explain why before continuing.
```

### When adding a feature

```
Add only what I described. No additional configuration options.
No "while we're at it" related features.
No anticipatory error handling for cases that don't exist yet.
Make the existing tests still pass; do not invent new tests unless I ask.
```

### When reading code to make changes

```
Read these files: [LIST]
Do not read any other files unless something is unclear and you need to disambiguate.
Do not "explore the codebase" beyond the listed files.
If something is unclear, list the file you want to read and ask before reading it.
```

## How to revert a scope-creep response

When the AI has already over-delivered:

```
Throw away everything except [the specific thing I asked for].
Show me only that part.
Do not justify the parts you removed.
```

## The "single source of truth" prompt

When the AI is about to duplicate config, constants, or logic:

```
Before you add this, search the codebase for similar logic.
If similar logic exists, modify it or extract a shared utility.
Do not create a parallel implementation.
If you can't find anything similar, list what you searched for and ask if I want to see the suggested location for a shared utility.
```
