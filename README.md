# C++ Coding Guide Pack

This pack defines a practical quality guide for modern C++ development, with a strong emphasis on maintainability, encapsulation, testability, SOLID design, and embedded C++ constraints.

It is intended to be used by human developers, reviewers, and AI coding agents such as Codex.

## Files

- `01-principles.md` — overall engineering principles and quality goals.
- `02-architecture-and-boundaries.md` — module boundaries, dependency direction, ownership, and build structure.
- `03-encapsulation-and-design.md` — encapsulation, cohesion, interfaces, SOLID, and class design.
- `04-modern-cpp-style.md` — modern C++ language usage and idioms.
- `05-naming-and-file-conventions.md` — naming, file naming, namespaces, tests, and public API naming.
- `06-code-documentation.md` — comments and public API documentation.
- `07-modern-embedded-cpp.md` — embedded-specific C++ guidance.
- `08-error-handling-and-state.md` — error handling, state management, invariants, and concurrency.
- `09-testing-and-refactoring.md` — characterization tests, refactoring workflow, and test strategy.
- `10-code-review-guide.md` — practical code review guide focused on critical risks.

## How to use this guide

Use this as a project coding standard. Adapt it where the project has existing constraints, but avoid weakening rules that protect correctness, testability, ownership, or maintainability.

The goal is not to make code more abstract, more object-oriented, or more clever.

The goal is code that is:

- strongly encapsulated
- cohesive
- loosely coupled
- explicit in ownership
- clear in dependency direction
- testable
- stable under change
- suitable for the target platform
