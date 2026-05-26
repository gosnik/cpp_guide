# 10. Code Review Guide

Code review should focus first on correctness, risk, and whether the change leaves the code easier to reason about.

This is not a complete style audit. Detailed rules live in the topic-specific guide files.

## Behavior and failure modes

The most important review question is whether the code still does the right thing.

Look for:

- behavior changes that are accidental or undocumented
- important edge cases that are unhandled
- failure paths that are silent, ambiguous, or reported as success
- ignored `Status`, `Result`, `expected`, or equivalent return values
- unexpected states that do not fail safely
- tests or characterization coverage for important behavior

## Ownership and lifetime

Review ownership where resources, pointers, references, callbacks, or cross-module state are involved.

Look for:

- ownership that is unclear from the type or API
- acquire/release resources that are not expressed with RAII or a justified alternative
- raw owning pointers, unsafe shared ownership, leaks, dangling references, or uninitialized state
- manual cleanup paths that could be replaced by destructor-owned guards
- array, span, or container access that is not bounds-safe

## Boundaries and design

A good change should respect the shape of the system.

Look for:

- public APIs that expose internals or make invalid states easy to create
- classes or modules gaining unrelated responsibilities
- core logic depending directly on hardware, OS, UI, storage, networking, or other infrastructure details
- new cycles or unclear dependency direction
- interfaces added without a meaningful boundary
- changes that break assumptions relied on by callers or dependent modules

## Error handling

Error handling should match the project and target policy.

Look for:

- exceptions crossing prohibited boundaries such as C ABI, ISR, RTOS, or driver boundaries
- disabled-exception paths using ad hoc `bool`, integer, nullable, or inconsistent out-parameter error reporting
- construction failure that can leave objects silently invalid
- logging used instead of returning or propagating an error
- inconsistent mixing of exceptions and explicit status/result values

## Embedded and resource constraints

For embedded code, review the execution context as carefully as the logic.

Look for:

- allocation, blocking, logging, or formatting in timing-sensitive paths
- ISR code that does more than minimal deterministic work
- hardware reservations, DMA channels, peripheral scopes, or bus transactions without clear lifetimes
- exception policy, RTTI policy, allocation policy, timing assumptions, and units that are missing or unclear
- race conditions, deadlocks, missed signals, or unsafe callback behavior across tasks, ISRs, drivers, and RTOS boundaries
- startup, shutdown, reset, and fault behavior that is accidental rather than designed

## Maintainability

The maintainability question is whether the change reduces complexity or isolates it better.

Look for:

- names that hide intent
- comments explaining obvious mechanics instead of non-obvious reasoning
- unnecessary copies, casts, repeated work, or clever expressions
- headers that expose unnecessary dependencies
- project-approved libraries or abstractions being reimplemented
- tests that require excessive setup because policy logic is coupled to I/O or platform code
- follow-up risks that are worth documenting
