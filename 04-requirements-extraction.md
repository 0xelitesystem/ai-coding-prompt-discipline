# 04. Requirements extraction prompts

Forcing the AI to ask questions instead of assuming. Most AI failures are unstated assumptions becoming wrong assumptions.

## Standing instruction

```
Before writing code for any task that has unstated decisions,
list every decision I have not explicitly made.
Ask me to confirm or specify each one.
Do not assume defaults unless I tell you to.
```

## The "questions first" prompt

```
Before you write any code, list every question you should ask me about this task.
Categorize them:
- Blockers (you cannot proceed without an answer)
- Clarifications (you can proceed but the wrong assumption would hurt later)
- Preferences (style/library choices where any reasonable answer is fine)

For blockers, stop and wait.
For clarifications, list your default assumption and proceed; I will override if needed.
For preferences, pick and note what you picked.
```

## Domain-specific extraction prompts

### Building a new endpoint

```
Before writing the endpoint, confirm:
- Auth: required? what method? what permissions?
- Validation: which fields are required, optional, what formats?
- Idempotency: can this be safely retried?
- Rate limiting: per-user, per-IP, or none?
- Response shape: single object, paginated list, status-only?
- Error responses: which status codes for which failures?
- Logging: which fields should be logged, which redacted?
```

### Building a new database table

```
Before writing the schema, confirm:
- Primary key: integer, UUID, or composite?
- Soft delete or hard delete?
- Timestamps: created_at, updated_at, both, neither?
- Multi-tenancy: tenant_id column or separate schemas/databases?
- Indexes: which queries will hit this table most?
- Foreign keys: cascading deletes, or restrict?
- Audit: do changes need history?
```

### Building a new UI component

```
Before writing the component, confirm:
- Controlled or uncontrolled?
- Form integration: standalone, or part of a form library (react-hook-form, formik, etc)?
- Accessibility: which ARIA roles and keyboard interactions?
- Loading state: required? how rendered?
- Error state: required? how rendered?
- Empty state: required? how rendered?
- Mobile behavior: any layout changes below a breakpoint?
- Theme: light/dark, brand tokens, or hardcoded?
```

### Adding caching

```
Before adding caching, confirm:
- What's the actual problem (slow page? expensive operation? rate limit)?
- Where: in-memory, Redis, edge CDN, HTTP cache headers?
- Invalidation strategy: TTL only, manual, write-through?
- Stampede protection needed?
- What happens if cache is unavailable?
- Privacy: any per-user data that shouldn't be shared in cache?
```

## The "rewrite my request" technique

When you've written a vague prompt, do this:

```
Restate my request in your own words.
Then list every implicit assumption you are making about how to interpret it.
Wait for me to correct the restatement before proceeding.
```

The AI's restatement usually reveals exactly which words in your prompt were ambiguous.

## The "specification first" prompt

For larger features:

```
Before writing code, write a specification:
- One paragraph: what the feature does and why
- A list of inputs and their valid ranges
- A list of outputs and their formats
- A list of side effects (network calls, DB writes, file changes)
- A list of error conditions and how each is handled
- A list of test cases that prove it works

Show me the spec. Wait for approval. Then write code.
```

## The "draw it" prompt

For UI or system design:

```
Before writing code, describe the system in text:
- ASCII diagram of the components and their connections
- For each component, one sentence of responsibility
- For each connection, the data that flows across it

Show me the diagram. Wait for approval. Then write code.
```

This catches architecture mistakes before they're encoded in 500 lines.

## When the AI asks too many questions

If the AI asks 15 clarification questions when you wanted to ship something:

```
You asked too many questions. Pick reasonable defaults for everything except:
1. [The one thing you actually want to decide]
2. [The other one]
Proceed with defaults for the rest. List the defaults you picked.
```
