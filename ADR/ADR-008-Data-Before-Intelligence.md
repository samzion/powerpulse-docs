# ADR-008: Data Before Intelligence

## Status

Accepted

## Date

2026-07-20

---

# Context

PowerPulse is an energy intelligence platform.

The long-term vision includes capabilities such as:

- Energy optimization
- Fuel forecasting
- Preventive maintenance
- Operational recommendations
- Digital twins
- Cost prediction
- Autonomous energy management

These capabilities are valuable only if they are built on reliable operational data.

A common mistake in software projects is to implement advanced intelligence before the underlying operational system exists.

Examples include:

- Predicting fuel consumption before fuel inventory is tracked.
- Building a digital twin before operational events exist.
- Creating optimization algorithms without historical usage data.
- Applying AI to data that has not yet been validated.

Such systems become demonstrations rather than dependable products.

---

# Decision

PowerPulse follows the principle:

> Reliable operational data always comes before intelligence.

The platform will be built in progressive layers.

Each layer must provide value on its own before the next layer is introduced.

---

# The Build Progression

PowerPulse evolves through the following stages:

```
Infrastructure

        ↓

Reliable Operations

        ↓

Rich Operational Data

        ↓

Knowledge

        ↓

Intelligence

        ↓

Optimization

        ↓

Autonomy
```

Every stage depends on the quality of the stage beneath it.

---

# Stage 1 — Infrastructure

Purpose:

Build the technical foundation.

Includes:

- Authentication
- Identity
- Energy Consumer
- Profiles
- Sites
- Assets
- Persistence
- Security
- API foundation

Goal:

The platform can reliably store and retrieve business data.

---

# Stage 2 — Reliable Operations

Purpose:

Capture real business activity.

Examples:

- Register assets
- Record generator runtime
- Record grid availability
- Record inverter usage
- Record fuel purchases
- Record maintenance activities

Goal:

Operational events become trustworthy.

---

# Stage 3 — Rich Operational Data

Purpose:

Accumulate historical business data.

Examples:

- Daily energy usage
- Weekly operating costs
- Fuel consumption history
- Generator runtime trends
- Maintenance history

Goal:

The platform knows what has happened.

---

# Stage 4 — Knowledge

Purpose:

Transform historical data into information.

Examples:

- Weekly reports
- Monthly summaries
- Energy cost breakdowns
- Usage trends
- Asset utilization

Goal:

Users understand their energy behaviour.

Knowledge explains the past.

---

# Stage 5 — Intelligence

Purpose:

Use accumulated knowledge to support decisions.

Examples:

- Fuel forecasts
- Maintenance recommendations
- Cost anomaly detection
- Energy efficiency scoring
- Generator dependency analysis

Goal:

The platform begins helping users decide what to do next.

Intelligence recommends.

It does not yet automate.

---

# Stage 6 — Optimization

Purpose:

Improve operational outcomes.

Examples:

- Optimal generator scheduling
- Fuel purchasing strategies
- Asset utilization improvements
- Cost reduction suggestions

Goal:

The platform actively helps reduce cost and improve reliability.

---

# Stage 7 — Autonomy

Purpose:

Allow controlled automation.

Possible future capabilities:

- Automatic maintenance scheduling
- Smart energy source switching
- Predictive purchasing
- Integration with IoT devices
- Digital twin simulations

Autonomy is the final stage.

It is never the starting point.

---

# Digital Twin Principle

PowerPulse intends to support digital twins in the future.

However:

A digital twin without operational history is simply a model.

It becomes valuable only after the platform has accumulated enough real operational events to represent reality accurately.

The digital twin depends on data.

The data does not depend on the digital twin.

---

# Artificial Intelligence

Artificial Intelligence is not a foundation.

It is an enhancement.

PowerPulse will not introduce AI simply because AI is available.

AI is introduced only when:

- sufficient historical data exists,
- business value is clear,
- deterministic rules are no longer sufficient.

The system must first solve problems through reliable engineering before introducing probabilistic intelligence.

---

# Engineering Implications

When choosing between two implementation options of similar effort:

Prefer the option that strengthens operational data.

Examples:

Good:

- Better event recording
- Better validation
- Better audit history
- Better data quality

Delay:

- Forecast engines
- Optimization algorithms
- AI assistants

until operational data justifies them.

---

# Decision Filter

Before implementing any new capability, ask:

1. Does reliable operational data already exist?
2. Can this feature be validated using historical records?
3. Would the feature still provide value if predictions were removed?

If the answer is "No", the feature is probably premature.

---

# Examples

## Good Sequence

```
Fuel Inventory

        ↓

Fuel Transactions

        ↓

Fuel Consumption History

        ↓

Fuel Forecasting
```

---

## Bad Sequence

```
Fuel Forecasting

        ↓

Fuel Inventory
```

Forecasting without inventory produces unreliable results.

---

## Good Sequence

```
Energy Operations

        ↓

Historical Reports

        ↓

Generator Efficiency Analysis
```

---

## Bad Sequence

```
Generator Efficiency AI

        ↓

No operational history
```

---

# Consequences

## Positive

PowerPulse gains:

- Reliable operational foundation
- Trustworthy analytics
- Explainable recommendations
- Easier testing
- Lower engineering risk
- Sustainable product growth

---

## Negative

Advanced intelligence features arrive later.

This is intentional.

The platform prioritizes correctness over novelty.

---

# Alternatives Considered

## AI First

Rejected.

Reason:

Artificial intelligence cannot compensate for missing operational data.

---

## Build All Layers Together

Rejected.

Reason:

Creates unnecessary complexity and slows delivery.

---

## Analytics Before Operations

Rejected.

Reason:

Analytics without trustworthy data has little business value.

---

# Final Decision

PowerPulse is built from the ground up.

The sequence is:

Infrastructure → Operations → Data → Knowledge → Intelligence → Optimization → Autonomy.

Every intelligent capability must stand on a foundation of trustworthy operational data.

PowerPulse earns intelligence through engineering discipline, not by skipping the fundamentals.
