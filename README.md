# ai-coding-prompt-discipline

Prompts and rules that make AI coding assistants push back, refuse over-engineering, and stay in scope.

The default behavior of AI coding tools is to agree, expand, and add. This repo teaches the opposite: disagree when wrong, stay narrow, do less.

## What this is NOT

This is not a prompt library for "10x your coding productivity." That repo is [ai-coding-prompt-recipes](https://github.com/0xelitesystem/ai-coding-prompt-recipes), which covers the affirmative side: how to ask for things well.

This repo covers the defensive side: how to keep the AI from making things worse.

## Contents

- `README.md` (this file)
- `01-push-back-prompts.md`: getting the AI to disagree with you when you're wrong
- `02-scope-discipline-prompts.md`: preventing feature creep
- `03-no-bloat-prompts.md`: cutting verbose code and over-abstraction
- `04-requirements-extraction.md`: forcing the AI to ask before assuming
- `05-anti-slop-checklist.md`: hard rules to copy-paste into every project
- `06-rejection-templates.md`: how to reject AI suggestions without making it worse
- `07-context-priming.md`: context block templates that produce better output
- `08-debugging-discipline.md`: structured debugging without rabbit holes
- `09-review-prompts.md`: structured code review prompts
- `10-anti-patterns.md`: the things users do that produce bad AI output

## How to use

1. Read `05-anti-slop-checklist.md` first; it's the standing ruleset.
2. Pick the file matching your current problem.
3. Adapt the prompts to your stack and project conventions.
4. Save the ones that work to your own AGENTS.md or CLAUDE.md or similar.

## The core insight

AI coding assistants have a strong agreement bias. They will:

- Build what you asked for even when what you asked for is wrong
- Add features you didn't request because "you'll probably need this"
- Wrap simple code in 4 layers of abstraction
- Apologize and try again when you push back, instead of explaining why their first try was correct
- Generate placeholder TODOs, mocks, and partial implementations and call it done

The prompts in this repo counter each of these defaults specifically.

## Pairs with

- [ai-coding-prompt-recipes](https://github.com/0xelitesystem/ai-coding-prompt-recipes): the affirmative side
- [readme-slop-checker](https://github.com/0xelitesystem/readme-slop-checker): catch AI slop in generated READMEs
- [ai-citability-scorer](https://github.com/0xelitesystem/ai-citability-scorer): catch AI slop in generated prose

## License

MIT. Copy, adapt, ship.
