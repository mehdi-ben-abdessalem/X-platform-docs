# WEIGHTED RANDOM MATCHING SYSTEM

This document explains how the RNG Companion system selects companions.

The goal is to balance:

- Service quality (rating)
- Marketplace fairness (exposure balancing)
- Operational reliability
- Supply health

RNG selection is not purely random and not purely rating-based.

It is weighted and balanced.

---

## 1. Eligibility Filtering

Before weighted selection occurs, companions must meet all conditions:

- ONLINE_AVAILABLE
- Service enabled (PER_GAME or TIME_BASED)
- Not currently in session
- Not already reserved in another RNG pool
- Not exposure-capped

Only eligible companions enter the weighting phase.

---

## 2. Weighted Selection Model

Each eligible companion is assigned a selection weight.

The weight is calculated using multiple factors:

- Rating score influence
- Exposure balancing boost
- Acceptance reliability score

Example conceptual formula:

selection_weight =
  ( rating_normalized * 0.5 ) +
  ( low_exposure_boost * 0.3 ) +
  ( acceptance_reliability * 0.2 )

Weights are probabilistic, not deterministic.

Higher weight increases probability of selection,
but does not guarantee selection.

---

## 3. Rating Influence

Ratings influence visibility but do not dominate it.

Higher-rated companions:

- Receive increased probability
- Justify higher pricing
- Improve overall user experience

However, rating alone does not control exposure.

This prevents monopoly effects.

---

## 4. Exposure Balancing

The system tracks:

- RNG appearances
- RNG selections
- RNG acceptance rate

Companions with high recent exposure:

- May have weight temporarily reduced

Companions with low exposure:

- May receive weight boost

This ensures:

- Fair distribution
- Long-term supply health
- Reduced starvation of new companions

---

## 5. Acceptance Reliability

Companions who:

- Frequently accept RNG
- Rarely abandon
- Maintain stable online presence

Receive reliability scoring.

Unreliable companions may receive reduced weight.

This ensures:

- Faster pool formation
- Better user experience
- Reduced dead pools

---

## 6. Selection Process

After weights are assigned:

- System performs weighted random sampling
- Up to 6 companions are selected
- Display order is shuffled

The display order is not sorted by:

- Rating
- Price
- Exposure

This prevents visual bias and preserves fairness.

---

## 7. Admin Visibility

Admins can monitor:

- RNG exposure distribution
- Revenue distribution
- Selection fairness
- Rating concentration

Metrics are internal only.

Companions and customers do not see weighting details.

---

## 8. System Goals

The weighted random system aims to:

- Promote top performers
- Protect mid-tier companions
- Give new companions opportunity
- Prevent dominance loops
- Maintain liquidity

This system may be adjusted over time to preserve marketplace balance.