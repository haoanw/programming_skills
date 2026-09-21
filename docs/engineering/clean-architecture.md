Quickstart:

```bash
npx skills add mattpocock/skills --skill=clean-architecture
```

```bash
npx skills update clean-architecture
```

[Source](https://github.com/mattpocock/skills/tree/main/skills/engineering/clean-architecture)

## What it does

`clean-architecture` helps design, change, and review system structure so long-lived business policy is not dominated by volatile frameworks, databases, transports, cloud services, or hardware mechanisms.

Its defining constraint is proportionality: a boundary must earn its interfaces, mappings, tests, and assembly cost through a real axis of change, stability, trust, testing, deployment, scaling, failure isolation, or hardware substitution. It never requires four layers, microservices, or an interface for every class.

## When to reach for it

Type `/clean-architecture`, or the agent reaches for it automatically when work affects module or service boundaries, dependency direction, business-rule placement, ports and adapters, framework or database coupling, modularization, architecture tests, or robotic and embedded software structure.

Reach for it when the relationship between parts is the problem. For naming, functions, classes, errors, tests, and local refactoring, use [clean-code](https://aihero.dev/skills-clean-code); for the focused vocabulary of deep module interfaces and seams, use [codebase-design](https://aihero.dev/skills-codebase-design).

## Policy inward, mechanisms outward

The leading idea is the **dependency rule**: source-code dependencies point toward higher-level, more stable policy even when runtime calls travel outward. Use cases own the ports they need; databases, HTTP, messaging, ROS/DDS, devices, and frameworks implement those ports at the edge. Boundary data stays independent of external types.

Architecture becomes real through controlled entry points, module visibility, an acyclic dependency graph, composition at the edge, and automated architecture tests, not through directory names or diagrams alone.

## Evolve boundaries incrementally

The skill starts from use cases, actors, invariants, constraints, and a current dependency map. Repair proceeds under test protection: establish an entry point, invert dependencies, isolate external types, move policy inward, and enforce the direction. Physical service or database splits come only when operational needs justify them.

## It's working if

- Every proposed boundary names the concrete axis of change or lifecycle need that pays for it.
- Findings show current dependency evidence and an incremental migration path.
- Core policy can be tested without starting external mechanisms.
- Architecture rules are enforceable rather than merely diagrammed.

## Where it fits

`clean-architecture` is a **model-invoked system discipline underneath implementation and review**. [implement](https://aihero.dev/skills-implement) applies it when a change affects structure, while [code-review](https://aihero.dev/skills-code-review) invokes it for architecture-relevant diffs. [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) is the larger periodic workflow that finds restructuring opportunities; this skill supplies principles for judging them. When you're unsure which skill or flow fits, [ask-matt](https://aihero.dev/skills-ask-matt) routes you.
