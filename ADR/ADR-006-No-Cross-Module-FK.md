<div align="center">

# 🔗🚫 ADR-006: No Cross-Module Foreign Keys

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

PowerPulse is designed as a modular monolith using: `☕ Java` · `🍃 Spring Boot` · `🧩 Spring Modulith` · `🐘 PostgreSQL` · `🔄 Liquibase`

The system contains multiple bounded contexts: `🪪 Identity` · `🌐 Energy Consumer` · `🏢 Organization Profile` · `🏠 Household Profile` · `📍 Site` · `🔋 Energy Asset` · `⚙️ Energy Operations` · `⛽ Fuel` · `🔧 Maintenance` · `📡 Monitoring` · `📊 Analytics` · `💬 Recommendation` · `🔔 Notification`

Although these modules run in one application and currently share one PostgreSQL instance, they represent **independent business capabilities**.

> ⚠️ A common architectural mistake in modular monoliths is allowing all modules to directly reference each other's database tables through foreign keys — this creates hidden coupling.

```
🔋 Asset Module
      FK
      ↓
📍 Site Module tables
```

Over time, database relationships become stronger than the business boundaries.

---

## ⚖️ Decision

<div align="center">

> ### 🔗🚫 *PowerPulse will not use database foreign keys across module boundaries.*

</div>

Each module owns its own tables. Relationships between modules are represented using: 🆔 UUID references · 📢 Domain events · ✅ Application-level validation

---

## 🗄️ Database Ownership Rule

Every module owns its persistence model:

| Module | Owns |
|---|---|
| 🌐 Consumer | `energy_consumers` |
| 🏢 Organization | `organization_profiles` |
| 🏠 Household | `household_profiles` |
| 📍 Site | `sites` |
| 🔋 Asset | `energy_assets` |
| ⚙️ Operations | `energy_operations` |

The owning module is responsible for: 📐 Schema changes · 🔄 Migration scripts · 🔒 Data integrity rules · 🎭 Domain behaviour

---

## ✅ Example: Correct Relationship

A Site belongs to an Energy Consumer:

```sql
CREATE TABLE sites (
    id           UUID PRIMARY KEY,
    consumer_id  UUID NOT NULL,
    name         VARCHAR(255) NOT NULL,
    created_at   TIMESTAMP NOT NULL
);
```

The relationship is represented by `consumer_id UUID`. The database does **not** contain:

```sql
FOREIGN KEY (consumer_id)
REFERENCES energy_consumers(id)
```

---

## 💡 Why No Cross-Module Foreign Keys?

### 1️⃣ Preserve Bounded Context Boundaries

A module should own its internal model. Without cross-module foreign keys, 🌐 Consumer Module (`energy_consumers`) and 📍 Site Module (`sites`) remain independent.

### 2️⃣ Prevent Database Coupling

| With Foreign Keys | Without Them |
|---|---|
| `Asset migration` depends on `Site migration` | 🔋 Asset Module can evolve independently |

> ⚠️ A small change in one module can break another.

### 3️⃣ Enable Future Service Extraction

| Today | Future |
|---|---|
| `PowerPulse Application` — 🌐 Consumer · 📍 Site · 🔋 Asset Modules | 🚀 Consumer Service · 🚀 Site Service · 🚀 Asset Service |

> 🧩 The database boundary already exists — the extraction path is cleaner.

### 4️⃣ Support Independent Deployment Evolution

Future modules may have: 🗄️ different storage strategies · 📈 different scaling requirements · 👥 different ownership teams.

> 🚫 Cross-module foreign keys prevent this.

---

## 🔒 Maintaining Integrity Without Foreign Keys

> ℹ️ Removing database foreign keys does not mean removing integrity — it moves to the appropriate layer.

| Method | Example |
|---|---|
| ✅ **Application Validation** | Before creating a Site: `Site Application Service` checks `Consumer exists` |
| ⚖️ **Domain Rules** | A Site cannot exist without a valid Energy Consumer reference — the rule belongs to the Site domain |
| 📢 **Domain Events** | `EnergyConsumerRegistered` → Site module becomes aware |
| 🧪 **Automated Tests** | Module contracts verify valid references, event flows, and boundary rules |

---

## 🗂️ Liquibase Structure

Each module owns its migrations:

```
📁 db/changelog
 ├── 🪪 identity
 │     └── 001-create-users.xml
 ├── 🌐 consumer
 │     └── 001-create-energy-consumers.xml
 ├── 📍 site
 │     └── 001-create-sites.xml
 └── 🔋 asset
       └── 001-create-energy-assets.xml
```

---

## ☕ JPA Implementation Rule

Entities must **not** create cross-module object relationships.

**❌ Avoid** (inside `Site`):

```java
@ManyToOne
private EnergyConsumer consumer;
```

**✅ Prefer:**

```java
@Entity
public class Site {

    @Id
    private UUID id;

    private UUID consumerId;

}
```

> 🏠 The module owns its model.

---

## 📢 Event Communication Example

| ❌ Instead of | ✅ Use |
|---|---|
| Site Module queries Consumer database table | Consumer Module publishes `EnergyConsumerRegistered` → Site Module reacts |

---

## 🔀 Alternatives Considered

### ❌ Foreign Keys Everywhere

**Rejected** — creates database-level coupling between bounded contexts.

### ❌ Separate Database Per Module Immediately

**Rejected** — operational complexity is unnecessary at the current stage.

### ❌ Shared Domain Database Model

**Rejected** — violates DDD boundaries.

---

## ✅ Consequences — Positive

- 🧩 Strong module independence
- 🎯 Cleaner DDD boundaries
- 🛠️ Easier schema evolution
- 🚀 Future microservice readiness

## ⚠️ Consequences — Negative

> 🔓 The application must handle some integrity checks previously handled by PostgreSQL — this requires discipline.

---

## 🏁 Final Decision

<div align="center">

> ### *PowerPulse modules own their own data.*

Cross-module relationships use: **🆔 UUID references + 📢 Domain events + ✅ Application validation**

Database foreign keys are restricted to relationships **inside the same bounded context only**.

This keeps the architecture aligned with Domain Driven Design and preserves future evolution paths. 🔗🧩

</div>
