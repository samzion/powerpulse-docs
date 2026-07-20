# ADR-002: Domain Driven Design Approach

## Status

Accepted

## Date

2026-07-20

---

# Context

PowerPulse is not simply a CRUD application.

The platform models a complex real-world energy ecosystem involving:

- Energy consumers
- Physical locations
- Energy assets
- Operational behaviour
- Fuel usage
- Maintenance
- Cost intelligence
- Recommendations

The initial model focused on organizations because SMEs are the first market entry point.

However, deeper domain analysis revealed that the fundamental business concept is not the organization itself.

The fundamental concept is:

> Energy consumption behaviour.

This led to the introduction of the Energy Consumer abstraction.

---

# Decision

PowerPulse will use:

# Domain Driven Design (DDD)

as the primary approach for modelling the system.

The architecture will be organised around:

- Bounded contexts
- Aggregates
- Domain events
- Domain services
- Explicit ownership boundaries

---

# Core Domain Insight

The system does not primarily model companies.

It models entities that consume and manage energy.

Therefore:


Energy Consumer

    |

+---------------+

| |

Business Household

    |

  Site

    |

Energy Assets

    |

Operations


---

# Why DDD

## 1. The Problem Domain Is Complex

Energy management involves:

- Physical equipment
- Cost calculations
- Operational decisions
- Human behaviour
- Reliability concerns

A simple database-first approach would hide important business concepts.

---

# 2. Business Rules Must Live In The Domain

PowerPulse follows:

> Business belongs in the domain.

Not:

- Controllers
- Database repositories
- Utility classes

Example:

A generator's cost behaviour belongs with energy domain logic.

Not:


EnergyController

calculateGeneratorCost()


---

# 3. Protect Business Invariants

Aggregates protect rules.

Example:

EnergyAsset:

Rules:

- Asset must have a type
- Asset must have lifecycle state
- Asset transitions must be valid

The aggregate protects these rules.

---

# 4. Bounded Contexts

PowerPulse is divided into contexts.


Identity

Consumer

Organization

Household

Site

Asset

Operations

Fuel

Maintenance

Monitoring

Analytics

Recommendation

Notification


Each context has:

- Own language
- Own responsibility
- Own data ownership

---

# 5. Domain Language

The system uses business language.

Examples:

Instead of:


customer_table


we use:


EnergyConsumer


Instead of:


equipment_record


we use:


EnergyAsset


Instead of:


usage_row


we use:


EnergyOperation


The model should speak the language of the problem.

---

# Aggregate Design Principles

PowerPulse aggregates follow:

## Small boundaries

Aggregates should not become large object graphs.

---

## Single ownership

Each aggregate owns its rules.

---

## Event communication

Aggregates communicate through domain events.

---

# Domain Events

Important events:


EnergyConsumerRegistered

OrganizationProfileCreated

HouseholdProfileCreated

SiteCreated

EnergyAssetRegistered

EnergyOperationRecorded

FuelConsumed

MaintenanceCompleted

RecommendationGenerated


---

# Strategic Design

DDD is applied at two levels.

---

# Strategic Design

Defines:

- Bounded contexts
- Context relationships
- Domain language

Example:


Consumer Context

    ↓

Site Context

    ↓

Asset Context

    ↓

Operations Context


---

# Tactical Design

Defines:

- Aggregates
- Entities
- Value Objects
- Domain Services
- Repositories

---

# Entity Rules

Entities have:

- Identity
- Lifecycle
- Behaviour

Examples:


EnergyConsumer

Site

EnergyAsset


---

# Value Object Rules

Value objects represent concepts without identity.

Examples:

Future:


Money

EnergyReading

FuelQuantity

Location


---

# Repository Rules

Repositories exist only for aggregate roots.

Example:

Allowed:


EnergyAssetRepository


Not:


EnergyAssetPartRepository


---

# Domain Services

Used when behaviour:

- Does not naturally belong to one entity
- Requires domain coordination

Examples:

Future:


EnergyCostCalculator

FuelPredictionService

OptimizationService


---

# Consequences

## Positive

### Better alignment with reality

The software model matches the energy domain.

---

### Easier evolution

New capabilities can be added without breaking existing concepts.

---

### Better testing

Business rules can be tested independently.

---

## Negative

### More upfront modelling

Understanding the domain requires time.

---

### More concepts

Engineers must understand DDD principles.

---

# Alternatives Considered

## Database First Design

Rejected.

Reason:

The database would dictate the business model.

---

## Simple CRUD Architecture

Rejected.

Reason:

Insufficient for energy intelligence complexity.

---

## Microservices DDD

Deferred.

Reason:

The boundaries are important before distribution.

---

# Implementation Guidance

DDD does not mean:

- every class must be complicated
- everything becomes an abstraction
- simple things become complex

The goal is:

> Put complexity where the business complexity exists, and keep everything else simple.

---

# Final Decision

PowerPulse will use Domain Driven Design because the platform is modelling a complex energy ecosystem.

The architecture begins with Energy Consumers and grows toward energy intelligence.

The domain model is the foundation of the product.# ADR-002: Domain Driven Design Approach

## Status

Accepted

## Date

2026-07-20

---

# Context

PowerPulse is not simply a CRUD application.

The platform models a complex real-world energy ecosystem involving:

- Energy consumers
- Physical locations
- Energy assets
- Operational behaviour
- Fuel usage
- Maintenance
- Cost intelligence
- Recommendations

The initial model focused on organizations because SMEs are the first market entry point.

However, deeper domain analysis revealed that the fundamental business concept is not the organization itself.

The fundamental concept is:

> Energy consumption behaviour.

This led to the introduction of the Energy Consumer abstraction.

---

# Decision

PowerPulse will use:

# Domain Driven Design (DDD)

as the primary approach for modelling the system.

The architecture will be organised around:

- Bounded contexts
- Aggregates
- Domain events
- Domain services
- Explicit ownership boundaries

---

# Core Domain Insight

The system does not primarily model companies.

It models entities that consume and manage energy.

Therefore:


Energy Consumer

    |

+---------------+

| |

Business Household

    |

  Site

    |

Energy Assets

    |

Operations


---

# Why DDD

## 1. The Problem Domain Is Complex

Energy management involves:

- Physical equipment
- Cost calculations
- Operational decisions
- Human behaviour
- Reliability concerns

A simple database-first approach would hide important business concepts.

---

# 2. Business Rules Must Live In The Domain

PowerPulse follows:

> Business belongs in the domain.

Not:

- Controllers
- Database repositories
- Utility classes

Example:

A generator's cost behaviour belongs with energy domain logic.

Not:


EnergyController

calculateGeneratorCost()


---

# 3. Protect Business Invariants

Aggregates protect rules.

Example:

EnergyAsset:

Rules:

- Asset must have a type
- Asset must have lifecycle state
- Asset transitions must be valid

The aggregate protects these rules.

---

# 4. Bounded Contexts

PowerPulse is divided into contexts.


Identity

Consumer

Organization

Household

Site

Asset

Operations

Fuel

Maintenance

Monitoring

Analytics

Recommendation

Notification


Each context has:

- Own language
- Own responsibility
- Own data ownership

---

# 5. Domain Language

The system uses business language.

Examples:

Instead of:


customer_table


we use:


EnergyConsumer


Instead of:


equipment_record


we use:


EnergyAsset


Instead of:


usage_row


we use:


EnergyOperation


The model should speak the language of the problem.

---

# Aggregate Design Principles

PowerPulse aggregates follow:

## Small boundaries

Aggregates should not become large object graphs.

---

## Single ownership

Each aggregate owns its rules.

---

## Event communication

Aggregates communicate through domain events.

---

# Domain Events

Important events:


EnergyConsumerRegistered

OrganizationProfileCreated

HouseholdProfileCreated

SiteCreated

EnergyAssetRegistered

EnergyOperationRecorded

FuelConsumed

MaintenanceCompleted

RecommendationGenerated


---

# Strategic Design

DDD is applied at two levels.

---

# Strategic Design

Defines:

- Bounded contexts
- Context relationships
- Domain language

Example:


Consumer Context

    ↓

Site Context

    ↓

Asset Context

    ↓

Operations Context


---

# Tactical Design

Defines:

- Aggregates
- Entities
- Value Objects
- Domain Services
- Repositories

---

# Entity Rules

Entities have:

- Identity
- Lifecycle
- Behaviour

Examples:


EnergyConsumer

Site

EnergyAsset


---

# Value Object Rules

Value objects represent concepts without identity.

Examples:

Future:


Money

EnergyReading

FuelQuantity

Location


---

# Repository Rules

Repositories exist only for aggregate roots.

Example:

Allowed:


EnergyAssetRepository


Not:


EnergyAssetPartRepository


---

# Domain Services

Used when behaviour:

- Does not naturally belong to one entity
- Requires domain coordination

Examples:

Future:


EnergyCostCalculator

FuelPredictionService

OptimizationService


---

# Consequences

## Positive

### Better alignment with reality

The software model matches the energy domain.

---

### Easier evolution

New capabilities can be added without breaking existing concepts.

---

### Better testing

Business rules can be tested independently.

---

## Negative

### More upfront modelling

Understanding the domain requires time.

---

### More concepts

Engineers must understand DDD principles.

---

# Alternatives Considered

## Database First Design

Rejected.

Reason:

The database would dictate the business model.

---

## Simple CRUD Architecture

Rejected.

Reason:

Insufficient for energy intelligence complexity.

---

## Microservices DDD

Deferred.

Reason:

The boundaries are important before distribution.

---

# Implementation Guidance

DDD does not mean:

- every class must be complicated
- everything becomes an abstraction
- simple things become complex

The goal is:

> Put complexity where the business complexity exists, and keep everything else simple.

---

# Final Decision

PowerPulse will use Domain Driven Design because the platform is modelling a complex energy ecosystem.

The architecture begins with Energy Consumers and grows toward energy intelligence.

The domain model is the foundation of the product.
