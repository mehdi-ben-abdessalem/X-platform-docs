# SECTION 1 — ENTITIES & STATES

## 1.1 Order (Persistent)

### What an Order represents
- The full boost request from start to finish
- Can span multiple sessions with different boosters

### Order data
- Customer ID
- Initial rank + LP
- Current rank + LP (mutable)
- Desired rank OR desired net wins
- Queue type (Solo / Flex)
- Execution mode (Duo / Account)
- Selected add-ons
- Unit pricing model (LP / win value)
- Total paid so far
- Order state

### Order states

ACTIVE  
COMPLETED  
CANCELLED

---

## 1.2 Session (Per Attempt)

### What a Session represents
- One booster working on the order
- Can succeed, fail, pause, or stop

### Session data
- Order ID
- Booster ID
- Start rank + LP
- End rank + LP
- Progress delta
- Session payout or penalty
- Session state
- Scheduled time (optional)

### Session states

REQUESTED  
ACCEPTED  
IN_PROGRESS  
PAUSED  
STOPPED  
COMPLETED  
FLAGGED

---

## 1.3 Booster Availability States

Boosters are always in exactly one of these states:

OFFLINE  
ONLINE_AVAILABLE  
ONLINE_IN_SESSION  
ONLINE_PAUSED

Only ONLINE_AVAILABLE boosters can receive work.
