# COMPANION — SERVICE STRUCTURE (HYBRID)

Companion services follow a hybrid pricing model.

---

## PER_GAME Services (Fixed)

Defined by:
- game_type
- price_per_game

Customer selects:
- number_of_games

Total price = price_per_game × number_of_games

Session ends when:
- All games completed
- Or session manually ended

---

## TIME_BASED Services

Defined by:
- price_per_hour (internally calculated per minute)
- optional minimum duration

Customer selects:
- duration

Total price calculated upfront.

Timer starts when session becomes ACTIVE.

Session ends when:
- Time expires
- Or manually ended

```mermaid
flowchart TD
    A["Companion Service"] --> B["PER_GAME"]
    A --> C["TIME_BASED"]

    B --> D["Fixed price per game"]
    B --> E["Quantity selected"]
    B --> F["Ends when games completed"]

    C --> G["Price per hour"]
    C --> H["Duration selected"]
    C --> I["Ends when timer expires"]
```
