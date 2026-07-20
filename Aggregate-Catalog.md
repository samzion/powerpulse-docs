<div align="center">

# 🧱 PowerPulse Aggregate Catalog

### *The consistency boundaries of the PowerPulse domain model*

![Aggregates](https://img.shields.io/badge/document-aggregate%20catalog-9b59b6?style=for-the-badge)
![Count](https://img.shields.io/badge/aggregates-9-orange?style=for-the-badge)
![DDD](https://img.shields.io/badge/methodology-DDD-blue?style=for-the-badge)

</div>

---

## 🎯 Purpose

This document defines the **aggregate boundaries** within PowerPulse. Aggregates are the primary consistency boundaries of the domain model.

**An aggregate:**

- 🏠 Owns related domain objects
- ⚖️ Protects business invariants
- 🔒 Controls state changes
- 🔲 Defines transactional boundaries
- 📢 Publishes domain events when meaningful changes occur

> 🚫 Aggregates are **not** database tables. **They represent business concepts.**

---

## 🧭 Aggregate Design Principles

### Rule 1 — Aggregate Roots Control Access

External modules communicate with aggregates **through the aggregate root**. Internal entities are not directly modified by external code.

### Rule 2 — Aggregates Protect Invariants

Business rules must be enforced inside the aggregate.

| ❌ Wrong | ✅ Correct |
|---|---|
| `organization.setVerified(true);` | `organization.verify();` |

> ⚖️ The aggregate decides whether verification is allowed.

### Rule 3 — Aggregates Should Remain Small

Large aggregates create: 🔒 locking problems · 🧵 complex transactions · 🧪 difficult testing

> 🚫 PowerPulse avoids modelling the entire energy ecosystem as one aggregate.

### Rule 4 — Cross-Aggregate Communication Uses IDs and Events

Aggregates do not contain references to other aggregate objects.

| ✅ Correct | ❌ Incorrect |
|---|---|
| `EnergyAsset { siteId: UUID }` | `EnergyAsset { Site site; }` |

---

## 1️⃣ 🏢 Organization Aggregate

**Bounded Context:** Organization · **Purpose:** Represents a business entity using PowerPulse *(bakery, hotel, retail company)*

| 🧩 Field | Detail |
|---|---|
| **Aggregate Root** | `Organization` |
| **Entities** | Organization |
| **Value Objects** | OrganizationName · Address · VerificationCredential |
| **Attributes** | id · name · status · createdAt · updatedAt |
| **Possible States** | 🟡 `PENDING_VERIFICATION` · 🟢 `ACTIVE` · 🔴 `SUSPENDED` |
| **Commands** | RegisterOrganization · VerifyOrganization · SuspendOrganization |
| **Domain Events** | `OrganizationRegistered` · `OrganizationVerified` · `OrganizationSuspended` |

**⚖️ Invariants**
- Organization must have a valid name
- Organization identity must be unique
- Verification status transitions must be controlled
- Suspended organizations cannot perform restricted operations

---

## 2️⃣ 📍 Site Aggregate

**Bounded Context:** Site · **Purpose:** Represents a physical location where energy is consumed

```
🏢 Mama Ngozi Bakery
  ├── 📍 Mushin Branch
  └── 📍 Ikeja Branch
```

| 🧩 Field | Detail |
|---|---|
| **Aggregate Root** | `Site` |
| **Entities** | Site |
| **Value Objects** | SiteAddress · Location |
| **Attributes** | id · organizationId · name · address · status · createdAt |
| **Commands** | CreateSite · UpdateSiteDetails · DeactivateSite |
| **Domain Events** | `SiteCreated` · `SiteUpdated` · `SiteDeactivated` |

**⚖️ Invariants**
- Site must belong to an organization
- Site name cannot be empty
- Site identifier must be unique within organization
- Deactivated sites cannot receive new operations

---

## 3️⃣ 🔋 Energy Asset Aggregate

**Bounded Context:** Energy Asset · **Purpose:** Represents physical equipment for energy production or storage

| 🧩 Field | Detail |
|---|---|
| **Aggregate Root** | `EnergyAsset` |
| **Entities** | EnergyAsset |
| **Value Objects** | AssetSpecification · Capacity · ManufacturerDetails |
| **Attributes** | id · siteId · assetType · name · capacity · status · installedAt |
| **Asset Types** | ⚡ `GENERATOR` · 🔌 `INVERTER` · 🔋 `BATTERY` · ☀️ `SOLAR` |
| **Commands** | RegisterEnergyAsset · UpdateAssetSpecification · RetireAsset |
| **Domain Events** | `EnergyAssetRegistered` · `EnergyAssetUpdated` · `EnergyAssetRetired` |

**⚖️ Invariants**
- Asset must belong to a site
- Asset type must be valid
- Retired assets cannot generate operations
- Capacity must be positive

---

## 4️⃣ ⚙️ Energy Operation Aggregate

**Bounded Context:** Energy Operations · **Purpose:** Represents actual energy usage behaviour — *the beginning of operational intelligence*

| 🧩 Field | Detail |
|---|---|
| **Aggregate Root** | `EnergyOperation` |
| **Entities** | EnergyOperation |
| **Value Objects** | EnergyMeasurement · RuntimeDuration · EnergyCost |
| **Attributes** | id · assetId · operationType · startTime · endTime · energyConsumed · cost |
| **Operation Types** | `GENERATION` · `CONSUMPTION` · `GRID_AVAILABILITY` · `BATTERY_DISCHARGE` |
| **Commands** | RecordEnergyUsage · CloseOperation · CalculateOperationCost |
| **Domain Events** | `EnergyUsageRecorded` · `EnergyOperationCompleted` |

**⚖️ Invariants**
- Energy values cannot be negative
- End time cannot be before start time
- Operations must reference valid assets
- Historical operations cannot be silently changed

---

## 5️⃣ ⛽ Fuel Inventory Aggregate

**Bounded Context:** Fuel · **Purpose:** Tracks fuel availability and consumption

| 🧩 Field | Detail |
|---|---|
| **Aggregate Root** | `FuelInventory` |
| **Entities** | FuelInventory · FuelTransaction |
| **Value Objects** | FuelQuantity · Money · FuelType |
| **Attributes** | id · assetId · currentQuantity · fuelType |
| **Commands** | AddFuel · ConsumeFuel · AdjustFuelInventory |
| **Domain Events** | `FuelAdded` · `FuelConsumed` · `FuelAdjusted` |

**⚖️ Invariants**
- Fuel quantity cannot be negative
- Consumption cannot exceed available inventory
- Fuel transactions cannot modify historical records

---

## 6️⃣ 🔧 Maintenance Aggregate

**Bounded Context:** Maintenance · **Purpose:** Tracks equipment reliability and servicing

| 🧩 Field | Detail |
|---|---|
| **Aggregate Root** | `MaintenanceRecord` |
| **Entities** | MaintenanceRecord |
| **Value Objects** | MaintenanceType · MaintenanceCost |
| **Commands** | ScheduleMaintenance · CompleteMaintenance |
| **Domain Events** | `MaintenanceScheduled` · `MaintenanceCompleted` |

**⚖️ Invariants**
- Maintenance belongs to an asset
- Completion requires a valid schedule
- Maintenance history is immutable

---

## 7️⃣ 📡 Monitoring Aggregate

**Bounded Context:** Monitoring · **Purpose:** Detect operational conditions requiring attention

| 🧩 Field | Detail |
|---|---|
| **Aggregate Root** | `Alert` |
| **Entities** | Alert |
| **Value Objects** | AlertCondition · Severity |
| **Commands** | CreateAlert · ResolveAlert |
| **Domain Events** | `AlertCreated` · `AlertResolved` |

**⚖️ Invariants**
- Alert must have a reason
- Resolved alerts cannot be modified

---

## 8️⃣ 📊 Analytics Aggregate

**Bounded Context:** Analytics · **Purpose:** Transforms operational facts into business understanding

| 🧩 Field | Detail |
|---|---|
| **Aggregate Root** | `EnergyReport` |
| **Entities** | EnergyReport |
| **Value Objects** | ReportPeriod · CostSummary · ConsumptionSummary |
| **Commands** | GenerateEnergyReport |
| **Domain Events** | `EnergyReportGenerated` |

**⚖️ Invariants**
- Analytics cannot modify operational records
- Reports must reference valid periods
- Calculations must be reproducible

---

## 9️⃣ 💬 Recommendation Aggregate

**Bounded Context:** Recommendation · **Purpose:** Produces actionable energy improvement suggestions

| 🧩 Field | Detail |
|---|---|
| **Aggregate Root** | `Recommendation` |
| **Value Objects** | RecommendationType · RecommendationPriority |
| **Commands** | GenerateRecommendation · DismissRecommendation · AcceptRecommendation |
| **Domain Events** | `RecommendationGenerated` · `RecommendationAccepted` |

**⚖️ Invariants**
- Recommendations must be based on known facts
- Recommendations must explain their reasoning
- Recommendations cannot alter source data

---

## 🗺️ Aggregate Relationship Overview

```
🏢 Organization
  └── 📍 Site
        └── 🔋 Energy Asset
              ├── ⚙️ Energy Operation
              ├── ⛽ Fuel Inventory
              └── 🔧 Maintenance
```

---

## 🏗️ Aggregate Implementation Rule

When implementing PowerPulse, build in this order:

1. 🏢 Start with **Organization**
2. 📍 Add **Site**
3. 🔋 Add **Energy Asset**
4. ⚙️ Add **Operations**
5. ✨ Add supporting capabilities

> 🚫 Do **not** implement analytics or recommendations before operational aggregates exist.
>
> 💡 The quality of intelligence depends on the quality of the aggregates producing the data.

---

## 🏁 Final Principle

<div align="center">

> ### *Aggregates are not created to mirror database tables.*
> ### *They exist to protect the business reality PowerPulse represents.*

</div>
