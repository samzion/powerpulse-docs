<div align="center">

# 🧠 PowerPulse Domain Design Bible

### *v2.0 — Domain Evolution: From Organization Intelligence to Energy Consumer Intelligence*

![Domain](https://img.shields.io/badge/document-domain%20model-blueviolet?style=for-the-badge)
![Version](https://img.shields.io/badge/version-2.0-red?style=for-the-badge)
![DDD](https://img.shields.io/badge/methodology-DDD-blue?style=for-the-badge)
![Scope](https://img.shields.io/badge/scope-all%20energy%20consumers-orange?style=for-the-badge)

</div>

---

## 1️⃣ Purpose

This document defines the core domain model of PowerPulse.

PowerPulse is an energy intelligence platform designed to understand, measure, and optimize energy behaviour. The domain model is built around one fundamental concept:

<div align="center">

> ### 🧭 *"Energy consumers require intelligence about how energy is generated, stored, consumed, and paid for."*

</div>

The platform begins with **Nigerian SMEs** but is designed to support: `🏢 Businesses` · `🏠 Households` · `🏛️ Institutions` · `🌆 Communities` · `🌐 Distributed energy systems`

---

## 2️⃣ Core Domain Principle

<div align="center">

> ### 💎 *PowerPulse does not primarily model businesses. PowerPulse models Energy Behaviour.*

</div>

Every consumer follows the same fundamental energy lifecycle:

```
⚡ Energy Source
    └── 🔄 Energy Conversion
          └── 🔋 Energy Storage
                └── 🔌 Energy Consumption
                      └── 💵 Cost
                            └── 📊 Operational Behaviour
                                  └── 🎯 Optimization
```

---

## 3️⃣ Domain Language

### 🌐 Energy Consumer

The **central domain concept**. An Energy Consumer is any entity that:

- 🔌 consumes energy
- 🔋 manages energy assets
- 💵 pays for energy
- 🧠 makes energy decisions

**Examples:** SME · Household · School · Hospital · Estate

### 🧩 Consumer Types

| Status | Types |
|---|---|
| ✅ **Initial** | `BUSINESS` · `HOUSEHOLD` |
| 🔮 **Future** | `INSTITUTION` · `COMMUNITY` · `GOVERNMENT` |

---

## 4️⃣ High-Level Domain Model

**From:**

```
🏢 Organization
   └── 📍 Site
         └── 🔋 Energy Asset
               └── ⚙️ Energy Operation
```

**To:**

```
                    🌐 Energy Consumer
                          │
              ┌───────────┴───────────┐
              │                       │
     🏢 Organization Profile    🏠 Household Profile
              │                       │
          📍 Site                 🏠 Residence
              │                       │
       🔋 Energy Assets         🔋 Energy Assets
              │                       │
              └───────────┬───────────┘
                          │
                ⚙️ Energy Operations
```

---

## 5️⃣ Bounded Contexts

### 🪪 Identity Context

**Responsibility:** Manages authentication and user identity.

| ✅ Owns | 🚫 Does Not Own |
|---|---|
| Users · Credentials · Sessions · Authentication state | Businesses · Energy assets · Operations |

---

### 🌐 Consumer Context

**Responsibility:** Manages the energy consumer lifecycle.

- 🧱 **Aggregate:** `EnergyConsumer`
- 📦 **Owns:** Energy Consumer · Consumer type · Consumer status

**⚖️ Invariants** — An Energy Consumer:
- must have a valid identity
- must have a consumer type
- must have a lifecycle state

---

### 🏢 Organization Profile Context

**Responsibility:** Business-specific information.

- 📦 **Owns:** Business name · Business category · Business classification · Registration information

> Example: *Mama Ngozi Bakery* belongs here.

---

### 🏠 Household Profile Context

**Responsibility:** Residential-specific information.

- 📦 **Owns:** Household identity · Residence information · Household characteristics

> Example: *Samson Residence* belongs here.

---

### 📍 Site Context

**Responsibility:** Represents physical locations where energy activity occurs *(shop branch, home, factory, estate block)*.

- 📦 **Owns:** Site identity · Location · Operational status

---

### 🔋 Energy Asset Context

**Responsibility:** Models physical energy equipment *(generator, inverter, solar system, battery, grid connection)*.

- 📦 **Owns:** Asset identity · Asset type · Asset lifecycle · Asset characteristics

---

### ⚙️ Energy Operations Context

**Responsibility:** Captures energy behaviour *(generator runtime, grid availability, battery usage, energy consumption)*.

- 📦 **Owns:** Operational events · Runtime records · Consumption records

---

### ⛽ Fuel Context

**Responsibility:** Manages fuel intelligence.

- 📦 **Owns:** Fuel inventory · Fuel purchases · Fuel consumption

---

### 🔧 Maintenance Context

**Responsibility:** Manages equipment health.

- 📦 **Owns:** Maintenance schedules · Repairs · Service history

---

### 📡 Monitoring Context

**Responsibility:** Observes system behaviour.

- 📦 **Owns:** Alerts · Threshold monitoring · Operational warnings

---

### 📊 Analytics Context

**Responsibility:** Transforms operational data into knowledge.

- 📦 **Owns:** Reports · Trends · Aggregations

---

### 💬 Recommendation Context

**Responsibility:** Provides actionable intelligence.

- 📦 **Owns:** Recommendations · Optimization rules · Decision support

---

## 6️⃣ Aggregate Catalogue

| 🧱 Aggregate | Root | Purpose |
|---|---|---|
| **Energy Consumer** | `EnergyConsumer` | Protect consumer identity and lifecycle *(Consumer ID · Consumer Type · Status)* |
| **Organization** | `OrganizationProfile` | Business representation |
| **Household** | `HouseholdProfile` | Residential representation |
| **Site** | `Site` | Physical energy location |
| **Energy Asset** | `EnergyAsset` | Protect asset lifecycle *(Generator · Inverter · Solar Panel · Battery)* |
| **Energy Operation** | `EnergyOperation` | Capture energy activity |
| **Fuel** | `FuelInventory` | Protect fuel state |

---

## 7️⃣ Domain Events

```
📢 EnergyConsumerRegistered
📢 OrganizationProfileCreated
📢 HouseholdProfileCreated
📢 SiteCreated
📢 EnergyAssetRegistered
📢 EnergyAssetActivated
📢 EnergyOperationRecorded
📢 FuelAdded
📢 FuelConsumed
📢 MaintenanceScheduled
📢 MaintenanceCompleted
📢 RecommendationGenerated
```

---

## 8️⃣ Relationships

> 🚫 **Important rule:** Modules do not share database ownership.

Relationships are represented through: 🆔 UUID references · 📢 Domain events · 📞 Explicit contracts

| ✅ Allowed | 🚫 Not Allowed |
|---|---|
| `EnergyAsset { siteId: UUID }` | `FOREIGN KEY(site_id)` |

---

## 9️⃣ Data Ownership

| 🧩 Context | 🗄️ Owns |
|---|---|
| 🪪 Identity | Users |
| 🌐 Consumer | Energy Consumers |
| 🏢 Organization | Business Profiles |
| 🏠 Household | Household Profiles |
| 📍 Site | Sites |
| 🔋 Asset | Energy Assets |
| ⚙️ Operations | Energy Events |
| ⛽ Fuel | Fuel Data |
| 🔧 Maintenance | Maintenance Data |
| 📊 Analytics | Reports |

---

## 🔟 Evolution Strategy

```
🏗️ Foundation
     └── ⚙️ Reliable Operations
           └── 📈 Rich Data
                 └── 🧠 Knowledge
                       └── 💬 Intelligence
                             └── 🎯 Optimization
                                   └── 🤖 Autonomy
```

> 🔑 Intelligence depends on **trustworthy operational data**.

---

## 1️⃣1️⃣ Design Principle

> 🚫 The platform must not build intelligence before understanding reality.
>
> 💭 A digital twin without operational history is only a data structure.

**PowerPulse must first capture:** 🔋 assets · 📢 events · 💵 costs · 📊 behaviour — **before attempting prediction.**

---

## 🏁 Final Domain Statement

<div align="center">

> ### *PowerPulse is an energy intelligence platform built around the concept of Energy Consumers.*

**🏢 Organizations are the first users.**
**🏠 Households are natural extensions.**

### *The true domain is energy behaviour.*

**The system exists to understand, measure, and improve how energy is used. 🌐⚡**

</div>
