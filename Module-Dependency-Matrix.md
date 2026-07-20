<div align="center">

# 🧭 PowerPulse Module Dependency Matrix

### *Dependency rules between PowerPulse bounded contexts*

![Matrix](https://img.shields.io/badge/document-dependency%20matrix-16a085?style=for-the-badge)
![Modules](https://img.shields.io/badge/modules-11-blue?style=for-the-badge)
![Architecture](https://img.shields.io/badge/architecture-Spring%20Modulith-brightgreen?style=for-the-badge&logo=spring&logoColor=white)

</div>

---

## 🎯 Purpose

This document defines the dependency rules between PowerPulse bounded contexts. The purpose is to **prevent architectural drift** as the system grows.

PowerPulse is designed as a **modular monolith** using Spring Modulith. Although all modules initially run inside one application, they must behave as independently owned business capabilities.

**A module:**
- ⚖️ Owns its business rules
- 🧠 Owns its internal models
- 🗄️ Owns its persistence
- 📞 Exposes only intentional contracts
- 📢 Communicates through defined interfaces and domain events

---

## 🏛️ Architectural Principle

The dependency direction follows business ownership. The system should evolve from:

```
🏗️ Foundation
    └── ⚡ Operational Reality
          └── 🔍 Understanding
                └── 💬 Intelligence
```

> 🔼 Modules lower in the stack provide capabilities to modules above them.

---

## 🗂️ Module Overview

```
📦 shared · 🪪 identity · 🏢 organization · 📍 site · 🔋 asset
⚙️ operations · ⛽ fuel · 🔧 maintenance · 📡 monitoring
📊 analytics · 💬 recommendation
```

---

## 📐 Dependency Rules

### 🔗 Shared Module

**Responsibility:** Concepts genuinely shared across the system — domain event base contracts, common exceptions, UUID utilities, time abstraction, shared primitives.

| | |
|---|---|
| **Depends On** | `NONE` |
| **Used By** | `ALL MODULES` |

> 🚫 **Restrictions:** The shared module must never contain business entities, domain rules, module-specific services, or database models. **It is not a dumping ground.**

---

### 🪪 Identity Module

**Responsibility:** Authentication and user identity.

| | |
|---|---|
| **Depends On** | `shared` |
| **Used By** | `organization` · *(future: billing, notification, access-control)* |

> 🚫 **Does not know:** Organizations · Sites · Energy assets · Operations

---

### 🏢 Organization Module

**Responsibility:** Business ownership and company identity.

| | |
|---|---|
| **Depends On** | `shared` · `identity` |
| **Used By** | `site` · *(future: billing, analytics)* |
| **Publishes** | 📢 `OrganizationRegistered` · `OrganizationVerified` · `OrganizationSuspended` |

---

### 📍 Site Module

**Responsibility:** Physical operating locations.

| | |
|---|---|
| **Depends On** | `shared` · `organization` |
| **Used By** | `asset` |
| **Consumes** | 📥 `OrganizationRegistered` |
| **Publishes** | 📢 `SiteCreated` · `SiteUpdated` |

---

### 🔋 Energy Asset Module

**Responsibility:** Physical energy equipment *(generator, inverter, battery, solar)*.

| | |
|---|---|
| **Depends On** | `shared` · `site` |
| **Used By** | `operations` · `fuel` · `maintenance` · `monitoring` |
| **Consumes** | 📥 `SiteCreated` |
| **Publishes** | 📢 `EnergyAssetRegistered` · `EnergyAssetUpdated` · `EnergyAssetRetired` |

---

### ⚙️ Energy Operations Module

**Responsibility:** Energy usage behaviour.

| | |
|---|---|
| **Depends On** | `shared` · `asset` |
| **Used By** | `analytics` · `fuel` · `monitoring` · `recommendation` |
| **Consumes** | 📥 `EnergyAssetRegistered` |
| **Publishes** | 📢 `EnergyUsageRecorded` · `EnergyOperationCompleted` |

---

### ⛽ Fuel Module

**Responsibility:** Fuel economics and inventory.

| | |
|---|---|
| **Depends On** | `shared` · `asset` · `operations` |
| **Used By** | `analytics` · `recommendation` |
| **Consumes** | 📥 `EnergyUsageRecorded` |
| **Publishes** | 📢 `FuelAdded` · `FuelConsumed` |

---

### 🔧 Maintenance Module

**Responsibility:** Asset reliability.

| | |
|---|---|
| **Depends On** | `shared` · `asset` |
| **Used By** | `analytics` · `monitoring` |
| **Consumes** | 📥 `EnergyAssetRegistered` · `EnergyAssetRetired` |
| **Publishes** | 📢 `MaintenanceScheduled` · `MaintenanceCompleted` |

---

### 📡 Monitoring Module

**Responsibility:** Operational awareness.

| | |
|---|---|
| **Depends On** | `shared` · `asset` · `operations` · `maintenance` |
| **Used By** | `recommendation` |
| **Consumes** | 📥 `EnergyUsageRecorded` · `EnergyAssetRegistered` · `MaintenanceCompleted` |
| **Publishes** | 📢 `AlertCreated` · `AlertResolved` |

---

### 📊 Analytics Module

**Responsibility:** Business intelligence and reporting.

| | |
|---|---|
| **Depends On** | `shared` · `operations` · `fuel` · `maintenance` |
| **Consumes** | 📥 `EnergyUsageRecorded` · `EnergyOperationCompleted` · `FuelConsumed` · `MaintenanceCompleted` |
| **Publishes** | 📢 `EnergyReportGenerated` |

> 🚫 **Does not own:** Energy records · Fuel records · Maintenance records — **it only interprets them.**

---

### 💬 Recommendation Module

**Responsibility:** Actionable energy improvement suggestions.

| | |
|---|---|
| **Depends On** | `shared` · `analytics` · `monitoring` |
| **Consumes** | 📥 `EnergyReportGenerated` · `AlertCreated` |
| **Publishes** | 📢 `RecommendationGenerated` · `RecommendationAccepted` |

> 🚫 **Does not calculate** operational truth — it consumes reports, metrics, and alerts.

---

## 📊 Dependency Matrix

**Legend:** ✅ = Allowed dependency · 🚫 = Forbidden dependency · 📢 = Event communication

| Module | Shared | Identity | Org | Site | Asset | Ops | Fuel | Maint | Monitor | Analytics | Rec. |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Shared** | – | 🚫 | 🚫 | 🚫 | 🚫 | 🚫 | 🚫 | 🚫 | 🚫 | 🚫 | 🚫 |
| **Identity** | ✅ | – | 🚫 | 🚫 | 🚫 | 🚫 | 🚫 | 🚫 | 🚫 | 🚫 | 🚫 |
| **Organization** | ✅ | ✅ | – | 📢 | 🚫 | 🚫 | 🚫 | 🚫 | 🚫 | 📢 | 🚫 |
| **Site** | ✅ | 🚫 | ✅ | – | 📢 | 🚫 | 🚫 | 🚫 | 🚫 | 🚫 | 🚫 |
| **Asset** | ✅ | 🚫 | 🚫 | ✅ | – | 📢 | 📢 | 📢 | 📢 | 🚫 | 🚫 |
| **Operations** | ✅ | 🚫 | 🚫 | 🚫 | ✅ | – | 📢 | 🚫 | 📢 | 📢 | 📢 |
| **Fuel** | ✅ | 🚫 | 🚫 | 🚫 | ✅ | ✅ | – | 🚫 | 🚫 | 📢 | 📢 |
| **Maintenance** | ✅ | 🚫 | 🚫 | 🚫 | ✅ | 🚫 | 🚫 | – | 📢 | 📢 | 🚫 |
| **Monitoring** | ✅ | 🚫 | 🚫 | 🚫 | ✅ | ✅ | 🚫 | ✅ | – | 🚫 | 📢 |
| **Analytics** | ✅ | 🚫 | 📢 | 🚫 | 🚫 | ✅ | ✅ | ✅ | 🚫 | – | 📢 |
| **Recommendation** | ✅ | 🚫 | 🚫 | 🚫 | 🚫 | 📢 | 📢 | 📢 | ✅ | ✅ | – |

---

## 🚫 Forbidden Dependencies

The following are **architectural violations**.

| ⚠️ Violation | Example | Reason |
|---|---|---|
| **Domain depending on infrastructure** | `domain → JPA repository` | Domain must stay pure |
| **Analytics owning operational data** | `analytics.energy_usage_table` | Analytics consumes facts, doesn't own them |
| **Recommendation directly querying assets** | `recommendation → asset database` | Recommendation consumes analytics/monitoring outputs only |
| **Cross-module database foreign keys** | `asset.site_id FK → site.id` | Use `asset.site_id UUID` instead — relationships are managed through business contracts |

---

## 🏗️ Implementation Order

```
📦 shared
   └── 🪪 identity
         └── 🏢 organization
               └── 📍 site
                     └── 🔋 asset
                           └── ⚙️ operations
                                 └── ⛽ fuel
                                       └── 🔧 maintenance
                                             └── 📡 monitoring
                                                   └── 📊 analytics
                                                         └── 💬 recommendation
```

---

## 🏁 Final Principle

<div align="center">

> ### *PowerPulse grows through controlled evolution.*
> ### *Strong module boundaries today create the flexibility required for tomorrow's intelligence.*

**The architecture must protect the domain, not the other way around. 🛡️**

</div>
