# 8. Error Handling, State, and Concurrency

## Error handling conventions

Each project should define one primary error-handling style.

Exceptions, explicit result types, or both may be appropriate depending on the project and target policy.

Exceptions fit rare, non-local failures where unwinding is allowed and RAII cleanup makes the failure path safer.

Explicit result/status types fit expected and recoverable failures such as timeouts, missing devices, malformed packets, CRC errors, invalid user input, and bus contention.

A codebase may use both exceptions and result types, but only with clear boundary rules.

Errors should preserve useful context without forcing every caller to parse strings.

When exceptions are disabled, use a standard result type rather than ad hoc booleans, integers, or nullable returns.

Prefer:

- `Result<T, Error>`
- `Status`
- `expected<T, Error>` where available
- project-specific lightweight equivalents for embedded targets

Avoid:

- swallowing errors
- returning ambiguous booleans
- returning `nullptr` without error detail
- mixing return codes with out-parameters inconsistently
- logging an error but returning success
- ignoring returned status values
- logging instead of reporting failure
- using exceptions in code compiled with exceptions disabled
- mixing exceptions, status codes, and sentinel values without clear rules

Every meaningful failure mode should be propagated, handled, or logged according to the project convention. Silent fallback is acceptable only when the fallback is intentional and safe.

## Invariants

Objects should maintain their own invariants.

A caller should not need to call methods in a fragile sequence to keep an object valid unless that sequence is explicitly modeled.

Prefer APIs that make invalid transitions impossible or explicit.

## State ownership

State should have one clear owner.

Avoid multiple unrelated objects mutating the same state.

If shared state is necessary, define:

- owner
- access rules
- synchronization
- lifetime
- valid transitions
- test strategy

## State machines

Use explicit state machines for workflows with important states or transitions.

Prefer named states and named transitions over scattered booleans.

Avoid using many loosely related flags to represent complex state.

## Concurrency

Concurrency rules must be documented where relevant.

For each concurrent component, define:

- owning thread/task
- allowed calling contexts
- blocking behavior
- synchronization primitives
- callback threading model
- shutdown behavior

Avoid data races by design rather than relying on review to catch them.

When modifying cross-module behavior, check for race conditions, deadlocks, missed signals, and invalid startup or shutdown ordering.

## Callbacks

Callbacks should have clear rules.

Document:

- whether callbacks are synchronous or asynchronous
- which context invokes them
- whether they may call back into the owner
- whether they may block
- lifetime guarantees for callback arguments

Avoid holding locks while invoking callbacks.

## Integration checks

Modules should integrate through explicit contracts.

When adding or changing a module, verify:

- the public API matches its documented responsibility
- dependent modules still receive the states and signals they expect
- startup and shutdown sequences preserve existing invariants
- hardware drivers, communication interfaces, middleware, and OS APIs are used according to their ownership and threading rules
- external dependencies such as sensors, actuators, protocols, and services can be mocked or abstracted for tests where practical

Avoid changes that rely on undocumented behavior in another subsystem.
