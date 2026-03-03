# 🧠 How Money Moves Technically Inside Our Database


This document explains how our database models money.

We will explain:

- What debit and credit mean (in simple terms)
    
- What asset, liability, revenue, and expense mean
    
- What the general_ledger table does
    
- What actor_balance_snapshot is
    
- What actor_balance_lock is
    
- What watermark_id is
    

---

# 1️⃣ The Core Idea: Our Database Tracks Ownership

Our system is built around one rule:

Every time money moves, we record:

• Where it came from  
• Where it went

That record lives in the **general_ledger** table.

The ledger is the "source of truth".  
Everything else (like wallet balances) is a fast summary built on top of it.

---

# 2️⃣ What Is Debit and Credit?

Forget traditional accounting definitions.

In our system:

- **Debit (DEBIT)** means: increase an asset OR decrease a liability
    
- **Credit (CREDIT)** means: increase a liability OR increase revenue
    

But the easier way to understand it:

Each money movement has two sides.

Example: Customer buys €10 coins

We insert TWO rows into general_ledger:

1. DEBIT → Cash account (1000)
    
2. CREDIT → Customer Coins Liability (2000)
    

That means:

- Our bank money increased
    
- Our obligation to the customer increased
    

Money did not magically appear.  
It changed ownership.

---

# 3️⃣ What Do Asset, Liability, Revenue, Expense Mean?

These are just categories of accounts.

## 🟢 Asset

Something we own.

Example:

- 1000 – Cash
    
- 1100 – Booster Receivable (booster owes us money)
    

Assets increase with DEBIT.

---

## 🔵 Liability

Money we owe to someone else.

Examples:

- 2000 – Customer Free Coins Liability
    
- 2100 – Customer Escrow Liability
    
- 2300 – Booster Payout Held
    

Liabilities increase with CREDIT.

If a liability increases, it means:  
We owe more money to someone.

---

## 🟣 Revenue

Money we earned because service was delivered.

Example:

- 4000 – Boost Revenue
    

Revenue increases with CREDIT.

Revenue only happens when real progress is delivered.

---

## 🔴 Expense

Money we spent or owe as cost.

Examples:

- 5000 – Booster Cost
    
- 5200 – Promo Cost
    
- 5300 – Chargeback Loss
    

Expenses increase with DEBIT.

---

# 4️⃣ What Is the general_ledger Table?

This is the heart of the system.

Each row represents one side of a money movement.

Important columns:

- transaction_id → groups the two sides together
    
- account_code → which account (Cash, Revenue, Liability, etc)
    
- direction → DEBIT or CREDIT
    
- amount → how much
    
- idempotency_key → prevents duplicates
    

Every financial event inserts exactly two rows:

DEBIT row  
CREDIT row

If both rows don’t exist, the transaction is invalid.

---

# 5️⃣ What Is actor_balance_snapshot?

This table is NOT the source of truth.

It is a **performance optimization layer**.

Instead of recalculating a user's balance from all ledger rows every time,  
we keep a running total.

For each actor (customer or booster), we store:

- free_coins_usd
    
- promo_coins_usd
    
- payout_held_usd
    
- payout_available_usd
    
- debt_usd
    

This is a cached summary of their financial position.

Think of it like a materialized view that we maintain manually.

The real truth is still in general_ledger.

---

# 6️⃣ What Is watermark_id?

Think of `watermark_id` as a "last processed event" pointer.

Our ledger is an append-only event log. Every financial change creates new rows with increasing IDs.

When we update `actor_balance_snapshot`, we remember the highest ledger ID that has already been applied to that snapshot.

Simple example:

- Snapshot has `watermark_id = 78`
    
- A new transaction creates ledger rows 79 and 80
    
- We apply those rows to the snapshot balances
    
- We update `watermark_id = 80`
    

Now the snapshot knows: "I have already processed everything up to 80."

If the same transaction is accidentally retried or replayed, the system checks the watermark. If the ledger ID is not greater than the current watermark, it will NOT apply the update again.

So in simple terms:

`watermark_id` prevents applying the same ledger event twice.

It guarantees that balances only move forward based on new events — never duplicated ones.

---

# 7️⃣ What Is actor_balance_lock?

This table exists for concurrency safety.

When we update someone’s financial state,  
we SELECT ... FOR UPDATE the actor_balance_lock row.

This ensures:

- Only one transaction can modify that actor’s balance at a time
    
- No race conditions
    
- No double updates
    
- No inconsistent balances
    

It is a row-level mutex implemented in the database.

It does not store money.  
It stores locking authority.

---

# 8️⃣ How a Coin Purchase Flows Through the System

Customer pays €10.

Step 1 – recordPayment (payments table)  
Step 2 – postPaymentToLedger inserts:

DEBIT 1000 Cash €10  
CREDIT 2000 Customer Liab €10

Step 3 – actor_balance_snapshot increases free_coins_usd by 10

Now:

- Bank money increased
    
- Customer balance increased
    
- Revenue = 0
    

---

# 9️⃣ Why We Separate Ledger and Snapshot

Ledger = immutable history.  
Snapshot = current balance view.

If snapshot is corrupted,  
we can always recompute it from ledger.

This makes the system resilient.

---

# 🔟 The Most Important Technical Identity

At all times:

# Total Assets

Total Liabilities  
+  
Total Revenue (minus expenses)

In simpler developer terms:

# Cash

Customer balances

- Booster balances
    
- Platform profit
    

If this identity breaks,  
our system is financially corrupted.

The ledger design prevents that.

---

# Final Mental Model

Think of the system like this:

- general_ledger = append-only financial event log
    
- actor_balance_snapshot = cached state
    
- actor_balance_lock = concurrency guard
    
- chart_of_accounts = account definitions
    

Money does not live in one table.

It flows through a controlled pipeline.

And every euro is traceable.