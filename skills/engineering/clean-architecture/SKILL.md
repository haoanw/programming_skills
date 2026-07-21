---
name: clean-architecture
description: Apply pragmatic Clean Architecture principles when designing, changing, or reviewing system structure. Use for module and service boundaries, dependency direction, business-rule placement, ports and adapters, framework or database isolation, modularization, legacy modernization, architecture tests, and robotics, embedded, backend, or distributed-system design.
---

# Clean Architecture

Protect long-lived policy from volatile mechanisms while keeping architectural cost proportional to the system's real complexity. Preserve options that matter; do not add layers or interfaces for hypothetical change.

## Start from change and policy

Before proposing structure:

1. Identify critical use cases, business rules, invariants, and quality constraints.
2. Identify actors and independent reasons to change.
3. Map components, external systems, data stores, runtime calls, deployment units, and trust boundaries.
4. Draw source-code dependency direction separately from runtime control flow.
5. Locate cycles, framework leakage, shared mutable data, and business rules buried in adapters.

## Enforce the dependency rule

Source-code dependencies should point toward higher-level, more stable policy:

```text
entry adapter -> use case -> domain policy
external mechanism -> port owned by the use case
```

- Keep HTTP, ORM, messaging, cloud, ROS/DDS, device-driver, and UI types outside core policy.
- Define ports from the needs of their high-level clients, then implement them in adapters.
- Pass simple request, response, event, or value types across boundaries.
- Assemble concrete implementations in a composition root.
- Keep the component dependency graph acyclic.
- Make boundaries executable through module visibility, dependency checks, and architecture tests.

## Pay for boundaries only when earned

Justify a boundary through a real axis of change, dependency stability, security or trust, testing, deployment, scaling, fault isolation, or hardware substitution. Prefer the lightest sufficient form: package visibility, a facade, a one-way interface, or a full port-and-adapter boundary.

Do not assume that four layers, repositories, interfaces for every class, microservices, or a rich domain model are universally correct. Preserve performance, real-time, memory, transaction, operational, and deployment constraints with evidence.

## Evolve incrementally

When repairing architecture:

1. Protect current behavior with tests.
2. Establish a controlled internal entry point.
3. Invert incorrect dependencies.
4. isolate external types and move business rules inward.
5. Add automated dependency enforcement.
6. Split deployment units only when operational needs justify it.

Record consequential trade-offs and reevaluation triggers in ADRs.

## Use the detailed reference

Read [references/principles.md](references/principles.md) for substantial architecture design or review, or when evaluating SOLID, component cohesion and coupling, use-case layers, services, testing boundaries, embedded/robotic systems, partial boundaries, or migration plans. Search its headings first and load only the relevant sections when possible.

Use the `clean-code` skill for local naming, function, class, test, and refactoring quality. Use this skill for relationships across modules and system boundaries.

## Produce actionable architecture findings

Tie every finding to concrete evidence and lifecycle impact. Show the current dependency problem, the proposed boundary and direction, an incremental migration path, and the tests or tooling that will enforce it. Separate urgent structural risks from optional improvements.
