<div align="center">

# 🍰 ADR-009: Vertical Slice Development

![Status](https://img.shields.io/badge/status-Accepted-success?style=for-the-badge)
![Date](https://img.shields.io/badge/date-2026--07--20-blue?style=for-the-badge)

</div>

---

## 📋 Status

✅ **Accepted**

## 📅 Date

`2026-07-20`

---

## 🧭 Context

PowerPulse is a modular monolith built using: `☕ Java 17` · `🍃 Spring Boot` · `🧩 Spring Modulith` · `🐘 PostgreSQL` · `🔄 Liquibase`

The platform consists of multiple bounded contexts: `🪪 Identity` · `🌐 Energy Consumer` · `🏢 Organization Profile` · `🏠 Household Profile` · `📍 Site` · `🔋 Energy Asset` · `⚙️ Energy Operations` · `⛽ Fuel` · `🔧 Maintenance` · `📡 Monitoring` · `📊 Analytics` · `💬 Recommendation` · `🔔 Notification`

> ⚠️ A common mistake in software projects is building systems **horizontally**:

```
📦 Build all entities
      ↓
🗄️ Build all repositories
      ↓
🧵 Build all services
      ↓
🎛️ Build all controllers
      ↓
🤞 Finally make everything work
```

This creates large amounts of unfinished code. Features cannot be demonstrated until very late in the project. Business feedback arrives too late. Integration problems accumulate.

---

## ⚖️ Decision

<div align="center">

> ### 🍰 *PowerPulse will be developed using Vertical Slice Development.*

</div>

Each slice delivers **one complete business capability**. A slice is complete only when it includes everything required for that capability.

---

## 🍰 What Is a Vertical Slice?

A vertical slice includes every layer necessary to deliver one business use case:

```
🌐 HTTP API
     ↓
🧵 Application
     ↓
🧠 Domain
     ↓
🗄️ Persistence
     ↓
🐘 Database
     ↓
📢 Events
     ↓
🧪 Tests
```

> ✅ The slice is deployable and testable.

---

## ✅ Slice Completeness

A slice is considered complete only when it includes:

`🧠 Domain model` · `📥 Commands` · `🔍 Queries` · `🧵 Application services` · `🗄️ Repository` · `🔄 Liquibase migration` · `🌐 REST API` · `✅ Validation` · `📢 Domain events` · `🧪 Unit tests` · `🔗 Integration tests` · `📄 Documentation`

> 🚫 Nothing is left "for later."

---

## 🏗️ Build Order

PowerPulse follows the implementation sequence defined by **ADR-008**. Within that sequence, each module is completed vertically before moving to the next:

```
🪪 Identity
   └── 🌐 Energy Consumer
         └── 🏢 Organization Profile
               └── 🏠 Household Profile
                     └── 📍 Site
                           └── 🔋 Energy Asset
                                 └── ⚙️ Energy Operations
                                       └── ⛽ Fuel
                                             └── 🔧 Maintenance
                                                   └── 📡 Monitoring
                                                         └── 📊 Analytics
                                                               └── 💬 Recommendation
                                                                     └── 🔔 Notification
```

> ✅ Each module is finished before the next begins.

---

## 🌐 Example: Energy Consumer Slice

The Energy Consumer slice is complete only after the following exist:

```
🧱 EnergyConsumer Aggregate
      ↓
🗄️ Repository
      ↓
🔄 Liquibase Migration
      ↓
🧵 Application Service
      ↓
🎛️ REST Controller
      ↓
📥 Request DTOs
      ↓
📤 Response DTOs
      ↓
✅ Validation
      ↓
📢 Domain Events
      ↓
🧪 Unit Tests
      ↓
🔗 Integration Tests
      ↓
📄 Documentation
```

> 🎉 At that point, a user can register an Energy Consumer — **the feature is production-ready.** Only then does development move to the next slice.

---

## 💡 Why Vertical Slices?

### 1️⃣ Deliver Working Software Earlier

Every completed slice produces usable functionality. Progress is measurable by **business capability**, not file count.

### 2️⃣ Faster Feedback

Stakeholders can validate features as they are completed — incorrect assumptions are discovered early.

### 3️⃣ Better Quality

Because each slice is fully integrated: 🌐 APIs are exercised · 🔄 database migrations are verified · ⚖️ domain rules are tested · 📢 events are validated.

> 🔍 Problems are found immediately.

### 4️⃣ Reduced Technical Debt

No incomplete layers accumulate — the codebase remains **deployable throughout development**.

---

## 🧩 Relationship with Spring Modulith

Each bounded context is implemented as a Spring Modulith module:

```
🧠 Domain
   └── 🧵 Application
         └── 🗄️ Infrastructure
               └── 🌐 API
```

> 🔒 The module exposes only what other modules need — internal implementation remains encapsulated.

---

## 🧠 Relationship with Domain-Driven Design

Vertical slices **complement** DDD.

| DDD Defines | Vertical Slices Define |
|---|---|
| Business boundaries · aggregates · domain language | Implementation order · delivery strategy |

| Question | Answered By |
|---|---|
| *"What should we build?"* | 🧠 DDD |
| *"In what order should we build it?"* | 🍰 Vertical Slices |

---

## 🧪 Testing Strategy

Every slice must include:

| Test Type | Verifies |
|---|---|
| 🧠 **Domain Tests** | Business rules |
| 🧵 **Application Tests** | Use cases |
| 🔗 **Integration Tests** | Persistence · Liquibase · REST endpoints |
| 🧩 **Module Verification** | Spring Modulith verification ensures module boundaries remain intact |

> 🚫 A slice is not complete without passing all tests.

---

## ✅ Definition of Done

A PowerPulse slice is complete only when:

- 🧠 Domain model is implemented
- ⚖️ Business rules are enforced
- 🔄 Liquibase migration exists
- 🗄️ Database schema is verified
- 🌐 REST endpoints are operational
- 📢 Events are published correctly
- 🧪 Tests pass
- 📄 Documentation is updated

> ⚠️ If any item is missing, the slice is **incomplete**.

---

## 🚫 Anti-Patterns

| ⚠️ Anti-Pattern | Description |
|---|---|
| ↔️ **Horizontal Development** | All Entities → All Repositories → All Controllers → All Services |
| 🗄️ **Database-First Development** | Building tables before understanding the domain |
| 🌐 **API-First Without Domain** | Creating endpoints before business behaviour exists |
| 🤖 **Intelligence-First Development** | Building forecasting/optimization before operational capabilities exist *(see ADR-008)* |

All **rejected**.

---

## ✅ Consequences — Positive

- 🔄 Continuous delivery
- 📣 Faster stakeholder feedback
- 📉 Lower integration risk
- 🧠 Better code quality
- 🎯 Clear implementation milestones
- 🛠️ Easier project management

## ⚠️ Consequences — Negative

Each slice requires discipline — developers must resist the temptation to "stub" future functionality. **Features are finished before new features begin.**

---

## 🔗 Relationship to Other ADRs

This ADR builds on:

- 🏛️ **ADR-001** — Modular Monolith Architecture
- 🧠 **ADR-002** — Domain-Driven Design
- 🗄️ **ADR-003** — Database Ownership
- 🆔 **ADR-005** — UUID Identifiers
- 🔗🚫 **ADR-006** — No Cross-Module Foreign Keys
- ⚖️ **ADR-007** — Domain Owns Business Rules
- 📊 **ADR-008** — Data Before Intelligence

> 🧩 Together these ADRs define both the architecture and the delivery strategy for PowerPulse.

---

## 🏁 Final Decision

<div align="center">

> ### *PowerPulse will be implemented one complete business capability at a time.*
>
> ### *Every slice includes its domain model, persistence, API, events, tests, and documentation.*

**Working software takes precedence over partially completed frameworks.**

The project progresses through finished vertical slices rather than unfinished horizontal layers. 🍰🚀

</div>
