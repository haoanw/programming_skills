---
name: clean-code
description: Apply pragmatic Clean Code principles when writing, changing, refactoring, or reviewing production code and tests. Use for implementation, code review, legacy cleanup, naming and API design, error handling, function and class structure, test readability, or whenever code is difficult to understand, verify, or change safely.
---

# Clean Code

Optimize code for safe change: correctness first, then verifiability, clarity, and maintainability. Prefer the smallest behavior-preserving improvement over an aesthetic rewrite.

## Apply the hierarchy

Use this order when principles compete:

1. Preserve correctness, safety, public contracts, and required performance.
2. Make behavior verifiable through focused tests and explicit boundaries.
3. Reduce cognitive load with honest names, cohesive units, and visible side effects.
4. Remove duplication only when the shared concept is real.
5. Follow repository conventions over personal style.

Do not enforce line-count, argument-count, class-count, or pattern quotas mechanically. Treat every smell as evidence to investigate, not proof of a defect.

## Work from behavior

Before changing code:

1. State its purpose, inputs, outputs, side effects, and constraints.
2. Identify correctness, security, data-loss, concurrency, and resource risks before readability issues.
3. Confirm existing behavior with tests or add characterization coverage when practical.
4. Make small, focused changes and verify after each meaningful step.

While writing or refactoring code:

- Use domain language and names that reveal intent, units, state, and side effects.
- Keep one level of abstraction within a function and separate unrelated reasons to change.
- Prefer explicit dependencies and results over globals, service locators, and hidden mutation.
- Separate commands from queries where practical and translate errors at the boundary that understands them.
- Keep third-party types and mechanisms from spreading through core logic.
- Write tests against observable behavior through stable interfaces, not private implementation details.
- Remove dead code and stale comments; use comments for rationale, constraints, and contracts that code cannot express.
- Improve only the touched area unless broader cleanup is explicitly in scope.

## Use the detailed reference

Read [references/principles.md](references/principles.md) when performing a substantial implementation or review, when a smell needs precise remediation guidance, or when working on tests, concurrency, error handling, third-party boundaries, or legacy refactoring. Search its headings first and load only the relevant sections when the whole reference is unnecessary.

## Report findings usefully

For reviews, prioritize findings by actual risk. For each finding, cite the evidence, explain the change or maintenance risk, name the relevant principle, and recommend the smallest effective correction. Distinguish blocking correctness or safety problems from maintainability suggestions and stylistic nits.

Always state what was verified and what remains unverified.
