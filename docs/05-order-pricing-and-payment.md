# SECTION 5 — ORDER PRICING & PAYMENT

System calculates:
- Base order value (rank delta or net wins)
- Add-on costs
- Express multiplier (if selected)

Customer sees a fixed price.

## Diagram — Order Pricing & Payment Flow


```mermaid
flowchart TD
    Config["Order Configuration Complete"]
    Base["Calculate Base Value
    (Rank Delta or Net Wins)"]
    Addons["Add Add-on Costs"]
    Express["Apply Express Multiplier
    (if selected)"]
    Price["Final Fixed Price"]
    Pay["Customer Payment"]
    Escrow["Payment Held in Escrow"]

    Config --> Base
    Base --> Addons
    Addons --> Express
    Express --> Price
    Price --> Pay
    Pay --> Escrow
```