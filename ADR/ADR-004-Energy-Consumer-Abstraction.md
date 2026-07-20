# ADR-004: Introduce Energy Consumer Abstraction

## Status

Accepted

## Date

2026-07-20

## Decision Owners

PowerPulse Engineering

---

# Context

PowerPulse began with a clear initial target:

> Help Nigerian SMEs understand, measure, and reduce their energy costs.

The first domain model naturally focused on businesses:


Organization
|
|
Site
|
|
Energy Asset
|
|
Energy Operations


This model correctly represents an SME such as:

- Bakery
- Hotel
- Retail shop
- Workshop
- Office
- Cold storage facility

However, as the PowerPulse vision has matured, a broader reality has become clear.

The energy problem in Nigeria is not limited to businesses.

The same operational blindness exists across:

- Households
- Estates
- Schools
- Churches
- Government facilities
- Communities
- Distributed energy networks

The underlying problem is not:

"business power management."

The deeper problem is:

"energy consumption intelligence for any energy consumer."

---

# Decision

PowerPulse will introduce the concept of an:

# Energy Consumer

An Energy Consumer represents any entity that consumes, generates, stores, or manages energy.

The Energy Consumer becomes the top-level customer concept.

---

# New Domain Model

The domain evolves from:


Organization

  |
  |
 Site

  |
  |

Energy Asset


to:

             Energy Consumer

                   |

    +--------------+--------------+

    |                             |

Organization Household

    |                             |

  Sites                      Residence

    |                             |

Energy Assets Energy Assets


---

# Initial Consumer Types

The first supported consumer types are:

## 1. Business

Examples:

- Bakery
- Hotel
- Retail shop
- Factory

Representation:


OrganizationProfile


---

## 2. Household

Examples:

- Family home
- Apartment
- Estate unit

Representation:


HouseholdProfile


---

# Strategic Reasoning

## 1. The Physics Is The Same

Whether the user is:

- a bakery owner
- a homeowner
- a school administrator

the energy lifecycle remains:


Energy Source

  ↓

Conversion

  ↓

Storage

  ↓

Consumption

  ↓

Cost

  ↓

Behaviour


The intelligence layer does not fundamentally care whether the consumer is a company or a family.

---

# 2. Future Expansion

This abstraction allows PowerPulse to support:

## Households

Example:


Home

Generator

Inverter

Solar

NEPA

Battery


---

## Communities

Example:


Neighbourhood

Shared Solar

Mini Grid

Battery Storage


This aligns with future concepts such as a Neighborhood Operating System.

---

## Government Infrastructure

Example:


Hospital

School

Public Facility

Energy Assets


---

# 3. Prevent Domain Lock-In

Without this decision, PowerPulse risks becoming:


SME Generator Expense Tracker


The larger opportunity is:


Energy Intelligence Infrastructure


The architecture must preserve this possibility.

---

# Revised Module Direction

The current:


Organization Module


evolves into:


Consumer Module


or:


Customer Account Module


The module owns:


EnergyConsumer


and manages consumer identity.

---

# Future Module Structure


identity

consumer

organization-profile

household-profile

site

asset

operations

fuel

maintenance

monitoring

analytics

recommendation


---

# Aggregate Impact

New aggregate:


EnergyConsumer Aggregate


Responsibilities:

- Consumer identity
- Consumer lifecycle
- Consumer type

---

Organization becomes:


Organization Profile


Responsible for:

- Business information
- Business classification
- Business details

---

Household becomes:


Household Profile


Responsible for:

- Residential information
- Household characteristics

---

# Database Impact

Future direction:

Instead of:


organizations


as the root:

Use:


energy_consumers


Example:


energy_consumers

id UUID PRIMARY KEY

consumer_type

status

created_at

updated_at


Then:


organization_profiles

id

consumer_id

business_name

business_type


and:


household_profiles

id

consumer_id

household_name

occupants


---

# Event Impact

The generic event becomes:


EnergyConsumerRegistered


Consumers may then create:


OrganizationProfileCreated

HouseholdProfileCreated


Example:


EnergyConsumerRegistered

      ↓

OrganizationProfileCreated

      ↓

SiteCreated

      ↓

EnergyAssetRegistered


---

# Consequences

## Positive Consequences

### Larger Market Opportunity

PowerPulse can serve:

- businesses
- homes
- institutions
- communities

without redesigning the core.

---

### Better Alignment With Energy Reality

Energy systems are not organized around businesses.

They are organized around consumers and assets.

---

### Stronger Long-Term Vision

The architecture supports:

- smart homes
- distributed energy
- microgrids
- energy communities

---

## Negative Consequences

### Additional Abstraction

The model becomes slightly more complex.

---

### More Initial Design Work

The first implementation requires clearer boundaries.

---

### More Care Required

The abstraction must remain meaningful.

A generic layer must not become a meaningless wrapper.

---

# Alternatives Considered

---

# Alternative 1: Keep Organization As Root Forever

Rejected.

Reason:

It limits PowerPulse to commercial users.

---

# Alternative 2: Create Separate Household Product Later

Rejected.

Reason:

The energy intelligence engine is fundamentally the same.

Separate products would duplicate core capabilities.

---

# Alternative 3: Model Everything As Organization

Rejected.

Reason:

Technically possible but creates a false domain model.

A family home is not a company.

---

# Implementation Guidance

This decision does NOT require immediate implementation of households.

The first release may still target SMEs.

However:

All new design decisions must avoid preventing household support.

---

# Final Decision Statement

PowerPulse is not an SME energy application.

It is an energy intelligence platform.

Organizations are the first customers.

Energy consumers are the foundation.

The architecture must reflect the future, while implementation remains focused on the present.
