<div align="center">

# 🧠 ADR-002: Domain Driven Design Approach

![Status](https://img.shields.io/badge/status-Accepted-success?style=for-the-badge)
![Date](https://img.shields.io/badge/date-2026--07--20-blue?style=for-the-badge)
![Scope](https://img.shields.io/badge/scope-all%20energy%20consumers-blueviolet?style=for-the-badge)

</div>

---

## 📋 Status

✅ **Accepted**

## 📅 Date

`2026-07-20`

---

## 🧭 Context

PowerPulse is **not** simply a CRUD application. The platform models a complex real-world energy ecosystem involving:

`🌐 Energy consumers` · `📍 Physical locations` · `🔋 Energy assets` · `⚙️ Operational behaviour` · `⛽ Fuel usage` · `🔧 Maintenance` · `💵 Cost intelligence` · `💬 Recommendations`

The initial model focused on **organizations** because SMEs are the first market entry point. However, deeper domain analysis revealed that the fundamental business concept is **not** the organization itself.

<div align="center">

> ### 🧭 *The fundamental concept is: Energy consumption behaviour.*

</div>

> 🌐 This led to the introduction of the **Energy Consumer** abstraction (see [ADR-004](./ADR-004-Energy-Consumer-Abstraction.md)).

---

## ⚖️ Decision

<div align="center">

> ### 🧠 *PowerPulse will use Domain Driven Design (DDD) as the primary approach for modelling the system.*

</div>

**The architecture will be organised around:**

`🧩 Bounded contexts` · `🧱 Aggregates` · `📢 Domain events` · `🛠️ Domain services` · `🔒 Explicit ownership boundaries`

---

## 💎 Core Domain Insight

The system does not primarily model companies. **It models entities that consume and manage energy.**

```
                🌐 Energy Consumer
                       │
            ┌──────────┴──────────┐
            │                     │
      🏢 Business            🏠 Household
            └──────────┬──────────┘
                       │
                    📍 Site
                       │
               🔋 Energy Assets
                       │
                 ⚙️ Operations
```

---

## 💡 Why DDD

### 1️⃣ The Problem Domain Is Complex

Energy management involves: 🔩 Physical equipment · 💵 Cost calculations · 🧠 Operational decisions · 🧍 Human behaviour · 🛡️ Reliability concerns.

> 🚫 A simple database-first approach would hide important business concepts.

### 2️⃣ Business Rules Must Live in the Domain

<div align="center">

> ### 🧭 *"Business belongs in the domain."*

</div>

**Not:** 🎛️ Controllers · 🗄️ Database repositories · 🧰 Utility classes

| ❌ Wrong | ✅ Right |
|---|---|
| `EnergyController.calculateGeneratorCost()` | Generator cost behaviour lives with energy domain logic |

### 3️⃣ Protect Business Invariants

**Aggregates protect rules** — e.g. `EnergyAsset`:

- Asset must have a type
- Asset must have lifecycle state
- Asset transitions must be valid

> 🔒 The aggregate protects these rules.

### 4️⃣ Bounded Contexts

PowerPulse is divided into contexts:

`🪪 Identity` · `🌐 Consumer` · `🏢 Organization` · `🏠 Household` · `📍 Site` · `🔋 Asset` · `⚙️ Operations` · `⛽ Fuel` · `🔧 Maintenance` · `📡 Monitoring` · `📊 Analytics` · `💬 Recommendation` · `🔔 Notification`

Each context has its own: 🗣️ Language · ⚖️ Responsibility · 🗄️ Data ownership

### 5️⃣ Domain Language

The system uses business language, not database jargon:

| ❌ Instead of | ✅ We Use |
|---|---|
| `customer_table` | `EnergyConsumer` |
| `equipment_record` | `EnergyAsset` |
| `usage_row` | `EnergyOperation` |

> 🗣️ The model should speak the language of the problem.

---

## 🧱 Aggregate Design Principles

| Principle | Detail |
|---|---|
| 📏 **Small boundaries** | Aggregates should not become large object graphs |
| 🔒 **Single ownership** | Each aggregate owns its rules |
| 📢 **Event communication** | Aggregates communicate through domain events |

---

## 📢 Domain Events

```
📢 EnergyConsumerRegistered
📢 OrganizationProfileCreated
📢 HouseholdProfileCreated
📢 SiteCreated
📢 EnergyAssetRegistered
📢 EnergyOperationRecorded
📢 FuelConsumed
📢 MaintenanceCompleted
📢 RecommendationGenerated
```

---

## 🗺️ Strategic Design

Defines: 🧩 Bounded contexts · 🔗 Context relationships · 🗣️ Domain language

```
🌐 Consumer Context
      ↓
📍 Site Context
      ↓
🔋 Asset Context
      ↓
⚙️ Operations Context
```

---

## 🔧 Tactical Design

Defines: 🧱 Aggregates · 📦 Entities · 💎 Value Objects · 🛠️ Domain Services · 🗄️ Repositories

### 📦 Entity Rules

Entities have: 🆔 Identity · 🔄 Lifecycle · 🎭 Behaviour

**Examples:** `EnergyConsumer` · `Site` · `EnergyAsset`

### 💎 Value Object Rules

Value objects represent concepts without identity.

**Future examples:** `Money` · `EnergyReading` · `FuelQuantity` · `Location`

### 🗄️ Repository Rules

Repositories exist **only** for aggregate roots.

| ✅ Allowed | ❌ Not Allowed |
|---|---|
| `EnergyAssetRepository` | `EnergyAssetPartRepository` |

### 🛠️ Domain Services

Used when behaviour does not naturally belong to one entity, or requires domain coordination.

**Future examples:** `EnergyCostCalculator` · `FuelPredictionService` · `OptimizationService`

---

## ✅ Consequences — Positive

| ✨ Benefit | Detail |
|---|---|
| 🎯 **Better alignment with reality** | The software model matches the energy domain |
| 🌱 **Easier evolution** | New capabilities can be added without breaking existing concepts |
| 🧪 **Better testing** | Business rules can be tested independently |

## ⚠️ Consequences — Negative

| 🚨 Cost | Detail |
|---|---|
| 🧠 **More upfront modelling** | Understanding the domain requires time |
| 📚 **More concepts** | Engineers must understand DDD principles |

---

## 🔀 Alternatives Considered

### ❌ Database First Design

**Rejected** — the database would dictate the business model.

### ❌ Simple CRUD Architecture

**Rejected** — insufficient for energy intelligence complexity.

### ⏸️ Microservices DDD

**Deferred** — the boundaries are important before distribution.

---

## 🛠️ Implementation Guidance

**DDD does not mean:**
- ❌ every class must be complicated
- ❌ everything becomes an abstraction
- ❌ simple things become complex

<div align="center">

> ### 🎯 *"Put complexity where the business complexity exists, and keep everything else simple."*

</div>

---

## 🏁 Final Decision

<div align="center">

> ### *PowerPulse will use Domain Driven Design because the platform is modelling a complex energy ecosystem.*
>
> ### *The architecture begins with Energy Consumers and grows toward energy intelligence.*

**The domain model is the foundation of the product. 🧠🌐**

</div>
