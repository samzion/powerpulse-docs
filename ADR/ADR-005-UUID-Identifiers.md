# ADR-005: UUID Identifiers

## Status

Accepted

## Date

2026-07-20

---

# Context

PowerPulse is a Nigerian energy intelligence platform designed to model and understand energy consumption behaviour across:

- Businesses
- Households
- Future energy consumer categories

The system manages multiple business capabilities:

- Identity
- Energy Consumer management
- Organization profiles
- Household profiles
- Sites
- Energy assets
- Energy operations
- Fuel management
- Maintenance
- Monitoring
- Analytics
- Recommendations

These capabilities are represented as bounded contexts within a modular monolith architecture.

Each bounded context owns its domain model and requires a reliable identity mechanism.

The identifier strategy must support:

- Strong domain identity
- Module independence
- Future service extraction
- Large volumes of operational data
- External API exposure
- Distributed generation capability

---

# Decision

PowerPulse will use:

# UUID v7 as the identifier strategy for all aggregate roots.

Every major domain entity receives a UUID v7 identifier.

Examples:


User
id UUID

EnergyConsumer
id UUID

OrganizationProfile
id UUID

HouseholdProfile
id UUID

Site
id UUID

EnergyAsset
id UUID

EnergyOperation
id UUID

FuelTransaction
id UUID


---

# Why UUID Instead of Sequential IDs?

## Avoid Predictable Identifiers

Sequential identifiers expose system information.

Example:


/consumers/10001

/consumers/10002

/consumers/10003


This reveals:

- Registration volume
- Creation order
- Possible business growth patterns

UUIDs avoid exposing this information.

---

# Why UUID v7 Instead of UUID v4?

Traditional UUID v4 provides uniqueness but is completely random.

Example:


8a4f...
1c92...
f03b...
42aa...


Random insertion patterns can negatively affect database index locality.

PowerPulse will store high-volume time-oriented records:

- Energy operations
- Fuel consumption
- Monitoring events
- Maintenance records
- Telemetry readings

These records naturally grow chronologically.

UUID v7 combines:

- UUID uniqueness
- Timestamp ordering
- Better database locality

Example:


0197d8e4-a001-7xxx-xxxx

0197d8e4-a002-7xxx-xxxx

0197d8e4-a003-7xxx-xxxx


The identifiers remain globally unique while maintaining approximate creation order.

---

# Benefits of UUID v7

## 1. Database Performance

UUID v7 provides better index locality compared with random UUID v4.

Benefits:

- Fewer random page insertions
- Reduced B-tree fragmentation
- Better write performance

This is important for future operational event volumes.

---

## 2. Distributed Generation

Identifiers can be generated independently.

Example:


Asset Module generates assetId

Fuel Module generates transactionId

Operations Module generates operationId


No central ID generator is required.

---

## 3. Future Service Extraction

Today:


PowerPulse Modular Monolith

Consumer Module

Site Module

Asset Module


Future:


Consumer Service

Site Service

Asset Service


UUIDs remain valid across:

- Different services
- Different databases
- Different deployment environments

---

# Energy Consumer Identity Model

The introduction of the Energy Consumer abstraction changes the identity hierarchy.

The system no longer begins with Organization.

The core identity becomes:


User

|

EnergyConsumer

|

+----------------+

| |

Business Household

|

Site

|

EnergyAsset

|

EnergyOperation


---

# Identity Ownership Rules

Each aggregate root owns its own identity.

Examples:

Consumer Module owns:


consumerId


Site Module owns:


siteId


Asset Module owns:


assetId


Operations Module owns:


operationId


No module creates another module's identifiers.

---

# Domain Identity Rule

Identity belongs to the domain.

The application generates the identifier before persistence.

The lifecycle is:


Create Aggregate

    ↓

Generate UUID v7

    ↓

Validate Business Rules

    ↓

Persist Aggregate


The database stores identity.

The database does not define identity.

---

# Database Representation

All primary keys use UUID.

Example:

```sql
CREATE TABLE energy_consumers (

    id UUID PRIMARY KEY,

    consumer_type VARCHAR(50) NOT NULL,

    status VARCHAR(50) NOT NULL,

    created_at TIMESTAMP NOT NULL,

    updated_at TIMESTAMP NOT NULL

);
Cross Module References

Modules reference each other using UUID values.

Example:

Site module:

CREATE TABLE sites (

    id UUID PRIMARY KEY,

    consumer_id UUID NOT NULL,

    name VARCHAR(255)

);

The relationship is represented by:

consumer_id UUID

not by a database foreign key.

The ownership boundary is maintained by the application architecture.

Java Implementation Guidance

UUID generation occurs in the application layer.

Recommended approach:

UUID id = UuidCreator.getTimeOrderedEpoch();

Using:

<dependency>
    <groupId>com.github.f4b6a3</groupId>
    <artifactId>uuid-creator</artifactId>
</dependency>

Example:

@Entity
public class EnergyConsumer {

    @Id
    private UUID id;

}
PostgreSQL Considerations

PostgreSQL supports UUID natively:

UUID

PowerPulse does not store UUIDs as:

VARCHAR
TEXT

because native UUID provides:

Better storage efficiency
Native comparison
Better indexing behaviour
Alternatives Considered
Auto Increment BIGINT

Rejected.

Reasons:

Predictable
Database-dependent
Difficult across distributed systems
UUID v4

Rejected.

Reasons:

Excellent uniqueness
Poor temporal locality
Less suitable for high-volume event-oriented systems
Composite Natural Keys

Rejected.

Example:

business_name + location

Reasons:

Business attributes change
Identity should remain stable
Database Generated UUID

Rejected.

Reasons:

Domain should control aggregate creation
Identity should exist before persistence
Consequences
Positive

PowerPulse gains:

Strong aggregate identity
Safe public identifiers
Better event data ordering
Future distributed-system readiness
Clear DDD ownership
Negative

Developers must:

Avoid relying on numeric ordering
Use explicit timestamps for business ordering
Understand UUIDs are identifiers, not sequence numbers
Final Decision

PowerPulse uses UUID v7 identifiers across all aggregate roots.

The Energy Consumer becomes the central business identity.

UUID v7 provides the right balance between:

Domain-driven design
Database efficiency
Operational scalability
Future platform evolution

This identifier strategy is foundational for building PowerPulse into a long-term energy intelligence platform.
