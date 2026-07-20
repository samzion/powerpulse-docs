<div align="center">

# 📡 PowerPulse Event Catalog

### *v2.0 — Domain Events After Energy Consumer Abstraction*

![Events](https://img.shields.io/badge/document-event%20catalog-e67e22?style=for-the-badge)
![Version](https://img.shields.io/badge/version-2.0-red?style=for-the-badge)
![Count](https://img.shields.io/badge/events-20-blue?style=for-the-badge)

</div>

---

## 1️⃣ Purpose

This document defines the domain events within PowerPulse.

**Domain events represent:**
- 💥 Something important happened in the business domain
- 🔄 A state transition occurred
- 📡 Other modules may react

> 🚫 Events are **not** database notifications. **They represent business meaning.**

---

## 2️⃣ Event Design Principles

| # | Rule |
|---|---|
| 1️⃣ | Events are named in **past tense** — `EnergyConsumerRegistered`, not `RegisterEnergyConsumer` |
| 2️⃣ | The **publishing module** owns the event meaning; consumers only react |
| 3️⃣ | Events **reduce coupling** — a module does not need to know who listens |
| 4️⃣ | Events contain **business facts**, not database details |

---

## 3️⃣ Event Flow Overview

```
🪪 UserRegistered
    └── 🌐 EnergyConsumerRegistered
          └── 🏢/🏠 ProfileCreated
                └── 📍 SiteCreated
                      └── 🔋 EnergyAssetRegistered
                            └── ⚙️ EnergyOperationRecorded
                                  └── ⛽ FuelConsumed
                                        └── 📊 AnalyticsGenerated
                                              └── 💬 RecommendationGenerated
```

---

## 4️⃣ 🪪 Identity Events

### 📢 `UserRegistered`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🪪 Identity Module |
| **Meaning** | A new user account has been created |
| **Payload** | `userId` UUID · `email` · `registeredAt` |
| **Consumers** | 🌐 Consumer Module · 🔔 Notification Module |

---

## 5️⃣ 🌐 Consumer Events

### 📢 `EnergyConsumerRegistered`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🌐 Consumer Module |
| **Meaning** | A new energy consumer has entered PowerPulse |
| **Payload** | `consumerId` UUID · `consumerType` · `createdAt` |
| **Consumers** | 🏢 Organization Profile Module · 🏠 Household Profile Module · 🔔 Notification Module |

**Consumer Types:** ✅ Initial: `BUSINESS` · `HOUSEHOLD` — 🔮 Future: `INSTITUTION` · `COMMUNITY`

---

### 📢 `EnergyConsumerActivated`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🌐 Consumer Module |
| **Meaning** | Consumer is now active and can operate |
| **Payload** | `consumerId` UUID · `activatedAt` |
| **Consumers** | 📍 Site Module · 🧾 Billing Module · 🔔 Notification Module |

---

### 📢 `EnergyConsumerSuspended`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🌐 Consumer Module |
| **Meaning** | Consumer access has been suspended |
| **Payload** | `consumerId` UUID · `reason` · `suspendedAt` |
| **Consumers** | 📍 Site Module · 🧾 Billing Module |

---

## 6️⃣ 🏢 Organization Profile Events

### 📢 `OrganizationProfileCreated`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🏢 Organization Profile Module |
| **Meaning** | Business information has been created |
| **Payload** | `organizationId` UUID · `consumerId` UUID · `businessType` · `createdAt` |
| **Consumers** | 📍 Site Module · 📊 Analytics Module |

### 📢 `OrganizationProfileUpdated`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🏢 Organization Profile Module |
| **Consumers** | 📊 Analytics Module |

---

## 7️⃣ 🏠 Household Profile Events

### 📢 `HouseholdProfileCreated`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🏠 Household Profile Module |
| **Meaning** | Residential profile created |
| **Payload** | `householdId` UUID · `consumerId` UUID · `createdAt` |
| **Consumers** | 📍 Site Module · 📊 Analytics Module |

### 📢 `HouseholdProfileUpdated`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🏠 Household Profile Module |
| **Consumers** | 📊 Analytics Module |

---

## 8️⃣ 📍 Site Events

### 📢 `SiteCreated`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 📍 Site Module |
| **Meaning** | A physical energy location exists |
| **Payload** | `siteId` UUID · `consumerId` UUID · `createdAt` |
| **Consumers** | 🔋 Energy Asset Module · 📊 Analytics Module |

### 📢 `SiteActivated`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 📍 Site Module |
| **Consumers** | 🔋 Asset Module · 📡 Monitoring Module |

### 📢 `SiteDeactivated`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 📍 Site Module |
| **Consumers** | 🔋 Asset Module · 📡 Monitoring Module |

---

## 9️⃣ 🔋 Energy Asset Events

### 📢 `EnergyAssetRegistered`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🔋 Energy Asset Module |
| **Meaning** | A physical energy asset has been registered |
| **Payload** | `assetId` UUID · `siteId` UUID · `assetType` · `registeredAt` |
| **Consumers** | ⚙️ Operations Module · ⛽ Fuel Module · 🔧 Maintenance Module · 📡 Monitoring Module |

### 📢 `EnergyAssetActivated`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🔋 Energy Asset Module |
| **Meaning** | Asset is operational |
| **Consumers** | ⚙️ Operations Module · 📡 Monitoring Module |

### 📢 `EnergyAssetRetired`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🔋 Energy Asset Module |
| **Consumers** | ⚙️ Operations Module · 🔧 Maintenance Module · 📊 Analytics Module |

---

## 🔟 ⚙️ Energy Operation Events

### 📢 `EnergyOperationRecorded`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | ⚙️ Energy Operations Module |
| **Meaning** | An energy activity occurred |
| **Payload** | `operationId` UUID · `assetId` UUID · `operationType` · `energyConsumed` · `recordedAt` |
| **Consumers** | 📊 Analytics Module · ⛽ Fuel Module · 📡 Monitoring Module |

---

## 1️⃣1️⃣ ⛽ Fuel Events

### 📢 `FuelAdded`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | ⛽ Fuel Module |
| **Meaning** | Fuel inventory increased |
| **Payload** | `inventoryId` UUID · `quantity` · `fuelType` · `timestamp` |
| **Consumers** | 📊 Analytics Module |

### 📢 `FuelConsumed`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | ⛽ Fuel Module |
| **Meaning** | Fuel was used |
| **Payload** | `assetId` UUID · `quantity` · `timestamp` |
| **Consumers** | 📊 Analytics Module · 💬 Recommendation Module |

### 📢 `FuelLevelLow`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | ⛽ Fuel Module |
| **Meaning** | Fuel requires attention |
| **Consumers** | 🔔 Notification Module · 💬 Recommendation Module |

---

## 1️⃣2️⃣ 🔧 Maintenance Events

### 📢 `MaintenanceScheduled`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🔧 Maintenance Module |
| **Consumers** | 🔔 Notification Module · 🔋 Asset Module |

### 📢 `MaintenanceCompleted`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🔧 Maintenance Module |
| **Consumers** | 📊 Analytics Module · 🔋 Asset Module |

---

## 1️⃣3️⃣ 📡 Monitoring Events

### 📢 `AlertTriggered`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 📡 Monitoring Module |
| **Payload** | `alertId` · `assetId` · `severity` · `message` |
| **Consumers** | 🔔 Notification Module · 💬 Recommendation Module |

### 📢 `AlertResolved`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 📡 Monitoring Module |
| **Consumers** | 📊 Analytics Module |

---

## 1️⃣4️⃣ 📊 Analytics Events

### 📢 `AnalyticsGenerated`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 📊 Analytics Module |
| **Meaning** | Operational data has been transformed into insight |
| **Consumers** | 💬 Recommendation Module · 🔔 Notification Module |

---

## 1️⃣5️⃣ 💬 Recommendation Events

### 📢 `RecommendationGenerated`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 💬 Recommendation Module |
| **Meaning** | The system produced actionable guidance |
| **Payload** | `recommendationId` · `consumerId` · `category` · `priority` |
| **Consumers** | 🔔 Notification Module · 🖥️ User Interface |

---

## 1️⃣6️⃣ Event Ownership Map

| 📢 Event | 📤 Publisher | 📥 Consumers |
|---|---|---|
| `UserRegistered` | Identity | Consumer, Notification |
| `EnergyConsumerRegistered` | Consumer | Profiles, Notification |
| `OrganizationProfileCreated` | Organization | Site, Analytics |
| `HouseholdProfileCreated` | Household | Site, Analytics |
| `SiteCreated` | Site | Asset, Analytics |
| `EnergyAssetRegistered` | Asset | Operations, Fuel, Maintenance |
| `EnergyOperationRecorded` | Operations | Analytics, Fuel |
| `FuelConsumed` | Fuel | Analytics, Recommendation |
| `MaintenanceCompleted` | Maintenance | Analytics |
| `AlertTriggered` | Monitoring | Notification |
| `RecommendationGenerated` | Recommendation | Notification |

---

## 1️⃣7️⃣ Event Evolution Strategy

> 📜 Events are **contracts**. Changes must be **backward compatible**.

**Breaking changes require:** 🆕 New event version · 🔄 Migration strategy · 🔧 Consumer updates

---

## 🏁 Final Event Decision

<div align="center">

> ### *PowerPulse uses domain events to preserve module independence.*
> ### *Events describe business reality.*

**They allow PowerPulse to evolve from a modular monolith into a distributed energy intelligence platform when required. 📡🌐**

</div>
