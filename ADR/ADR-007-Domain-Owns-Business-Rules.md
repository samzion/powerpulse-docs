# ADR-007: Domain Owns Business Rules

## Status

Accepted

## Date

2026-07-20

---

# Context

PowerPulse is an energy intelligence platform.

The system is not only responsible for storing information about:

- Energy consumers
- Sites
- Assets
- Fuel
- Operations
- Maintenance

It must also enforce real-world business behaviour.

Examples:

- A generator cannot be considered operational before registration.
- An energy asset cannot move through invalid lifecycle states.
- Fuel consumption must follow domain rules.
- Recommendations must be based on meaningful operational conditions.
- Energy calculations must represent real business concepts.

A common failure in software systems is placing business rules inside:

- Controllers
- Application services
- Database repositories
- Utility classes

This creates a system where the business logic becomes scattered and difficult to maintain.

---

# Decision

PowerPulse follows the principle:

> Business rules belong in the domain model.

The domain layer owns:

- Business invariants
- State transitions
- Domain calculations
- Business decisions
- Core policies

Application services coordinate use cases.

Infrastructure handles technical concerns.

---

# Layer Responsibilities

PowerPulse follows this separation:


Presentation Layer

    ↓

Application Layer

    ↓

Domain Layer

    ↓

Infrastructure Layer


---

# Presentation Layer

Responsible for:

- HTTP requests
- Request validation
- Response formatting
- Authentication boundaries

Example:


EnergyAssetController


should not contain:


calculateFuelConsumption()


---

# Application Layer

Responsible for:

- Orchestration
- Transaction boundaries
- Calling domain behaviour
- Coordinating workflows

Example:


RegisterEnergyAssetUseCase


may:

1. Receive command
2. Create aggregate
3. Persist aggregate
4. Publish event

It does not decide business rules.

---

# Domain Layer

Responsible for:

- Business meaning
- Rules
- Invariants
- Behaviour

Example:


EnergyAsset


owns:

- Lifecycle transitions
- Valid states
- Asset rules

---

# Infrastructure Layer

Responsible for:

- Database
- External systems
- Messaging
- File storage

Infrastructure should not contain business decisions.

---

# Example: Energy Asset Lifecycle

Incorrect:

```java
@Service
public class AssetService {

    public void activateAsset(EnergyAsset asset) {

        if(asset.getStatus() == REGISTERED) {

            asset.setStatus(ACTIVE);

        }

    }

}

The service now owns the rule.

Correct:

public class EnergyAsset {

    public void activate(){

        if(status != REGISTERED){

            throw new InvalidAssetStateException();

        }

        status = ACTIVE;

    }

}

The aggregate protects itself.

Aggregate Responsibilities

Aggregates protect business consistency.

Example:

EnergyConsumer Aggregate

Rules:

Consumer must have a valid type
Consumer cannot activate before registration
Consumer status transitions must be valid
Site Aggregate

Rules:

Site requires a valid consumer reference
Site lifecycle must be controlled
EnergyAsset Aggregate

Rules:

Asset type is required
Asset lifecycle transitions are controlled
Retired assets cannot operate
Domain Services

Not every rule belongs to an entity.

Some rules require domain services.

A domain service is used when:

The logic is business-specific
It does not naturally belong to one entity
It coordinates domain concepts

Examples:

EnergyCostCalculator

FuelConsumptionEstimator

EnergyEfficiencyAnalyzer
Example

Cost calculation:

Incorrect:

EnergyUsageController

calculateCost()

Incorrect:

EnergyUsageRepository

calculateCost()

Correct:

EnergyCostCalculator

inside domain
Value Objects

Business concepts should become explicit types.

Instead of:

BigDecimal fuelAmount

Prefer:

FuelQuantity

Instead of:

BigDecimal amount

Prefer:

Money

Value objects protect meaning.

Domain Events

When important business actions occur, the domain publishes events.

Examples:

EnergyConsumerRegistered

SiteCreated

EnergyAssetRegistered

FuelConsumed

MaintenanceCompleted

Events communicate business facts.

Anti-Patterns Forbidden
Fat Controllers

Example:

Controller

+ validation

+ calculation

+ business rules

+ persistence

Rejected.

Anemic Domain Model

Example:

class EnergyAsset {

    UUID id;

    String status;

}

with all behaviour elsewhere.

Rejected.

Service Layer God Objects

Example:

PowerPulseService

handles:

consumer

assets

fuel

analytics

billing

Rejected.

Testing Implications

Because rules live in the domain:

Domain tests can run without:

Database
Spring context
HTTP layer

Example:

EnergyAssetTest

tests:

activation rules

retirement rules

lifecycle behaviour

This produces faster and more reliable tests.

Consequences
Positive

PowerPulse gains:

Clear business ownership
Easier testing
Better maintainability
Stronger alignment with DDD
Safer evolution
Negative

Developers need discipline.

Simple CRUD thinking is insufficient.

The team must understand:

Aggregates
Entities
Value Objects
Domain Services
Alternatives Considered
Business Logic In Services

Rejected.

Reason:

Creates procedural code and weak domain models.

Database Stored Procedures

Rejected.

Reason:

Business rules become coupled to persistence technology.

Controller-Based Logic

Rejected.

Reason:

Presentation layer should not own business decisions.

Final Decision

PowerPulse domain models own business rules.

The application layer coordinates.

The infrastructure layer supports.

The domain layer decides.

This ensures the software model reflects the real energy business instead of becoming a collection of database operations.
