# PowerPulse Aggregate Catalog

## Version 2.0

## Aggregate Design After Energy Consumer Abstraction

---

# 1. Purpose

This document defines the aggregate boundaries inside PowerPulse.

Aggregates represent:

- Business consistency boundaries
- Domain ownership boundaries
- Behaviour protection boundaries

An aggregate is not a database grouping.

An aggregate exists to protect business rules.

---

# 2. Aggregate Design Principles

PowerPulse follows these rules:

## Rule 1

Every aggregate has exactly one aggregate root.

---

## Rule 2

External modules reference aggregates through:

- IDs
- Domain events
- Explicit contracts

---

## Rule 3

Aggregates protect their own invariants.

---

## Rule 4

Large aggregates are avoided.

A single aggregate should not own unrelated business concepts.

---

# 3. Aggregate Overview

PowerPulse aggregates:


EnergyConsumer

OrganizationProfile

HouseholdProfile

Site

EnergyAsset

EnergyOperation

FuelInventory

MaintenanceRecord

MonitoringAlert

Recommendation


---

# 4. EnergyConsumer Aggregate

## Aggregate Root


EnergyConsumer


## Context

Consumer Context

---

## Purpose

Represents any entity that consumes or manages energy.

Examples:

- Business
- Household
- Institution
- Community

---

## Responsibilities

Owns:

- Consumer identity
- Consumer type
- Consumer lifecycle
- Consumer status

---

## Attributes


id UUID

consumerType

status

createdAt

updatedAt


---

## Consumer Types

Initial:


BUSINESS

HOUSEHOLD


Future:


INSTITUTION

COMMUNITY

GOVERNMENT


---

## Invariants

An EnergyConsumer:

- must have a type
- must have an active lifecycle state
- cannot exist without identity

---

## Events Produced


EnergyConsumerRegistered

EnergyConsumerActivated

EnergyConsumerSuspended


---

# 5. OrganizationProfile Aggregate

## Aggregate Root


OrganizationProfile


## Context

Organization Profile Context

---

## Purpose

Represents business information belonging to a consumer.

---

## Relationship


EnergyConsumer

   |

OrganizationProfile


---

## Responsibilities

Owns:

- Business name
- Business category
- Business classification
- Registration details

---

## Attributes


id UUID

consumerId UUID

businessName

businessType

registrationNumber


---

## Invariants

An organization profile:

- must belong to an EnergyConsumer
- must have a business identity
- must have a valid business type

---

## Events Produced


OrganizationProfileCreated

OrganizationProfileUpdated


---

# 6. HouseholdProfile Aggregate

## Aggregate Root


HouseholdProfile


## Context

Household Profile Context

---

## Purpose

Represents residential consumers.

---

## Relationship


EnergyConsumer

   |

HouseholdProfile


---

## Responsibilities

Owns:

- Household information
- Residential details

---

## Attributes


id UUID

consumerId UUID

householdName

location

occupancyInformation


---

## Events Produced


HouseholdProfileCreated

HouseholdProfileUpdated


---

# 7. Site Aggregate

## Aggregate Root


Site


## Context

Site Context

---

## Purpose

Represents a physical location where energy activity occurs.

Examples:

- Shop
- Factory
- Home
- Estate

---

## Relationship


EnergyConsumer

    |

   Site

---

## Responsibilities

Owns:

- Location identity
- Site status
- Site information

---

## Attributes


id UUID

consumerId UUID

name

address

status


---

## Invariants

A Site:

- must belong to a consumer
- must have a valid status
- cannot operate without identity

---

## Events Produced


SiteCreated

SiteActivated

SiteDeactivated


---

# 8. EnergyAsset Aggregate

## Aggregate Root


EnergyAsset


## Context

Energy Asset Context

---

## Purpose

Represents physical energy equipment.

Examples:

- Generator
- Inverter
- Solar system
- Battery

---

## Relationship


Site

|

EnergyAsset


---

## Responsibilities

Owns:

- Asset identity
- Asset type
- Asset lifecycle

---

## Attributes


id UUID

siteId UUID

assetType

status

manufacturer

model


---

## Asset Types


GENERATOR

INVERTER

SOLAR

BATTERY

GRID_CONNECTION


---

## Invariants

An asset:

- must belong to a site
- must have a valid type
- must follow lifecycle transitions

---

## Events Produced


EnergyAssetRegistered

EnergyAssetActivated

EnergyAssetRetired


---

# 9. EnergyOperation Aggregate

## Aggregate Root


EnergyOperation


## Context

Energy Operations

---

## Purpose

Captures actual energy behaviour.

---

## Examples


Generator ran for 6 hours

Battery discharged

Grid available


---

## Responsibilities

Owns:

- Runtime events
- Consumption events
- Operational measurements

---

## Attributes


id UUID

assetId UUID

operationType

startTime

endTime

energyConsumed


---

## Events Produced


EnergyOperationRecorded


---

# 10. FuelInventory Aggregate

## Aggregate Root


FuelInventory


## Context

Fuel

---

## Purpose

Tracks fuel availability and consumption.

---

## Relationship


EnergyAsset

   |

FuelInventory


---

## Responsibilities

Owns:

- Fuel balance
- Fuel transactions

---

## Attributes


id UUID

assetId UUID

fuelType

quantity


---

## Events Produced


FuelAdded

FuelConsumed

FuelLevelLow


---

# 11. Maintenance Aggregate

## Aggregate Root


MaintenanceRecord


## Context

Maintenance

---

## Purpose

Tracks equipment health activities.

---

## Responsibilities

Owns:

- Maintenance schedules
- Repairs
- Service history

---

## Attributes


id UUID

assetId UUID

maintenanceType

scheduledDate

completedDate


---

## Events Produced


MaintenanceScheduled

MaintenanceCompleted


---

# 12. Monitoring Aggregate

## Aggregate Root


MonitoringAlert


---

## Purpose

Represents abnormal conditions.

Examples:

- High fuel consumption
- Equipment failure risk
- Unexpected runtime

---

## Events Produced


AlertTriggered

AlertResolved


---

# 13. Recommendation Aggregate

## Aggregate Root


Recommendation


---

## Purpose

Stores actionable intelligence.

---

## Examples


Reduce generator runtime by 3 hours daily

Service generator within 20 hours


---

## Events Produced


RecommendationGenerated


---

# 14. Aggregate Relationship Map

            EnergyConsumer

                   |

    +--------------+--------------+

    |                             |

OrganizationProfile HouseholdProfile

                   |

                  Site


                   |

             EnergyAsset


                   |

          EnergyOperation


                   |

      +------------+------------+

      |                         |

FuelInventory MaintenanceRecord

                   |

            Monitoring


                   |

         Recommendation

---

# 15. Cross Aggregate Communication

Aggregates communicate through:

## Domain Events

Example:


EnergyAssetRegistered

    ↓

Fuel module creates inventory tracking


---

## Explicit Contracts

Example:


AssetService

    ↓

SiteExistenceChecker


---

# 16. Database Ownership Rule

Aggregates own their data.

No aggregate directly manipulates another aggregate's tables.

No cross-module foreign keys.

References are UUID only.

---

# Final Aggregate Decision

PowerPulse aggregates are designed around business capability, not database structure.

EnergyConsumer is the foundation.

All future intelligence depends on the correctness of these boundaries.
