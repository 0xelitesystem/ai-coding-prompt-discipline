# 03. No-bloat prompts

Preventing verbose code, over-abstraction, premature optimization, and ceremony.

## Standing instruction

```
Write the minimum code that works.
No abstraction without two existing concrete uses.
No design pattern unless the problem matches the pattern's specific intent.
No factory class for one type.
No interface for one implementation.
No options object for one optional argument.
No defensive null checks for inputs that cannot be null in this call site.
```

## The "delete-everything-suspicious" prompt

After the AI writes code:

```
Read through your code and delete:
- Any try/catch where the catch block just rethrows or logs and rethrows
- Any null/undefined check on a value that cannot be null at the call site
- Any comment that just restates the code
- Any abstraction with one concrete user
- Any helper function used in exactly one place that is under 5 lines (inline it)
- Any options object with fewer than 3 fields (use positional args)
- Any class with only one method and no state (use a function)
Show me the cleaned version.
```

## The "no premature abstraction" prompt

```
Implement this concretely with no abstraction.
Use specific types, specific variable names, specific values.
Do not parameterize anything that is not currently varying.
If you find yourself wanting to extract a helper or constant, hardcode it for now.
We will extract when we have a second concrete use case.
```

## The "no premature optimization" prompt

```
Write the obvious, slow version.
Do not cache, memoize, or batch unless you have specific performance numbers showing it matters.
Use the most readable algorithm even if a faster one exists.
If you are tempted to write a comment explaining a performance choice, stop and write the simple version instead.
```

## The "stop generating useless tests" prompt

```
Write tests only for:
- Cases I explicitly listed
- Edge cases where the function's behavior is non-obvious
- Bug regressions

Do NOT write tests for:
- Getter/setter trivia
- "Returns the correct type" checks
- Mocking the function and asserting it was called
- Tests that just repeat the implementation in a different syntax
```

## The "stop with the comments" prompt

```
Write code with no comments unless:
- The reason for an unusual choice needs explaining
- A workaround needs a link to the bug it works around
- A regex needs an example of what it matches

Do NOT write comments that:
- Restate the function name
- List the parameters
- Describe what obvious code does
- Mark sections like "// Initialize state"
```

## Cut these patterns specifically

### The wrapper class

```javascript
// Bloated
class UserService {
  constructor(db) { this.db = db; }
  async getUser(id) { return this.db.users.findOne({ id }); }
}
const userService = new UserService(db);
const user = await userService.getUser(id);

// Lean
const user = await db.users.findOne({ id });
```

Cut it. The class added no behavior.

### The unnecessary factory

```javascript
// Bloated
function createLogger(level) {
  return {
    info: (msg) => console.log(`[${level}] ${msg}`)
  };
}
const logger = createLogger('INFO');
logger.info('starting');

// Lean
console.log('[INFO] starting');
```

Cut it. One call site, no real polymorphism.

### The options-object-of-one

```javascript
// Bloated
function fetchUser({ id, includeProfile = false }) {
  ...
}
fetchUser({ id: 123 });

// Lean
function fetchUser(id) {
  ...
}
fetchUser(123);
```

The `includeProfile` flag was never used. Cut.

### The catch-and-rethrow

```javascript
// Bloated
try {
  doThing();
} catch (e) {
  console.error('Failed to do thing', e);
  throw e;
}

// Lean
doThing();
```

If the only "handling" is logging and rethrowing, let it propagate. Log at the boundary, not at every layer.

## The "is this earning its keep" prompt

For any piece of code over 10 lines:

```
For each helper function, class, or module in this code, tell me:
- The number of call sites it has right now
- Whether removing it would make the code shorter or longer in total
- Whether it exists for a specific reason or "for cleanliness"
Anything that has 1 call site and exists for cleanliness gets inlined.
```
