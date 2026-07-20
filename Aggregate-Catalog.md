<div align="center">

# 🧱 PowerPulse Aggregate Catalog

### *v2.0 — Aggregate Design After Energy Consumer Abstraction*

![Aggregates](https://img.shields.io/badge/document-aggregate%20catalog-9b59b6?style=for-the-badge)
![Version](https://img.shields.io/badge/version-2.0-red?style=for-the-badge)
![Count](https://img.shields.io/badge/aggregates-10-orange?style=for-the-badge)
![DDD](https://img.shields.io/badge/methodology-DDD-blue?style=for-the-badge)

</div>

---

## 1️⃣ Purpose

This document defines the aggregate boundaries inside PowerPulse.

**Aggregates represent:**

- ⚖️ Business consistency boundaries
- 🏠 Domain ownership boundaries
- 🔒 Behaviour protection boundaries

> 🚫 An aggregate is **not** a database grouping. **It exists to protect business rules.**

---

## 2️⃣ Aggregate Design Principles

| # | Rule |
|---|---|
| 1️⃣ | Every aggregate has **exactly one** aggregate root |
| 2️⃣ | External modules reference aggregates through IDs · domain events · explicit contracts |
| 3️⃣ | Aggregates protect their own invariants |
| 4️⃣ | Large aggregates are avoided — a single aggregate should not own unrelated business concepts |

---

## 3️⃣ Aggregate Overview

```
🌐 EnergyConsumer
🏢 OrganizationProfile
🏠 HouseholdProfile
📍 Site
🔋 EnergyAsset
⚙️ EnergyOperation
⛽ FuelInventory
🔧 MaintenanceRecord
📡 MonitoringAlert
💬 Recommendation
```

---

## 4️⃣ 🌐 EnergyConsumer Aggregate

**Aggregate Root:** `EnergyConsumer` · **Context:** Consumer Context

**Purpose:** Represents any entity that consumes or manages energy — Business · Household · Institution · Community.

| 🧩 Field | Detail |
|---|---|
| **Responsibilities** | Consumer identity · Consumer type · Consumer lifecycle · Consumer status |
| **Attributes** | id UUID · consumerType · status · createdAt · updatedAt |
| **Consumer Types (Initial)** | `BUSINESS` · `HOUSEHOLD` |
| **Consumer Types (Future)** | `INSTITUTION` · `COMMUNITY` · `GOVERNMENT` |
| **Events Produced** | `EnergyConsumerRegistered` · `EnergyConsumerActivated` · `EnergyConsumerSuspended` |

**⚖️ Invariants** — An EnergyConsumer:
- must have a type
- must have an active lifecycle state
- cannot exist without identity

---

## 5️⃣ 🏢 OrganizationProfile Aggregate

**Aggregate Root:** `OrganizationProfile` · **Context:** Organization Profile Context

**Purpose:** Represents business information belonging to a consumer.

```
🌐 EnergyConsumer
    └── 🏢 OrganizationProfile
```

| 🧩 Field | Detail |
|---|---|
| **Responsibilities** | Business name · Business category · Business classification · Registration details |
| **Attributes** | id UUID · consumerId UUID · businessName · businessType · registrationNumber |
| **Events Produced** | `OrganizationProfileCreated` · `OrganizationProfileUpdated` |

**⚖️ Invariants** — An organization profile:
- must belong to an EnergyConsumer
- must have a business identity
- must have a valid business type

---

## 6️⃣ 🏠 HouseholdProfile Aggregate

**Aggregate Root:** `HouseholdProfile` · **Context:** Household Profile Context

**Purpose:** Represents residential consumers.

```
🌐 EnergyConsumer
    └── 🏠 HouseholdProfile
```

| 🧩 Field | Detail |
|---|---|
| **Responsibilities** | Household information · Residential details |
| **Attributes** | id UUID · consumerId UUID · householdName · location · occupancyInformation |
| **Events Produced** | `HouseholdProfileCreated` · `HouseholdProfileUpdated` |

---

## 7️⃣ 📍 Site Aggregate

**Aggregate Root:** `Site` · **Context:** Site Context

**Purpose:** Represents a physical location where energy activity occurs — Shop · Factory · Home · Estate.

```
🌐 EnergyConsumer
    └── 📍 Site
```

| 🧩 Field | Detail |
|---|---|
| **Responsibilities** | Location identity · Site status · Site information |
| **Attributes** | id UUID · consumerId UUID · name · address · status |
| **Events Produced** | `SiteCreated` · `SiteActivated` · `SiteDeactivated` |

**⚖️ Invariants** — A Site:
- must belong to a consumer
- must have a valid status
- cannot operate without identity

---

## 8️⃣ 🔋 EnergyAsset Aggregate

**Aggregate Root:** `EnergyAsset` · **Context:** Energy Asset Context

**Purpose:** Represents physical energy equipment — Generator · Inverter · Solar system · Battery.

```
📍 Site
    └── 🔋 EnergyAsset
```

| 🧩 Field | Detail |
|---|---|
| **Responsibilities** | Asset identity · Asset type · Asset lifecycle |
| **Attributes** | id UUID · siteId UUID · assetType · status · manufacturer · model |
| **Asset Types** | `GENERATOR` · `INVERTER` · `SOLAR` · `BATTERY` · `GRID_CONNECTION` |
| **Events Produced** | `EnergyAssetRegistered` · `EnergyAssetActivated` · `EnergyAssetRetired` |

**⚖️ Invariants** — An asset:
- must belong to a site
- must have a valid type
- must follow lifecycle transitions

---

## 9️⃣ ⚙️ EnergyOperation Aggregate

**Aggregate Root:** `EnergyOperation` · **Context:** Energy Operations

**Purpose:** Captures actual energy behaviour.

**Examples:** *Generator ran for 6 hours · Battery discharged · Grid available*

| 🧩 Field | Detail |
|---|---|
| **Responsibilities** | Runtime events · Consumption events · Operational measurements |
| **Attributes** | id UUID · assetId UUID · operationType · startTime · endTime · energyConsumed |
| **Events Produced** | `EnergyOperationRecorded` |

---

## 🔟 ⛽ FuelInventory Aggregate

**Aggregate Root:** `FuelInventory` · **Context:** Fuel

**Purpose:** Tracks fuel availability and consumption.

```
🔋 EnergyAsset
    └── ⛽ FuelInventory
```

| 🧩 Field | Detail |
|---|---|
| **Responsibilities** | Fuel balance · Fuel transactions |
| **Attributes** | id UUID · assetId UUID · fuelType · quantity |
| **Events Produced** | `FuelAdded` · `FuelConsumed` · `FuelLevelLow` |

---

## 1️⃣1️⃣ 🔧 Maintenance Aggregate

**Aggregate Root:** `MaintenanceRecord` · **Context:** Maintenance

**Purpose:** Tracks equipment health activities.

| 🧩 Field | Detail |
|---|---|
| **Responsibilities** | Maintenance schedules · Repairs · Service history |
| **Attributes** | id UUID · assetId UUID · maintenanceType · scheduledDate · completedDate |
| **Events Produced** | `MaintenanceScheduled` · `MaintenanceCompleted` |

---

## 1️⃣2️⃣ 📡 Monitoring Aggregate

**Aggregate Root:** `MonitoringAlert`

**Purpose:** Represents abnormal conditions — High fuel consumption · Equipment failure risk · Unexpected runtime.

| 🧩 Field | Detail |
|---|---|
| **Events Produced** | `AlertTriggered` · `AlertResolved` |

---

## 1️⃣3️⃣ 💬 Recommendation Aggregate

**Aggregate Root:** `Recommendation`

**Purpose:** Stores actionable intelligence.

**Examples:** *Reduce generator runtime by 3 hours daily · Service generator within 20 hours*

| 🧩 Field | Detail |
|---|---|
| **Events Produced** | `RecommendationGenerated` |

---

## 1️⃣4️⃣ Aggregate Relationship Map

```
                    🌐 EnergyConsumer
                          │
              ┌───────────┴───────────┐
              │                       │
     🏢 OrganizationProfile     🏠 HouseholdProfile
              └───────────┬───────────┘
                          │
                       📍 Site
                          │
                    🔋 EnergyAsset
                          │
                  ⚙️ EnergyOperation
                          │
              ┌───────────┴───────────┐
              │                       │
      ⛽ FuelInventory        🔧 MaintenanceRecord
              └───────────┬───────────┘
                          │
                    📡 Monitoring
                          │
                  💬 Recommendation
```

---

## 1️⃣5️⃣ Cross-Aggregate Communication

| Method | Example |
|---|---|
| 📢 **Domain Events** | `EnergyAssetRegistered` → Fuel module creates inventory tracking |
| 📞 **Explicit Contracts** | `AssetService` → `SiteExistenceChecker` |

---

## 1️⃣6️⃣ Database Ownership Rule

> 🗄️ Aggregates own their data.
>
> 🚫 No aggregate directly manipulates another aggregate's tables.
>
> 🚫 No cross-module foreign keys — **references are UUID only.**

---

## 🏁 Final Aggregate Decision

<div align="center">

> ### *PowerPulse aggregates are designed around business capability, not database structure.*
>
> ### *EnergyConsumer is the foundation.*

**All future intelligence depends on the correctness of these boundaries. 🧱🌐**

</div>
