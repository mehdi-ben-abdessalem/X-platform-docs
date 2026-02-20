# COMPANION — ORDER LIFECYCLE OVERVIEW

This document defines the complete lifecycle of a Companion Order.

It applies to:
- Direct Booking (Instant & Scheduled)
- RNG Booking (Instant Only)
- PER_GAME, TIME_BASED, and ONE_TIME services

This file unifies the entire Companion flow into one coherent lifecycle.

---

# 1. High-Level Separation

The Companion system separates:

Order → Commercial agreement & payment state  
Session → Service execution state  

An Order may exist before a Session starts.
A Session only exists after payment confirmation.

Activation depends on **service type**, not booking path.

---

# 2. Activation Model (Authoritative Rule)

Session activation is determined by service type:

TIME_BASED (Direct & RNG)  
→ Payment  
→ Session READY (5-minute handshake window)  
→ Both Confirm  
→ ACTIVE  

PER_GAME (Direct & RNG)  
→ Payment  
→ ACTIVE immediately  

ONE_TIME  
→ Payment  
→ Immediate fulfillment  

---

# 3. Order Creation Paths

There are two booking paths:

## A. Direct Booking

Customer selects a specific companion.

Supports:
- Instant booking
- Scheduled booking (requires companion approval)

---

## B. RNG Booking

Customer selects a service first.

System:
- Performs eligibility filtering
- Applies weighted random matching
- Generates a pool (max 6 companions)
- Customer selects one
- Payment occurs

RNG does NOT support scheduling.
RNG does NOT apply to ONE_TIME services.

Activation still follows the service-type rule above.

---

# 4. Order State Progression

## 4.1 Scheduled Booking Flow

1. Customer selects companion and time.
2. Order → PENDING_COMPANION_APPROVAL
3. Companion:
   - ACCEPT → Order → PENDING_PAYMENT
   - PROPOSE_NEW_TIME → remains PENDING_COMPANION_APPROVAL
   - DECLINE → Order → CANCELLED
4. Customer pays.
5. Order → CONFIRMED
6. Session → SCHEDULED
7. At scheduled time:

   If TIME_BASED:
   → Session → READY (handshake phase)
   → Both Confirm → ACTIVE

   If PER_GAME:
   → Session → ACTIVE immediately

---

## 4.2 Instant Direct Booking Flow

1. Customer selects companion.
2. Order → PENDING_PAYMENT
3. Customer pays.
4. Order → CONFIRMED

   If TIME_BASED:
   → Session → READY

   If PER_GAME:
   → Session → ACTIVE

---

## 4.3 RNG Booking Flow

1. Customer selects service.
2. Weighted matching generates pool.
3. Customer selects companion.
4. Order → PENDING_PAYMENT
5. Customer pays.
6. Order → CONFIRMED

   If TIME_BASED:
   → Session → READY

   If PER_GAME:
   → Session → ACTIVE

If no selection:
- All companions released
- Abandonment recorded

---

# 5. Session Execution Phase

When Session → ACTIVE:

- Chat becomes available.
- Companion state → IN_SESSION.
- Execution begins.

Execution differs by service type:

PER_GAME:
- Tracks games_completed.

TIME_BASED:
- Timer begins only after successful handshake.
- Tracks time_consumed.

ONE_TIME:
- No session execution.
- Single-action fulfillment.

---

# 6. Session End Conditions

A session may end due to:

- Normal completion
- Manual termination
- Early termination
- Flagging (admin intervention)

---

## 6.1 Normal Completion

- Session → ENDED
- Order → COMPLETED
- Chat disabled
- Companion returns to ONLINE_AVAILABLE (if still online)
- Customer prompted to rate and optionally tip

---

## 6.2 Early Termination

May occur only during ACTIVE phase.

System:
- Records consumed time or games
- Applies refund rules (if applicable)
- Order → COMPLETED or CANCELLED depending on context

Early termination rules are defined in:
`companion-early-termination.md`

READY-phase failures are not considered early termination.

---

## 6.3 Flagged Sessions

If flagged:

- Session → FLAGGED
- Order → FLAGGED
- Payout frozen
- Admin review required

---

# 7. Payment Lifecycle

Payment is escrow-based.

Rules:

- No session execution begins without payment confirmation.
- Scheduled booking requires companion approval before payment.
- RNG requires selection before payment.
- TIME_BASED sessions require handshake before execution.
- ONE_TIME services are fulfilled immediately after payment.

Refunds follow rule-based logic only.

---

# 8. Availability Transitions

Companion availability transitions:

ONLINE_AVAILABLE  
→ (PER_GAME payment) → IN_SESSION  
→ (Completion) → ONLINE_AVAILABLE  

ONLINE_AVAILABLE  
→ (TIME_BASED payment) → READY (still ONLINE_AVAILABLE)  
→ (Handshake success) → IN_SESSION  
→ (Completion) → ONLINE_AVAILABLE  

If scheduled:
ONLINE_AVAILABLE  
→ SCHEDULED  
→ At start time:
   - TIME_BASED → READY → IN_SESSION  
   - PER_GAME → IN_SESSION  

If in RNG:
ONLINE_AVAILABLE  
→ SOFT_RESERVED  
→ (Selected + Paid) → READY or IN_SESSION depending on service type  
→ (Released) → ONLINE_AVAILABLE  

---

# 9. Abuse & Protection Integration

Throughout lifecycle:

- Abandonment detection protects supply.
- Weighted matching protects exposure fairness.
- Soft-reserve timers prevent lock abuse.
- Handshake timeouts prevent ghost behavior.
- Cooldowns prevent spam behavior.

These mechanisms operate independently of Order state but influence eligibility.

---

# 10. Lifecycle Summary

A Companion Order progresses through:

Creation  
→ Approval (if scheduled)  
→ Payment  
→ Confirmation  
→ READY (if TIME_BASED)  
→ ACTIVE  
→ Completion / Termination  
→ Rating & Tipping  

At every stage:

- State transitions are deterministic.
- Concurrency conflicts are prevented.
- Payment boundaries are enforced.
- Supply protection is prioritized.

This lifecycle model ensures:

- Fair marketplace dynamics
- Reliable execution
- Abuse resistance
- Clear financial boundaries