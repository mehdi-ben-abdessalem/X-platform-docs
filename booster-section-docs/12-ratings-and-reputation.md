# SECTION 12 — RATINGS & REPUTATION

After STOPPED or COMPLETED sessions:
- Customer rates booster
- Booster rates customer

Ratings affect:
- Booster visibility
- RNG eligibility
- Customer trust score

## Diagram — Ratings & Reputation Impact

```mermaid
flowchart TD
    A["Session STOPPED or COMPLETED"] --> B["Ratings submitted"]

    B --> C["Update booster rating"]
    B --> D["Update customer trust score"]

    C --> E["Affects booster visibility"]
    C --> F["Affects RNG eligibility"]

    D --> G["Affects future order trust"]

```