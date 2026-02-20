# COMPANION — DIRECT BOOKING WORKFLOW

This workflow applies to manual browsing and direct booking only.

RNG booking is documented separately.

Activation depends on service type.

---

## 1. Browse Companions

Customer can filter by:

- Online status  
- Service type  
- Price range  
- Rating  
- Language  

No chat is allowed before booking.

---

## 2. Booking Creation

Customer selects:

- Service  
- Quantity (game count or duration tier)  
- Booking type:
  - Instant
  - Scheduled  

System calculates total price.

---

# INSTANT BOOKING FLOW

Instant booking requires the companion to be:

- ONLINE_AVAILABLE  
- Not IN_SESSION  
- Not SOFT_RESERVED (RNG)  

---

## Instant Booking Steps

1. Order created → `PENDING_PAYMENT`
2. Customer pays
3. Order → `CONFIRMED`

Activation now depends on service type:

### If PER_GAME:
- Session → `ACTIVE`
- Companion state → `IN_SESSION`
- Chat enabled immediately

### If TIME_BASED:
- Session → `READY`
- 5-minute handshake window begins
- Companion remains `ONLINE_AVAILABLE`
- Session becomes `ACTIVE` only after both confirm
- Upon activation → Companion state → `IN_SESSION`

---

# SCHEDULED BOOKING FLOW

Scheduled booking requires companion approval before payment.

---

## 1. Request Creation

Customer selects:

- Future start time

System validates:

- No overlapping session
- No conflict with scheduled bookings

Order → `PENDING_COMPANION_APPROVAL`

No payment occurs at this stage.

---

## 2. Companion Decision

Companion can:

- ACCEPT  
- PROPOSE_NEW_TIME  
- DECLINE  

### If ACCEPT:
- Order → `PENDING_PAYMENT`
- Customer prompted to pay

### If PROPOSE_NEW_TIME:
- Customer can accept or decline new time
- Order remains `PENDING_COMPANION_APPROVAL`

### If DECLINE:
- Order → `CANCELLED`

---

## 3. Payment

After final time agreement:

1. Customer pays
2. Order → `CONFIRMED`
3. Session → `SCHEDULED`

---

## 4. Scheduled Session Activation

At scheduled start time:

### If PER_GAME:
- Session → `ACTIVE`
- Companion state → `IN_SESSION`
- Chat enabled

### If TIME_BASED:
- Session → `READY`
- 5-minute handshake window begins
- Companion remains `ONLINE_AVAILABLE`
- After successful handshake → `ACTIVE`
- Companion state → `IN_SESSION`

---

# Availability Rules (Direct Booking Only)

### ONLINE_AVAILABLE
- Can be booked instantly.
- Can receive scheduled requests.
- Remains ONLINE_AVAILABLE during READY phase.

### IN_SESSION
- Session is ACTIVE.
- Cannot be booked instantly.
- Can receive scheduled requests for future time.

### SCHEDULED
- Has future confirmed session.
- Cannot be booked instantly during overlapping time.
- Can receive additional scheduled requests outside overlapping windows.

### OFFLINE
- Cannot be booked instantly.
- May receive scheduled requests (if enabled).

---

## Diagram — Direct Booking Flow (Service-Type Aware)

```mermaid
flowchart TD

A["Customer Browses Companions"] --> B["Select Service"]
B --> C["Choose Quantity"]
C --> D{"Instant or Scheduled?"}

D -->|Instant| E["Create Order: PENDING_PAYMENT"]
E --> F["Customer Pays"]
F --> G["Order CONFIRMED"]

G --> H{"Service Type?"}

H -->|PER_GAME| I["Session ACTIVE"]
H -->|TIME_BASED| J["Session READY"]

J --> K{"Both Confirm?"}
K -->|Yes| I
K -->|No| L["Session CANCELLED"]

D -->|Scheduled| M["Create Order: PENDING_COMPANION_APPROVAL"]
M --> N{"Companion Decision"}

N -->|Accept| O["Order PENDING_PAYMENT"]
O --> P["Customer Pays"]
P --> Q["Order CONFIRMED"]
Q --> R["Session SCHEDULED"]

R --> S["At Scheduled Time"]
S --> T{"Service Type?"}

T -->|PER_GAME| I
T -->|TIME_BASED| J

N -->|Propose New Time| M
N -->|Decline| U["Order CANCELLED"]
```
