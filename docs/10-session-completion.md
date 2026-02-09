# SECTION 10 — SESSION COMPLETION

## 10.1 Successful Completion

Triggered when:
- Target rank + LP reached
OR
- Net wins fulfilled

System actions:
- Session → COMPLETED
- Chat disabled
- Final payout released
- Order → COMPLETED

## Diagram — Successful Session Completion

```mermaid
flowchart TD
    A["Session IN_PROGRESS"] --> B{"Completion condition met?"}

    B -->|Target rank + LP reached| C["Session COMPLETED"]
    B -->|Net wins fulfilled| C

    C --> D["Chat disabled"]
    D --> E["Final payout released"]
    E --> F["Order COMPLETED"]
```
