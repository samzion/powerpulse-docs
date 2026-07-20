<div align="center">

# 🗄️ ADR-003: Database Ownership and No Cross-Module Foreign Keys

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

PowerPulse is being developed as a **modular monolith** using: `🍃 Spring Boot` · `🧩 Spring Modulith` · `🐘 PostgreSQL` · `🔄 Liquibase`

Although the application initially runs as a single deployment, it contains multiple business capabilities:

`🌐 Consumer management` · `📍 Site management` · `🔋 Asset management` · `⚙️ Energy operations` · `⛽ Fuel intelligence` · `📊 Analytics`

Each capability has its own business rules and should maintain ownership of its data.

> ⚠️ A common failure in modular monoliths is allowing modules to share database structures freely. This creates: 🔗 hidden coupling · 🚧 difficult migrations · 🙈 unclear ownership · 🧨 fragile architecture.

---

## ⚖️ Decision

<div align="center">

> ### 🗄️ *Each PowerPulse module owns its database tables. Modules must not create database foreign keys across module boundaries.*

</div>

Cross-module relationships use **UUID references** and communicate through: 📢 Domain events · 📞 Application contracts · 🔌 Explicit interfaces

---

## 🗂️ Database Ownership Model

The ownership model follows bounded contexts.

### 🌐 Consumer Module

**Owns:** `energy_consumers`

```sql
energy_consumers
  id           UUID PRIMARY KEY
  consumer_type
  status
  created_at
  updated_at
```

### 🏢 Organization Module

**Owns:** `organization_profiles`

```sql
organization_profiles
  id            UUID PRIMARY KEY
  consumer_id   UUID
  business_name
  business_type
```

> ℹ️ `consumer_id` is a **reference only** — it is **not** `FOREIGN KEY (consumer_id)`.

### 🏠 Household Module

**Owns:** `household_profiles`

```sql
household_profiles
  id            UUID PRIMARY KEY
  consumer_id   UUID
  household_name
```

### 📍 Site Module

**Owns:** `sites`

```sql
sites
  id            UUID PRIMARY KEY
  consumer_id   UUID
  name
  location
  status
```

### 🔋 Asset Module

**Owns:** `energy_assets`

```sql
energy_assets
  id            UUID PRIMARY KEY
  site_id       UUID
  asset_type
  status
```

### ⚙️ Operations Module

**Owns:** `energy_operations`

```sql
energy_operations
  id              UUID PRIMARY KEY
  asset_id        UUID
  operation_type
  energy_consumed
  recorded_at
```

### ⛽ Fuel Module

**Owns:** `fuel_inventory`, `fuel_transactions`

### 🔧 Maintenance Module

**Owns:** `maintenance_records`

### 📊 Analytics Module

**Owns:** `reports`, `analytics_results`

---

## 💡 Why No Cross-Module Foreign Keys?

### 1️⃣ Preserve Module Independence

Without cross-module constraints, the 🔋 **Asset Module** does not depend on the internal database structure of the 📍 **Site Module**.

### 2️⃣ Enable Future Extraction

| Today | Future |
|---|---|
| `PowerPulse Application` └── 🔋 Asset Module | 🚀 `Asset Service` ⟷ 🚀 `Site Service` |

> 🧩 The database boundary already exists.

### 3️⃣ Allow Independent Evolution

A module can change its internal schema without forcing unrelated modules to migrate.

---

## 🔍 Example

### ❌ Bad Design

```sql
CREATE TABLE energy_assets (
  id       UUID,
  site_id  UUID REFERENCES sites(id)
);
```

> ⚠️ **Problem:** Asset module now owns knowledge of Site database implementation.

### ✅ Correct Design

```sql
CREATE TABLE energy_assets (
  id       UUID PRIMARY KEY,
  site_id  UUID NOT NULL
);
```

> ✅ The relationship exists. The ownership does not.

---

## 🔒 Referential Integrity

Without database foreign keys, integrity is maintained through:

| Method | Example |
|---|---|
| 📏 **Application Rules** | Before creating an asset: `Check SiteExists(siteId)` |
| 📢 **Domain Events** | `SiteCreated` → Asset module becomes aware |
| 🧪 **Automated Tests** | Module contracts ensure consistency |

---

## 🗂️ Migration Ownership

Each module owns its Liquibase migrations.

```
📁 db/changelog
 ├── 🌐 consumer
 │     └── 001-create-energy-consumers.xml
 ├── 📍 site
 │     └── 001-create-sites.xml
 └── 🔋 asset
       └── 001-create-energy-assets.xml
```

---

## ✅ Consequences — Positive

- 🧩 Strong module boundaries
- 🎯 Clear ownership
- 🛠️ Easier refactoring
- 🚀 Future service extraction path
- 🧠 Cleaner DDD implementation

## ⚠️ Consequences — Negative

- 🔓 Some integrity checks move from database to application
- 📐 More discipline required
- 🧩 Developers must respect ownership rules

---

## 🔀 Alternatives Considered

### ❌ Shared Database With Foreign Keys Everywhere

**Rejected** — creates a distributed monolith inside one database.

### ⏸️ Separate Database Per Module

**Deferred** — operational complexity is unnecessary at current scale.

---

## 🏁 Final Decision

<div align="center">

> ### *PowerPulse will use: One PostgreSQL instance + Module-owned tables + UUID references + No cross-module foreign keys*

**The database structure reflects the business boundaries.**

The goal is not only storing data. **The goal is preserving architecture. 🗄️🧩**

</div>
