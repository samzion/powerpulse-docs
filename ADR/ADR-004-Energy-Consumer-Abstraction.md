<div align="center">

# 🌐 ADR-004: Introduce Energy Consumer Abstraction

![Status](https://img.shields.io/badge/status-Accepted-success?style=for-the-badge)
![Date](https://img.shields.io/badge/date-2026--07--20-blue?style=for-the-badge)
![Owner](https://img.shields.io/badge/owner-PowerPulse%20Engineering-orange?style=for-the-badge)
![Impact](https://img.shields.io/badge/impact-strategic%20pivot-critical?style=for-the-badge)

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

PowerPulse began with a clear initial target:

<div align="center">

> ### 🎯 *"Help Nigerian SMEs understand, measure, and reduce their energy costs."*

</div>

The first domain model naturally focused on businesses:

```
🏢 Organization
   └── 📍 Site
         └── 🔋 Energy Asset
               └── ⚙️ Energy Operations
```

This model correctly represents an SME such as: 🍞 bakery · 🏨 hotel · 🛍️ retail shop · 🔧 workshop · 🏢 office · ❄️ cold storage facility.

However, as the PowerPulse vision has matured, a **broader reality** has become clear. The energy problem in Nigeria is not limited to businesses. The same operational blindness exists across:

`🏠 Households` · `🏘️ Estates` · `🏫 Schools` · `⛪ Churches` · `🏛️ Government facilities` · `🌆 Communities` · `🌐 Distributed energy networks`

| ❌ The problem isn't | ✅ The deeper problem is |
|---|---|
| *"business power management"* | *"energy consumption intelligence for **any** energy consumer"* |

---

## ⚖️ Decision

<div align="center">

> ### 🌐 *PowerPulse will introduce the concept of an Energy Consumer.*

</div>

An **Energy Consumer** represents any entity that consumes, generates, stores, or manages energy. The Energy Consumer becomes the **top-level customer concept**.

---

## 🏗️ New Domain Model

**From:**

```
🏢 Organization
   └── 📍 Site
         └── 🔋 Energy Asset
```

**To:**

```
                    🌐 Energy Consumer
                          │
              ┌───────────┴───────────┐
              │                       │
        🏢 Organization          🏠 Household
              │                       │
          📍 Sites                🏠 Residence
              │                       │
       🔋 Energy Assets         🔋 Energy Assets
```

---

## 🧩 Initial Consumer Types

### 1️⃣ 🏢 Business

**Examples:** Bakery · Hotel · Retail shop · Factory
**Representation:** `OrganizationProfile`

### 2️⃣ 🏠 Household

**Examples:** Family home · Apartment · Estate unit
**Representation:** `HouseholdProfile`

---

## 💡 Strategic Reasoning

### 1️⃣ The Physics Is the Same

Whether the user is a bakery owner, a homeowner, or a school administrator, the energy lifecycle remains:

```
⚡ Energy Source
     └── 🔄 Conversion
           └── 🔋 Storage
                 └── 🔌 Consumption
                       └── 💵 Cost
                             └── 📊 Behaviour
```

> 🧠 The intelligence layer does not fundamentally care whether the consumer is a company or a family.

### 2️⃣ Future Expansion

| 🌐 Future Scope | Example |
|---|---|
| 🏠 **Households** | Home · Generator · Inverter · Solar · NEPA · Battery |
| 🌆 **Communities** | Neighbourhood · Shared Solar · Mini Grid · Battery Storage *(aligns with a future Neighborhood Operating System)* |
| 🏛️ **Government Infrastructure** | Hospital · School · Public Facility · Energy Assets |

### 3️⃣ Prevent Domain Lock-In

| ❌ Without this decision | ✅ With this decision |
|---|---|
| PowerPulse risks becoming an *"SME Generator Expense Tracker"* | PowerPulse becomes **Energy Intelligence Infrastructure** |

> 🏛️ The architecture must preserve this larger possibility.

---

## 🔄 Revised Module Direction

| Current | Evolves Into |
|---|---|
| `Organization Module` | `Consumer Module` (or `Customer Account Module`) |

The module owns `EnergyConsumer` and manages consumer identity.

---

## 🗂️ Future Module Structure

```
📦 identity
📦 consumer
📦 organization-profile
📦 household-profile
📦 site
📦 asset
📦 operations
📦 fuel
📦 maintenance
📦 monitoring
📦 analytics
📦 recommendation
```

---

## 🧱 Aggregate Impact

| 🧱 Aggregate | Responsibilities |
|---|---|
| 🆕 **EnergyConsumer** | Consumer identity · Consumer lifecycle · Consumer type |
| 🏢 **Organization** → `Organization Profile` | Business information · Business classification · Business details |
| 🏠 **Household** → `Household Profile` | Residential information · Household characteristics |

---

## 🗄️ Database Impact

**Future direction:** instead of `organizations` as the root, use `energy_consumers`.

```sql
energy_consumers
  id             UUID PRIMARY KEY
  consumer_type
  status
  created_at
  updated_at
```

```sql
organization_profiles
  id
  consumer_id
  business_name
  business_type
```

```sql
household_profiles
  id
  consumer_id
  household_name
  occupants
```

---

## 📢 Event Impact

The generic event becomes `EnergyConsumerRegistered`, which consumers then specialize:

```
📢 EnergyConsumerRegistered
      └── 📢 OrganizationProfileCreated
            └── 📢 SiteCreated
                  └── 📢 EnergyAssetRegistered

📢 EnergyConsumerRegistered
      └── 📢 HouseholdProfileCreated
```

---

## ✅ Consequences — Positive

| ✨ Benefit | Detail |
|---|---|
| 🌍 **Larger Market Opportunity** | PowerPulse can serve businesses, homes, institutions, and communities without redesigning the core |
| 🎯 **Better Alignment With Energy Reality** | Energy systems are organized around consumers and assets — not businesses |
| 🚀 **Stronger Long-Term Vision** | Architecture supports smart homes, distributed energy, microgrids, energy communities |

---

## ⚠️ Consequences — Negative

| 🚨 Cost | Detail |
|---|---|
| 🧩 **Additional Abstraction** | The model becomes slightly more complex |
| 🏗️ **More Initial Design Work** | The first implementation requires clearer boundaries |
| ⚠️ **More Care Required** | The abstraction must remain meaningful — a generic layer must not become a meaningless wrapper |

---

## 🔀 Alternatives Considered

### ❌ Alternative 1: Keep Organization As Root Forever

**Rejected** — it limits PowerPulse to commercial users.

### ❌ Alternative 2: Create Separate Household Product Later

**Rejected** — the energy intelligence engine is fundamentally the same; separate products would duplicate core capabilities.

### ❌ Alternative 3: Model Everything As Organization

**Rejected** — technically possible but creates a false domain model. A family home is not a company.

---

## 🛠️ Implementation Guidance

> ℹ️ This decision does **not** require immediate implementation of households. The first release may still target SMEs.
>
> ⚠️ However, **all new design decisions must avoid preventing household support.**

---

## 🏁 Final Decision Statement

<div align="center">

> ### *PowerPulse is not an SME energy application.*
> ### *It is an energy intelligence platform.*
>
> **Organizations are the first customers.**
> **Energy consumers are the foundation.**

The architecture must reflect the future, while implementation remains focused on the present. 🌐⚡

</div>
