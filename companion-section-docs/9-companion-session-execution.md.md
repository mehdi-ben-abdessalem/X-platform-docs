# COMPANION — SESSION EXECUTION

When a session becomes ACTIVE:

- Chat is enabled
- Companion state → IN_SESSION

---

## PER_GAME Execution

- System tracks completed games
- Counter increases after each finished game
- Session ends when all games are completed

---

## TIME_BASED Execution

- Timer starts at session activation
- Timer counts down
- Session ends automatically when time expires
- Session may also end manually

```mermaid
flowchart TD
    A["Session ACTIVE"] --> B{"Pricing Model?"}

    B -->|PER_GAME| C["Track Completed Games"]
    C --> D{"All Games Completed?"}
    D -->|Yes| E["Session ENDED"]
    D -->|No| C

    B -->|TIME_BASED| F["Start Timer"]
    F --> G{"Time Expired?"}
    G -->|Yes| E
    G -->|No| F
```
