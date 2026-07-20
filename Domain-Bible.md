# PowerPulse Domain Design Bible

## Version 2.0

## Domain Evolution: From Organization Intelligence to Energy Consumer Intelligence

---

# 1. Purpose

This document defines the core domain model of PowerPulse.

PowerPulse is an energy intelligence platform designed to understand, measure, and optimize energy behaviour.

The domain model is built around one fundamental concept:

> Energy consumers require intelligence about how energy is generated, stored, consumed, and paid for.

The platform begins with Nigerian SMEs but is designed to support:

- Businesses
- Households
- Institutions
- Communities
- Distributed energy systems

---

# 2. Core Domain Principle

PowerPulse does not primarily model businesses.

PowerPulse models:

> Energy behaviour.

Every consumer follows the same fundamental energy lifecycle:


Energy Source

  ↓

Energy Conversion

  ↓

Energy Storage

  ↓

Energy Consumption

  ↓

Cost

  ↓

Operational Behaviour

  ↓

Optimization


---

# 3. Domain Language

## Energy Consumer

The central domain concept.

An Energy Consumer is any entity that:

- consumes energy
- manages energy assets
- pays for energy
- makes energy decisions

Examples:

- SME
- Household
- School
- Hospital
- Estate

---

## Consumer Types

Initial supported types:


BUSINESS

HOUSEHOLD


Future:


INSTITUTION

COMMUNITY

GOVERNMENT


---

# 4. High-Level Domain Model

The system evolves from:


Organization

  |

Site

  |

Energy Asset

  |

Energy Operation


into:

             Energy Consumer

                   |

    +--------------+--------------+

    |                             |

Organization Profile Household Profile

    |                             |

   Site                       Residence


    |                             |

Energy Assets Energy Assets

          |

  Energy Operations

---

# 5. Bounded Contexts

PowerPulse is divided into business capabilities.

---

# Identity Context

## Responsibility

Manages authentication and user identity.

Owns:

- Users
- Credentials
- Sessions
- Authentication state

Does not own:

- Businesses
- Energy assets
- Operations

---

# Consumer Context

## Responsibility

Manages the energy consumer lifecycle.

Owns:

- Energy Consumer
- Consumer type
- Consumer status

Aggregate:


EnergyConsumer


---

## Consumer Invariants

An Energy Consumer:

- must have a valid identity
- must have a consumer type
- must have a lifecycle state

---

# Organization Profile Context

## Responsibility

Business-specific information.

Owns:

- Business name
- Business category
- Business classification
- Registration information

Example:


Mama Ngozi Bakery


belongs here.

---

# Household Profile Context

## Responsibility

Residential-specific information.

Owns:

- Household identity
- Residence information
- Household characteristics

Example:


Samson Residence


belongs here.

---

# Site Context

## Responsibility

Represents physical locations where energy activity occurs.

Examples:

- Shop branch
- Home
- Factory
- Estate block

Owns:

- Site identity
- Location
- Operational status

---

# Energy Asset Context

## Responsibility

Models physical energy equipment.

Examples:

- Generator
- Inverter
- Solar system
- Battery
- Grid connection

Owns:

- Asset identity
- Asset type
- Asset lifecycle
- Asset characteristics

---

# Energy Operations Context

## Responsibility

Captures energy behaviour.

Examples:

- Generator runtime
- Grid availability
- Battery usage
- Energy consumption

Owns:

- Operational events
- Runtime records
- Consumption records

---

# Fuel Context

## Responsibility

Manages fuel intelligence.

Owns:

- Fuel inventory
- Fuel purchases
- Fuel consumption

---

# Maintenance Context

## Responsibility

Manages equipment health.

Owns:

- Maintenance schedules
- Repairs
- Service history

---

# Monitoring Context

## Responsibility

Observes system behaviour.

Owns:

- Alerts
- Threshold monitoring
- Operational warnings

---

# Analytics Context

## Responsibility

Transforms operational data into knowledge.

Owns:

- Reports
- Trends
- Aggregations

---

# Recommendation Context

## Responsibility

Provides actionable intelligence.

Owns:

- Recommendations
- Optimization rules
- Decision support

---

# 6. Aggregate Catalogue

## EnergyConsumer Aggregate

Root:


EnergyConsumer


Purpose:

Protect consumer identity and lifecycle.

Contains:

- Consumer ID
- Consumer Type
- Status

---

## Organization Aggregate

Root:


OrganizationProfile


Purpose:

Business representation.

---

## Household Aggregate

Root:


HouseholdProfile


Purpose:

Residential representation.

---

## Site Aggregate

Root:


Site


Purpose:

Physical energy location.

---

## EnergyAsset Aggregate

Root:


EnergyAsset


Purpose:

Protect asset lifecycle.

Examples:


Generator

Inverter

Solar Panel

Battery


---

## EnergyOperation Aggregate

Root:


EnergyOperation


Purpose:

Capture energy activity.

---

## Fuel Aggregate

Root:


FuelInventory


Purpose:

Protect fuel state.

---

# 7. Domain Events

Major events:


EnergyConsumerRegistered

OrganizationProfileCreated

HouseholdProfileCreated

SiteCreated

EnergyAssetRegistered

EnergyAssetActivated

EnergyOperationRecorded

FuelAdded

FuelConsumed

MaintenanceScheduled

MaintenanceCompleted

RecommendationGenerated


---

# 8. Relationships

Important rule:

Modules do not share database ownership.

Relationships are represented through:

- UUID references
- Domain events
- Explicit contracts

Example:


EnergyAsset

siteId UUID


Not:


FOREIGN KEY(site_id)


---

# 9. Data Ownership

| Context | Owns |
|-|-|
| Identity | Users |
| Consumer | Energy Consumers |
| Organization | Business Profiles |
| Household | Household Profiles |
| Site | Sites |
| Asset | Energy Assets |
| Operations | Energy Events |
| Fuel | Fuel Data |
| Maintenance | Maintenance Data |
| Analytics | Reports |

---

# 10. Evolution Strategy

PowerPulse follows:


Foundation

↓

Reliable Operations

↓

Rich Data

↓

Knowledge

↓

Intelligence

↓

Optimization

↓

Autonomy


Intelligence depends on trustworthy operational data.

---

# 11. Design Principle

The platform must not build intelligence before understanding reality.

A digital twin without operational history is only a data structure.

PowerPulse must first capture:

- assets
- events
- costs
- behaviour

before attempting prediction.

---

# Final Domain Statement

PowerPulse is an energy intelligence platform built around the concept of Energy Consumers.

Organizations are the first users.

Households are natural extensions.

The true domain is energy behaviour.

The system exists to understand, measure, and improve how energy is used.
