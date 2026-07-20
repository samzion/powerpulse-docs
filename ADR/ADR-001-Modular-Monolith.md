<div align="center">

# 🏛️ ADR-001: Modular Monolith Architecture

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

PowerPulse requires a software architecture capable of supporting:

- 🚀 Rapid MVP development
- 🧩 Strong domain boundaries
- 🏢 Independent business capabilities
- 📈 Future growth into distributed systems

**The system must avoid two common failures:**

1. 🔗 A tightly coupled monolith that becomes difficult to change
2. 🐙 Premature microservices that introduce operational complexity before scale requires them

---

## ⚖️ Decision

<div align="center">

> ### 🏛️ *PowerPulse will be built as a Modular Monolith*

</div>

**Using:** `☕ Java` · `🍃 Spring Boot` · `🧩 Spring Modulith` · `🐘 PostgreSQL` · `🔄 Liquibase`

---

## 💡 Why Modular Monolith

### 🧱 Strong Boundaries

Each business capability owns: 🧠 Domain logic · 🗄️ Data ownership · 🧵 Application services · 📦 Internal models

### 🚀 Simple Deployment

Initially: **one application · one deployment · one database instance**

### 🔮 Future Extraction Capability

Modules can later become services if required.

| Today | Future |
|---|---|
| `powerpulse application` └── ⛽ fuel module | 🚀 `Fuel Intelligence Service` |

---

## 🗂️ Module Structure

The system is organized around bounded contexts.

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

## 🧩 Module Responsibilities

| Module | Responsibility |
|---|---|
| 🪪 **Identity** | Authentication and authorization |
| 🌐 **Consumer** | Energy consumer lifecycle *(Business consumer · Household consumer)* |
| 🏢 **Organization** | Business-specific information |
| 🏠 **Household** | Residential-specific information |
| 📍 **Site** | Physical energy locations |
| 🔋 **Asset** | Energy equipment |
| ⚙️ **Operations** | Energy behaviour and measurements |
| ⛽ **Fuel** | Fuel intelligence |
| 🔧 **Maintenance** | Asset health |
| 📡 **Monitoring** | Operational observation |
| 📊 **Analytics** | Data transformation into knowledge |
| 💬 **Recommendation** | Decision support |
| 🔔 **Notification** | Communication delivery |

---

## 🔗 Dependency Rules

Modules communicate through:

### 📢 Domain Events

Preferred for: state changes · notifications · asynchronous reactions

```
📢 EnergyAssetRegistered
        ↓
⛽ Fuel module creates tracking
```

### 📞 Explicit Contracts

Used when an immediate response is required.

```
🔋 AssetApplicationService
        ↓
📍 SiteExistenceChecker
```

---

## 🚫 Forbidden Practices

| ⚠️ Violation | Example |
|---|---|
| **Cross-module database access** | ⛽ Fuel module querying asset tables directly |
| **Shared domain models** | 🔋 Asset importing 📍 Site entity |
| **Circular dependencies** | `Site → Asset` **and** `Asset → Site` |

---

## 📐 Package Boundary Rules

Spring Modulith will enforce:

```
📁 module.api
📁 module.application
📁 module.domain
📁 module.infrastructure
```

> 🔒 Each module controls its internal implementation.

---

## 🗄️ Database Ownership

Each module owns its tables.

| Module | Owns |
|---|---|
| 🌐 Consumer | `energy_consumers` |
| 🔋 Asset | `energy_assets` |
| 📍 Site | `sites` |

> 🔗 Cross-module relationships use **UUID references**, not database foreign keys.

---

## ✅ Consequences — Positive

- 🎯 Clear business ownership
- 🧪 Easier testing
- 🛠️ Easier maintenance
- 🚀 Future extraction path
- 🧠 Better alignment with DDD

## ⚠️ Consequences — Negative

- 🏗️ More initial structure
- 📐 Requires discipline
- 🧩 Developers must respect boundaries

---

## 🔀 Alternatives Considered

### ❌ Traditional Layered Monolith

**Rejected** — business boundaries become mixed.

### ❌ Microservices From Day One

**Rejected** — operational complexity exceeds current needs.

---

## 🏁 Final Decision

<div align="center">

> ### *PowerPulse will begin as a modular monolith.*

**The architecture prioritizes domain clarity over deployment complexity.**

The system is designed to evolve from a single application into an energy intelligence platform. 🏛️🌐

</div>
