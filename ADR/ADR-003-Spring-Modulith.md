<div align="center">

# 🗄️ ADR-003: Database Ownership and No Cross-Module Foreign Keys

![Status](https://img.shields.io/badge/status-Accepted-success?style=for-the-badge)
![Date](https://img.shields.io/badge/date-2026--07--20-blue?style=for-the-badge)
![Owner](https://img.shields.io/badge/owner-PowerPulse%20Engineering-orange?style=for-the-badge)

</div>

---

## 📋 Status

✅ **Accepted**

## 📅 Date

`2026-07-20`

## 👥 Decision Owners

PowerPulse Engineering

---

## 🧭 Context

PowerPulse uses a **modular monolith** architecture. Although all modules initially run inside one application, each bounded context represents an independent business capability.

The system contains relationships between concepts:

```
🏢 Organization
    └── 📍 Site
          └── 🔋 Energy Asset
                └── ⚙️ Energy Operation
```

A traditional relational design would naturally create foreign keys across tables:

```sql
energy_assets.site_id

FOREIGN KEY(site_id)
REFERENCES sites(id)
```

> ⚠️ However, this creates **strong coupling between modules**. The database becomes aware of domain boundaries — this conflicts with the modular architecture.

---

## ⚖️ Decision

<div align="center">

> ### 🗄️ *PowerPulse modules own their own database structures. Cross-module relationships use UUID references only. No foreign keys exist between bounded contexts.*

</div>

### ✅ Allowed

```sql
-- Energy Asset table
energy_assets
  id       UUID PRIMARY KEY
  site_id  UUID NOT NULL
```

> The asset module simply knows: *"This asset belongs to this site."*

### 🚫 Not Allowed

```sql
energy_assets
  site_id UUID

  FOREIGN KEY(site_id)
  REFERENCES sites(id)
```

> The database should **not** enforce relationships across module boundaries.

---

## 💡 Reasoning

### 1️⃣ Module Independence

Each module owns its data.

| 🧩 Module | Owns |
|---|---|
| 📍 Site | `sites` |
| 🔋 Asset | `energy_assets` |

> 🚫 The Asset module should not depend on Site's database structure.

### 2️⃣ Future Service Extraction

| Today | Future |
|---|---|
| `PowerPulse Application` └── 📍 Site Module + 🔋 Asset Module | 📍 `Site Service` ⟷ 🌐 API ⟷ 🔋 `Asset Service` |

> 🔗 Cross-service foreign keys would be **impossible**. UUID references already follow this future model.

### 3️⃣ Clear Ownership

Database ownership mirrors business ownership.

| Module | Owns |
|---|---|
| 🔋 **Asset** | Asset lifecycle · Asset table · Asset rules |
| 📍 **Site** | Site lifecycle · Site table · Site rules |

### 4️⃣ Prevent Hidden Coupling

Foreign keys create hidden dependencies — e.g. changing the Site schema could break Asset migrations.

> ✅ Without cross-module constraints, each module evolves independently.

---

## 🔒 Data Integrity Strategy

Removing foreign keys does **not** mean removing validation — integrity moves to the **application boundary**.

```
📥 RegisterEnergyAsset
        ↓
✅ Validate site exists
        ↓
🔋 Create asset
```

Validation happens through: 🧠 domain services · 🧵 application services · 📞 module APIs

---

## 📡 Cross-Module Communication

| Method | Example |
|---|---|
| 📢 **Domain Events** | Site module publishes `SiteCreated` → Asset module can react |
| 📞 **Explicit Module APIs** | Asset module requests `SiteExistenceChecker` — the dependency is visible in code |

---

## ✅ Consequences — Positive

| ✨ Benefit | Detail |
|---|---|
| 🧩 **Strong Module Boundaries** | Modules remain independently owned |
| 🧪 **Easier Testing** | Modules can be tested without requiring the entire database |
| 🚀 **Future Distributed Readiness** | Design naturally supports service extraction |
| 🎯 **Better Domain Alignment** | Database structure reflects business boundaries |

---

## ⚠️ Consequences — Negative

| 🚨 Cost | Mitigation |
|---|---|
| 🔓 **Reduced Database-Level Protection** — an asset may technically contain a nonexistent site UUID | Application validation |
| 🧵 **More Application Responsibility** | Developers must consciously maintain integrity |
| 📐 **More Design Discipline Required** | Engineers cannot rely on database constraints to hide poor boundaries |

---

## 🔀 Alternatives Considered

### ❌ Alternative 1: Foreign Keys Across All Tables

**Rejected because:** 🔗 creates coupling · 🫥 weakens module ownership · 🚧 makes future extraction difficult

### ⏸️ Alternative 2: Separate Database Per Module

**Deferred** — a future option, but unnecessary complexity at the current stage. The modular monolith approach provides boundaries first.

### ❌ Alternative 3: Single Shared Database Model

**Rejected because:** 🔗 encourages direct table coupling · 🫥 makes ownership unclear · 📇 promotes CRUD thinking

---

## 📏 Implementation Rules

| # | Rule |
|---|---|
| 1️⃣ | Every module owns its tables |
| 2️⃣ | Cross-module references use **UUID** |
| 3️⃣ | **No** cross-module database foreign keys |
| 4️⃣ | Module relationships must be expressed through events, interfaces, or application contracts |
| 5️⃣ | Historical data must remain **immutable** |

---

## 🗂️ Example Module Ownership

| 🧩 Module | 🗄️ Owns Tables |
|---|---|
| 🪪 Identity | `users`, `credentials` |
| 🏢 Organization | `organizations`, `verification_credentials` |
| 📍 Site | `sites` |
| 🔋 Asset | `energy_assets` |
| ⚙️ Operations | `energy_operations` |
| ⛽ Fuel | `fuel_inventory`, `fuel_transactions` |
| 🔧 Maintenance | `maintenance_records` |
| 📡 Monitoring | `alerts` |
| 📊 Analytics | `reports` |

---

## 🏁 Final Decision Statement

<div align="center">

> ### *PowerPulse will treat database ownership as an extension of domain ownership.*
> ### *Modules must remain independent business capabilities.*

**The database supports the architecture. The database does not define the architecture. 🗄️🧩**

</div>
