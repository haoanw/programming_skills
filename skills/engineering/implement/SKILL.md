---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

Apply `/clean-code` while writing production code and tests. Apply `/clean-architecture` whenever the change creates or alters module boundaries, dependency direction, business-rule placement, external adapters, or composition. Repository standards and explicit spec constraints take precedence over either heuristic.

Use /tdd where possible, at pre-agreed seams.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, use /code-review to review the work; its Standards axis applies both clean-code and relevant clean-architecture checks.

Commit your work to the current branch.
