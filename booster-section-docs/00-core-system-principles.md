# SECTION 0 — CORE SYSTEM PRINCIPLES (IMMUTABLE)

These principles apply to every workflow below.

## Order ≠ Session
- An Order represents the customer’s intent and final goal
- A Session is one booster’s attempt on that order

## Boosting is probabilistic
- Wins and losses are both valid outcomes
- Progress (positive or negative) is always counted

## Rule-first & math-first
- No emotional refunds
- No manual judgment unless a session is flagged

## Chat is session-bound
- Chat exists only while a session is IN_PROGRESS
- Chat is disabled immediately when a session ends, pauses, or stops
- Messages are permanently logged for admins
## Diagram — Order vs Session

```mermaid
flowchart TD
    Order[Order<br/>Customer Intent & Final Goal]
    Session1[Session #1<br/>Booster A]
    Session2[Session #2<br/>Booster B]
    Session3[Session #3<br/>Booster C]

    Order --> Session1
    Order --> Session2
    Order --> Session3

    Session1 -->|Progress| Order
    Session2 -->|Progress| Order
    Session3 -->|Progress| Order
```
