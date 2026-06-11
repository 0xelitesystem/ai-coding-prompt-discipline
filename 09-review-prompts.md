# 09. Code review prompts

Structured review prompts that catch real issues without generating noise.

## The general review prompt

```
Review this diff. For each issue:
- Severity: blocker / important / nitpick
- Location: file:line
- What is wrong
- Why it matters
- Suggested fix

Do not include style preferences as "important." Style is "nitpick" or omitted.
Do not include things the linter or type checker already catches.
Do not list things that are correct (skip "looks good" notes).
```

## The "skim first, deep dive second" prompt

For large diffs:

```
First pass: 60-second skim.
- What is this change trying to accomplish?
- Is the scope appropriate (one logical change, not several)?
- Are there any structural red flags (giant function, new abstraction without justification)?

If the first pass fails, stop. Tell me what to fix at the structural level before I waste your time on details.

Second pass: line-by-line review for issues.
```

## Domain-specific review prompts

### Security review

```
Review this code for security issues only:

- Input validation: every external input validated before use?
- Auth/authz: every protected operation checked?
- Secrets: any hardcoded credentials, API keys, tokens?
- Injection: any string concatenation building queries, commands, URLs, HTML?
- Crypto: any custom crypto? Any disabled certificate verification?
- Logging: any sensitive data in logs?
- Error messages: any internal information leaking through error responses?
- Dependencies: any new deps with known vulnerabilities?

Style and architecture issues are out of scope for this pass.
```

### Performance review

```
Review this code for performance only:

- Algorithmic complexity: anything that becomes O(N^2) or worse?
- Database: N+1 queries? Missing indexes for new queries? Large IN clauses?
- Network: unnecessary serial requests that could be parallel?
- Memory: anything loading large datasets fully into memory?
- Allocations: hot paths creating short-lived objects?

Cite the specific line. Cite the specific complexity. Do not handwave "this could be slow."
```

### API design review

```
Review this API design only:

- Resource naming: nouns? Consistent pluralization?
- HTTP methods: GET safe and idempotent? POST not idempotent? PUT/PATCH appropriate?
- Status codes: 200/201/204 used correctly? 4xx vs 5xx mapped correctly?
- Errors: consistent error response shape? Useful error codes?
- Versioning: explicit?
- Pagination: present for list endpoints? Consistent style?
- Rate limiting: addressed?

Implementation details are out of scope for this pass.
```

### Database migration review

```
Review this migration only:

- Reversibility: does it have a documented rollback path?
- Locking: any operation that holds a long lock on a hot table?
- Backfill: any large UPDATE that should be batched?
- Constraints: NOT NULL on existing tables without a default?
- Indexes: CREATE INDEX without CONCURRENTLY (Postgres) or ALGORITHM=INPLACE (MySQL)?
- Data loss: any DROP COLUMN that loses data without an explicit acknowledgment?
- Deploy ordering: does this require code to be deployed before/after the migration?

The migration may be unsafe even if it validates syntactically. Be paranoid.
```

### Test review

```
Review these tests only:

- Do they actually test the behavior, or just exercise the code path?
- Would they catch the bug they are supposed to prevent?
- Are they mocking the system under test?
- Are they testing implementation details (will break on harmless refactors)?
- Are setup and teardown explicit, or is there shared mutable state?
- Are the test names clear about what behavior is being verified?
- Do failing tests give clear messages, or just "expected true, got false"?

Skip "you should also test X" unless X is a critical missing case.
```

## The "show me the worst part" prompt

For sessions where you want the highest-signal feedback:

```
This is a long diff and your time is short.
Show me only the single worst issue in this code.
- Where it is
- Why it is the worst
- What to do about it

I will fix it, then come back for the next worst issue.
```

This forces prioritization and avoids the wall-of-feedback that nobody reads.

## The "what would future-me complain about" prompt

```
Imagine you are reading this code 6 months from now, having forgotten everything about why it was written.
What will be confusing?
What will look like a mistake even if it is correct?
What lacks the comment that would have explained it?

Add only the minimum comments needed to prevent that future confusion.
Do not add comments restating what the code does.
```

## What NOT to ask in a review

Bad review prompts:
- "Make this better" (no signal; produces churn)
- "Refactor for clarity" (clarity is subjective; produces churn)
- "Add error handling" (where? what kind? what does it do?)
- "Add tests" (for what behavior?)

Good review prompts have a specific question and a specific scope.

## The reviewer-as-teacher prompt

When you genuinely don't know what's right:

```
I am unsure whether [specific pattern] is idiomatic in [language/framework].
Tell me:
- What the idiomatic version looks like
- Why the idiomatic version is preferred
- Cases where my version would actually be better

Do not just say "the idiomatic way is X." Explain when X is right and when it is not.
```
