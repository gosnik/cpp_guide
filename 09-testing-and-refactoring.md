# 9. Testing and Refactoring

## Refactoring principle

Refactoring is expected to preserve behavior unless the behavior change is intentional and documented.

For fragile or poorly understood code, characterization tests are usually the safest first step before major structural changes.

## Characterization tests

Characterization tests capture what the system currently does.

They are most useful around:

- critical workflows
- fragile logic
- legacy behavior
- bug-prone state transitions
- protocol handling
- serialization/deserialization
- safety or recovery behavior
- boundary conditions

The goal is not to prove the current design is good. The goal is to make the current behavior visible enough that accidental changes are caught.

## Test levels

A healthy test strategy usually includes a mix of:

- unit tests for pure logic
- component tests for module boundaries
- fake hardware or fake services for infrastructure seams
- integration tests for important workflows
- hardware-in-the-loop tests where target behavior cannot be validated off-target

## Testable design

Testable code tends to have these properties:

- policy logic is separated from I/O
- hardware and platform calls are isolated
- clocks, storage, network, and hardware boundaries can be substituted where useful
- inputs and outputs are deterministic
- hidden global state is avoided
- mutable global variables are avoided
- tests do not rely on real sleeps
- fake clocks and fake transports are available where timing or I/O matters
- major logic paths can be exercised without exposing production internals unnecessarily

Good API boundaries document behavior expectations well enough that tests can assert outcomes without relying on implementation details.

## Refactoring approach

Refactoring is usually safest when it starts with understanding and stabilization, then moves one meaningful workflow at a time.

A typical sequence is:

1. Map the current structure.
2. Identify high-value fragile workflows.
3. Add characterization tests around behavior that should not change accidentally.
4. Define or update the architecture guide when boundaries or ownership rules are unclear.
5. Extract core logic from monoliths where doing so improves testability or cohesion.
6. Improve encapsulation by moving invariants into the owning class or module.
7. Consolidate excessive micro-abstractions where fragmentation obscures the design.
8. Introduce clean seams at meaningful boundaries.
9. Enforce important boundaries with the build system where practical.
10. Refactor vertically, one workflow at a time.
11. Build and run tests after meaningful changes.

## Vertical iteration

Refactoring one feature or workflow end-to-end is usually safer than rearranging the whole codebase by layer.

Each vertical slice should leave the system a little better than before.

## Avoid big-bang rewrites

A full rewrite should be reserved for cases where the existing code is demonstrably unrecoverable.

A rewrite loses implicit behavior, field knowledge, bug fixes, and edge cases.

Incremental recovery is usually safer unless there is strong evidence that replacement has lower risk.
