# SECTION 9 — RESCHEDULING WORKFLOW

## 9.1 Reschedule Process
- Either party requests reschedule
- Both agree on date & time
- Session → PAUSED
- Chat disabled
- Booster availability blocked only for that time window

Outside that window, booster may accept other orders.

## 9.2 Reschedule Failure
- Booster no-shows → STOPPED, booster penalized
- Customer no-shows → STOPPED, no booster penalty

Order continues.

## Diagram — Rescheduling Workflow

```mermaid
flowchart TD
    A["Session IN_PROGRESS or REQUESTED"] --> B{"Reschedule requested?"}

    B -->|Customer| C["Reschedule request sent"]
    B -->|Booster| C

    C --> D["Proposed date & time"]
    D --> E{"Both parties agree?"}

    E -->|Yes| F["Session state = PAUSED"]
    F --> G["Chat disabled"]
    G --> H["Booster blocked only for scheduled window"]

    H --> I["Scheduled time arrives"]
    I --> J{"Who no-shows?"}

    J -->|No one| K["Session resumes"]
    K --> L["Session state = IN_PROGRESS"]

    J -->|Booster| M["Session STOPPED"]
    M --> N["Booster penalized"]

    J -->|Customer| O["Session STOPPED"]
    O --> P["No booster penalty"]
```
