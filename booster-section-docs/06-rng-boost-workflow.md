# SECTION 6 — RNG BOOST WORKFLOW

## 6.1 RNG Order Creation (Customer)
- Customer selects RNG Boost
- Customer pays upfront
- Order created → ACTIVE
- Session #1 created:
  - assignment_mode = RNG
  - session.state = REQUESTED
- Payment held in escrow

## 6.2 RNG Booster Assignment

### Eligible boosters
- BOOSTER role ACTIVE
- ONLINE_AVAILABLE
- Match rank, region, add-ons

### System behavior
All eligible boosters receive:
- Sound notification
- Popup with order summary
- Payout amount
- Express flag (if any)

## 6.3 RNG Acceptance
- First booster to click ACCEPT wins
- Backend applies atomic lock
- Booster assigned
- Session → IN_PROGRESS
- Booster → ONLINE_IN_SESSION
- Chat enabled

## 6.4 RNG Session Execution
- Booster plays games
- System tracks rank, LP, wins
- Add-ons enforced
- Chat allowed only while IN_PROGRESS

## 6.5 RNG Session End

### COMPLETED
- Goal achieved
- Session → COMPLETED
- Chat disabled
- Payout released
- Order → COMPLETED

### STOPPED / FLAGGED / CANCELLED
Handled via stop & penalty logic (see Section 8)

## ## Diagram — RNG Boost End-to-End Flow

```mermaid
flowchart TD
    A["Customer selects RNG Boost"] --> B["Customer pays upfront"]
    B --> C["Order created (ACTIVE)"]
    C --> D["Session created (REQUESTED, assignment_mode = RNG)"]
    D --> E["System finds eligible boosters"]

    E --> F["Notify all ONLINE_AVAILABLE boosters"]
    F --> G{"Any booster clicks ACCEPT?"}

    G -->|First accept| H["Atomic lock applied"]
    H --> I["Booster assigned"]
    I --> J["Session state = IN_PROGRESS"]

    J --> K["Booster plays games"]
    K --> L["System tracks rank, LP, wins"]

    L --> M{"Goal achieved?"}

    M -->|No| J
    M -->|Yes| N["Session COMPLETED"]

    N --> O["Chat disabled"]
    O --> P["Payout released"]
    P --> Q["Order COMPLETED"]

    J --> R["Session STOPPED / FLAGGED / CANCELLED"]
    R --> S["Handle via stop & penalty logic (Section 8)"]
```
