# PowerPulse Module Dependency Matrix

## Version 2.0

## Module Boundaries After Energy Consumer Abstraction

---

# 1. Purpose

This document defines the dependency rules between PowerPulse modules.

The objective is to maintain:

- Strong boundaries
- Low coupling
- Clear ownership
- Future extraction readiness

A module may depend only on modules below it in the dependency direction.

---

# 2. Core Dependency Principle

PowerPulse follows:

> Dependencies point toward stable business foundations.

The flow is:


Identity

↓

Consumer

↓

Profiles

↓

Site

↓

Energy Asset

↓

Energy Operations

↓

Fuel / Maintenance / Monitoring

↓

Analytics

↓

Recommendation

↓

Notification


---

# 3. Module Overview

| Module | Responsibility |
|-|-|
| Identity | User authentication and authorization |
| Consumer | Energy consumer lifecycle |
| Organization Profile | Business information |
| Household Profile | Residential information |
| Site | Physical locations |
| Asset | Energy equipment |
| Operations | Energy behaviour records |
| Fuel | Fuel intelligence |
| Maintenance | Equipment health |
| Monitoring | Alerts and observations |
| Analytics | Reports and insights |
| Recommendation | Decision support |
| Notification | User communication |

---

# 4. Identity Module

## Owns


users

credentials

authentication


---

## Depends On

None.

Identity is foundational.

---

## Publishes


UserRegistered


---

## Consumed By

- Consumer Module
- Notification Module

---

# 5. Consumer Module

## Owns


energy_consumers


---

## Responsibility

Creates and manages energy consumers.

Examples:


Business

Household


---

## Depends On


Identity


---

## Publishes


EnergyConsumerRegistered

EnergyConsumerActivated

EnergyConsumerSuspended


---

## Consumed By

- Organization Profile
- Household Profile
- Site

---

# 6. Organization Profile Module

## Owns


organization_profiles


---

## Depends On


Consumer


---

## Publishes


OrganizationProfileCreated


---

## Consumed By

- Site
- Analytics

---

# 7. Household Profile Module

## Owns


household_profiles


---

## Depends On


Consumer


---

## Publishes


HouseholdProfileCreated


---

## Consumed By

- Site
- Analytics

---

# 8. Site Module

## Owns


sites


---

## Depends On


Consumer

Profiles


---

## Responsibility

Manages physical operating locations.

Examples:


Shop

Home

Factory


---

## Publishes


SiteCreated

SiteActivated

SiteDeactivated


---

## Consumed By

- Asset
- Analytics

---

# 9. Asset Module

## Owns


energy_assets


---

## Depends On


Site


---

## Responsibility

Manages:

- Generator
- Inverter
- Solar
- Battery
- Grid connection

---

## Publishes


EnergyAssetRegistered

EnergyAssetActivated

EnergyAssetRetired


---

## Consumed By

- Operations
- Fuel
- Maintenance
- Monitoring

---

# 10. Operations Module

## Owns


energy_operations


---

## Depends On


Asset


---

## Responsibility

Records energy behaviour.

---

## Publishes


EnergyOperationRecorded


---

## Consumed By

- Analytics
- Fuel
- Monitoring

---

# 11. Fuel Module

## Owns


fuel_inventory

fuel_transactions


---

## Depends On


Asset

Operations


---

## Publishes


FuelAdded

FuelConsumed

FuelLevelLow


---

## Consumed By

- Analytics
- Recommendation
- Notification

---

# 12. Maintenance Module

## Owns


maintenance_records


---

## Depends On


Asset


---

## Publishes


MaintenanceScheduled

MaintenanceCompleted


---

## Consumed By

- Analytics
- Notification

---

# 13. Monitoring Module

## Owns


alerts


---

## Depends On


Asset

Operations

Fuel


---

## Publishes


AlertTriggered

AlertResolved


---

## Consumed By

- Recommendation
- Notification

---

# 14. Analytics Module

## Owns


reports

aggregations


---

## Depends On


Operations

Fuel

Maintenance

Monitoring


---

## Publishes


AnalyticsGenerated


---

## Consumed By

- Recommendation
- Notification

---

# 15. Recommendation Module

## Owns


recommendations


---

## Depends On


Analytics

Monitoring


---

## Publishes


RecommendationGenerated


---

## Consumed By

- Notification
- Frontend

---

# 16. Notification Module

## Owns

Communication delivery.

---

## Depends On

Almost all modules through events.

---

## Responsibilities

- Email
- SMS
- Push notifications
- User alerts

---

# 17. Dependency Rules

## Rule 1

No circular dependencies.

Invalid:


Asset → Site

Site → Asset


---

## Rule 2

No direct database access between modules.

Invalid:


AssetRepository

querying

sites table


---

## Rule 3

Cross-module communication uses:

- Events
- Interfaces
- Contracts

---

## Rule 4

Higher intelligence modules depend on operational modules.

Example:

Allowed:


Analytics → Operations


Not:


Operations → Analytics


---

# 18. Database Boundary Rule

Each module owns its schema objects.

Example:

Asset owns:


energy_assets


Site owns:


sites


References:


siteId UUID


No foreign keys across modules.

---

# 19. Spring Modulith Package Direction

Expected structure:


com.powerpulse

├── identity

├── consumer

├── organization

├── household

├── site

├── asset

├── operations

├── fuel

├── maintenance

├── monitoring

├── analytics

├── recommendation

└── notification


---

# Final Dependency Decision

PowerPulse modules are organized around business capabilities.

The dependency direction follows the energy lifecycle.

The foundation is identity and consumer.

The intelligence layer sits on top of trustworthy operational data.
