<div align="center">

# ⚙️ PowerPulse Engineering Bible

### *Engineering standards, architectural rules, and implementation principles*

![Engineering](https://img.shields.io/badge/document-engineering%20standards-2ea44f?style=for-the-badge)
![Stack](https://img.shields.io/badge/Java-17-orange?style=for-the-badge&logo=openjdk&logoColor=white)
![Framework](https://img.shields.io/badge/Spring%20Boot-3.3.4-brightgreen?style=for-the-badge&logo=spring&logoColor=white)
![DB](https://img.shields.io/badge/PostgreSQL-16-blue?style=for-the-badge&logo=postgresql&logoColor=white)

</div>

---

## 🎯 Purpose

This document defines the engineering standards, architectural rules, and implementation principles for building PowerPulse.

It exists to ensure that **every engineer — human or AI-assisted — builds the system consistently.**

**This document governs:**

| 📌 Area |
|---|
| 🧱 Code structure |
| 🏗️ Architectural decisions |
| 🧩 Module boundaries |
| 🛠️ Development practices |
| 🧪 Testing approach |
| 🗄️ Persistence rules |
| 🌐 API standards |
| ☁️ Infrastructure decisions |

> 🎯 The objective is not simply to produce working software. **The objective is to build a maintainable energy intelligence platform capable of serving Nigeria and eventually Africa.**

---

## 1️⃣ Engineering Philosophy

```
💼 Business first
    └── 🧠 Domain model
          └── 🧵 Application orchestration
                └── 🗄️ Infrastructure implementation
                      └── 🔧 Technology details
```

> ⚖️ Technology exists to support the business. **The business does not adapt itself around technology limitations.**

---

## 2️⃣ Core Architecture Decision

<div align="center">

> ### 🏛️ *PowerPulse is implemented as a Modular Monolith using Spring Modulith*

</div>

The system begins as one deployable application while maintaining strong internal business boundaries.

**Reasons:**
- 🚀 Faster development
- 📉 Lower operational complexity
- 🎯 Clear domain ownership
- 🔄 Easier evolution toward distributed systems if required

---

## 3️⃣ Backend Technology Stack

| 🧩 Layer | Choice | Notes |
|---|---|---|
| ☕ **Language** | Java 17 | Long-term support · records · pattern matching · strong enterprise ecosystem |
| 🍃 **Framework** | Spring Boot 3.3.4 | + Spring Modulith 1.2.4 · Spring Data JPA · Spring Security · Spring Validation |
| 🐘 **Database** | PostgreSQL 16 | Reliability · relational modelling · JSON support · analytical capability |
| 🔄 **Migration** | Liquibase | No `ddl-auto=update` · every schema change requires migration · version-controlled |
| 🧰 **Build Tool** | Maven | — |
| 🐳 **Containerization** | Docker | Local dev · infrastructure dependencies · reproducible environments |

---

## 4️⃣ Package Architecture

The backend follows Spring Modulith conventions.

**Root:** `com.powerpulse`

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

**Each module owns:**

```
📁 module
 ├── 🧠 domain
 ├── 🧵 application
 ├── 🗄️ infrastructure
 └── 🖥️ presentation
```

---

## 5️⃣ Module Responsibilities

| 🧩 Module | Responsibility |
|---|---|
| 🔗 **Shared** | Truly shared concepts only — domain events, common exceptions, UUID utilities, time abstraction, shared interfaces. ⚠️ **Must not contain business logic.** |
| 🪪 **Identity** | Users · Authentication · Authorization. Does not know about energy concepts. |
| 🏢 **Organization** | Organizations · Business ownership · Verification |
| 📍 **Site** | Physical locations · Operational sites |
| 🔋 **Asset** | Generators · Inverters · Batteries · Energy equipment |
| ⚙️ **Operations** | Energy usage · Runtime · Measurements |
| ⛽ **Fuel** | Fuel inventory · Fuel consumption · Fuel cost |
| 🔧 **Maintenance** | Service records · Asset health |
| 📊 **Analytics** | Reports · Trends · Aggregations. ⚠️ **Consumes facts — does not create operational truth.** |

---

## 6️⃣ Dependency Rules

**✅ Allowed:**

```
🖥️ presentation
      ↓
🧵 application
      ↓
🧠 domain
      ↓
🗄️ infrastructure
```

> Infrastructure implements domain contracts.

**🚫 Forbidden:**

The domain must **never** depend on:
- 🍃 Spring Framework
- 🗄️ JPA
- 🐘 Database
- 🌐 REST
- ☁️ External APIs

> ☕ The domain must remain **pure Java**.

---

## 7️⃣ Domain Rules

**Business Logic Location:** Business rules belong in the **domain** — not controllers, services, or repositories.

**Application services coordinate:**

```
📥 RegisterOrganizationCommand

Application Service:
  1. Receive command
  2. Create aggregate
  3. Save aggregate
  4. Publish event
```

> ⚖️ **The aggregate decides validity.**

---

## 8️⃣ Entity Rules

Entities must:
- 🔒 Protect their state
- ✅ Validate transitions
- 🚫 Avoid public setters

| ❌ Avoid | ✅ Prefer |
|---|---|
| `entity.setStatus(...)` | `organization.verify()` |

> 💬 Behaviour expresses business meaning.

---

## 9️⃣ Value Object Rules

Value objects are preferred for concepts with rules.

| ❌ Primitive | ✅ Value Object |
|---|---|
| `String fuelType` | `FuelType` |
| `BigDecimal amount` | `Money` *(when behaviour exists)* |

---

## 🔟 Persistence Rules

**🗄️ Database Ownership** — each module owns its tables.

**🔗 Cross-Module Relationships** — no foreign keys across modules.

| ✅ Allowed | 🚫 Not Allowed |
|---|---|
| `energy_asset.site_id UUID` | `FOREIGN KEY(site_id) REFERENCES site(id)` |

> 📞 Reason: Modules communicate through **contracts**, not database coupling.

---

## 1️⃣1️⃣ Identifier Rules

All entities use **UUID**.

**Reasons:** 🌐 Distributed readiness · 🔐 Security · 🚫 No enumeration risk

---

## 1️⃣2️⃣ Time Rules

| ❌ Avoid | ✅ Use |
|---|---|
| `LocalDateTime.now()` directly in business logic | `Clock` or a time abstraction |

**Reasons:** 🧪 Testing · 🎯 Deterministic behaviour

---

## 1️⃣3️⃣ DTO Rules

API boundaries use **DTOs**. Requests are Java records:

```java
public record CreateOrganizationRequest(
    String name
) {}
```

> 🚫 Entities are **never** returned directly.

---

## 1️⃣4️⃣ API Rules

All APIs are versioned: **`/api/v1/`**

```
POST /api/v1/organizations
```

**Controllers:**
1. 📥 Receive requests
2. ✅ Validate input
3. 📞 Call application services
4. 📤 Return responses

> 🚫 Controllers contain **no business rules**.

---

## 1️⃣5️⃣ Event Rules

Events represent **completed business facts**.

| ✅ Good | ❌ Bad |
|---|---|
| `OrganizationRegistered` | `RegisterOrganizationCommandStarted` |

**Events are:** ⏳ Immutable · 📜 Past tense · 💬 Business meaningful

---

## 1️⃣6️⃣ Testing Philosophy

Testing follows the architecture:

```
🧠 Domain Tests
      ↓
🧵 Application Tests
      ↓
🔗 Integration Tests
      ↓
🌐 API Tests
```

| Layer | Must Verify |
|---|---|
| 🧠 **Domain Tests** | Fast · pure Java · no Spring context |
| 🧵 **Application Tests** | Workflow · event publishing · repository interaction |
| 🔗 **Integration Tests** | Database mapping · Liquibase · Spring configuration |

---

## 1️⃣7️⃣ Lombok Rules

| ✅ Allowed | ❌ Avoid |
|---|---|
| `@Getter` · `@Builder` · `@Slf4j` · `@RequiredArgsConstructor` | `@Data` on entities |

> ⚠️ Reason: Generated `equals`/`hashCode` can create persistence problems.

---

## 1️⃣8️⃣ Logging Rules

Use **structured logging**. Every important operation should provide:
- 🆔 Entity identifier
- ⚙️ Operation performed
- 📤 Result

**🚫 Avoid logging:** Passwords · Sensitive credentials · Secrets

---

## 1️⃣9️⃣ Security Rules

| 🔐 Concern | Approach |
|---|---|
| **Authentication** | Spring Security + JWT |
| **Authorization** | Role-based access control |

**Initial roles:** 👑 `OWNER` · 🛡️ `ADMIN` · 👤 `STAFF`

---

## 2️⃣0️⃣ Development Sequence

```
🏗️ Infrastructure
      ↓
🔗 Shared Kernel
      ↓
🪪 Identity
      ↓
🏢 Organization
      ↓
📍 Site
      ↓
🔋 Energy Asset
      ↓
⚙️ Operations
      ↓
⛽ Fuel
      ↓
🔧 Maintenance
      ↓
📡 Monitoring
      ↓
📊 Analytics
      ↓
💬 Recommendation
```

---

## 2️⃣1️⃣ Engineering Decision Principle

When choosing between two approaches, **prefer the one that:**

- 🧠 Preserves domain clarity
- 🧩 Protects module boundaries
- 🧪 Improves testability
- 📉 Reduces future complexity
- 💼 Serves the business problem

---

## 🏁 Final Engineering Statement

<div align="center">

> ### *PowerPulse is not built as a collection of features.*
> ### *It is built as a domain-driven energy intelligence platform.*

**The code must always reflect the reality of the business it represents. ⚙️🇳🇬**

</div>
