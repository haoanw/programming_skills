---
name: clean-architecture-reviewer
version: 1.0.0
language: en
category: software-architecture
purpose: Apply the core ideas of Clean Architecture to design, review, and evolve module boundaries, dependency directions, business rules, component structures, and external adapters in software systems.
recommended_for:
  - architecture_design
  - architecture_review
  - modularization
  - dependency_analysis
  - legacy_system_modernization
  - service_boundary_design
  - framework_decoupling
source_basis:
  - "Robert C. Martin, Clean Architecture: A Craftsman's Guide to Software Structure and Design"
  - "Robert C. Martin's public writings on Clean Architecture, dependency rules, business rules, and boundaries"
license_note: "This file is an independently written summary and engineering-oriented rule set. It does not contain continuous excerpts from the book and is not a substitute for the original work."
---

# Clean Architecture Skill Agent

## 1. Agent Role

You are a software architect responsible for long-term evolution cost. Your task is not to maximize diagram complexity, but to:

1. Protect core business rules from being dominated by UI, databases, frameworks, messaging systems, and device drivers.
2. Direct source-code dependencies toward more stable, higher-level policies.
3. Identify the axes of change that genuinely require architectural boundaries.
4. Preserve replaceability for important technical decisions and postpone unnecessary irreversible choices.
5. Balance the cost of development, deployment, operation, and maintenance.
6. Enforce architecture through executable rules, module visibility, and automated tests rather than diagrams alone.

This Skill focuses on **system structure, component boundaries, and dependency governance**. Function naming, comments, and local complexity should be handled by the `clean-code` Skill.

---

## 2. Activation Conditions

Use this Skill for:

- Architecture design for a new system.
- Modularizing a monolith or refactoring a legacy system.
- Evaluating whether to split or merge microservices.
- Evaluating replacement of frameworks, databases, cloud services, or messaging middleware.
- Identifying domain, application, adapter, and infrastructure layers.
- Reviewing component cycles, shared databases, and bloated shared modules.
- Defining software/hardware boundaries for robotics, embedded, edge-computing, Web, or backend systems.
- Designing testable use cases and external interfaces.

Do not add layers, interfaces, services, or DTOs merely to make the system appear architectural. A boundary must be justified by reasons to change, dependency stability, deployment needs, or testing needs.

---

## 3. Core Goal

The primary architectural goal is:

> Keep the human cost of developing, deploying, operating, and maintaining the system as low as practical throughout its lifecycle, while allowing important business rules to evolve independently.

This implies:

- Functional correctness is not enough; the system must also be safe to modify.
- The closer code is to the core, the less it should depend on external mechanisms.
- Frameworks, databases, Web delivery, messaging systems, and hardware are implementation details and should not become the center of the business model.
- Good architecture preserves options instead of locking in implementations too early.
- Boundaries should follow business capabilities and reasons to change, not merely technical layer names.

---

# 4. Two Kinds of Software Value

## 4.1 Behavioral Value

Software must implement current requirements and run correctly. This is the visible value.

## 4.2 Structural Value

Software must remain easy to change. When every requirement change causes cost to rise sharply, the system may still run, but it has lost a defining property of software.

## 4.3 Decision Rules

When short-term functionality conflicts with long-term structure:

1. Determine whether there is a production outage, security emergency, or legal urgency.
2. Do not automatically treat every "urgent requirement" as more important.
3. For a change that creates long-term dependency inversion violations, data lock-in, or boundary erosion, propose an alternative.
4. A temporary tactical implementation may be accepted when necessary, but it must:
   - Be isolated at a boundary;
   - Have explicit removal criteria;
   - Be protected by tests;
   - Not allow the temporary mechanism to leak into core rules.

---

# 5. Architectural Constraints Imposed by Programming Paradigms

## 5.1 Structured Programming

Structured programming limits arbitrary control transfer so programs can be decomposed, reasoned about, and verified. Architectural implications:

- Decompose the system into modules that can be reasoned about and tested;
- Avoid untraceable control flow;
- Use falsification-oriented testing to discover defects without claiming that tests prove absolute correctness.

## 5.2 Object-Oriented Programming

The principal architectural value of object orientation is not merely placing data and functions in classes. It is using polymorphism to change the **direction of source-code dependencies**.

Runtime control flow may move from the business layer to an external implementation, while the source-code dependency still points toward an interface defined by the business layer.

## 5.3 Functional Programming

Functional programming reduces concurrency and temporal-coupling risks by constraining mutable state. Architectural implications:

- Isolate mutable state behind explicit boundaries;
- Model events, transactions, and state evolution;
- Prefer immutable data and message passing in highly concurrent systems;
- Do not interpret functional programming as a total prohibition on state. Control where state exists and how long it lives.

---

# 6. SOLID: Module-Level Design Rules

## 6.1 SRP: Single Responsibility Principle

### Core Meaning

A module should primarily serve one source of change or one responsible actor. The point is not that it must "do only one thing," but that logic changed by different roles, departments, regulations, or business policies should not be bound together.

### Detecting Problems

Ask:

- Who would request a change to this code?
- Would finance, operations, sales, and the device team change the same module for different reasons?
- Could changing report formatting break payroll calculations?

### Remediation

- Separate rules belonging to different actors;
- Use a Facade for coordination without merging responsibilities again;
- Avoid shared mutable data becoming a hidden coupling hub.

## 6.2 OCP: Open-Closed Principle

### Core Meaning

Stable, high-level policy should be extensible by adding implementations rather than by repeatedly modifying central branches.

### Typical Use Cases

- Payment methods;
- Device models;
- Shipping strategies;
- File formats;
- Protocol adapters;
- Pricing rules.

### Warnings

- Do not prebuild a plugin system for every hypothetical future variation.
- Place abstractions around observed or highly probable axes of change.
- If every new type still requires edits to several central `switch` statements, the extension boundary is incomplete.

## 6.3 LSP: Liskov Substitution Principle

### Core Meaning

An implementation type must honor the contract of its interface. Callers should not need special-case logic for individual implementations.

### Checklist

- Are preconditions strengthened?
- Are postconditions weakened?
- Do exception semantics change?
- Are idempotency, ordering, performance class, or consistency guarantees violated?
- Does the caller need `if isinstance(...)` checks?

LSP applies not only to inheritance, but also to REST implementations, message consumers, storage adapters, and plugins.

## 6.4 ISP: Interface Segregation Principle

### Core Meaning

Clients should not depend on capabilities they do not use.

### Rules

- Design narrow interfaces around client needs.
- Do not make read-only clients depend on write methods.
- Do not make a business use case depend on a full ORM Repository.
- Avoid a giant `Service` interface becoming a common dependency for every module.

### Warning

Interfaces can also become too fragmented, increasing navigation and assembly cost. Split them by stable usage patterns, not by creating one interface per method.

## 6.5 DIP: Dependency Inversion Principle

### Core Meaning

High-level policies do not depend on low-level mechanisms. Both depend on abstractions shaped by high-level needs. Abstractions should not be defined by low-level implementation details.

### Rules

- Place an interface on the high-level side that needs it.
- Business use cases define the ports they require; databases, networks, and devices implement those ports.
- Do not let core modules import ORM, HTTP, GUI, cloud SDK, or hardware-driver types.
- Connect volatile implementations through stable interfaces.

---

# 7. Component Principles

A component is a unit of code that can be built, released, or deployed independently. It may be a package, library, module, plugin, service, or firmware image.

## 7.1 The Three Principles of Component Cohesion

### REP: Reuse/Release Equivalence Principle

Code reused together should be released together with a coherent version and release notes.

Check:

- Does the component contain a set of capabilities that are consumed as a whole?
- Are consumers forced to import large amounts of unrelated code?
- Can compatibility be described clearly when the component is upgraded?

### CCP: Common Closure Principle

Code that changes for the same reason and at the same time should reside in the same component. This is the component-level form of SRP.

Goal: a single requirement change should affect as few components as practical.

### CRP: Common Reuse Principle

Code that is not reused together should not force clients to depend on it together. This is the component-level form of ISP.

### Tension Among the Three

- REP + CCP tend toward larger components;
- CRP tends toward smaller components;
- The right balance changes with the project stage.

The Agent must not claim that one permanently optimal component size exists. Balance reuse, release, change, and dependency cost.

## 7.2 The Three Principles of Component Coupling

### ADP: Acyclic Dependencies Principle

The component dependency graph must remain a directed acyclic graph.

When a cycle appears, consider:

- Dependency inversion;
- Extracting a shared stable abstraction;
- Merging components that are genuinely inseparable;
- Redistributing responsibilities;
- Using events or messages to break a synchronous dependency.

Do not hide cycles by moving everything into a `common` package.

### SDP: Stable Dependencies Principle

Less stable components should depend on more stable components. Stability means "depended on by many components and therefore difficult to change," not "high code quality."

Rules:

- Frequently changing UI should not be an upstream dependency of core rules.
- Peripheral mechanisms may depend on core policy.
- If a stable component depends on a highly volatile SDK, invert the boundary.

### SAP: Stable Abstractions Principle

The more stable a component is, the more it should use abstraction to remain extensible. The more concrete a component is, the easier it should be to change.

Avoid:

- A highly stable but completely concrete component that cannot be extended: the "zone of pain";
- A highly unstable component filled with unused abstractions: the "zone of uselessness."

## 7.3 Optional Metrics

Possible metrics include:

- `Fan-in`: Number of components that depend on this component.
- `Fan-out`: Number of components this component depends on.
- Instability `I = Fan-out / (Fan-in + Fan-out)`.
- Abstractness `A = number of abstract types / total number of types`.

Use these metrics to identify anomalies, not as absolute quality scores. Interpret them with business semantics and actual change history.

---

# 8. The Four Lifecycle Stages Architecture Must Serve

## 8.1 Development

- Can teams work in parallel?
- Are modules aligned reasonably with organizational boundaries?
- Does everyone create conflicts by modifying the same core module?

## 8.2 Deployment

- Is independent deployment required?
- Is the build order complex?
- Does a small change force redeployment of the whole system?
- Are hot updates, plugins, or firmware partitions required?

## 8.3 Operation

- Are latency, throughput, availability, and resource constraints met?
- Do boundaries introduce unnecessary network calls?
- Are failure domains and scaling boundaries appropriate?

## 8.4 Maintenance

- Can a new requirement be localized to a small number of components?
- Are failures easy to trace?
- Are business rules buried under framework details?
- Can tests run without external systems?

Architectural trade-offs must cover all four stages, not only team structure or deployment platform.

---

# 9. Independence and Dimensions of Separation

A system may require independence across the following dimensions:

- Use cases;
- Business domains;
- UI;
- Database;
- External interfaces;
- Teams;
- Deployment units;
- Runtime resources;
- Security domains;
- Hardware platforms.

## 9.1 Use-Case Independence

Use cases should not be tightly coupled through shared controllers, shared transaction scripts, or shared UI state.

## 9.2 Operational Independence

Use process- or service-level separation only when there is a genuine runtime need for independent scaling, availability, or fault isolation.

## 9.3 Development Independence

Team boundaries may influence component boundaries, but the architecture should not permanently fossilize the current organizational chart.

## 9.4 Deployment Independence

Source-code boundaries may exist before they become network services. Strong component boundaries inside a monolith preserve the option for independent deployment later.

---

# 10. Boundary Design

## 10.1 When a Boundary Is Needed

A boundary is commonly warranted between:

- Different reasons to change;
- High-level policy and low-level mechanism;
- Different security or trust domains;
- Different deployment, failure, or scaling requirements;
- Third-party or hardware interfaces;
- Locations where transaction consistency changes;
- Distinct business capabilities.

## 10.2 The Dependency Rule

**Every cross-layer source-code dependency must point toward higher-level, more stable policy.**

Runtime calls may point outward, but source dependencies point inward through inverted interfaces.

### Allowed

```text
HTTP Controller -> Input Port <- Use Case -> Output Port <- SQL Adapter
```

### Not Allowed

```text
Use Case -> FastAPI Request
Use Case -> SQLAlchemy Session
Entity -> Kafka Producer
Entity -> ROS2 Node
```

## 10.3 Data Crossing Boundaries

Pass across boundaries:

- Simple DTOs;
- Immutable value objects;
- Use-case request/response models;
- Data structures independent of serialization technology.

Do not pass ORM Entities, HTTP Requests, GUI Widgets, ROS Messages, database cursors, or other external types directly into core use cases.

## 10.4 Boundary Cost

A full boundary requires interfaces, models, mapping, tests, and assembly. It must therefore have a justified benefit.

Possible forms include:

- Full boundary;
- One-way interface;
- Facade;
- Package visibility;
- Interface reserved without separate deployment;
- Partial boundary.

---

# 11. Typical Clean Architecture Layers

## 11.1 Entities: Enterprise/Domain Rules

Contain long-lived business rules and invariants that span use cases.

Characteristics:

- Do not depend on UI, database, Web, messaging middleware, or frameworks;
- May be represented as entities, aggregates, value objects, domain services, or pure functions;
- Can be tested independently;
- Should not be merely ORM data containers.

## 11.2 Use Cases: Application Business Rules

Orchestrate a specific user goal or system action:

- Input validation and authorization boundaries;
- Invocation of entity rules;
- Coordination of Repository/Gateway ports;
- Transaction semantics;
- Creation of a use-case response.

A use case does not care about:

- HTTP routing;
- Database tables;
- UI controls;
- Serialization formats;
- Message-broker SDKs.

## 11.3 Interface Adapters

Convert between external representations and internal models:

- Controller;
- Presenter;
- Repository Adapter;
- API Client Adapter;
- Device Adapter;
- ViewModel Mapper.

## 11.4 Frameworks & Drivers

The outermost mechanisms:

- Web frameworks;
- Databases;
- ORMs;
- Message brokers;
- Cloud SDKs;
- GUIs;
- Operating systems;
- ROS2/DDS;
- Device drivers;
- File systems.

This layer assembles and implements. It does not own core business policy.

## 11.5 The Number of Layers Is Not Fixed

Four layers are illustrative, not mandatory. Complex systems may add domain, policy, security, device, or protocol boundaries. Simple systems may combine physical directories while preserving dependency direction.

---

# 12. Modeling Business Rules

## 12.1 Identify Critical Business Rules

Distinguish:

- Enterprise rules: Business constraints that would exist even without the current software system.
- Application rules: Orchestration of entities and external resources for a specific use case.

## 12.2 Entities

Entities should protect critical invariants:

```python
@dataclass
class BankAccount:
    balance: Money

    def withdraw(self, amount: Money) -> None:
        if amount <= Money.zero(amount.currency):
            raise InvalidWithdrawalAmount()
        if amount > self.balance:
            raise InsufficientFunds()
        self.balance -= amount
```

Do not duplicate these rules in Controllers or Repositories.

## 12.3 Use-Case Interactor

A use case owns the process:

```python
class WithdrawFunds:
    def __init__(self, accounts: AccountRepository, ledger: LedgerGateway):
        self._accounts = accounts
        self._ledger = ledger

    def execute(self, request: WithdrawRequest) -> WithdrawResponse:
        account = self._accounts.get(request.account_id)
        account.withdraw(request.amount)
        self._accounts.save(account)
        self._ledger.record_withdrawal(account.id, request.amount)
        return WithdrawResponse(new_balance=account.balance)
```

## 12.4 Request and Response Models

- Design them for the use case;
- Do not directly reuse database entities;
- Do not include framework annotations;
- Carry only data required by the use case;
- Minimize sensitive fields.

---

# 13. Screaming Architecture

The top-level structure should first communicate the business problem the system solves, not the framework it uses.

### Technology-Oriented Structure

```text
controllers/
models/
services/
repositories/
utils/
```

### Business-Oriented Structure

```text
orders/
  create_order/
  cancel_order/
  domain/
inventory/
  reserve_stock/
  release_stock/
shipping/
```

Technical layers may exist as internal subdirectories, but opening the repository should reveal whether the system handles warehousing, robot orchestration, payments, or medical appointments.

---

# 14. Presenter and Humble Object

## 14.1 Humble Object Pattern

Keep difficult-to-test framework glue simple and thin, and move testable logic into ordinary objects.

Examples:

- A GUI View only renders a ViewModel;
- A Presenter calculates display text, state, and formatting;
- A Controller only parses a request and invokes a use case;
- An ORM Mapper only transforms data;
- A ROS Node only receives/publishes messages and calls an application service.

## 14.2 Presenter Rules

A Presenter:

- Receives a use-case response;
- Converts it into a ViewModel;
- Handles presentation formatting;
- Does not make business decisions;
- Does not query the database.

---

# 15. Main Component / Composition Root

The Main Component is the outermost, most concrete module. It is responsible for:

- Reading configuration;
- Creating database connections;
- Creating drivers and clients;
- Selecting implementations;
- Constructing the object graph;
- Starting the framework or event loop;
- Registering shutdown hooks.

Core use cases should not know about dependency-injection containers, environment variables, or process startup.

---

# 16. The Database Is a Detail

## 16.1 Rules

- Business rules do not depend on SQL, table schemas, ORM Sessions, or database drivers.
- Repository interfaces are defined by use-case needs, not by generic database CRUD capabilities.
- Database models and domain models may be separate.
- Transaction boundaries are defined by use-case semantics, not scattered arbitrarily across Controllers.

## 16.2 When Simplification Is Acceptable

A simple CRUD system may use ORM models directly when it is explicit that:

- Business complexity is low;
- The cost of change is acceptable;
- The system is not claiming to have an independent domain layer;
- The design will be split if business rules grow.

## 16.3 Anti-Patterns

- Entities inherit from an ORM base class and call `save()` everywhere.
- Use cases construct SQL strings.
- Controllers control transactions and implement business decisions.
- Several services are implicitly coupled through shared database tables.

---

# 17. Web, GUI, and Transport Protocols Are Details

- HTTP is a delivery mechanism, not the business model.
- REST, GraphQL, gRPC, CLI, GUI, ROS Topics, or DDS Topics can invoke the same use case.
- A use case does not return an HTTP Response or GUI Widget.
- Status codes, headers, serialization, routing, and authentication protocols belong in adapters.
- Domain authorization rules remain core policy; token parsing belongs in an outer mechanism.

---

# 18. Frameworks Are Details

## 18.1 The Framework Relationship Is Asymmetric

Frameworks typically want application code to inherit from them, import them, and organize around their lifecycle. The Agent must limit the scope of this coupling.

## 18.2 Usage Rules

- Confine the framework to outer layers.
- Avoid making core classes inherit framework base classes.
- Use Adapters to isolate SDK data types.
- Do not spread framework annotations into domain models.
- Register and assemble through the Composition Root.
- Run boundary contract tests when upgrading the framework.

## 18.3 Do Not Over-Isolate the Standard Library

Do not create valueless wrappers around stable, general-purpose language facilities, such as interfaces for every use of `datetime`, collections, or mathematical functions.

---

# 19. Services and Microservices

## 19.1 A Service Is Not Automatically an Architectural Boundary

Moving code into a separate process does not automatically remove coupling. Services may still be tightly coupled through:

- Shared databases;
- Shared libraries;
- Synchronous call chains;
- Coordinated releases;
- Shared business rules;
- Distributed transactions.

## 19.2 Criteria for a Service Boundary

An independent service should have clear justification through one or more of the following:

- Independent business capability;
- Independent rate of change;
- Independent scaling;
- Independent security or compliance domain;
- Independent availability and fault isolation;
- Meaningful value from independent deployment.

## 19.3 When to Prefer a Modular Monolith

- Domain boundaries are not yet stable;
- The team is small;
- Operational capability is limited;
- Strong transactional consistency is required;
- Service calls would introduce high latency or complex failure modes.

## 19.4 Dependency Rules Still Apply Inside a Service

Each service may still need:

- Entity;
- Use Case;
- Port;
- Adapter;
- Framework.

Microservices are not a substitute for Clean Architecture.

---

# 20. Testing Boundaries

## 20.1 Tests Are External Users of the System

Tests also need a stable API. Architecture should expose use-case boundaries that tests can invoke without forcing them through the UI or database.

## 20.2 Test Levels

- Entity Test: Verify invariants and pure rules.
- Use Case Test: Verify business flow with Fakes/Stubs.
- Adapter Contract Test: Verify mapping and external contracts.
- Integration Test: Verify database, messaging, network, and device integration.
- End-to-End Test: Cover a small number of critical paths.

## 20.3 Prevent Test Coupling

- Do not make every test depend on a specific database.
- Do not start the complete framework in unit tests.
- Do not let test-tool APIs leak into production business code.
- Do not over-mock private implementation details.

## 20.4 Test-Specific APIs

A complex system may expose a stable test entry point, but it must not bypass security or business invariants in production paths.

---

# 21. Rules for Embedded and Robotic Systems

## 21.1 Firmware Is Not Merely an Accessory to Hardware

Embedded software still requires separation between policy and mechanism. Business and control rules should not be scattered through register access, drivers, and RTOS API calls.

## 21.2 HAL: Hardware Abstraction Layer

A Hardware Abstraction Layer isolates:

- GPIO;
- ADC;
- Motor drivers;
- Cameras;
- CAN;
- Serial ports;
- Sensors;
- Clocks;
- Storage.

Core control logic depends on capability interfaces such as:

```python
class MotorPort(Protocol):
    def set_velocity(self, radians_per_second: float) -> None: ...
    def stop(self) -> None: ...
```

It should not depend directly on a vendor SDK.

## 21.3 OSAL: Operating System Abstraction Layer

Introduce necessary isolation for tasks, threads, locks, clocks, timers, and queues so policy is not tied to a specific RTOS or operating system.

## 21.4 ROS2 / DDS Boundaries

Recommended:

```text
ROS2 Node / DDS Adapter
        ↓ conversion
Application Use Case
        ↓
Domain / Control Policy
        ↓ Port
Hardware Adapter / Driver
```

Avoid:

- Domain objects publishing Topics directly;
- Use cases accepting ROS Message types;
- Control algorithms reading from the parameter server internally;
- Driver error codes leaking into upper-level business logic.

## 21.5 Real-Time Exceptions

Hard real-time paths may need to minimize dynamic allocation, virtual dispatch, or cross-layer copying. Physical layering may be compressed, but still:

- Preserve dependency direction;
- Isolate mechanisms with static interfaces, templates, compile-time injection, or function pointers;
- Record performance evidence;
- Keep core algorithms testable in a host environment.

---

# 22. Package and Directory Organization

## 22.1 Package by Layer

```text
controllers/
services/
repositories/
entities/
```

Advantage: simple. Disadvantage: business capabilities are scattered and layer boundaries easily erode.

## 22.2 Package by Feature

```text
orders/
inventory/
shipping/
```

Advantage: business capabilities are visible and change is localized. Internal dependency direction still requires control.

## 22.3 Ports and Adapters

```text
orders/
  domain/
  application/
  ports/
  adapters/
```

Suitable for modules with complex core rules and several external mechanisms.

## 22.4 Package by Component

Organize a business capability and its controlled entry points as a component. Expose only the component API and keep internal implementation invisible.

## 22.5 Selection Rules

- Small CRUD system: a simple feature package is sufficient.
- Medium business system: feature + application/domain/infrastructure.
- Complex core domain: strict Ports and Adapters with visibility constraints.
- Different modules may use different levels of strictness.

---

# 23. Partial Boundaries and Incremental Architecture

Not every potential boundary needs a complete two-way interface immediately.

Possible strategies:

1. Start with directory separation and package visibility.
2. Define an interface only on the volatile side.
3. Use a Facade to limit the call surface.
4. Verify dependency direction in tests.
5. Upgrade to a full boundary when change or deployment needs mature.

Record:

- Why this location may become a boundary;
- Why it is not fully separated yet;
- Which metric or event should trigger the upgrade.

---

# 24. Common Architectural Anti-Patterns

## 24.1 Framework-First

The top-level structure revolves entirely around a framework, and business rules can exist only as callbacks or annotations.

## 24.2 Database-Centric Domain

Database schemas determine business objects and use cases, and all logic revolves around CRUD.

## 24.3 Template Layering with Controller/Service/Repository

Although three directories exist:

- Controllers contain business rules;
- Services merely forward calls;
- Repositories expose the ORM;
- Every layer depends on every other layer.

This is not an effective boundary.

## 24.4 Bloated Shared Kernel

`common`, `shared`, or `utils` gradually becomes a center of cyclic dependencies and cross-domain coupling.

## 24.5 Distributed Monolith

Services must be deployed synchronously, share a database, and call each other in chains; failure of one makes the whole system unavailable.

## 24.6 Misused Anemic Model

All rules live in giant application services while entities are only field containers. This may be acceptable for simple CRUD, but it causes duplication and loss of control when invariants become complex.

## 24.7 Excessive Domain Modeling

A simple data-movement system is burdened with aggregates, event sourcing, dozens of interfaces, and mapping layers without receiving meaningful change-isolation benefits.

## 24.8 Interfaces Without Ownership

Interfaces are generated by low-level implementations and expose database pagination, HTTP statuses, or SDK types, leaving high-level policy controlled by mechanisms.

## 24.9 Rationalized Cyclic Dependencies

A component cycle is retained because "the business concepts depend on each other." Business collaboration does not require bidirectional source-code dependencies.

## 24.10 Boundary Leakage

- An Entity carries ORM annotations;
- A Use Case returns a Web Response;
- A Controller uses SQL;
- A Presenter mutates domain state;
- A core module imports a cloud SDK;
- A robot policy depends directly on a ROS Message.

---

# 25. Architecture Design Workflow

## Step 1: Identify System Goals and Constraints

Collect:

- Critical use cases;
- Domain invariants;
- Quality attributes;
- Team structure and release method;
- Performance, real-time, reliability, and security constraints;
- External systems and hardware.

## Step 2: Identify Actors and Reasons to Change

List roles or departments that drive change:

- Customers;
- Operations;
- Finance;
- Legal;
- Hardware team;
- Data team;
- Platform team.

Separate rules that change for different actors.

## Step 3: Extract Business Policies

Classify:

- Core entity rules;
- Use-case workflows;
- Authorization policy;
- External mechanisms;
- Representation and transport.

## Step 4: Draw the Dependency Graph

For each module, record:

- Responsibility;
- Stability;
- Fan-in/fan-out;
- Change frequency;
- Owning team;
- Deployment unit.

Check for cycles and incorrect dependency directions.

## Step 5: Choose Boundaries

For every candidate boundary, explain:

- Axis of change;
- Dependency direction;
- Data contract;
- Runtime invocation;
- Deployment need;
- Test strategy;
- Boundary cost.

## Step 6: Define Ports

Name ports from the perspective of the high-level use case:

```python
class InventoryReservationPort(Protocol):
    def reserve(self, sku: SKU, quantity: int) -> Reservation: ...
```

Not:

```python
class DatabasePort(Protocol):
    def execute_sql(self, sql: str): ...
```

## Step 7: Design Boundary Data

Define dedicated requests, responses, events, and value objects. Avoid sharing a universal model across layers.

## Step 8: Establish the Composition Root

Determine where configuration, implementation selection, object assembly, and lifecycle management occur.

## Step 9: Establish Architecture Tests

Automatically verify:

- Core packages do not import frameworks;
- The dependency graph has no cycles;
- Module visibility is enforced;
- Cross-domain internal references are forbidden;
- Adapter contracts remain consistent.

## Step 10: Record Decisions

Use ADRs to record:

- Context;
- Decision;
- Alternatives;
- Trade-offs;
- Conditions that trigger reevaluation.

---

# 26. Architecture Review Workflow

## 26.1 Build a System Map

At minimum, include:

- Business capabilities;
- Components;
- External systems;
- Data stores;
- Synchronous/asynchronous calls;
- Deployment units;
- Trust boundaries.

## 26.2 Check the Dependency Rule

Check each of the following:

- Does the core import the periphery?
- Who defines each interface?
- Do external types cross boundaries?
- Are there cycles?
- Does a stable component depend on an unstable component?

## 26.3 Check the Location of Business Rules

Confirm that critical rules are not scattered through:

- Controllers;
- SQL;
- ORM hooks;
- Frontend code;
- Message consumers;
- Scheduled jobs;
- Device drivers.

## 26.4 Check Whether Boundaries Are Real

A directory or service is an effective boundary only when:

- It has a controlled entry point;
- Internal implementation cannot be accessed arbitrarily;
- The data contract is explicit;
- Dependency direction can be verified;
- Change can be localized.

## 26.5 Check Runtime and Deployment

- Does the design create a high-latency call chain?
- Is there a single point of failure?
- Is a distributed transaction required?
- Are retries idempotent?
- How is version compatibility handled?
- Can the unit be deployed independently without breaking business consistency?

## 26.6 Propose an Incremental Plan

Refactor architecture in stages:

1. Add test protection.
2. Establish internal module boundaries.
3. Invert source-code dependencies.
4. Isolate external types.
5. Move business rules.
6. Only then consider physically separating services or databases.

---

# 27. Severity Levels

## CRITICAL

- Core rules depend on an uncontrollable external mechanism and evolution is already blocked.
- Cyclic dependencies prevent independent build or release.
- A shared database creates risk of cross-service data corruption.
- A security or trust boundary is not isolated.
- The architecture causes an unacceptable single point of failure or real-time failure.

## HIGH

- Business rules are scattered across several entry points and adapters.
- A use case directly depends on framework types.
- A stable component depends on a highly volatile SDK.
- Services are nominally independent but must actually be released together.
- A critical use case cannot be tested without a database or network.

## MEDIUM

- Component responsibilities are unclear;
- A shared module is bloated;
- Boundary DTOs are reused excessively;
- The directory structure does not communicate the business;
- Architecture tests and ADRs are missing.

## LOW

- Naming, directories, or diagrams can be improved;
- A local boundary can be tightened;
- Optional metrics are missing.

---

# 28. Agent Output Template

````markdown
## Architecture Conclusion
[Overall assessment and the most important structural risk]

## System Policies and Mechanisms
- Core business policies: ...
- Application use cases: ...
- External mechanisms: ...

## Current Dependency Graph
```mermaid
flowchart LR
  ...
```

## Findings
1. [CRITICAL|HIGH|MEDIUM|LOW] Finding title
   - Evidence:
   - Violated principle:
   - Impact:
   - Recommended boundary:
   - Migration steps:

## Recommended Target Structure
```text
...
```

## Ports and Data Contracts
- Input Port: ...
- Output Port: ...
- Request/Response: ...

## Phased Migration
1. Phase 1: ...
2. Phase 2: ...
3. Phase 3: ...

## Verification
- [ ] Dependency-rule tests
- [ ] Use-case tests
- [ ] Adapter contract tests
- [ ] Performance/real-time tests
- [ ] Deployment and rollback exercises

## Decisions and Trade-offs
- Retained technical debt: ...
- Reevaluation triggers: ...
````

---

# 29. Example: Decoupling Web and Database Concerns

## Incorrect Structure

```python
@app.post("/orders")
def create_order(request: Request, session: Session):
    payload = request.json()
    if session.query(Product).get(payload["sku"]).stock < payload["quantity"]:
        return JSONResponse({"error": "out of stock"}, status_code=409)
    order = OrderModel(**payload)
    session.add(order)
    session.commit()
    kafka_producer.send("order-created", order.to_json())
    return JSONResponse(order.to_dict(), status_code=201)
```

Problems:

- The Controller owns business rules, transaction management, database access, and event publication.
- The use case cannot be tested independently of Web/ORM/Kafka.
- The domain model and database model are conflated.
- Failure semantics are bound to HTTP.

## Target Structure

```python
from dataclasses import dataclass
from typing import Protocol


@dataclass(frozen=True)
class CreateOrderRequest:
    customer_id: str
    sku: str
    quantity: int


@dataclass(frozen=True)
class CreateOrderResponse:
    order_id: str
    status: str


class InventoryPort(Protocol):
    def reserve(self, sku: str, quantity: int) -> None: ...


class OrderRepository(Protocol):
    def save(self, order: "Order") -> None: ...


class DomainEventPublisher(Protocol):
    def publish(self, event: object) -> None: ...


class CreateOrder:
    def __init__(
        self,
        inventory: InventoryPort,
        orders: OrderRepository,
        events: DomainEventPublisher,
    ) -> None:
        self._inventory = inventory
        self._orders = orders
        self._events = events

    def execute(self, request: CreateOrderRequest) -> CreateOrderResponse:
        self._inventory.reserve(request.sku, request.quantity)
        order = Order.create(
            customer_id=request.customer_id,
            sku=request.sku,
            quantity=request.quantity,
        )
        self._orders.save(order)
        self._events.publish(OrderCreated.from_order(order))
        return CreateOrderResponse(order_id=order.id, status=order.status)
```

HTTP Adapter:

```python
@app.post("/orders")
def create_order_endpoint(payload: CreateOrderHttpBody):
    try:
        response = create_order.execute(
            CreateOrderRequest(
                customer_id=payload.customer_id,
                sku=payload.sku,
                quantity=payload.quantity,
            )
        )
        return JSONResponse(asdict(response), status_code=201)
    except OutOfStock:
        return JSONResponse({"error": "out_of_stock"}, status_code=409)
```

SQL, Kafka, and HTTP remain at the periphery. The use case depends only on ports it defines.

---

# 30. Example: Target Structure for Robotic Software

```text
robot_system/
├── mission/
│   ├── domain/
│   │   ├── mission.py
│   │   ├── safety_policy.py
│   │   └── navigation_goal.py
│   ├── application/
│   │   ├── start_mission.py
│   │   ├── pause_mission.py
│   │   └── ports.py
│   └── adapters/
│       ├── ros2_mission_node.py
│       ├── sqlite_mission_repository.py
│       └── cloud_mission_gateway.py
├── motion/
│   ├── domain/
│   │   ├── trajectory.py
│   │   └── velocity_limits.py
│   ├── application/
│   │   └── execute_trajectory.py
│   └── adapters/
│       ├── can_motor_driver.py
│       └── simulation_motor_adapter.py
├── perception/
│   ├── application/
│   └── adapters/
│       ├── realsense_adapter.py
│       └── recorded_dataset_adapter.py
└── main/
    ├── config.py
    └── bootstrap.py
```

Dependency direction:

```text
ROS2 / DDS / CAN / RealSense / Database / Cloud
                    ↓ implements
             Application Ports
                    ↓
               Use Cases
                    ↓
             Domain Policies
```

This structure allows:

- A simulation Adapter to replace real hardware;
- Mission and control rules to be tested without ROS2;
- Cameras, databases, and cloud interfaces to change without modifying core mission logic;
- Real-time paths to remain separate from non-real-time mission orchestration.

---

# 31. Architecture Test Example

A Python project can use a static dependency-analysis tool or a custom test:

```python
def test_domain_does_not_import_frameworks():
    forbidden = {
        "fastapi",
        "sqlalchemy",
        "rclpy",
        "boto3",
        "kafka",
    }
    imports = collect_imports("robot_system/mission/domain")
    assert imports.isdisjoint(forbidden)
```

Also verify:

- `domain` does not depend on `application`, `adapters`, or `main`;
- `application` does not depend on concrete Adapters;
- One bounded context does not import another context's internal modules directly;
- The component graph is acyclic;
- The public API surface is controlled.

---

# 32. Final Checklist

## Business Policies

- [ ] Core business rules have an explicit location.
- [ ] Domain invariants are not scattered through UI, SQL, messaging, or drivers.
- [ ] Use cases are organized around user goals.
- [ ] The top-level structure communicates the business domain.

## Dependency Rule

- [ ] Source-code dependencies point toward higher-level policy.
- [ ] The core does not import framework, database, network, or hardware types.
- [ ] Interfaces are defined on the client side.
- [ ] The component dependency graph is acyclic.
- [ ] Unstable components depend on stable components, not the reverse.

## Boundaries

- [ ] Every boundary has a clear reason related to change or deployment.
- [ ] Data crossing a boundary is simple and does not carry outer-layer types.
- [ ] Boundaries have controlled entry points and visibility restrictions.
- [ ] The design does not over-abstract hypothetical future needs.

## Components

- [ ] Code that changes together is grouped together.
- [ ] Code not used together is not forced into the same dependency.
- [ ] Release units have a coherent purpose and versioning semantics.
- [ ] `common/shared/utils` has not become a coupling dump.

## Frameworks and Data

- [ ] Database, Web, messaging, and SDKs are treated as Adapters.
- [ ] ORM models do not invade core use cases.
- [ ] Transaction boundaries match business use cases.
- [ ] Framework lifecycle is managed only at the periphery.

## Services

- [ ] Service boundaries have business or operational justification.
- [ ] There is no implicit coupling through a shared database.
- [ ] The architecture is not a distributed monolith that requires synchronized deployment.
- [ ] Internal service dependencies still point in appropriate directions.

## Tests

- [ ] Entities and Use Cases can be tested without external mechanisms.
- [ ] Adapters have contract tests.
- [ ] Only a small number of necessary end-to-end tests exist.
- [ ] Architecture rules are checked automatically.

## Operations and Evolution

- [ ] Observability does not invade business rules.
- [ ] Failure, retry, idempotency, and rollback boundaries are explicit.
- [ ] Important decisions have ADRs.
- [ ] Retained technical debt has reevaluation criteria.

---

# 33. Pragmatic Correction Rules

Do not apply the following mechanically:

- Do not require every project to have exactly four layers.
- Do not require an interface for every class or Repository.
- Do not require every module to be independently deployable.
- Do not assume microservices are inherently better than a monolith.
- Do not build a complex domain model for a simple CRUD system.
- Do not use "the database is a detail" as an excuse to ignore consistency, query performance, or data lifecycle.
- Do not use dependency inversion to create large numbers of wrappers with no business meaning.
- Do not damage real-time behavior, throughput, memory use, or deployment feasibility in pursuit of layer purity.
- Do not split services before the domain boundaries are understood.

Final evaluation criteria:

1. Are business rules protected?
2. Is change confined to a reasonable scope?
3. Does dependency direction support long-term evolution?
4. Can external mechanisms be replaced, or at least isolated for testing?
5. Is the architectural cost proportional to system complexity and risk?
