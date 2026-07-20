# PowerPulse Event Catalog

## Version 2.0

## Domain Events After Energy Consumer Abstraction

---

# 1. Purpose

This document defines the domain events within PowerPulse.

Domain events represent:

- Something important happened in the business domain
- A state transition occurred
- Other modules may react

Events are not database notifications.

Events represent business meaning.

---

# 2. Event Design Principles

PowerPulse follows these rules:

## Rule 1

Events are named in past tense.

Example:


EnergyConsumerRegistered


Not:


RegisterEnergyConsumer


---

## Rule 2

The publishing module owns the event meaning.

Consumers only react.

---

## Rule 3

Events reduce coupling between modules.

A module does not need to know who listens.

---

## Rule 4

Events contain business facts.

They do not contain database details.

---

# 3. Event Flow Overview

The core PowerPulse lifecycle:


UserRegistered

    ↓

EnergyConsumerRegistered

    ↓

ProfileCreated

    ↓

SiteCreated

    ↓

EnergyAssetRegistered

    ↓

EnergyOperationRecorded

    ↓

FuelConsumed

    ↓

AnalyticsGenerated

    ↓

RecommendationGenerated


---

# 4. Identity Events

## UserRegistered

### Publisher

Identity Module

---

### Meaning

A new user account has been created.

---

### Payload


userId UUID

email

registeredAt


---

### Consumers

- Consumer Module
- Notification Module

---

# 5. Consumer Events

## EnergyConsumerRegistered

### Publisher

Consumer Module

---

### Meaning

A new energy consumer has entered PowerPulse.

---

### Payload


consumerId UUID

consumerType

createdAt


---

### Consumers

- Organization Profile Module
- Household Profile Module
- Notification Module

---

# Consumer Types

Initial:


BUSINESS

HOUSEHOLD


Future:


INSTITUTION

COMMUNITY


---

## EnergyConsumerActivated

### Publisher

Consumer Module

---

### Meaning

Consumer is now active and can operate.

---

### Payload


consumerId UUID

activatedAt


---

### Consumers

- Site Module
- Billing Module
- Notification Module

---

## EnergyConsumerSuspended

### Publisher

Consumer Module

---

### Meaning

Consumer access has been suspended.

---

### Payload


consumerId UUID

reason

suspendedAt


---

### Consumers

- Site Module
- Billing Module

---

# 6. Organization Profile Events

## OrganizationProfileCreated

### Publisher

Organization Profile Module

---

### Meaning

Business information has been created.

---

### Payload


organizationId UUID

consumerId UUID

businessType

createdAt


---

### Consumers

- Site Module
- Analytics Module

---

## OrganizationProfileUpdated

### Publisher

Organization Profile Module

---

### Consumers

- Analytics Module

---

# 7. Household Profile Events

## HouseholdProfileCreated

### Publisher

Household Profile Module

---

### Meaning

Residential profile created.

---

### Payload


householdId UUID

consumerId UUID

createdAt


---

### Consumers

- Site Module
- Analytics Module

---

## HouseholdProfileUpdated

### Publisher

Household Profile Module

---

### Consumers

- Analytics Module

---

# 8. Site Events

## SiteCreated

### Publisher

Site Module

---

### Meaning

A physical energy location exists.

---

### Payload


siteId UUID

consumerId UUID

createdAt


---

### Consumers

- Energy Asset Module
- Analytics Module

---

## SiteActivated

### Publisher

Site Module

---

### Consumers

- Asset Module
- Monitoring Module

---

## SiteDeactivated

### Publisher

Site Module

---

### Consumers

- Asset Module
- Monitoring Module

---

# 9. Energy Asset Events

## EnergyAssetRegistered

### Publisher

Energy Asset Module

---

### Meaning

A physical energy asset has been registered.

---

### Payload


assetId UUID

siteId UUID

assetType

registeredAt


---

### Consumers

- Operations Module
- Fuel Module
- Maintenance Module
- Monitoring Module

---

## EnergyAssetActivated

### Publisher

Energy Asset Module

---

### Meaning

Asset is operational.

---

### Consumers

- Operations Module
- Monitoring Module

---

## EnergyAssetRetired

### Publisher

Energy Asset Module

---

### Consumers

- Operations Module
- Maintenance Module
- Analytics Module

---

# 10. Energy Operation Events

## EnergyOperationRecorded

### Publisher

Energy Operations Module

---

### Meaning

An energy activity occurred.

---

### Payload


operationId UUID

assetId UUID

operationType

energyConsumed

recordedAt


---

### Consumers

- Analytics Module
- Fuel Module
- Monitoring Module

---

# 11. Fuel Events

## FuelAdded

### Publisher

Fuel Module

---

### Meaning

Fuel inventory increased.

---

### Payload


inventoryId UUID

quantity

fuelType

timestamp


---

### Consumers

- Analytics Module

---

## FuelConsumed

### Publisher

Fuel Module

---

### Meaning

Fuel was used.

---

### Payload


assetId UUID

quantity

timestamp


---

### Consumers

- Analytics Module
- Recommendation Module

---

## FuelLevelLow

### Publisher

Fuel Module

---

### Meaning

Fuel requires attention.

---

### Consumers

- Notification Module
- Recommendation Module

---

# 12. Maintenance Events

## MaintenanceScheduled

### Publisher

Maintenance Module

---

### Consumers

- Notification Module
- Asset Module

---

## MaintenanceCompleted

### Publisher

Maintenance Module

---

### Consumers

- Analytics Module
- Asset Module

---

# 13. Monitoring Events

## AlertTriggered

### Publisher

Monitoring Module

---

### Payload


alertId

assetId

severity

message


---

### Consumers

- Notification Module
- Recommendation Module

---

## AlertResolved

### Publisher

Monitoring Module

---

### Consumers

- Analytics Module

---

# 14. Analytics Events

## AnalyticsGenerated

### Publisher

Analytics Module

---

### Meaning

Operational data has been transformed into insight.

---

### Consumers

- Recommendation Module
- Notification Module

---

# 15. Recommendation Events

## RecommendationGenerated

### Publisher

Recommendation Module

---

### Meaning

The system produced actionable guidance.

---

### Payload


recommendationId

consumerId

category

priority


---

### Consumers

- Notification Module
- User Interface

---

# 16. Event Ownership Map

| Event | Publisher | Consumers |
|-|-|-|
| UserRegistered | Identity | Consumer, Notification |
| EnergyConsumerRegistered | Consumer | Profiles, Notification |
| OrganizationProfileCreated | Organization | Site, Analytics |
| HouseholdProfileCreated | Household | Site, Analytics |
| SiteCreated | Site | Asset, Analytics |
| EnergyAssetRegistered | Asset | Operations, Fuel, Maintenance |
| EnergyOperationRecorded | Operations | Analytics, Fuel |
| FuelConsumed | Fuel | Analytics, Recommendation |
| MaintenanceCompleted | Maintenance | Analytics |
| AlertTriggered | Monitoring | Notification |
| RecommendationGenerated | Recommendation | Notification |

---

# 17. Event Evolution Strategy

Events are contracts.

Changes must be backward compatible.

Breaking changes require:

- New event version
- Migration strategy
- Consumer updates

---

# Final Event Decision

PowerPulse uses domain events to preserve module independence.

Events describe business reality.

They allow PowerPulse to evolve from a modular monolith into a distributed energy intelligence platform when required.
