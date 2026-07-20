<div align="center">

# 📡 PowerPulse Event Catalog

### *The domain events used throughout PowerPulse*

![Events](https://img.shields.io/badge/document-event%20catalog-e67e22?style=for-the-badge)
![Count](https://img.shields.io/badge/events-16-blue?style=for-the-badge)
![Stages](https://img.shields.io/badge/stages-2%20documented-9b59b6?style=for-the-badge)

</div>

---

## 🎯 Purpose

This document defines the domain events used throughout PowerPulse.

Events represent **business facts that have already happened**. They are not commands — they do not request actions. They communicate changes in business reality between bounded contexts.

| 📤 Command | 📢 Resulting Event |
|---|---|
| `RegisterOrganization` | `OrganizationRegistered` |

> 💬 The event states: *"This business fact has occurred."*

---

## 🧭 Event Design Principles

### 1️⃣ Events Are Past Tense

| ✅ Good | ❌ Bad |
|---|---|
| `OrganizationRegistered` | `RegisterOrganization` |
| `SiteCreated` | `CreateSite` |
| `EnergyAssetRegistered` | — |
| `FuelConsumed` | `ConsumeFuel` |

> 📤 Commands **request**. 📢 Events **confirm**.

### 2️⃣ Events Are Immutable

Once published, an event **cannot change**. If the meaning changes, create a **new event version**.

### 3️⃣ Events Belong to the Publishing Module

The module that owns the business rule owns the event.

> Example: the **Organization module** owns `OrganizationRegistered` — other modules only **consume** it.

### 4️⃣ Events Carry Business Facts

Events should contain information needed by consumers — they should **not** expose internal implementation details.

---

## 🏗️ Stage 1 Events — Foundation

Stage 1 establishes the identity and ownership foundation.

**Modules:** 🪪 Identity · 🏢 Organization · 📍 Site

### 1️⃣ 📢 `UserRegistered`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🪪 Identity Module |
| **Purpose** | Indicates that a new PowerPulse user account has been created |
| **Payload** | `eventId` · `userId` · `email` · `registeredAt` |
| **Consumers** | 🔔 Notification Module *(future)* · 📜 Audit Module *(future)* |

---

### 2️⃣ 📢 `UserActivated`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🪪 Identity Module |
| **Purpose** | Indicates that a user account is active and can access the platform |
| **Payload** | `eventId` · `userId` · `activatedAt` |
| **Consumers** | 🔔 Notification Module *(future)* |

---

### 3️⃣ 📢 `OrganizationRegistered`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🏢 Organization Module |
| **Purpose** | Indicates that a new business organization has joined PowerPulse |
| **Payload** | `eventId` · `organizationId` · `organizationName` · `registeredByUserId` · `registeredAt` |

**📥 Consumers**
- 📍 **Site Module** — a site may later be created under this organization
- 🔔 **Notification Module** — welcome communication
- 📜 **Audit Module** — track business lifecycle

---

### 4️⃣ 📢 `OrganizationVerified`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🏢 Organization Module |
| **Purpose** | Indicates that organization verification requirements have been completed |
| **Payload** | `eventId` · `organizationId` · `verificationType` · `verifiedAt` |
| **Consumers** | 🔐 Access Control Module *(future)* · 🧾 Billing Module *(future)* |

---

### 5️⃣ 📢 `OrganizationSuspended`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🏢 Organization Module |
| **Purpose** | Indicates that an organization is temporarily restricted |
| **Payload** | `eventId` · `organizationId` · `reason` · `suspendedAt` |
| **Consumers** | 📍 Site Module · 🔔 Notification Module |

---

### 6️⃣ 📢 `SiteCreated`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 📍 Site Module |
| **Purpose** | Indicates that a physical operating location has been created |
| **Payload** | `eventId` · `siteId` · `organizationId` · `siteName` · `createdAt` |

**📥 Consumers**
- 🔋 **Energy Asset Module** — assets can only exist inside sites
- 🔔 **Notification Module** — operational updates

---

### 7️⃣ 📢 `SiteUpdated`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 📍 Site Module |
| **Payload** | `eventId` · `siteId` · `updatedAt` |
| **Consumers** | 📊 Analytics Module *(future)* · 📜 Audit Module *(future)* |

---

## ⚡ Stage 2 Events — Energy Reality

Stage 2 introduces the physical energy world.

**Modules:** 🔋 Energy Asset · ⚙️ Energy Operations · ⛽ Fuel · 🔧 Maintenance

### 8️⃣ 📢 `EnergyAssetRegistered`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🔋 Energy Asset Module |
| **Purpose** | Indicates that a physical energy asset has been registered *(generator, inverter, battery, solar)* |
| **Payload** | `eventId` · `assetId` · `siteId` · `assetType` · `registeredAt` |

**📥 Consumers**
- ⚙️ **Energy Operations Module** — operations can now reference the asset
- 🔧 **Maintenance Module** — asset becomes maintainable
- 📡 **Monitoring Module** — asset can be observed

---

### 9️⃣ 📢 `EnergyAssetUpdated`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🔋 Energy Asset Module |
| **Payload** | `eventId` · `assetId` · `changedFields` · `updatedAt` |
| **Consumers** | 📊 **Analytics Module** — historical analysis may require updated asset information |

---

### 🔟 📢 `EnergyAssetRetired`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🔋 Energy Asset Module |
| **Payload** | `eventId` · `assetId` · `retiredAt` · `reason` |
| **Consumers** | ⚙️ Operations Module · 🔧 Maintenance Module · 📡 Monitoring Module |

---

### 1️⃣1️⃣ 📢 `EnergyUsageRecorded` ⭐

| 🧩 Field | Detail |
|---|---|
| **Publisher** | ⚙️ Energy Operations Module |
| **Purpose** | Indicates that energy usage has been captured — **one of the most important events in PowerPulse** |
| **Payload** | `eventId` · `operationId` · `assetId` · `siteId` · `energyConsumed` · `duration` · `recordedAt` |

**📥 Consumers**
- ⛽ **Fuel Module** — generator operations may require fuel tracking
- 📊 **Analytics Module** — usage contributes to reports
- 📡 **Monitoring Module** — detect abnormal behaviour
- 💬 **Recommendation Module** — generate improvement opportunities

---

### 1️⃣2️⃣ 📢 `EnergyOperationCompleted`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | ⚙️ Energy Operations Module |
| **Payload** | `eventId` · `operationId` · `completedAt` · `totalEnergyConsumed` · `totalCost` |
| **Consumers** | 📊 Analytics Module |

---

### 1️⃣3️⃣ 📢 `FuelAdded`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | ⛽ Fuel Module |
| **Purpose** | Indicates that fuel inventory increased |
| **Payload** | `eventId` · `fuelInventoryId` · `assetId` · `quantity` · `cost` · `addedAt` |
| **Consumers** | 📊 Analytics Module |

---

### 1️⃣4️⃣ 📢 `FuelConsumed`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | ⛽ Fuel Module |
| **Purpose** | Indicates fuel has been consumed |
| **Payload** | `eventId` · `fuelInventoryId` · `assetId` · `quantityConsumed` · `cost` · `consumedAt` |

**📥 Consumers**
- 📊 **Analytics Module** — calculate energy cost
- 💬 **Recommendation Module** — identify fuel efficiency opportunities

---

### 1️⃣5️⃣ 📢 `MaintenanceScheduled`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🔧 Maintenance Module |
| **Payload** | `eventId` · `maintenanceId` · `assetId` · `scheduledDate` |
| **Consumers** | 🔔 Notification Module |

---

### 1️⃣6️⃣ 📢 `MaintenanceCompleted`

| 🧩 Field | Detail |
|---|---|
| **Publisher** | 🔧 Maintenance Module |
| **Payload** | `eventId` · `maintenanceId` · `assetId` · `completedAt` · `cost` |

**📥 Consumers**
- 🔋 **Asset Module** — update asset history
- 📊 **Analytics Module** — maintenance analysis

---

## 🗺️ Event Flow Overview

```
🪪 Identity
    └── 📢 UserRegistered

🏢 Organization
    └── 📢 OrganizationRegistered
          └── 📢 SiteCreated
                └── 📢 EnergyAssetRegistered
                      └── 📢 EnergyUsageRecorded
                            ├── ⛽ Fuel
                            └── 📊 Analytics
                                  └── 💬 Recommendations
```

---

## 🏷️ Event Naming Convention

**Format:** `<Entity><PastTenseAction>`

```
✅ OrganizationRegistered
✅ SiteCreated
✅ EnergyAssetRegistered
✅ FuelConsumed
✅ MaintenanceCompleted
```

---

## 🚫 Events Not Yet Allowed

The following must **not** be created until sufficient operational data exists:

| 🔒 Locked Event |
|---|
| `EnergyPredictionGenerated` |
| `AIOptimizationSuggested` |
| `AutomaticEnergyDecisionMade` |
| `DigitalTwinUpdated` |

> 🧠 Reason: Intelligence must be **earned** through real operational history.

---

## 🏁 Final Event Principle

<div align="center">

> ### *Events are the memory of PowerPulse.*
> ### *The quality of future intelligence depends on the quality of facts captured today.*

### 📥 Capture reality first. 🧠 Understand reality second. 🎯 Optimize reality later.

</div>
