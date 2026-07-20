<div align="center">

# 🆔 ADR-005: UUID Identifiers

![Status](https://img.shields.io/badge/status-Accepted-success?style=for-the-badge)
![Date](https://img.shields.io/badge/date-2026--07--20-blue?style=for-the-badge)
![Strategy](https://img.shields.io/badge/strategy-UUID%20v7-9b59b6?style=for-the-badge)

</div>

---

## 📋 Status

✅ **Accepted**

## 📅 Date

`2026-07-20`

---

## 🧭 Context

PowerPulse is a Nigerian energy intelligence platform designed to model and understand energy consumption behaviour across: 🏢 Businesses · 🏠 Households · 🔮 Future energy consumer categories.

The system manages multiple business capabilities — `🪪 Identity` · `🌐 Energy Consumer` · `🏢 Organization Profile` · `🏠 Household Profile` · `📍 Site` · `🔋 Energy Asset` · `⚙️ Energy Operations` · `⛽ Fuel` · `🔧 Maintenance` · `📡 Monitoring` · `📊 Analytics` · `💬 Recommendations` — each represented as a bounded context in a modular monolith.

Each bounded context owns its domain model and requires a reliable identity mechanism supporting:

- 💪 Strong domain identity
- 🧩 Module independence
- 🚀 Future service extraction
- 📈 Large volumes of operational data
- 🌐 External API exposure
- 🔀 Distributed generation capability

---

## ⚖️ Decision

<div align="center">

> ### 🆔 *PowerPulse will use UUID v7 as the identifier strategy for all aggregate roots.*

</div>

Every major domain entity receives a UUID v7 identifier:

```
User               id UUID
EnergyConsumer     id UUID
OrganizationProfile id UUID
HouseholdProfile   id UUID
Site               id UUID
EnergyAsset        id UUID
EnergyOperation    id UUID
FuelTransaction    id UUID
```

---

## 💡 Why UUID Instead of Sequential IDs?

Sequential identifiers expose system information:

```
/consumers/10001
/consumers/10002
/consumers/10003
```

This reveals: 📊 registration volume · 🔢 creation order · 📈 possible business growth patterns.

> 🔒 UUIDs avoid exposing this information.

---

## 💡 Why UUID v7 Instead of UUID v4?

Traditional UUID v4 provides uniqueness but is completely random:

```
8a4f...
1c92...
f03b...
42aa...
```

> ⚠️ Random insertion patterns can negatively affect database index locality.

PowerPulse stores high-volume, time-oriented records: ⚙️ Energy operations · ⛽ Fuel consumption · 📡 Monitoring events · 🔧 Maintenance records · 📶 Telemetry readings — which naturally grow chronologically.

**UUID v7 combines:** 🆔 UUID uniqueness · ⏱️ Timestamp ordering · 🗄️ Better database locality

```
0197d8e4-a001-7xxx-xxxx
0197d8e4-a002-7xxx-xxxx
0197d8e4-a003-7xxx-xxxx
```

> ✅ Identifiers remain globally unique while maintaining approximate creation order.

---

## ✨ Benefits of UUID v7

### 1️⃣ Database Performance

UUID v7 provides better index locality vs random UUID v4: fewer random page insertions · reduced B-tree fragmentation · better write performance.

### 2️⃣ Distributed Generation

Identifiers can be generated independently:

```
🔋 Asset Module      generates assetId
⛽ Fuel Module        generates transactionId
⚙️ Operations Module  generates operationId
```

> 🎯 No central ID generator is required.

### 3️⃣ Future Service Extraction

| Today | Future |
|---|---|
| `PowerPulse Modular Monolith` — 🌐 Consumer · 📍 Site · 🔋 Asset | 🚀 Consumer Service · 🚀 Site Service · 🚀 Asset Service |

> 🌐 UUIDs remain valid across different services, databases, and deployment environments.

---

## 🌐 Energy Consumer Identity Model

The Energy Consumer abstraction changes the identity hierarchy — the system no longer begins with Organization:

```
🪪 User
    └── 🌐 EnergyConsumer
          ├── 🏢 Business
          └── 🏠 Household
                └── 📍 Site
                      └── 🔋 EnergyAsset
                            └── ⚙️ EnergyOperation
```

---

## 🔒 Identity Ownership Rules

Each aggregate root owns its own identity:

| Module | Owns |
|---|---|
| 🌐 Consumer | `consumerId` |
| 📍 Site | `siteId` |
| 🔋 Asset | `assetId` |
| ⚙️ Operations | `operationId` |

> 🚫 No module creates another module's identifiers.

---

## 🧠 Domain Identity Rule

> 🎯 Identity belongs to the domain. The application generates the identifier **before** persistence.

```
🧱 Create Aggregate
     └── 🆔 Generate UUID v7
           └── ✅ Validate Business Rules
                 └── 🗄️ Persist Aggregate
```

> 🗄️ The database **stores** identity. The database does **not define** identity.

---

## 🗄️ Database Representation

All primary keys use UUID:

```sql
CREATE TABLE energy_consumers (
    id             UUID PRIMARY KEY,
    consumer_type  VARCHAR(50) NOT NULL,
    status         VARCHAR(50) NOT NULL,
    created_at     TIMESTAMP NOT NULL,
    updated_at     TIMESTAMP NOT NULL
);
```

**Cross-module references** use UUID values:

```sql
CREATE TABLE sites (
    id           UUID PRIMARY KEY,
    consumer_id  UUID NOT NULL,
    name         VARCHAR(255)
);
```

> 🔗 `consumer_id UUID` — not a database foreign key. The ownership boundary is maintained by the application architecture.

---

## ☕ Java Implementation Guidance

UUID generation occurs in the **application layer**:

```java
UUID id = UuidCreator.getTimeOrderedEpoch();
```

```xml
<dependency>
    <groupId>com.github.f4b6a3</groupId>
    <artifactId>uuid-creator</artifactId>
</dependency>
```

```java
@Entity
public class EnergyConsumer {

    @Id
    private UUID id;

}
```

---

## 🐘 PostgreSQL Considerations

PostgreSQL supports **UUID** natively.

| ❌ Avoid | ✅ Use |
|---|---|
| `VARCHAR` / `TEXT` | Native `UUID` |

> 🚀 Native UUID provides better storage efficiency, native comparison, and better indexing behaviour.

---

## 🔀 Alternatives Considered

### ❌ Auto Increment BIGINT

**Rejected** — predictable · database-dependent · difficult across distributed systems.

### ❌ UUID v4

**Rejected** — excellent uniqueness, but poor temporal locality; less suitable for high-volume event-oriented systems.

### ❌ Composite Natural Keys

**Rejected** — e.g. `business_name + location`: business attributes change, but identity should remain stable.

### ❌ Database-Generated UUID

**Rejected** — the domain should control aggregate creation; identity should exist before persistence.

---

## ✅ Consequences — Positive

- 💪 Strong aggregate identity
- 🔒 Safe public identifiers
- 📈 Better event data ordering
- 🚀 Future distributed-system readiness
- 🎯 Clear DDD ownership

## ⚠️ Consequences — Negative

Developers must:
- 🚫 Avoid relying on numeric ordering
- ⏱️ Use explicit timestamps for business ordering
- 🧠 Understand UUIDs are identifiers, not sequence numbers

---

## 🏁 Final Decision

<div align="center">

> ### *PowerPulse uses UUID v7 identifiers across all aggregate roots.*
> ### *The Energy Consumer becomes the central business identity.*

UUID v7 provides the right balance between:

**🧠 Domain-driven design · 🗄️ Database efficiency · 📈 Operational scalability · 🚀 Future platform evolution**

This identifier strategy is foundational for building PowerPulse into a long-term energy intelligence platform. 🆔🌐

</div>
