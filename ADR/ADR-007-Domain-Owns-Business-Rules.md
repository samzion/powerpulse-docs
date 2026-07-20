<div align="center">

# ⚖️ ADR-007: Domain Owns Business Rules

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

PowerPulse is an energy intelligence platform. The system is not only responsible for storing information about: 🌐 Energy consumers · 📍 Sites · 🔋 Assets · ⛽ Fuel · ⚙️ Operations · 🔧 Maintenance — it must also **enforce real-world business behaviour**.

**Examples:**
- A generator cannot be considered operational before registration
- An energy asset cannot move through invalid lifecycle states
- Fuel consumption must follow domain rules
- Recommendations must be based on meaningful operational conditions
- Energy calculations must represent real business concepts

> ⚠️ A common failure in software systems is placing business rules inside controllers, application services, database repositories, or utility classes — this creates a system where the business logic becomes scattered and difficult to maintain.

---

## ⚖️ Decision

<div align="center">

> ### 🧭 *"Business rules belong in the domain model."*

</div>

**The domain layer owns:** ⚖️ Business invariants · 🔄 State transitions · 🧮 Domain calculations · 🧠 Business decisions · 📜 Core policies

Application services **coordinate** use cases. Infrastructure handles **technical concerns**.

---

## 🏗️ Layer Responsibilities

```
🖥️  Presentation Layer
        ↓
🧵  Application Layer
        ↓
🧠  Domain Layer
        ↓
🗄️  Infrastructure Layer
```

### 🖥️ Presentation Layer

**Responsible for:** HTTP requests · request validation · response formatting · authentication boundaries

> 🚫 `EnergyAssetController` should not contain `calculateFuelConsumption()`

### 🧵 Application Layer

**Responsible for:** Orchestration · transaction boundaries · calling domain behaviour · coordinating workflows

`RegisterEnergyAssetUseCase` may: 1️⃣ Receive command → 2️⃣ Create aggregate → 3️⃣ Persist aggregate → 4️⃣ Publish event

> 🚫 It does **not** decide business rules.

### 🧠 Domain Layer

**Responsible for:** Business meaning · rules · invariants · behaviour

> `EnergyAsset` owns its lifecycle transitions, valid states, and asset rules.

### 🗄️ Infrastructure Layer

**Responsible for:** Database · external systems · messaging · file storage

> 🚫 Infrastructure should not contain business decisions.

---

## 🔍 Example: Energy Asset Lifecycle

### ❌ Incorrect

```java
@Service
public class AssetService {

    public void activateAsset(EnergyAsset asset) {
        if (asset.getStatus() == REGISTERED) {
            asset.setStatus(ACTIVE);
        }
    }

}
```

> ⚠️ The **service** now owns the rule.

### ✅ Correct

```java
public class EnergyAsset {

    public void activate() {
        if (status != REGISTERED) {
            throw new InvalidAssetStateException();
        }
        status = ACTIVE;
    }

}
```

> ✅ The **aggregate** protects itself.

---

## 🧱 Aggregate Responsibilities

Aggregates protect business consistency.

| 🧱 Aggregate | Rules |
|---|---|
| 🌐 **EnergyConsumer** | Consumer must have a valid type · cannot activate before registration · status transitions must be valid |
| 📍 **Site** | Requires a valid consumer reference · lifecycle must be controlled |
| 🔋 **EnergyAsset** | Asset type is required · lifecycle transitions are controlled · retired assets cannot operate |

---

## 🛠️ Domain Services

Not every rule belongs to an entity. A domain service is used when:
- The logic is business-specific
- It does not naturally belong to one entity
- It coordinates domain concepts

**Examples:** `EnergyCostCalculator` · `FuelConsumptionEstimator` · `EnergyEfficiencyAnalyzer`

| ❌ Incorrect | ✅ Correct |
|---|---|
| `EnergyUsageController.calculateCost()` | `EnergyCostCalculator` inside domain |
| `EnergyUsageRepository.calculateCost()` | — |

---

## 💎 Value Objects

Business concepts should become explicit types.

| ❌ Instead of | ✅ Prefer |
|---|---|
| `BigDecimal fuelAmount` | `FuelQuantity` |
| `BigDecimal amount` | `Money` |

> 🔒 Value objects protect meaning.

---

## 📢 Domain Events

When important business actions occur, the domain publishes events:

```
📢 EnergyConsumerRegistered
📢 SiteCreated
📢 EnergyAssetRegistered
📢 FuelConsumed
📢 MaintenanceCompleted
```

> 📜 Events communicate business facts.

---

## 🚫 Anti-Patterns Forbidden

| ⚠️ Anti-Pattern | Description |
|---|---|
| 🎛️ **Fat Controllers** | Controller + validation + calculation + business rules + persistence |
| 🫥 **Anemic Domain Model** | `class EnergyAsset { UUID id; String status; }` with all behaviour elsewhere |
| 👹 **Service Layer God Objects** | `PowerPulseService` handling consumer, assets, fuel, analytics, billing |

All **rejected**.

---

## 🧪 Testing Implications

Because rules live in the domain, domain tests can run **without**: 🗄️ Database · 🍃 Spring context · 🌐 HTTP layer

```java
// EnergyAssetTest tests: activation rules · retirement rules · lifecycle behaviour
```

> ⚡ This produces faster and more reliable tests.

---

## ✅ Consequences — Positive

- 🎯 Clear business ownership
- 🧪 Easier testing
- 🛠️ Better maintainability
- 🧠 Stronger alignment with DDD
- 🛡️ Safer evolution

## ⚠️ Consequences — Negative

Developers need discipline. Simple CRUD thinking is insufficient — the team must understand: 🧱 Aggregates · 📦 Entities · 💎 Value Objects · 🛠️ Domain Services

---

## 🔀 Alternatives Considered

### ❌ Business Logic in Services

**Rejected** — creates procedural code and weak domain models.

### ❌ Database Stored Procedures

**Rejected** — business rules become coupled to persistence technology.

### ❌ Controller-Based Logic

**Rejected** — the presentation layer should not own business decisions.

---

## 🏁 Final Decision

<div align="center">

> ### *PowerPulse domain models own business rules.*
> ### *The application layer coordinates.*
> ### *The infrastructure layer supports.*
> ### *The domain layer decides.*

This ensures the software model reflects the real energy business instead of becoming a collection of database operations. ⚖️🧠

</div>
