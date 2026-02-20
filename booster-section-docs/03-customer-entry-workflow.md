# SECTION 3 — CUSTOMER ENTRY WORKFLOW

## Requirements
- Customer must be logged in
- CUSTOMER role must be ACTIVE

## Customer chooses
- RNG Boost
- Custom Boost

## Diagram — Customer Entry Decision

```mermaid
flowchart TD
    Customer[Customer Logged In<br/>CUSTOMER role ACTIVE]
    Choice{Choose Boost Type}

    Customer --> Choice
    Choice --> RNG[RNG Boost]
    Choice --> Custom[Custom Boost]
```
