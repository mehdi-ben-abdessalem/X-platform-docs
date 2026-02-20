# COMPANION SYSTEM — OVERVIEW

The Companion system is a service-based marketplace model designed for interactive, non-result-based experiences.

Unlike Boosting (which is performance-driven and outcome-based), Companion services are experience-driven and session-based.

This section documents the full architecture of the Companion domain.

---

# 1. Core Philosophy

Companion services are:

- Service-based, not performance-based
- Time or activity driven
- Escrow-protected
- Rule-governed, not emotion-governed

The system prioritizes:

- Marketplace fairness
- Supply protection
- Deterministic state transitions
- Abuse prevention
- Clear payment boundaries

---

# 2. Service Types

Companion supports three service models:

### PER_GAME
Fixed price per game session.

### TIME_BASED
Fixed duration tiers (15 / 30 / 60 minutes).

### ONE_TIME
Single-action services (e.g., approved social access).

RNG booking applies only to PER_GAME and TIME_BASED services.

---

# 3. Booking Models

There are two booking paths:

## Direct Booking
Customer selects a specific companion manually.
Supports:
- Instant booking
- Scheduled booking (with companion approval)

## RNG Booking
Customer selects a service first.
System generates a weighted-random pool of eligible companions.
Supports:
- Instant booking only
- Soft-reserve protection
- Reroll system
- Exposure balancing

---

# 4. Order vs Session

The system separates:

Order → Commercial agreement  
Session → Service execution

Orders handle:
- Pricing
- Payment
- Confirmation
- Completion state

Sessions handle:
- Execution
- Timing
- Game count
- Active chat window
- Early termination

This separation prevents state corruption and allows clear lifecycle control.

---

# 5. Availability & Matching

Companion availability states determine eligibility:

- OFFLINE
- ONLINE_AVAILABLE
- SOFT_RESERVED (RNG)
- IN_SESSION
- SCHEDULED

RNG uses a weighted random matching system to:

- Promote high-rated companions
- Prevent exposure monopolies
- Maintain supply fairness
- Protect marketplace liquidity

---

# 6. Abuse & Protection Systems

The Companion system includes:

- Abandonment detection
- Cooldown enforcement
- Soft-reserve timers
- Exposure balancing
- Admin-level monitoring
- Escrow-based payments
- Rule-based refund logic

These mechanisms ensure both customer and companion protection.

---

# 7. Off-Platform Boundaries


Off-platform payments are strictly prohibited.

Violations may result in suspension or permanent removal.

---

# 8. Documentation Structure

This folder contains:

1. Core principles
2. Entities & states
3. Service structure
4. Direct booking workflow
5. RNG booking workflow
6. Abandonment & cooldown policy
7. Weighted matching system
8. Session execution
9. Early termination
10. Custom services
11. Tipping system

Each file documents one layer of the Companion system.

---

# 9. System Objective

The Companion system is designed to:

- Maximize engagement
- Protect supply health
- Prevent booking conflicts
- Prevent abuse
- Maintain fair exposure
- Create scalable marketplace mechanics

This documentation defines the authoritative architecture for Companion operations.