# 05. Anti-slop checklist

Hard rules to copy-paste into every project's AGENTS.md / CLAUDE.md / system prompt.

## The starter checklist

```
RULES FOR ALL CODE GENERATION IN THIS PROJECT:

WRITING STYLE
- No em-dashes anywhere. Use commas, colons, or two sentences.
- No filler openers: cut "in today's world", "in modern software", "when it comes to".
- No empty adjectives in code comments or docs: cut "robust", "powerful", "scalable", "comprehensive", "seamless", "leverage".
- No emoji in code, comments, READMEs, or commit messages.
- No rule-of-three lists ("fast, reliable, scalable") unless each item is doing real work.

CODE STRUCTURE
- No abstraction without two existing concrete uses.
- No design pattern unless the problem matches the pattern's intent.
- No options object for fewer than 3 fields.
- No class wrapper for stateless logic; use a function.
- No factory for one type.
- No interface for one implementation.

ERROR HANDLING
- No try/catch where the catch only logs and rethrows.
- No defensive null checks for values that cannot be null at the call site.
- No swallowing exceptions silently.
- Errors propagate to a boundary that handles them; intermediate layers do not catch.

COMMENTS
- No comments that restate the code.
- No section header comments ("// Initialize state").
- No JSDoc/docstrings on internal helpers.
- Public API needs short docstrings; private code does not.

DEPENDENCIES
- No new dependency for something the standard library can do.
- No new dependency that doesn't pass a 1-year-of-commits test.
- No left-pad-style micro-deps.

PROHIBITED IDIOMS
- No console.log shipped to production code (use a logger or remove).
- No localStorage or sessionStorage in code that should work in restricted environments without explicit user approval.
- No hardcoded credentials, API keys, or secrets in code or examples.
- No eval, no Function constructor.
- No "git add ." in scripted commits; explicit file paths.

OUTPUT FORMAT
- One thing at a time. If a request has multiple parts, build the first, show it, ask before continuing.
- No "I have also taken the liberty of..." additions.
- No mocks or placeholders presented as finished code.
- If a function is incomplete, label it "// TODO: [specific thing]" not "// implement this".
```

## The README-specific checklist

Add for any README, docs, or marketing copy generation:

```
RULES FOR DOCUMENTATION GENERATION:

- No "comprehensive" or "complete" claims (the doc is never complete).
- No marketing voice ("delight your users", "unlock potential", "supercharge your workflow").
- No "what is X" sections that just restate the obvious.
- No "why X matters" sections that are filler.
- Start with what the thing does in plain language.
- Show the smallest working example before any explanation.
- Use real example values, not "foo bar baz" or "MyApp".
- Tables for comparisons. Lists for steps. Prose for context.
- No "Quick Start" header followed by 14 steps.
- No emoji in headers.
- No table of contents unless the doc is over 1500 words.
```

## The commit-message checklist

```
RULES FOR COMMIT MESSAGES:

- Subject line: imperative mood, under 60 characters.
- No emoji in subject lines.
- No "feat:", "fix:", "chore:" prefixes unless the project explicitly uses conventional commits.
- Body wraps at 72 characters.
- Body explains why, not what (the diff shows what).
- Reference issue numbers when relevant.
- No "various improvements" or "minor updates" as the whole message.
- Multiple unrelated changes get multiple commits.
```

## The test-writing checklist

```
RULES FOR TESTS:

- One assertion per test (or one logical group of related assertions).
- Test names describe behavior, not implementation: "saves user with valid email" not "calls db.save".
- No mocking the system under test.
- No tests that pass without exercising real code paths.
- Setup is explicit per test; no shared mutable fixtures that hide dependencies.
- No snapshot tests for anything that isn't a pure rendering output.
- A failing test should make it obvious what went wrong without reading the implementation.
```

## How to verify the AI is following the checklist

```
Read the code/docs you just generated.
Find every place you violated the project's rules in AGENTS.md.
List the violations with line numbers.
Rewrite to fix them.
```

If the AI claims it didn't violate anything, paste a specific section back with:

```
This violates rule [X]. Fix it.
```

The first few times this happens, point out specific violations. After that the model usually self-corrects.
