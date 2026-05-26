# 6. Comments and Documentation

Prefer self-documenting code over explanatory comments for ordinary control flow.

Code should communicate intent through clear names, small functions, strong types, narrow interfaces, and cohesive modules.

## Useful comments

Comments are most valuable when they explain information the code cannot express clearly.

Good comments often explain:

- why a decision was made
- hardware or platform constraints
- protocol quirks
- timing assumptions
- safety assumptions
- concurrency, ISR, or locking rules
- ownership and lifetime caveats
- units, ranges, and calibration details
- non-obvious tradeoffs
- links to standards, datasheets, issues, or ADRs

Avoid comments that simply repeat the code.

## Public API documentation

Public APIs should document the behavior that callers need to rely on.

Document:

- behavior and invariants
- ownership and lifetime expectations
- error cases and error-reporting style
- blocking behavior
- thread, task, and ISR safety
- units, valid ranges, and calibration assumptions
- side effects

The goal is for callers to use the API correctly without knowing the implementation.

## Implementation comments

Implementation comments should be rare but valuable.

Use them to preserve context that would otherwise be lost, especially around subtle tradeoffs, hardware behavior, concurrency, safety, or compatibility constraints.

If a comment is needed to explain ordinary control flow, first consider whether clearer names, smaller functions, stronger types, or a narrower interface would remove the need for the comment.
