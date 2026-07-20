# ADR-009: Vertical Slice Development

## Status

Accepted

## Date

2026-07-20

---

# Context

PowerPulse is a modular monolith built using:

- Java 17
- Spring Boot
- Spring Modulith
- PostgreSQL
- Liquibase

The platform consists of multiple bounded contexts:

- Identity
- Energy Consumer
- Organization Profile
- Household Profile
- Site
- Energy Asset
- Energy Operations
- Fuel
- Maintenance
- Monitoring
- Analytics
- Recommendation
- Notification

A common mistake in software projects is to build systems horizontally.

For example:

```
Build all entities

↓

Build all repositories

↓

Build all services

↓

Build all controllers

↓

Finally make everything work
```

This creates large amounts of unfinished code.

Features cannot be demonstrated until very late in the project.

Business feedback arrives too late.

Integration problems accumulate.

---

# Decision

PowerPulse will be developed using Vertical Slice Development.

Each slice delivers one complete business capability.

A slice is complete only when it includes everything required for that capability.

---

# What Is a Vertical Slice?

A vertical slice includes every layer necessary to deliver one business use case.

Example:

```
HTTP API

↓

Application

↓

Domain

↓

Persistence

↓

Database

↓

Events

↓

Tests
```

The slice is deployable and testable.

---

# Slice Completeness

A slice is considered complete only when it includes:

- Domain model
- Commands
- Queries
- Application services
- Repository
- Liquibase migration
- REST API
- Validation
- Domain events
- Unit tests
- Integration tests
- Documentation

Nothing is left "for later."

---

# Build Order

PowerPulse follows the implementation sequence defined by ADR-008.

Within that sequence, each module is completed vertically before moving to the next.

Example:

```
Identity

↓

Energy Consumer

↓

Organization Profile

↓

Household Profile

↓

Site

↓

Energy Asset

↓

Energy Operations

↓

Fuel

↓

Maintenance

↓

Monitoring

↓

Analytics

↓

Recommendation

↓

Notification
```

Each module is finished before the next begins.

---

# Example: Energy Consumer Slice

The Energy Consumer slice is complete only after the following exist:

```
EnergyConsumer Aggregate

↓

Repository

↓

Liquibase Migration

↓

Application Service

↓

REST Controller

↓

Request DTOs

↓

Response DTOs

↓

Validation

↓

Domain Events

↓

Unit Tests

↓

Integration Tests

↓

Documentation
```

At that point:

A user can register an Energy Consumer.

The feature is production-ready.

Only then does development move to the next slice.

---

# Why Vertical Slices?

## 1. Deliver Working Software Earlier

Every completed slice produces usable functionality.

Progress is measurable by business capability rather than file count.

---

## 2. Faster Feedback

Stakeholders can validate features as they are completed.

Incorrect assumptions are discovered early.

---

## 3. Better Quality

Because each slice is fully integrated:

- APIs are exercised.
- Database migrations are verified.
- Domain rules are tested.
- Events are validated.

Problems are found immediately.

---

## 4. Reduced Technical Debt

No incomplete layers accumulate.

The codebase remains deployable throughout development.

---

# Relationship with Spring Modulith

Each bounded context is implemented as a Spring Modulith module.

Within each module:

```
Domain

↓

Application

↓

Infrastructure

↓

API
```

The module exposes only what other modules need.

Internal implementation remains encapsulated.

---

# Relationship with Domain-Driven Design

Vertical slices complement Domain-Driven Design.

DDD defines:

- business boundaries
- aggregates
- domain language

Vertical slices define:

- implementation order
- delivery strategy

DDD answers:

"What should we build?"

Vertical slices answer:

"In what order should we build it?"

---

# Testing Strategy

Every slice must include:

## Domain Tests

Business rules.

---

## Application Tests

Use cases.

---

## Integration Tests

Persistence.

Liquibase.

REST endpoints.

---

## Module Verification

Spring Modulith verification ensures module boundaries remain intact.

A slice is not complete without passing all tests.

---

# Definition of Done

A PowerPulse slice is complete only when:

- Domain model is implemented.
- Business rules are enforced.
- Liquibase migration exists.
- Database schema is verified.
- REST endpoints are operational.
- Events are published correctly.
- Tests pass.
- Documentation is updated.

If any item is missing, the slice is incomplete.

---

# Anti-Patterns

The following are explicitly rejected.

## Horizontal Development

```
All Entities

↓

All Repositories

↓

All Controllers

↓

All Services
```

Rejected.

---

## Database-First Development

Building tables before understanding the domain.

Rejected.

---

## API-First Without Domain

Creating endpoints before business behaviour exists.

Rejected.

---

## Intelligence-First Development

Building forecasting or optimization before operational capabilities exist.

Rejected.

See ADR-008.

---

# Consequences

## Positive

PowerPulse gains:

- Continuous delivery
- Faster stakeholder feedback
- Lower integration risk
- Better code quality
- Clear implementation milestones
- Easier project management

---

## Negative

Each slice requires discipline.

Developers must resist the temptation to "stub" future functionality.

Features are finished before new features begin.

---

# Relationship to Other ADRs

This ADR builds on:

- ADR-001: Modular Monolith Architecture
- ADR-002: Domain-Driven Design
- ADR-003: Database Ownership
- ADR-005: UUID Identifiers
- ADR-006: No Cross-Module Foreign Keys
- ADR-007: Domain Owns Business Rules
- ADR-008: Data Before Intelligence

Together these ADRs define both the architecture and the delivery strategy for PowerPulse.

---

# Final Decision

PowerPulse will be implemented one complete business capability at a time.

Every slice includes its domain model, persistence, API, events, tests, and documentation.

Working software takes precedence over partially completed frameworks.

The project progresses through finished vertical slices rather than unfinished horizontal layers.
At this point, the architecture documentation is effectively complete.

You now have:

powerpulse-docs/
│
├── Vision.md
├── Domain-Bible.md
├── Engineering-Bible.md
├── Aggregate-Catalog.md
├── Event-Catalog.md
├── Module-Dependency-Matrix.md
│
└── ADR/
    ├── ADR-001-Modular-Monolith-Architecture.md
    ├── ADR-002-Domain-Driven-Design.md
    ├── ADR-003-Database-Ownership.md
    ├── ADR-005-UUID-Identifiers.md
    ├── ADR-006-No-Cross-Module-FK.md
    ├── ADR-007-Domain-Owns-Business-Rules.md
    ├── ADR-008-Data-Before-Intelligence.md
    └── ADR-009-Vertical-Slice-Development.md

This is a solid architectural foundation. From this point forward, implementation decisions should trace back to these documents rather than introducing new architectural concepts unless a genuine architectural change is required.
