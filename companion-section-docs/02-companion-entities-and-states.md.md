# COMPANION — ENTITIES & STATES

This document defines the core entities and state machines for the Companion system.

It applies to:
- Direct Booking
- RNG Booking

---

# 1. Companion Order (Persistent Entity)

An Order represents the commercial agreement between customer and companion.

It exists independently from the session lifecycle.

## Contains

- customer_id
- companion_id
- service_id
- pricing_model (PER_GAME / TIME_BASED / ONE_TIME)
- quantity (games or duration tier)
- total_price
- scheduled_time (optional)
- order_state

---

## Order States

- PENDING_COMPANION_APPROVAL
- PENDING_PAYMENT
- CONFIRMED
- COMPLETED
- CANCELLED
- FLAGGED

---

### State Meaning

PENDING_COMPANION_APPROVAL  
→ Scheduled request awaiting companion response.

PENDING_PAYMENT  
→ Companion accepted (or booking created), awaiting customer payment.

CONFIRMED  
→ Payment successful. Session is created and enters:
   - READY (TIME_BASED)
   - ACTIVE (PER_GAME)
   - Immediate fulfillment (ONE_TIME)
   - SCHEDULED (future scheduled booking)

COMPLETED  
→ Session ended normally.

CANCELLED  
→ Declined, expired, handshake failure, or cancelled before completion.

FLAGGED  
→ Under admin review.

---

# 2. Companion Session (Execution Entity)

A Session represents the execution of a confirmed order.

## Contains

- order_id
- start_timestamp
- end_timestamp
- games_completed (PER_GAME)
- time_consumed (TIME_BASED)
- session_state

---

## Session States

- SCHEDULED
- READY
- ACTIVE
- ENDED
- FLAGGED

---

### State Meaning

SCHEDULED  
→ Future session confirmed, waiting for start time.

READY  
→ TIME_BASED handshake phase (5-minute window).
   - Timer not running.
   - Both parties must confirm.
   - Companion remains ONLINE_AVAILABLE.

ACTIVE  
→ Session currently running.
   - Timer running (TIME_BASED)
   - Games tracked (PER_GAME)
   - Companion state → IN_SESSION
   - Chat enabled.

ENDED  
→ Session finished normally or manually ended.

FLAGGED  
→ Session under admin investigation.

---

# 3. Companion Availability States

Availability determines booking eligibility.

## States

- OFFLINE
- ONLINE_AVAILABLE
- IN_SESSION
- SOFT_RESERVED (RNG)
- SCHEDULED

---

### Availability Meaning

OFFLINE  
→ Not visible for instant booking or RNG. May allow scheduled requests.

ONLINE_AVAILABLE  
→ Eligible for:
   - Instant booking (PER_GAME only)
   - RNG matching
   - Scheduled booking
   - READY phase (TIME_BASED)

IN_SESSION  
→ Session ACTIVE.
   Not eligible for:
   - Instant booking
   - RNG matching  
   Eligible for:
   - Scheduled booking (future time only)

SOFT_RESERVED (RNG)  
→ Temporarily reserved inside RNG pool.  
   Not eligible for:
   - Instant booking
   - Other RNG pools  
   Can accept scheduled bookings beyond safe time threshold.

SCHEDULED  
→ Has upcoming confirmed session.  
   Not eligible for:
   - Instant booking during overlapping time  
   Eligible for:
   - Additional scheduled bookings outside overlapping windows.

---

# Diagram — Order & Session Lifecycle (Service-Type Aware)

```mermaid
flowchart TD

A["Order: PENDING_COMPANION_APPROVAL"] -->|Companion Accepts| B["Order: PENDING_PAYMENT"]
B --> C["Customer Pays"]
C --> D["Order: CONFIRMED"]

D --> E{"Service Type?"}

E -->|TIME_BASED| F["Session: READY (Handshake)"]
F --> G{"Both Confirm?"}
G -->|Yes| H["Session: ACTIVE"]
G -->|No| I["Session: CANCELLED"]

E -->|PER_GAME| H
E -->|ONE_TIME| J["Immediate Fulfillment"]
E -->|Scheduled| K["Session: SCHEDULED"]

H --> L{"Session Outcome"}

L -->|Normal Completion| M["Session: ENDED"]
M --> N["Order: COMPLETED"]

L -->|Cancelled| O["Session: ENDED"]
O --> P["Order: CANCELLED"]

L -->|Flagged| Q["Session: FLAGGED"]
Q --> R["Order: FLAGGED"]
```
