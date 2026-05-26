# 7. Modern C++ for Embedded Development

## Embedded C++ goals

Embedded C++ should be:

- deterministic
- resource-aware
- testable off-target where practical
- explicit about ownership and lifetime
- clear about hardware boundaries
- robust under startup, shutdown, reset, and fault conditions
- careful with concurrency, interrupts, DMA, and shared state

Modern C++ is suitable for embedded work when used deliberately. Avoid features or patterns that compromise predictability, binary size, timing, or debuggability on the target.

## Platform boundaries

Keep hardware and platform-specific code at the edges.

Core application logic should be buildable and testable on a host machine where practical.

Use narrow interfaces or adapter types around:

- GPIO
- I2C, SPI, UART, CAN, USB
- ADC, PWM, timers
- clocks and delays
- persistent storage
- DMA
- interrupts
- RTOS primitives
- bootloader/update mechanisms
- power management
- watchdogs

Do not let register-level or HAL-specific types leak into core application logic unless the module is explicitly a driver.

Use scope-bound guards for temporary platform state where practical, such as peripheral enable/disable scopes, interrupt masks, clock configuration changes, and hardware reservations.

## Allocation

Avoid unbounded dynamic allocation in real-time or safety-critical paths.

Prefer:

- static allocation
- stack allocation with known bounds
- fixed-capacity containers
- object pools where justified
- explicit initialization phases
- caller-provided buffers for drivers and codecs

Avoid large stack objects in timing-sensitive paths unless their size and lifetime are deliberate.

If heap allocation is used, define where it is allowed:

- startup only
- configuration loading only
- non-real-time tasks only
- never in interrupt context
- never in hard real-time control loops

Document the allocation policy.

Prefer stack or static storage for deterministic behavior when lifetime and capacity are known.

## Exceptions and RTTI

Embedded projects must define an explicit exception and RTTI policy.

Do not reject exceptions or RTTI automatically just because the target is embedded. On capable modern embedded hardware, both can improve correctness and readability when used deliberately.

For each target, document whether C++ exceptions are:

- enabled
- disabled
- allowed only outside real-time paths
- allowed only during startup/configuration
- allowed in host tools but not firmware
- prohibited across C ABI, ISR, RTOS, or driver boundaries

Exceptions are most useful for rare, non-local failures where the target supports unwinding and RAII cleanup makes failure paths safer. Good uses may include startup and configuration failures, host-side tools, non-real-time service code, complex parsing, unrecoverable construction failures, and failures that must propagate across several layers.

Exceptions are controversial in embedded C++ because the tradeoffs are target-specific. Older microcontrollers, minimal runtimes, strict certification environments, and hard real-time loops may make exceptions inappropriate. More capable microcontrollers, RTOS-based systems, Linux-capable SBCs, simulators, and host tools may support exceptions well enough that they reduce risk compared with manual error propagation.

The correct question is not "is this embedded?" but "does this target, execution context, and failure mode benefit from exceptions while still meeting timing, memory, and reliability requirements?"

Exceptions can improve embedded code when they prevent:

- partially initialized objects
- duplicated cleanup paths
- ignored status values
- deeply nested error checks
- manual rollback code across multiple acquired resources

Exceptions can harm embedded code when they introduce:

- unacceptable binary-size growth
- unacceptable stack or runtime overhead
- unbounded or poorly understood latency
- failure paths that cross unsafe ABI or scheduler boundaries
- dependencies on runtime support that is unavailable or untested on the target

For each embedded target that enables exceptions, document:

- whether the toolchain and standard library support exceptions correctly
- whether unwind tables and runtime support are acceptable
- where exceptions are allowed
- where exceptions are forbidden
- whether exception use affects binary size, latency, stack use, or memory budget
- whether exceptions may cross task, thread, C ABI, ISR, callback, or module boundaries

Exceptions should normally be forbidden in:

- interrupt handlers
- hard real-time control loops
- low-level drivers with strict timing
- DMA completion paths
- allocator-critical paths
- C ABI boundaries
- bootloader or minimal-runtime code
- code where the runtime cannot guarantee safe unwinding

If exceptions are enabled:

- use them only where the execution context permits unwinding
- keep ISR, hard real-time, driver hot-path, and C ABI boundary code exception-free unless explicitly proven safe
- avoid using exceptions for normal control flow
- measure binary-size, stack-use, and runtime impact where resource budgets are tight
- translate exceptions to explicit errors at boundaries that cannot throw
- use explicit `Status`, `Result<T, Error>`, or `expected<T, Error>` style returns for expected, local, recoverable failures

If exceptions are disabled:

- do not use APIs that rely on throwing for normal failure
- return explicit standard status/result types such as `Result<T, Error>`, `Status`, `expected<T, Error>`, or a project-specific lightweight equivalent
- avoid ambiguous `bool`, integer, or nullable returns for failures that need error detail
- avoid throwing standard library APIs in target paths
- avoid constructors that can fail silently
- use factories or explicit `init()` functions where construction can fail
- preserve RAII for cleanup even without exceptions
- ensure library choices are compatible with the exception policy

For each target, document whether RTTI is enabled or disabled.

RTTI is reasonable and often preferable when it improves type-safe diagnostics, tooling, framework integration, or controlled polymorphic behavior, and when binary-size and runtime costs are acceptable.

If RTTI is enabled:

- use it deliberately, not as a substitute for clear ownership or explicit state modeling
- keep type checks out of hot paths unless measured and justified
- avoid designs that require frequent `dynamic_cast` to recover concrete types

If RTTI is disabled:

- avoid `dynamic_cast` and `typeid`
- model alternatives with explicit variants, tags, or interfaces

## Interrupts

Interrupt handlers should be short and deterministic.

Prefer ISRs that:

- acknowledge hardware
- capture minimal data
- update atomic/volatile-safe flags where appropriate
- push work to a queue or scheduler
- avoid blocking
- avoid allocation
- avoid logging unless specifically safe
- avoid calling complex application logic

Clearly document data shared between interrupt and non-interrupt contexts.

Do not allocate dynamically or make blocking calls in interrupt context.

## Volatile, atomics, and memory-mapped I/O

Use `volatile` for memory-mapped registers and hardware-observed state.

Do not use `volatile` as a substitute for thread synchronization.

Use atomics or platform synchronization primitives for communication between execution contexts where appropriate.

Wrap memory-mapped I/O behind small driver or register-access abstractions.

## Concurrency and RTOS use

Thread/task interactions should be explicit.

Define:

- which task owns which state
- which APIs are thread-safe
- which APIs must be called from one context only
- which locks or queues protect shared data
- priority and timing assumptions
- blocking behavior

Avoid hidden blocking in low-level APIs.

Avoid holding locks across callbacks, I/O, logging, or user-provided code.

## Drivers

Drivers should expose intention-level operations, not arbitrary register manipulation.

A good driver:

- owns hardware initialization for its device
- validates configuration
- exposes a narrow API
- clearly documents timing and concurrency assumptions
- separates transport from device logic where useful
- is testable with fake transports where practical
- does not leak unnecessary HAL details

Drivers should use RAII-style ownership for resources with acquire/release semantics, including DMA channels, peripheral reservations, temporary register configuration, bus locks, and transaction scopes.

Use project-approved embedded libraries where available. Prefer ETL fixed-capacity containers and existing application-library abstractions such as `applibs` over new ad hoc data structures when they satisfy the constraints.

## Time

Do not scatter direct calls to system time, sleeps, or delays through application logic.

Prefer a time abstraction for testable logic:

- monotonic clock
- timer scheduler
- deadline or duration types
- fake clock for tests

Use strong types for time units where practical.

## Units and physical quantities

Avoid raw numbers for physical quantities.

Prefer named constants or strong value types for:

- milliseconds, microseconds, ticks
- volts, amps, watts
- pressure, depth, temperature
- encoder counts
- PWM values
- velocities and accelerations
- buffer sizes and packet lengths

Document units at API boundaries.

## Error handling in embedded code

Embedded failures should be explicit and diagnosable.

Prefer result/status types that can represent:

- timeout
- invalid configuration
- bus error
- device not present
- CRC/checksum failure
- buffer overflow
- unsupported command
- transient busy state
- hardware fault

Avoid silently ignoring hardware or communication errors.

## Logging and diagnostics

Logging must not compromise timing or reliability.

Define where logging is allowed and how it behaves under load.

For embedded diagnostics, prefer structured event codes or compact telemetry where resources are constrained.

Avoid excessive string formatting in hot paths.

Use the project-standard formatting and logging path consistently. Avoid mixing `printf`-style formatting, direct stream output, and formatting libraries unless the boundary requires it.

## Startup and initialization

Startup order should be deterministic.

Avoid initialization order dependencies across global objects.

Prefer explicit initialization through a composition root, board bring-up function, or application bootstrap.

Check and report initialization failures.

## Power and fault behavior

Embedded code should consider:

- brownout/reset behavior
- watchdog recovery
- safe outputs on startup and failure
- degraded modes
- persistent fault counters
- communication loss
- sensor stale data
- actuator limits
- power state transitions

Failure behavior should be designed, not accidental.

## Templates and zero-cost abstractions

Templates can be valuable in embedded code, especially for type safety and zero-cost abstraction.

Use templates when they provide:

- compile-time configuration
- type-safe units
- fixed-capacity containers
- transport/device composition
- elimination of runtime overhead

Avoid template designs that produce excessive compile times, large binaries, unreadable errors, or fragile APIs.

## Embedded rule of thumb

Use the highest-level C++ abstraction that still gives predictable timing, memory use, code size, and debuggability for the target.
