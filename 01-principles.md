# 1. Core C++ Development Principles

## Primary goals

C++ code in this project should be:

- clear before clever
- cohesive before generic
- explicit before implicit
- testable before convenient
- encapsulated before flexible
- stable under change
- suitable for the target runtime and platform

## Design priorities

Prefer code that makes illegal states difficult or impossible to represent.

Prefer small, intention-revealing APIs over broad objects that expose their internals.

Prefer explicit ownership and lifetime over implicit sharing or hidden global access.

Prefer vertical, behavior-preserving refactoring over large speculative rewrites.

Prefer simple value types, functions, and modules over unnecessary inheritance or design patterns.

## What this guide is not trying to do

This guide is not trying to make the codebase:

- more abstract for its own sake
- more object-oriented for its own sake
- full of interfaces where no meaningful boundary exists
- fragmented into tiny classes with no independent purpose
- rewritten from scratch without evidence that recovery is impractical

## Quality standard

A change should improve at least one of:

- readability
- encapsulation
- cohesion
- testability
- ownership clarity
- dependency direction
- reliability
- diagnosability
- portability
- resource predictability

A change that only moves complexity around is not an improvement.
