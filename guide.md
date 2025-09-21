1. **Code Quality & Maintainability**
    - Correct and consistent use of C++17 features (e.g., constexpr, structured bindings, smart pointers)
    - Clear separation of concerns, minimal coupling, and high cohesion
    - Readable, self-documenting code with minimal but sufficient comments
    - Avoidance of unnecessary complexity or overengineering
    - Prefer range-based for loops over traditional loops
    - Use of auto where appropriate
    - Use nullptr instead of NULL
    - Prefer override/final for virtual methods where appropriate
    - Use = default or = delete for special member functions where appropriate
    - Avoid magic numbers; use named constants
    - **SOLID Principles:**
      - **S: Single Responsibility** – Each class/module should have one well-defined responsibility
      - **O: Open/Closed** – Code should be open for extension but closed for modification
      - **L: Liskov Substitution** – Derived classes should be usable anywhere their base class is expected
      - **I: Interface Segregation** – No class should be forced to depend on methods it does not use
      - **D: Dependency Inversion** – High-level modules should not depend on low-level details; both should depend on abstractions

2. **Leverage Existing libraries**
    - Avoid unsafe C-style string manipulation; let fmtlib handle formatting and string sizes safely.
    - Ensure logging, debugging, and user-facing messages use fmt uniformly rather than mixing printf-style APIs.
    - If there is an ETL (Embedded Template Library) class(es) for the task being performed - use it.
    - If there is an applibs class(es) for the task being performed - use it.
    - Perfer library usage over re-creation.
  
3. **Memory Safety & Resource Management**
    - No memory leaks, dangling pointers, or uninitialized variables
    - Bounds checks for array or vector access
    - Proper RAII usage with smart pointers (unique_ptr, shared_ptr) where appropriate
    - No raw new/delete unless strictly necessary and justified
    - Prefer stack allocation for deterministic embedded performance

4. **Embedded-System Constraints**
    - No dynamic allocation or blocking calls in ISRs
    - Deterministic execution paths with bounded latency
    - Efficient use of limited CPU, RAM, and storage resources
    - Proper handling of hardware registers, interrupts, and peripherals

5. **Error Handling & Fault Tolerance**
    - Clear error propagation or logging for all failure modes
    - Fail-safe defaults and robust handling of unexpected states
    - Minimal use of exceptions in performance-critical or real-time paths

6. **System Integration & Interaction Checks**
    - Validate that new or modified modules integrate cleanly with existing subsystems
    - Confirm consistent interface contracts and proper use of APIs
    - Check for potential race conditions, deadlocks, or missed signals when interacting with other components
    - Ensure hardware drivers, comms interfaces, or middleware layers are used according to design requirements
    - Confirm changes do not break assumptions or invariants relied upon by dependent modules
    - Look for potential regressions in cross-module workflows or startup/shutdown sequences
    - Verify external dependencies (e.g., sensors, actuators, protocols) are correctly mocked or abstracted for testing

7. **Readability, Style & Consistency**
    - Consistent formatting, naming conventions, and brace style
    - Minimal dependencies and header cleanliness
    - Proper use of namespaces to prevent global pollution

8. **Performance & Efficiency**
    - Efficient data structures, minimal copies, and no redundant computations
    - Avoid redundant parentheses, unnecessary casts, or overly complex expressions
    - Correct use of constexpr, inline, and references where beneficial
    - Avoid unnecessary heap fragmentation or large stack objects

9. **Testability & Documentation**
    - Unit tests or clear testing hooks for all major logic paths
    - Avoid global variables
    - Ensure code is modular and unit-testable
    - Clear API boundaries and behavior expectations
    - Minimal but meaningful comments where code intent is non-obvious
