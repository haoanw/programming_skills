Quickstart:

```bash
npx skills add mattpocock/skills --skill=clean-code
```

```bash
npx skills update clean-code
```

[Source](https://github.com/mattpocock/skills/tree/main/skills/engineering/clean-code)

## What it does

`clean-code` supplies a pragmatic baseline for writing, changing, and reviewing production code and tests so they remain readable, verifiable, and safe to modify.

It treats code smells as evidence to investigate, not mechanical violations. Correctness, safety, public contracts, measured performance, repository conventions, and the smallest effective change all outrank aesthetic purity.

## When to reach for it

Type `/clean-code`, or the agent reaches for it automatically when a task involves implementation, refactoring, code review, legacy cleanup, naming, functions and classes, error handling, or test readability.

Reach for it whenever local code is difficult to understand, verify, or change. For dependency direction and structure across modules, services, frameworks, databases, or hardware, use [clean-architecture](https://aihero.dev/skills-clean-architecture) instead.

## Work from behavior

The discipline starts by understanding purpose, observable behavior, side effects, constraints, and risk. It then protects that behavior with tests where practical and improves the code in small verified steps.

Its leading idea is **low cognitive load**, not smallness for its own sake. Honest names, one level of abstraction, cohesive reasons to change, explicit dependencies, and visible effects matter because they make future changes safer—not because they satisfy a style quota.

## Pragmatic by design

The detailed reference covers naming, functions, comments, errors, objects and data, third-party boundaries, tests, concurrency, smells, and refactoring. Every rule remains subordinate to repository standards and real constraints. It rejects broad rewrites, premature abstractions, and performance changes without evidence.

## It's working if

- Findings explain concrete risk instead of merely naming a principle.
- Refactorings preserve behavior and stay focused on the touched area.
- Tests assert observable behavior rather than private implementation details.
- Verification and unverified assumptions are both explicit.

## Where it fits

`clean-code` is a **model-invoked discipline underneath the build chain**. [implement](https://aihero.dev/skills-implement) applies it while writing code, and the Standards axis of [code-review](https://aihero.dev/skills-code-review) uses it as a default baseline. Its system-level neighbour is [clean-architecture](https://aihero.dev/skills-clean-architecture); [codebase-design](https://aihero.dev/skills-codebase-design) supplies the more focused deep-module vocabulary. When you're unsure which skill or flow fits, [ask-matt](https://aihero.dev/skills-ask-matt) routes you.
