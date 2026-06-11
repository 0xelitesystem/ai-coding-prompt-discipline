# 07. Context priming templates

Context blocks that produce better AI output. Most bad output comes from the AI inferring missing context.

## The minimum context block

Drop this at the top of any coding session:

```
## Project context

Stack: [list languages, frameworks, key libraries with versions]
Conventions: [list relevant style decisions: tabs/spaces, quote style, import order, naming]
Boundaries: [list what is in scope; list what is explicitly out of scope]
Audience: [who reads/runs this code; what their level is]
Constraints: [list real constraints: deployment target, browser support, perf budget, no-new-deps rule]
```

## The expanded context block

For longer sessions or unfamiliar projects:

```
## Project context

### Stack
- Language: [e.g. TypeScript 5.4 strict]
- Runtime: [e.g. Node 20]
- Framework: [e.g. Next.js 14 App Router]
- Database: [e.g. Postgres 15 + Drizzle ORM]
- Testing: [e.g. Vitest + Playwright]
- Linter/formatter: [e.g. ESLint + Prettier with config X]

### Conventions
- Import style: [absolute vs relative; order]
- Function style: [arrow vs declaration]
- Async style: [async/await vs promises]
- Error handling: [throw vs Result types vs return tuples]
- Naming: [camelCase functions, PascalCase types, SCREAMING_SNAKE constants]

### Architecture
- [Brief description of the layers and where new code belongs]
- [Brief description of what is NOT to be touched]

### Anti-conventions
- [Things people often add that we don't want]
- [Patterns to avoid]

### Out of scope
- [Things that look related but are not]
```

## The "read this first" priming prompt

Before any task in an existing codebase:

```
Before writing any code, read these files in this order:
1. [README.md or AGENTS.md]
2. [The directory you will be editing]
3. [One representative existing implementation of similar functionality]

Tell me what you found that informs how you would approach my request.
Then ask any questions before writing code.
```

## Domain-specific priming

### Frontend project

```
This is a [React/Vue/Svelte] project using [Tailwind/CSS Modules/Emotion].
The component system uses [design tokens from file X / inline values].
We do not use [ShadCN / DaisyUI / specific lib] for styling unless asked.
Accessibility: every interactive element needs keyboard handlers and ARIA roles.
Tests: [Testing Library + Vitest / Cypress component tests].
No new dependencies without asking.
```

### Backend API project

```
This is a [Node Express / Fastify / Hono / Python FastAPI / Go net/http] project.
Authentication: [JWT / session / API key] handled by middleware in [path].
Validation: [Zod / Joi / Pydantic / manual] at the route handler entry.
Errors: throw a [HttpError class] with status code; the global error handler in [path] formats responses.
Database: [direct ORM / repository pattern / service layer] in [path].
Logging: [pino / winston / structlog] via the logger at [path].
No new dependencies without asking.
```

### Data pipeline

```
This is a [Python / TypeScript / Go] data pipeline.
Storage: [S3 / GCS / local] for inputs, [BigQuery / Postgres / Parquet on S3] for outputs.
Orchestration: [Airflow / Dagster / cron / Temporal].
We do NOT use Pandas for files over [size]; we use [Polars / DuckDB / SQL] instead.
Idempotency is required; jobs must be safely retryable.
No new dependencies without asking.
```

## The "look at this example" priming

When introducing the AI to a project pattern:

```
Here is how we do [pattern] in this project: [code example or file reference]
When you build [thing], match this style exactly.
If your version differs, point out the difference and ask whether to deviate.
```

## The "do not assume" priming

```
For this task, do not assume:
- Any HTTP client library (use the one we already have)
- Any logging style (match existing logs)
- Any directory layout (use the existing structure)
- Any test framework (match existing tests)

When in doubt, find an existing file that does something similar and match its style.
```

## How to refresh context mid-session

After 30+ messages, drop this:

```
Re-read AGENTS.md and the most recent code I asked you to write.
Tell me, in one paragraph, what you understand the task to be right now.
Wait for my correction before proceeding.
```

The AI loses context drift over long sessions; explicit refresh re-anchors.

## Avoiding context bloat

Do NOT paste:
- 2000-line files when 50 lines would do
- Entire databases of examples (pick 3 representative ones)
- Multiple competing style guides
- Every config file in the repo

Context costs tokens and degrades focus. The minimum useful context is better than the maximum available context.
