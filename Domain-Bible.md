<div align="center">

# 🧠 PowerPulse Domain Bible

### *The business domain model of PowerPulse*

![Domain](https://img.shields.io/badge/document-domain%20model-blueviolet?style=for-the-badge)
![DDD](https://img.shields.io/badge/methodology-DDD-blue?style=for-the-badge)
![Contexts](https://img.shields.io/badge/bounded%20contexts-11-orange?style=for-the-badge)

</div>

---

## 🎯 Purpose

This document defines the business domain model of PowerPulse.

**It describes:**

| 📌 Topic |
|---|
| 🏢 The business capabilities PowerPulse owns |
| 🧩 The bounded contexts that organize the system |
| 💡 The core concepts of the energy intelligence platform |
| 🧱 Aggregates and their responsibilities |
| 📢 Domain events |
| 🚧 Domain boundaries |
| ⚖️ Business invariants |

> 🌉 This document is the **bridge** between product vision and software implementation.
>
> The goal is not to describe code. **The goal is to describe the business reality the software must represent.**

---

## 1️⃣ Domain Overview

PowerPulse exists to model and understand distributed energy operations. The platform observes the relationship between:

```
🏢 Organization
  └── 📍 Sites
        └── 🔋 Energy Assets
              └── ⚙️ Energy Operations
                    └── ⛽ Fuel
                          └── 🔧 Maintenance
                                └── 📡 Monitoring
                                      └── 📊 Analytics
                                            └── 💬 Recommendations
```

> 🇳🇬 The domain represents how Nigerian businesses consume, manage, and optimize energy.

---

## 2️⃣ Core Domain

<div align="center">

> ### 💎 *The Core Domain of PowerPulse is: **Energy Intelligence***

</div>

Everything that differentiates PowerPulse comes from understanding energy behaviour better than existing alternatives.

**The Core Domain includes:**
- ⚙️ Energy operations tracking
- 💵 Cost understanding
- 📈 Consumption intelligence
- 🔍 Operational insights
- 💬 Optimization recommendations

---

## 3️⃣ Supporting Domains

Supporting domains provide the foundation required for intelligence.

| 🧩 Domain | Responsible For |
|---|---|
| 🪪 **Identity** | User authentication · Access control · User lifecycle |
| 🏢 **Organization** | Business ownership · Energy account ownership · Company identity |
| 📍 **Site** | Physical locations where energy is consumed *(factory branch, shop, office, hotel)* |
| 🔋 **Energy Asset** | Equipment producing or storing energy *(generator, inverter, battery, solar)* |
| ⛽ **Fuel** | Fuel inventory · Fuel consumption · Fuel cost tracking |
| 🔧 **Maintenance** | Asset servicing · Maintenance history · Equipment health |

---

## 4️⃣ Bounded Contexts

PowerPulse is organized into bounded contexts. Each context owns a specific business capability.

### 🪪 Identity Context

**Responsibility:** Manage who can access PowerPulse.

| ✅ Owns | 🚫 Does Not Own |
|---|---|
| User | Business organizations |
| Credentials | Energy assets |
| Authentication | Operational data |
| Authorization | — |

---

### 🏢 Organization Context

**Responsibility:** Represent businesses using PowerPulse.

- 🧱 **Aggregate Root:** `Organization`
- 📦 **Owns:** Organization profile · Business information · Verification information

**⚖️ Invariants**
- Organization must have a valid name
- Organization identity must be unique
- Verification state must follow defined rules

---

### 📍 Site Context

**Responsibility:** Represent physical operating locations. A single organization may have multiple sites.

```
🏢 Mama Ngozi Bakery Ltd
  ├── 📍 Mushin Branch
  └── 📍 Ikeja Branch
```

- 🧱 **Aggregate Root:** `Site`
- 📦 **Owns:** Site information · Location · Operational metadata

**⚖️ Invariants**
- Site must belong to an organization
- Site identity must be unique within organization

---

### 🔋 Energy Asset Context

**Responsibility:** Represent physical energy equipment — generators, inverters, batteries, solar systems.

- 🧱 **Aggregate Root:** `EnergyAsset`
- 📦 **Owns:** Asset identity · Asset type · Technical specifications · Installation information

**⚖️ Invariants**
- Asset must belong to a site
- Asset type determines valid behaviour

---

### ⚙️ Energy Operations Context

**Responsibility:** Represent energy usage events — **this is where PowerPulse begins creating intelligence.**

Examples: *generator ran for 6 hours · grid available for 4 hours · battery discharged*

- 🧱 **Aggregate Root:** `EnergyOperation`
- 📦 **Owns:** Usage records · Runtime · Consumption measurements

**⚖️ Invariants**
- Operation must reference an existing asset
- Measurements must be valid
- Time cannot be impossible

---

### ⛽ Fuel Context

**Responsibility:** Understand fuel economics.

- 📦 **Owns:** Fuel purchase · Fuel inventory · Consumption records

**⚖️ Invariants**
- Fuel quantity cannot be negative
- Consumption cannot exceed available inventory

---

### 🔧 Maintenance Context

**Responsibility:** Track asset reliability.

- 📦 **Owns:** Maintenance schedules · Maintenance events · Service history

**⚖️ Invariants**
- Maintenance belongs to an asset
- Maintenance records cannot alter historical operations

---

### 📡 Monitoring Context

**Responsibility:** Observe system behaviour.

- 📦 **Owns:** Alerts · Threshold monitoring · Operational signals

---

### 📊 Analytics Context

**Responsibility:** Transform operational data into understanding.

- 📦 **Owns:** Reports · Aggregations · Trends

> ⚠️ Analytics **consumes** domain facts. It does **not** own operational truth.

---

### 💬 Recommendation Context

**Responsibility:** Provide actionable advice.

Examples: *reduce generator runtime · service asset · adjust energy behaviour*

> 📌 Recommendations are based on observed facts.

---

## 5️⃣ Aggregate Principles

> 🧱 Aggregates **protect business rules**. They are not database grouping mechanisms.
>
> An aggregate exists where **consistency is required.**

<br>

<div align="center">

| 🧱 Aggregate | 🎯 Root | 📦 Entities | 💎 Value Objects | 📢 Events |
|---|---|---|---|---|
| **Organization** | `Organization` | Organization | OrganizationName · VerificationCredential · Address | `OrganizationRegistered` · `OrganizationVerified` |
| **Site** | `Site` | Site | SiteAddress · SiteIdentifier | `SiteCreated` |
| **Energy Asset** | `EnergyAsset` | EnergyAsset | AssetSpecification · Capacity | `EnergyAssetRegistered` |
| **Energy Operation** | `EnergyOperation` | EnergyUsageRecord | EnergyMeasurement · RuntimeDuration | `EnergyConsumed` · `EnergyUsageRecorded` |
| **Fuel** | `FuelInventory` | FuelTransaction | FuelQuantity · FuelCost | `FuelAdded` · `FuelConsumed` |
| **Maintenance** | `MaintenanceRecord` | — | — | `MaintenanceScheduled` · `MaintenanceCompleted` |

</div>

---

## 6️⃣ Domain Events

Domain events represent **business facts**. They are immutable statements about something that already happened.

```
📢 OrganizationRegistered
📢 SiteCreated
📢 EnergyAssetRegistered
📢 EnergyUsageRecorded
📢 FuelConsumed
📢 MaintenanceCompleted
```

> 🔗 Events allow future capabilities without tightly coupling modules.

---

## 7️⃣ Domain Services

Domain services exist where behaviour does not naturally belong to one entity.

| 🛠️ Service | Function |
|---|---|
| **⚡ EnergyCostCalculator** | Calculates cost based on energy source, consumption, and pricing rules |
| **⛽ FuelConsumptionAnalyzer** | Determines consumption patterns and fuel efficiency |
| **💬 EnergyRecommendationEngine** | Creates operational recommendations |

---

## 8️⃣ Domain Rules

| # | Rule |
|---|---|
| 1️⃣ | Energy assets belong to **sites** — they do not belong directly to organizations |
| 2️⃣ | Operational records describe what happened — they must **not** be modified to change history |
| 3️⃣ | Analytics **cannot** become the source of truth — it derives from operational data |
| 4️⃣ | Predictions **cannot** replace measurements |

---

## 9️⃣ Digital Twin Concept

The long-term PowerPulse vision is a digital representation of real energy environments:

```
🏢 Organization
  └── 📍 Site
        └── 🔋 Assets
              └── ⚙️ Operations
                    └── 📈 Behaviour
                          └── 🎯 Optimization
```

> ⚠️ **A digital twin without operational events is only a model.**

**Therefore, first build:**
1. 🪪 Identity
2. 🔋 Assets
3. ⚙️ Operations
4. 📜 History

**Then intelligence.**

---

## 🔟 Domain Evolution Path

PowerPulse evolves through stages:

| Stage | Focus | Includes |
|---|---|---|
| **1 — Foundation** 🏗️ | Basics | Identity · Organization · Site |
| **2 — Energy Reality** ⚡ | Ground truth | Assets · Operations · Fuel |
| **3 — Understanding** 🔍 | Insight | Monitoring · Analytics |
| **4 — Intelligence** 💬 | Advice | Recommendations · Optimization |
| **5 — Autonomy** 🤖 | Action | Automated decisions · Smart energy management |

---

## 🏁 Final Domain Principle

<div align="center">

> ### *PowerPulse does not start by predicting the future.*
> ### *It starts by accurately understanding the present.*

**The platform earns intelligence by building a trustworthy model of real energy behaviour. 🇳🇬**

</div>
