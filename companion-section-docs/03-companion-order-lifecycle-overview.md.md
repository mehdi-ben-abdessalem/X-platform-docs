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

---

# 2. Order Creation Paths

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
- Session starts immediately

RNG does NOT support scheduling.
RNG does NOT apply to ONE_TIME services.

---

# 3. Order State Progression

## 3.1 Scheduled Booking Flow

1. Customer selects companion and time.
2. Order → PENDING_COMPANION_APPROVAL
3. Companion:
   - ACCEPT → Order → PENDING_PAYMENT
   - PROPOSE_NEW_TIME → remains PENDING_COMPANION_APPROVAL
   - DECLINE → Order → CANCELLED
4. Customer pays.
5. Order → CONFIRMED
6. Session → SCHEDULED
7. At scheduled time → Session → ACTIVE

---

## 3.2 Instant Direct Booking Flow

1. Customer selects companion.
2. Order → PENDING_PAYMENT
3. Customer pays.
4. Order → CONFIRMED
5. Session → ACTIVE

---

## 3.3 RNG Booking Flow

1. Customer selects service.
2. Weighted matching generates pool.
3. Customer selects companion.
4. Order → PENDING_PAYMENT
5. Customer pays.
6. Order → CONFIRMED
7. Session → ACTIVE

If no selection:
- All companions released
- Abandonment recorded

---

# 4. Session Execution Phase

When Session → ACTIVE:

- Chat becomes available.
- Timer or game counter begins.
- Companion state → IN_SESSION.

Execution differs by service type:

PER_GAME:
- Tracks games_completed.

TIME_BASED:
- Tracks time_consumed.

ONE_TIME:
- No timer.
- Single-action fulfillment.

---

# 5. Session End Conditions

A session may end due to:

- Normal completion
- Manual termination
- Early termination
- Flagging (admin intervention)

---

## 5.1 Normal Completion

- Session → ENDED
- Order → COMPLETED
- Chat disabled
- Companion returns to ONLINE_AVAILABLE (if still online)
- Customer prompted to rate and optionally tip

---

## 5.2 Early Termination

May occur due to:

- Customer ending session
- Companion ending session
- Technical interruption

System:
- Records consumed time or games
- Applies refund rules (if applicable)
- Order → COMPLETED or CANCELLED depending on context

Early termination rules are defined in:
`companion-early-termination.md`

---

## 5.3 Flagged Sessions

If flagged:

- Session → FLAGGED
- Order → FLAGGED
- Payout frozen
- Admin review required

---

# 6. Payment Lifecycle

Payment is escrow-based.

Rules:

- No session begins without payment confirmation.
- Scheduled booking requires companion approval before payment.
- RNG requires selection before payment.
- ONE_TIME services are paid instantly upon booking.

Refunds follow rule-based logic only.

---

# 7. Tipping & Post-Session

After Order → COMPLETED:

Customer may:

- Leave rating
- Leave optional tip

Tip distribution:
- 90% Companion
- 10% Platform

Tipping is not available for:
- CANCELLED orders
- FLAGGED sessions

---

# 8. Availability Transitions

Companion availability transitions across lifecycle:

ONLINE_AVAILABLE  
→ (Booked) → IN_SESSION  
→ (Completion) → ONLINE_AVAILABLE  

If scheduled:
ONLINE_AVAILABLE  
→ SCHEDULED  
→ ACTIVE at start time  
→ IN_SESSION  

If in RNG:
ONLINE_AVAILABLE  
→ SOFT_RESERVED  
→ (Selected) → IN_SESSION  
→ (Released) → ONLINE_AVAILABLE  

---

# 9. Abuse & Protection Integration

Throughout lifecycle:

- Abandonment detection protects supply.
- Weighted matching protects exposure fairness.
- Soft-reserve timers prevent lock abuse.
- Cooldowns prevent spam behavior.

These mechanisms operate independently of Order state but influence eligibility.

---

# 10. Lifecycle Summary

A Companion Order progresses through:

Creation  
→ Approval (if scheduled)  
→ Payment  
→ Confirmation  
→ Session Execution  
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