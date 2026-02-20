# SECTION 11 — ADMIN & SAFETY

## 11.1 Flagged Sessions

Sessions may be flagged for:
- Misconduct
- Cheating
- Rank fraud

### Effects
- Session → FLAGGED
- Chat disabled
- Payout frozen

Admins may:
- Review logs
- Apply penalties
- Update role states

All admin actions are logged.

## Diagram — Flagged Session Handling

```mermaid
flowchart TD
    A["Session active or stopped"] --> B{"Session flagged?"}

    B -->|Yes| C["Session state = FLAGGED"]
    C --> D["Chat disabled"]
    D --> E["Payout frozen"]

    E --> F["Admin reviews logs"]
    F --> G["Admin decision applied"]

    G --> H["Apply penalties or corrections"]
    G --> I["Update session or user states"]
```
