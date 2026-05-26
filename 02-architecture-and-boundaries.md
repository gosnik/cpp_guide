# 2. Architecture and Boundaries

## Architecture guide

Each project should maintain a short architecture guide covering:

- module boundaries
- dependency direction
- ownership rules
- encapsulation rules
- error-handling conventions
- testing strategy
- naming conventions
- allowed global state, if any
- platform and hardware abstraction rules

This guide should be short, practical, and maintained with the code.

## Dependency direction

Dependencies should generally point inward:

```text
Application / policy / domain logic
        ^
        |
Infrastructure adapters
I/O, hardware, storage, networking, UI, OS APIs
```

Core logic should not directly depend on platform APIs, hardware drivers, networking stacks, file systems, or UI code.

Infrastructure should depend on interfaces, value types, or narrow abstractions owned by the application/domain layer.

## Module boundaries

A module should have:

- a clear responsibility
- a narrow public API
- private implementation details
- minimal dependencies
- tests at its boundary
- no hidden dependence on unrelated global state

Avoid modules that are just dumping grounds for helpers, constants, or unrelated classes.

## Build-system boundaries

Where practical, enforce architecture using the build system:

- split code into CMake targets or equivalent libraries
- keep core logic in platform-independent targets
- keep hardware, OS, networking, and UI adapters in edge targets
- avoid circular target dependencies
- make invalid dependencies fail at build time

## Cyclic dependencies

Cyclic dependencies are a design smell.

Resolve cycles by:

- moving shared value types to a lower-level module
- introducing a narrow interface at a meaningful boundary
- inverting dependencies
- splitting mixed responsibilities
- removing unnecessary cross-calls

## Global state

Global state is allowed only when explicitly justified.

If global state is unavoidable, it must be:

- small
- documented
- initialized deterministically
- accessed through a narrow API
- safe under concurrency/interrupt constraints
- replaceable or controllable in tests where practical

Avoid hidden singletons and service locators. They make dependencies invisible and testing harder.
