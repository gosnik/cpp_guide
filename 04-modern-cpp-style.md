# 4. Modern C++ Style

## General style

Write simple, idiomatic C++.

Prefer:

- RAII for resource management
- `const` correctness
- `constexpr` for compile-time constants and simple compile-time computation
- scoped enums
- range-based loops
- structured bindings when they improve readability
- standard library containers and algorithms
- clear value types
- move semantics where ownership transfer is intended
- `std::optional` for optional values
- `std::variant` for closed sets of alternatives
- `std::string_view` for non-owning string parameters where lifetime is clear
- `std::span` for non-owning contiguous buffers where supported

Avoid:

- raw owning pointers
- manual `new` and `delete`
- `NULL` instead of `nullptr`
- magic numbers where named constants would explain intent
- unchecked casts
- unnecessary casts
- redundant parentheses or overly complex expressions
- macro-heavy abstractions
- hidden ownership transfer
- excessive template cleverness
- broad using-directives in headers
- throwing exceptions across boundaries that prohibit them

Use `auto` when it removes noise without hiding important type information. Prefer explicit types when the type is part of the meaning, affects ownership, or makes conversions visible.

## Ownership

Ownership must be obvious from the type.

Use:

- values for simple owned data
- references for required non-owning access
- pointers for optional non-owning access where `nullptr` is meaningful
- `std::unique_ptr` for exclusive dynamic ownership
- `std::shared_ptr` only for genuine shared ownership
- `std::weak_ptr` to break shared ownership cycles

Do not use `shared_ptr` as a default ownership model.

Do not leave ownership implied by comments or naming. If a function transfers ownership, the function signature should make that transfer clear.

## RAII

Use RAII for every resource with acquire/release semantics.

This includes:

- memory
- file handles
- locks
- transactions
- hardware reservations
- DMA channels
- peripheral enable/disable scopes
- temporary configuration changes

Prefer destructor-owned guards or owning wrapper types when they can express the lifetime safely.

Do not rely on manual cleanup paths, paired `init()`/`deinit()` calls, or scattered rollback code when scope-bound cleanup can make the release deterministic.

## Headers

Headers should be minimal and stable.

Prefer forward declarations where appropriate.

Avoid placing unnecessary includes in headers.

Do not expose private implementation details in public headers.

Avoid non-inline function definitions in headers unless templates or deliberate header-only code require it.

Keep namespaces explicit and narrow. Avoid polluting global scope from headers.

## Names

Names should describe intent, not mechanics.

Follow the project naming and file conventions consistently. See `05-naming-and-file-conventions.md` for the detailed convention.

## Functions

Functions should be short enough to understand as one idea.

A function is likely too long if it:

- mixes validation, policy, state mutation, and I/O
- has deeply nested control flow
- requires comments to explain major sections
- handles several unrelated cases
- cannot be tested without exercising many unrelated paths

Extract functions when it improves naming, testing, or separation of responsibilities.

Do not extract functions merely to reduce line count if it hides the logic.

Prefer `override` on overriding virtual functions. Use `final` only when preventing further derivation is an intentional design constraint.

Use `= default` and `= delete` for special member functions when default behavior or forbidden operations should be explicit.

Avoid unnecessary copies. Pass by value, reference, pointer, or view according to ownership, lifetime, and cost.

## Libraries

Prefer established project libraries over recreating equivalent utilities.

Use formatting and string libraries consistently. If `fmt` is available in the project, prefer it over unsafe C-style string formatting and avoid mixing formatting styles without a reason.

If the project uses ETL or project-specific application libraries such as `applibs` for fixed-capacity containers, algorithms, messaging, logging, or platform abstractions, prefer those facilities where they fit the target constraints.

Do not introduce a new dependency for a trivial helper, but do not hand-roll complex or safety-sensitive behavior when a project-approved library already exists.

## Error handling

Use project-standard error handling consistently.

Acceptable approaches may include:

- return values
- status/result types
- `std::optional`
- `std::expected` where available
- exceptions where allowed by the platform and project

Do not mix unrelated error-handling styles without justification.
