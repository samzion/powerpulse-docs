# ADR-006: No Cross-Module Foreign Keys

## Status

Accepted

## Date

2026-07-20

---

# Context

PowerPulse is designed as a modular monolith using:

- Java
- Spring Boot
- Spring Modulith
- PostgreSQL
- Liquibase

The system contains multiple bounded contexts:

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

Although these modules run in one application and currently share one PostgreSQL instance, they represent independent business capabilities.

A common architectural mistake in modular monoliths is allowing all modules to directly reference each other's database tables through foreign keys.

This creates hidden coupling.

Example:


Asset Module

    FK

    ↓

Site Module tables


Over time, database relationships become stronger than the business boundaries.

---

# Decision

PowerPulse will not use database foreign keys across module boundaries.

Each module owns its own tables.

Relationships between modules are represented using:

- UUID references
- Domain events
- Application-level validation

---

# Database Ownership Rule

Every module owns its persistence model.

Example:

## Consumer Module

Owns:


energy_consumers


---

## Organization Module

Owns:


organization_profiles


---

## Household Module

Owns:


household_profiles


---

## Site Module

Owns:


sites


---

## Asset Module

Owns:


energy_assets


---

## Operations Module

Owns:


energy_operations


---

The owning module is responsible for:

- Schema changes
- Migration scripts
- Data integrity rules
- Domain behaviour

---

# Example: Correct Relationship

A Site belongs to an Energy Consumer.

The Site table contains:

```sql
CREATE TABLE sites (

    id UUID PRIMARY KEY,

    consumer_id UUID NOT NULL,

    name VARCHAR(255) NOT NULL,

    created_at TIMESTAMP NOT NULL

);

The relationship is represented by:

consumer_id UUID

The database does not contain:

FOREIGN KEY (consumer_id)
REFERENCES energy_consumers(id)
Why No Cross-Module Foreign Keys?
1. Preserve Bounded Context Boundaries

A module should own its internal model.

Without cross-module foreign keys:

Consumer Module

owns:

energy_consumers

and:

Site Module

owns:

sites

remain independent.

2. Prevent Database Coupling

With foreign keys:

Asset migration

depends on

Site migration

A small change in one module can break another module.

Without them:

Asset Module

can evolve independently
3. Enable Future Service Extraction

Today:

PowerPulse Application


Consumer Module

Site Module

Asset Module

Future:

Consumer Service

Site Service

Asset Service

The database boundary already exists.

The extraction path is cleaner.

4. Support Independent Deployment Evolution

Future modules may have:

Different storage strategies
Different scaling requirements
Different ownership teams

Cross-module foreign keys prevent this.

Maintaining Integrity Without Foreign Keys

Removing database foreign keys does not mean removing integrity.

Integrity moves to the appropriate layer.

1. Application Validation

Example:

Before creating a Site:

Site Application Service

checks:

Consumer exists
2. Domain Rules

Example:

A Site cannot exist without a valid Energy Consumer reference.

The rule belongs to the Site domain.

3. Domain Events

Example:

Consumer registration:

EnergyConsumerRegistered

        ↓

Site module becomes aware
4. Automated Tests

Module contracts verify:

Valid references
Event flows
Boundary rules
Liquibase Structure

Each module owns its migrations.

Recommended structure:

db/changelog

├── identity

│   └── 001-create-users.xml


├── consumer

│   └── 001-create-energy-consumers.xml


├── site

│   └── 001-create-sites.xml


├── asset

│   └── 001-create-energy-assets.xml
JPA Implementation Rule

Entities must not create cross-module object relationships.

Avoid:

@ManyToOne
private EnergyConsumer consumer;

inside Site.

Prefer:

@Entity
public class Site {

    @Id
    private UUID id;

    private UUID consumerId;

}

The module owns its model.

Event Communication Example

Instead of:

Site Module

queries

Consumer database table

Use:

Consumer Module

publishes:

EnergyConsumerRegistered


        ↓


Site Module

reacts
Alternatives Considered
Foreign Keys Everywhere

Rejected.

Reason:

Creates database-level coupling between bounded contexts.

Separate Database Per Module Immediately

Rejected.

Reason:

Operational complexity is unnecessary at current stage.

Shared Domain Database Model

Rejected.

Reason:

Violates DDD boundaries.

Consequences
Positive

PowerPulse gains:

Strong module independence
Cleaner DDD boundaries
Easier schema evolution
Future microservice readiness
Negative

The application must handle some integrity checks previously handled by PostgreSQL.

This requires discipline.

Final Decision

PowerPulse modules own their own data.

Cross-module relationships use:

UUID references

+

Domain events

+

Application validation

Database foreign keys are restricted to relationships inside the same bounded context only.

This keeps the architecture aligned with Domain Driven Design and preserves future evolution paths.
