<div align="center">

# 🧭 PowerPulse Module Dependency Matrix

### *v2.0 — Module Boundaries After Energy Consumer Abstraction*

![Matrix](https://img.shields.io/badge/document-dependency%20matrix-16a085?style=for-the-badge)
![Version](https://img.shields.io/badge/version-2.0-red?style=for-the-badge)
![Modules](https://img.shields.io/badge/modules-13-blue?style=for-the-badge)
![Architecture](https://img.shields.io/badge/architecture-Spring%20Modulith-brightgreen?style=for-the-badge&logo=spring&logoColor=white)

</div>

---

## 1️⃣ Purpose

This document defines the dependency rules between PowerPulse modules.

**The objective is to maintain:**
- 🧩 Strong boundaries
- 🔗 Low coupling
- 🎯 Clear ownership
- 🚀 Future extraction readiness

> ⬇️ A module may depend only on modules **below it** in the dependency direction.

---

## 2️⃣ Core Dependency Principle

<div align="center">

> ### 🧭 *Dependencies point toward stable business foundations.*

</div>

```
🪪 Identity
    └── 🌐 Consumer
          └── 🏢/🏠 Profiles
                └── 📍 Site
                      └── 🔋 Energy Asset
                            └── ⚙️ Energy Operations
                                  └── ⛽🔧📡 Fuel / Maintenance / Monitoring
                                        └── 📊 Analytics
                                              └── 💬 Recommendation
                                                    └── 🔔 Notification
```

---

## 3️⃣ Module Overview

| 🧩 Module | Responsibility |
|---|---|
| 🪪 **Identity** | User authentication and authorization |
| 🌐 **Consumer** | Energy consumer lifecycle |
| 🏢 **Organization Profile** | Business information |
| 🏠 **Household Profile** | Residential information |
| 📍 **Site** | Physical locations |
| 🔋 **Asset** | Energy equipment |
| ⚙️ **Operations** | Energy behaviour records |
| ⛽ **Fuel** | Fuel intelligence |
| 🔧 **Maintenance** | Equipment health |
| 📡 **Monitoring** | Alerts and observations |
| 📊 **Analytics** | Reports and insights |
| 💬 **Recommendation** | Decision support |
| 🔔 **Notification** | User communication |

---

## 4️⃣ 🪪 Identity Module

| | |
|---|---|
| **Owns** | `users`, `credentials`, `authentication` |
| **Depends On** | *None — foundational* |
| **Publishes** | 📢 `UserRegistered` |
| **Consumed By** | 🌐 Consumer Module · 🔔 Notification Module |

---

## 5️⃣ 🌐 Consumer Module

**Responsibility:** Creates and manages energy consumers — Business · Household.

| | |
|---|---|
| **Owns** | `energy_consumers` |
| **Depends On** | 🪪 Identity |
| **Publishes** | 📢 `EnergyConsumerRegistered` · `EnergyConsumerActivated` · `EnergyConsumerSuspended` |
| **Consumed By** | 🏢 Organization Profile · 🏠 Household Profile · 📍 Site |

---

## 6️⃣ 🏢 Organization Profile Module

| | |
|---|---|
| **Owns** | `organization_profiles` |
| **Depends On** | 🌐 Consumer |
| **Publishes** | 📢 `OrganizationProfileCreated` |
| **Consumed By** | 📍 Site · 📊 Analytics |

---

## 7️⃣ 🏠 Household Profile Module

| | |
|---|---|
| **Owns** | `household_profiles` |
| **Depends On** | 🌐 Consumer |
| **Publishes** | 📢 `HouseholdProfileCreated` |
| **Consumed By** | 📍 Site · 📊 Analytics |

---

## 8️⃣ 📍 Site Module

**Responsibility:** Manages physical operating locations — Shop · Home · Factory.

| | |
|---|---|
| **Owns** | `sites` |
| **Depends On** | 🌐 Consumer · 🏢/🏠 Profiles |
| **Publishes** | 📢 `SiteCreated` · `SiteActivated` · `SiteDeactivated` |
| **Consumed By** | 🔋 Asset · 📊 Analytics |

---

## 9️⃣ 🔋 Asset Module

**Responsibility:** Manages Generator · Inverter · Solar · Battery · Grid connection.

| | |
|---|---|
| **Owns** | `energy_assets` |
| **Depends On** | 📍 Site |
| **Publishes** | 📢 `EnergyAssetRegistered` · `EnergyAssetActivated` · `EnergyAssetRetired` |
| **Consumed By** | ⚙️ Operations · ⛽ Fuel · 🔧 Maintenance · 📡 Monitoring |

---

## 🔟 ⚙️ Operations Module

**Responsibility:** Records energy behaviour.

| | |
|---|---|
| **Owns** | `energy_operations` |
| **Depends On** | 🔋 Asset |
| **Publishes** | 📢 `EnergyOperationRecorded` |
| **Consumed By** | 📊 Analytics · ⛽ Fuel · 📡 Monitoring |

---

## 1️⃣1️⃣ ⛽ Fuel Module

| | |
|---|---|
| **Owns** | `fuel_inventory`, `fuel_transactions` |
| **Depends On** | 🔋 Asset · ⚙️ Operations |
| **Publishes** | 📢 `FuelAdded` · `FuelConsumed` · `FuelLevelLow` |
| **Consumed By** | 📊 Analytics · 💬 Recommendation · 🔔 Notification |

---

## 1️⃣2️⃣ 🔧 Maintenance Module

| | |
|---|---|
| **Owns** | `maintenance_records` |
| **Depends On** | 🔋 Asset |
| **Publishes** | 📢 `MaintenanceScheduled` · `MaintenanceCompleted` |
| **Consumed By** | 📊 Analytics · 🔔 Notification |

---

## 1️⃣3️⃣ 📡 Monitoring Module

| | |
|---|---|
| **Owns** | `alerts` |
| **Depends On** | 🔋 Asset · ⚙️ Operations · ⛽ Fuel |
| **Publishes** | 📢 `AlertTriggered` · `AlertResolved` |
| **Consumed By** | 💬 Recommendation · 🔔 Notification |

---

## 1️⃣4️⃣ 📊 Analytics Module

| | |
|---|---|
| **Owns** | `reports`, `aggregations` |
| **Depends On** | ⚙️ Operations · ⛽ Fuel · 🔧 Maintenance · 📡 Monitoring |
| **Publishes** | 📢 `AnalyticsGenerated` |
| **Consumed By** | 💬 Recommendation · 🔔 Notification |

---

## 1️⃣5️⃣ 💬 Recommendation Module

| | |
|---|---|
| **Owns** | `recommendations` |
| **Depends On** | 📊 Analytics · 📡 Monitoring |
| **Publishes** | 📢 `RecommendationGenerated` |
| **Consumed By** | 🔔 Notification · 🖥️ Frontend |

---

## 1️⃣6️⃣ 🔔 Notification Module

**Responsibilities:** 📧 Email · 📱 SMS · 🔔 Push notifications · ⚠️ User alerts

| | |
|---|---|
| **Owns** | Communication delivery |
| **Depends On** | Almost all modules, through events |

---

## 1️⃣7️⃣ Dependency Rules

| # | Rule | Example |
|---|---|---|
| 1️⃣ | **No circular dependencies** | 🚫 `Asset → Site` **and** `Site → Asset` |
| 2️⃣ | **No direct database access between modules** | 🚫 `AssetRepository` querying the `sites` table |
| 3️⃣ | **Cross-module communication uses** events · interfaces · contracts | — |
| 4️⃣ | **Higher intelligence modules depend on operational modules** | ✅ `Analytics → Operations` · 🚫 `Operations → Analytics` |

---

## 1️⃣8️⃣ Database Boundary Rule

Each module owns its schema objects.

| ✅ Owns | 🔗 Reference |
|---|---|
| Asset owns `energy_assets` | `siteId UUID` |
| Site owns `sites` | — |

> 🚫 **No foreign keys across modules.**

---

## 1️⃣9️⃣ Spring Modulith Package Direction

```
📦 com.powerpulse
 ├── 🪪 identity
 ├── 🌐 consumer
 ├── 🏢 organization
 ├── 🏠 household
 ├── 📍 site
 ├── 🔋 asset
 ├── ⚙️ operations
 ├── ⛽ fuel
 ├── 🔧 maintenance
 ├── 📡 monitoring
 ├── 📊 analytics
 ├── 💬 recommendation
 └── 🔔 notification
```

---

## 🏁 Final Dependency Decision

<div align="center">

> ### *PowerPulse modules are organized around business capabilities.*
> ### *The dependency direction follows the energy lifecycle.*

**The foundation is identity and consumer. The intelligence layer sits on top of trustworthy operational data. 🧭🌐**

</div>
