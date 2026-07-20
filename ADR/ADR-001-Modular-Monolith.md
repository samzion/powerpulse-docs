<div align="center">

# 🏛️ ADR-001: Adopt Modular Monolith Architecture

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

PowerPulse is being built as an energy intelligence platform intended to serve Nigerian businesses and eventually African energy ecosystems.

**The platform will eventually contain multiple business capabilities:**

`🪪 Identity` · `🏢 Organization` · `📍 Sites` · `🔋 Energy assets` · `⚙️ Energy operations` · `⛽ Fuel management` · `🔧 Maintenance` · `📡 Monitoring` · `📊 Analytics` · `💬 Recommendations` · `🎯 Optimization`

A natural evolution path could lead toward multiple independent services. However, beginning immediately with **microservices** introduces significant complexity:

- 🌐 Network communication
- 🚀 Service deployment management
- 🔀 Distributed transactions
- 🛠️ Operational overhead
- 💰 Infrastructure cost
- 🐛 Increased debugging complexity

> 🎯 At the current stage, the primary challenge is **not** scaling infrastructure. The primary challenge is **building the correct domain model and reliable operational foundation.**

---

## ⚖️ Decision

<div align="center">

> ### 🏛️ *PowerPulse will begin as a Modular Monolith implemented using Spring Modulith.*

</div>

The application will be deployed as **one executable system** while maintaining strict internal module boundaries. Each business capability will exist as an independent module.

---

## 🏗️ Architecture Model

```
📦 com.powerpulse
 ├── 🔗 shared
 ├── 🪪 identity
 ├── 🏢 organization
 ├── 📍 site
 ├── 🔋 asset
 ├── ⚙️ operations
 ├── ⛽ fuel
 ├── 🔧 maintenance
 ├── 📡 monitoring
 ├── 📊 analytics
 └── 💬 recommendation
```

**Each module contains:**

```
📁 module
 ├── 🧠 domain
 ├── 🧵 application
 ├── 🗄️ infrastructure
 └── 🖥️ presentation
```

---

## 💡 Why This Decision

### 1️⃣ Domain Clarity Before Distribution

The biggest unknown is not technical scaling — **it's whether the domain boundaries are correct.** A modular monolith allows PowerPulse to discover and refine boundaries before committing to distributed architecture.

### 2️⃣ Lower Operational Complexity

| 🐙 Microservices Require | 🏛️ Modular Monolith Gives |
|---|---|
| Multiple deployments | One deployable unit |
| Service discovery | Direct module calls |
| Distributed logging | Unified logging |
| Network reliability management | No network hops |
| Multiple databases/schemas | Simpler data layer |
| More DevOps overhead | Faster iteration |

### 3️⃣ Better Development Velocity

Early PowerPulse development requires: 🔄 rapid domain refinement · ✏️ frequent model changes · 🔁 strong feedback loops — a modular monolith supports this better.

### 4️⃣ Easier Future Evolution

A well-designed modular monolith creates **natural extraction points**.

```
📊 Analytics Module  →  🚀 Analytics Service
```

> 🧩 The boundary already exists.

---

## ✅ Consequences — Positive

| ✨ Benefit | Detail |
|---|---|
| 🎯 **Clear Business Ownership** | Each module owns its domain — Fuel owns fuel rules, Operations owns energy events, Analytics only consumes operational truth |
| 🧪 **Easier Testing** | Modules can be tested independently; domain tests remain fast and isolated |
| 📉 **Reduced Infrastructure Burden** | Team focuses on domain correctness, user value, and data quality — not distributed system management |
| 🛡️ **Stronger Architecture Discipline** | The application behaves like multiple systems internally, without the operational cost |

---

## ⚠️ Consequences — Negative

| 🚨 Risk | Mitigation |
|---|---|
| 🔗 **Single Deployment Unit** — a failure in one area may affect the whole application | Strong module boundaries · monitoring · good testing |
| 🚧 **Future Extraction Requires Discipline** — moving modules into services later requires stable contracts, event-based communication, clear ownership | Follow module dependency rules from the beginning |

---

## 🔀 Alternatives Considered

### ❌ Alternative 1: Microservices From Day One

**Rejected because:** 🛠️ too much operational complexity · ❓ domain boundaries not yet proven · 🐌 slower development · 💰 higher infrastructure cost

### ❌ Alternative 2: Traditional Layered Monolith

```
🎛️ controller → 🧵 service → 🗄️ repository → 📦 entity
```

**Rejected because:** 🧱 weak business boundaries · 🔗 easy coupling · 💧 business logic tends to leak into services · ⚖️ poor alignment with PowerPulse vision

### ❌ Alternative 3: Serverless Architecture

**Rejected because:** 🚫 not suitable for core domain modelling · 🎛️ less control over domain boundaries · 🌊 operational behaviour is still evolving

---

## 📏 Implementation Rules

| # | Rule |
|---|---|
| 1️⃣ | Modules communicate through **public APIs** and **domain events** — not direct internal access |
| 2️⃣ | A module owns its **database tables** |
| 3️⃣ | **No cross-module foreign keys** — use UUID references |
| 4️⃣ | Domain logic must remain **independent of Spring and infrastructure** |

---

## 🔮 Future Migration Path

If scale requires separation:

| Current | Future |
|---|---|
| `PowerPulse Application` └── 📊 Analytics Module | `PowerPulse Core` + 🚀 `Analytics Service` |

> 🧩 The modular boundary becomes the service boundary.

---

## 🏁 Final Decision Statement

<div align="center">

> ### *PowerPulse will not optimize prematurely for distributed scale.*
> ### *It will first optimize for correct domain modelling, reliable data, strong business boundaries, and sustainable engineering.*

**A strong modular foundation today creates the option for distributed architecture tomorrow. 🏛️➡️🚀**

</div>
