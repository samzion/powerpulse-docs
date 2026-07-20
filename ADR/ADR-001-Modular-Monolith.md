# ADR-001: Modular Monolith Architecture

## Status

Accepted

## Date

2026-07-20

---

# Context

PowerPulse requires a software architecture capable of supporting:

- Rapid MVP development
- Strong domain boundaries
- Independent business capabilities
- Future growth into distributed systems

The system must avoid two common failures:

1. A tightly coupled monolith that becomes difficult to change.
2. Premature microservices that introduce operational complexity before scale requires them.

---

# Decision

PowerPulse will be built as a:

# Modular Monolith

using:

- Java
- Spring Boot
- Spring Modulith
- PostgreSQL
- Liquibase

---

# Why Modular Monolith

A modular monolith provides:

## Strong boundaries

Each business capability owns:

- Domain logic
- Data ownership
- Application services
- Internal models

---

## Simple deployment

Initially:


One application

One deployment

One database instance


---

## Future extraction capability

Modules can later become services if required.

Example:

Today:


powerpulse application

|
|

fuel module


Future:


Fuel Intelligence Service


---

# Module Structure

The system is organized around bounded contexts.


com.powerpulse

├── identity

├── consumer

├── organization

├── household

├── site

├── asset

├── operations

├── fuel

├── maintenance

├── monitoring

├── analytics

├── recommendation

└── notification


---

# Module Responsibilities

## Identity

Authentication and authorization.

---

## Consumer

Energy consumer lifecycle.

Examples:

- Business consumer
- Household consumer

---

## Organization

Business-specific information.

---

## Household

Residential-specific information.

---

## Site

Physical energy locations.

---

## Asset

Energy equipment.

---

## Operations

Energy behaviour and measurements.

---

## Fuel

Fuel intelligence.

---

## Maintenance

Asset health.

---

## Monitoring

Operational observation.

---

## Analytics

Data transformation into knowledge.

---

## Recommendation

Decision support.

---

## Notification

Communication delivery.

---

# Dependency Rules

Modules communicate through:

## Domain Events

Preferred for:

- State changes
- Notifications
- Asynchronous reactions

Example:


EnergyAssetRegistered

    ↓

Fuel module creates tracking


---

## Explicit Contracts

Used when immediate response is required.

Example:


AssetApplicationService

    ↓

SiteExistenceChecker


---

# Forbidden Practices

The following are prohibited:

## Cross-module database access

Invalid:


Fuel module querying asset tables directly


---

## Shared domain models

Invalid:


Asset importing Site entity


---

## Circular dependencies

Invalid:


Site → Asset

Asset → Site


---

# Package Boundary Rules

Spring Modulith will enforce:


module.api

module.application

module.domain

module.infrastructure


Each module controls its internal implementation.

---

# Database Ownership

Each module owns its tables.

Example:

Consumer module:


energy_consumers


Asset module:


energy_assets


Site module:


sites


Cross-module relationships use:


UUID references


not database foreign keys.

---

# Consequences

## Positive

- Clear business ownership
- Easier testing
- Easier maintenance
- Future extraction path
- Better alignment with DDD

---

## Negative

- More initial structure
- Requires discipline
- Developers must respect boundaries

---

# Alternatives Considered

## Traditional Layered Monolith

Rejected.

Reason:

Business boundaries become mixed.

---

## Microservices From Day One

Rejected.

Reason:

Operational complexity exceeds current needs.

---

# Final Decision

PowerPulse will begin as a modular monolith.

The architecture prioritizes:


Domain clarity

over

deployment complexity


The system is designed to evolve from a single application into an energy intelligence platform.
