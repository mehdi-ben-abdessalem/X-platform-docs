# COMPANION — BOOKING WORKFLOW

## 1. Browse Companions

Customer can filter by:
- Online status
- Service type
- Price range
- Rating
- Language

No chat allowed before booking.

---

## 2. Booking Creation

Customer selects:
- Service
- Quantity (games or duration)
- Instant or Scheduled

System calculates total price.

Order state → PENDING_PAYMENT

Customer pays → Order state → CONFIRMED

---

## 3. Session Creation

If booking is Instant:
- Session state → ACTIVE immediately

If booking is Scheduled:
- Session state → SCHEDULED
- Session becomes ACTIVE at scheduled time

```mermaid
flowchart TD
    A["Customer Browses Companions"] --> B["Select Service"]
    B --> C["Choose Quantity (Games or Time)"]
    C --> D["Order Created (PENDING_PAYMENT)"]
    D --> E["Customer Pays"]
    E --> F["Order CONFIRMED"]

    F --> G{"Instant or Scheduled?"}

    G -->|Instant| H["Session ACTIVE"]
    G -->|Scheduled| I["Session SCHEDULED"]
    I --> J["At Scheduled Time → Session ACTIVE"]
```
