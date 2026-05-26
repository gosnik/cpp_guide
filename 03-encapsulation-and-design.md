# 3. Encapsulation and Design

## Encapsulation

Encapsulation is a first-class design goal.

Classes and modules should protect their invariants. Users of an API should not need to know how the object is internally represented to use it correctly.

Avoid:

- public mutable data
- exposed containers that callers can mutate freely
- public setters that allow invalid intermediate states
- friend declarations used to bypass design problems
- leaking implementation types in public APIs
- functions that require callers to manually maintain object invariants

Prefer:

- intention-revealing methods
- narrow APIs
- immutable value objects where practical
- constructors or factories that establish valid state
- methods that preserve invariants internally
- private helper functions for internal steps
- clear ownership and lifetime rules

## Class design

A class should have one clear reason to change.

A class is probably too large if it:

- manages unrelated state
- performs I/O and business logic together
- owns multiple independent workflows
- contains long methods with many decision branches
- requires many private helper sections for unrelated concerns
- is hard to test without large amounts of setup

A class may be too small or too fragmented if it:

- contains no meaningful state or invariant
- exists only to wrap one trivial function
- is always used together with several other tiny classes
- has an interface that mirrors a single implementation
- makes the design harder to understand

## SOLID, pragmatically applied

### Single Responsibility

Each class/module should have one primary responsibility and one main reason to change.

### Open/Closed

Prefer extension through composition and new modules rather than repeated modification of fragile core logic.

Do not introduce plugin-style abstractions unless variation is real or expected.

### Liskov Substitution

Derived types must preserve the expectations of the base type.

Avoid inheritance where behavior cannot be substituted safely.

### Interface Segregation

Prefer small, role-specific interfaces over large interfaces that force clients to depend on unused methods.

### Dependency Inversion

Core policy should depend on abstractions or value types, not infrastructure details.

Use interfaces at meaningful boundaries, not everywhere.

## Inheritance

Prefer composition over inheritance.

Use inheritance when:

- runtime polymorphism is genuinely needed
- the base interface represents a real substitutable role
- object lifetime is clear
- virtual destruction is correct where needed

Avoid inheritance for:

- code reuse only
- adding optional behavior
- hiding global state
- premature test seams
- artificial object-oriented design

## Interfaces

Add interfaces only at meaningful boundaries such as:

- hardware
- time
- storage
- network
- configuration
- message bus
- external APIs
- thread execution
- operating system services

Do not create an interface solely because a class exists.
