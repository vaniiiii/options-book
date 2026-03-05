# Delta Deep Dive

## Three Jobs, One Number

**Delta** measures how much an option's price changes when the underlying stock moves $1. But that one-line definition hides a bigger story: delta quietly holds down three jobs at once.[^1][^2]

It is a **price sensitivity** — how much your option reacts to the stock. It is a **hedge size** — how many shares offset your option risk. And it is an approximate **probability gauge** — how likely the option is to finish in the money.

When we need exact numbers in this module, we use a standard pricing calculator called **Black-Scholes**. You can think of it as a function: give it stock price, strike, time, volatility, and rates; it returns theoretical option price and Greeks. We will use the outputs now and unpack model assumptions in a later module.

Understanding all three — and knowing where each interpretation breaks down — is what separates someone who can read an options chain from someone who can trade one. Let's take delta apart.

### Delta as a Derivative (No Mystery)

If you are comfortable with calculus, this should feel familiar.

Option value is a function of stock price:

\\[
V = f(S)
\\]

Delta is the first derivative of that function with respect to stock price:

\\[
\Delta = \frac{\partial V}{\partial S}
\\]

So for a small stock move:

\\[
dV \approx \Delta \, dS
\\]

This is why traders care about delta: it is the best local linear approximation of option price movement around "right now."

<video controls width="100%"><source src="media/delta-deep-dive/DeltaAsDerivative.mp4" type="video/mp4">Your browser does not support video.</video>

### Modeling Assumptions Used in This Module

To keep comparisons clean, all computed tables and code examples use one consistent Black-Scholes setup unless explicitly noted:

- Spot price `S = $100`
- Risk-free rate `r = 5%`
- No dividends
- European exercise convention

Real U.S. equity options are usually American-style and many stocks pay dividends, so live-chain deltas can differ slightly from these model values. We will unpack those differences in later modules.

## Prerequisites

This module builds on everything in Modules 1 through 4. You should be comfortable with:

- **Calls and puts**, buyer and seller roles, payoff diagrams (Module 1)
- **Moneyness** (ITM, ATM, OTM), intrinsic value, time value (Module 2)
- **Why volatility, time, and rates** drive option prices (Module 3)
- **All five Greeks at survey level** — especially delta's sign convention, share equivalence, and the "delta ≈ probability of ITM" heuristic (Module 4)

If any feel shaky, revisit before continuing. This module assumes that foundation and goes deeper.

## The Delta S-Curve

In Module 4, we gave you three data points: deep OTM delta is near 0, ATM delta is near 0.50, deep ITM delta is near 1.0. Now let's see the full picture.

Plot delta against the stock price and you get a characteristic **S-shaped curve** — a smooth sigmoid that sweeps from nearly 0 on the left to nearly 1.0 on the right.[^1][^3]

<video controls width="100%"><source src="media/delta-deep-dive/DeltaSCurveSweep.mp4" type="video/mp4">Your browser does not support video.</video>

Think of a dimmer switch in a room. At the very bottom of the dial — lights nearly off — each click barely brightens the room. At the very top — lights already blazing — each click is imperceptible. But in the middle range, every small turn produces a noticeable jump in brightness.

Delta behaves the same way. In the middle of the S-curve — right at the money — delta changes fastest with every dollar the stock moves. At the extremes, delta is stubborn.

On the option-price graph itself (option price on Y-axis, stock price on X-axis), delta is the slope of the tangent line at your current stock price. If delta is 0.60, a small $1 stock move maps to roughly a $0.60 option move near that point:

\\[
dV \approx 0.60 \, dS
\\]

Because that slope changes as you move along the curve, delta is not constant. That "change in slope" is gamma:

\\[
\Gamma = \frac{\partial^2 V}{\partial S^2}
\\]

Module 6 explores gamma mechanics in depth.[^3][^4]

### Why ATM Delta Sits Near 0.50

There is nothing magical about 0.50. It reflects a simple symmetry: an ATM option has roughly equal odds of finishing above or below the strike. Until the stock moves decisively, delta sits near the middle of its range.[^1][^5]

A nuance worth flagging: the exact 0.50-delta strike is not always the at-the-money strike. For volatile stocks, the 0.50-delta strike can sit slightly above the current price because of how the distribution of future returns is shaped. For most practical purposes, "ATM ≈ 0.50 delta" is fine.[^5]

### Worked Numbers: ACME Across Five Strikes

Let's continue with ACME stock at $100 and look at call options with 30 days to expiration (IV = 30%). These are exact Black-Scholes values — no rounding:

| Strike | Moneyness | Call Delta | What It Means |
|--------|-----------|-----------|--------------|
| $80    | Deep ITM  | 0.996     | Moves essentially dollar-for-dollar with stock |
| $90    | ITM       | 0.906     | Captures 91¢ of each $1 stock move |
| $100   | ATM       | 0.536     | Captures about half the stock's move |
| $110   | OTM       | 0.154     | Barely responds to small stock moves |
| $120   | Deep OTM  | 0.021     | Almost unresponsive — needs a massive rally |

The steepest change is between the $90 and $110 strikes — the zone around ATM where the S-curve is steepest and gamma is highest.[^1][^3]

*The reader is encouraged to pull up a real options chain and verify this S-curve pattern. Pick any liquid stock and scan call deltas from deep ITM to deep OTM. The sigmoid shape is universal.*

## How Time Reshapes the Curve

The S-curve is not static. As expiration approaches, it transforms.

With 90 days left, the curve is a gentle slope — deltas change gradually across strikes. With 7 days left, the curve steepens dramatically. At expiration itself, the curve becomes a **step function**: delta is exactly 1.0 for any option finishing ITM, and exactly 0.0 for anything finishing OTM.[^6][^7]

Think of developing a photograph in a darkroom. When you first dip the paper into the chemical bath, the image is a gray blur — you cannot tell what the picture will show. As the chemicals work, details gradually emerge. The subject's outline sharpens. And at the very end, the image snaps into crisp focus — the subject is either there or it is not.

Time acts on delta the same way. Far from expiry, deltas are blurry — many outcomes are possible. As expiration approaches, the picture sharpens. ITM options become "certain" to stay ITM (delta → 1.0). OTM options become "certain" to expire worthless (delta → 0.0).[^6][^7]

Here is the evidence — the same five strikes at three different times to expiry:

| Strike | 90 Days | 30 Days | 7 Days | At Expiry |
|--------|---------|---------|--------|-----------|
| $80 (Deep ITM) | 0.951 | 0.996 | 1.000 | 1.000 |
| $90 (ITM)      | 0.806 | 0.906 | 0.995 | 1.000 |
| $100 (ATM)     | 0.562 | 0.536 | 0.517 | 0 or 1 |
| $110 (OTM)     | 0.315 | 0.154 | 0.012 | 0.000 |
| $120 (Deep OTM)| 0.143 | 0.021 | 0.000 | 0.000 |

Look at the $110 call. At 90 days, delta is 0.315 — a real chance. At 7 days, delta is 0.012 — effectively dead. And the stock never moved. Time alone collapsed the delta.[^6][^7]

<video controls width="100%"><source src="media/delta-deep-dive/TimeCollapseToStep.mp4" type="video/mp4">Your browser does not support video.</video>

### The Direction Depends on Moneyness

Here is the subtle part. More time does *not* always mean higher delta:

- **ITM calls:** delta increases as time passes (less time for the stock to fall back below the strike)
- **OTM calls:** delta decreases as time passes (less time for the stock to rally above the strike)
- **ATM calls:** delta stays near 0.50 until the very end, then snaps to 0 or 1[^6][^7][^8]

For OTM options, time passing is devastating. Your delta is evaporating alongside your time value.

## How Volatility Reshapes the Curve

Volatility acts on the delta curve in the same direction as time — but in reverse.

**Higher implied volatility flattens the curve**, pushing all deltas toward 0.50. **Lower implied volatility sharpens the curve**, pushing ITM deltas toward 1.0 and OTM deltas toward 0.[^9][^10]

Imagine a foggy morning. On a clear day, you can easily distinguish the nearby trees (ITM, high delta) from the ones on the distant ridge (OTM, low delta). The distinction is sharp.

Now thick fog rolls in. Every tree at every distance looks equally hazy. You cannot separate near from far. All your estimates blur toward the middle.[^9]

That is what high volatility does to delta. More uncertainty means more outcomes are plausible, so the market cannot strongly distinguish ITM from OTM. The delta curve flattens.

Here is the same ACME call chain at two volatility levels (30 days to expiry):

| Strike | IV = 20% | IV = 50% | Change |
|--------|----------|----------|--------|
| $80 (Deep ITM) | 1.000 | 0.951 | −0.049 (delta fell) |
| $90 (ITM)      | 0.974 | 0.798 | −0.176 (delta fell) |
| $100 (ATM)     | 0.540 | 0.540 | 0.000 (unchanged)  |
| $110 (OTM)     | 0.059 | 0.286 | +0.227 (delta rose) |
| $120 (Deep OTM)| 0.001 | 0.121 | +0.120 (delta rose) |

The stock did not move. Only the market's *expectation of future movement* changed — and that alone shifted the $110 call's delta from 0.06 to 0.29.[^9][^10]

<video controls width="100%"><source src="media/delta-deep-dive/VolatilityFlattensDelta.mp4" type="video/mp4">Your browser does not support video.</video>

*The reader is encouraged to estimate: if IV dropped from 50% back to 20%, what would happen to an OTM call you held? Your delta would collapse — and with it, your directional exposure — even if the stock stays flat.*

### The Mirror Between Time and Volatility

Low volatility and short time to expiry both *sharpen* the delta curve. High volatility and long time to expiry both *flatten* it. Both represent the same force: **uncertainty**.[^10][^11]

More uncertainty — whether from volatile prices or distant expiration — means more possible outcomes. Deltas cluster near the middle. As uncertainty resolves, deltas migrate to their extremes.

The second-order Greek **vanna** (dDelta/dIV) quantifies how delta changes with volatility. Similarly, **charm** (dDelta/dTime) quantifies how delta drifts with time passing. We mention them here as previews — Module 8 and later modules will explore them.[^12]

## Put Delta and the Mirror Relationship

Everything above describes call delta. What about puts?

At any given strike and expiration, the relationship is:

**Put delta = Call delta − 1**[^13][^14]

If the call has delta +0.70, the put at the same strike has delta −0.30. The absolute values always sum to 1.00.

| Strike | Call Delta | Put Delta | |Call Δ| + |Put Δ| |
|--------|-----------|-----------|---------------------|
| $80    | +0.996    | −0.004    | 1.000               |
| $90    | +0.906    | −0.094    | 1.000               |
| $100   | +0.536    | −0.464    | 1.000               |
| $110   | +0.154    | −0.846    | 1.000               |
| $120   | +0.021    | −0.979    | 1.000               |

Think of a seesaw. If one side is tilted 70% of the way up, the other side must be 30% of the way down. They are mechanically linked — one cannot move without the other adjusting.

<video controls width="100%"><source src="media/delta-deep-dive/CallPutDeltaMirror.mp4" type="video/mp4">Your browser does not support video.</video>

This follows from **put-call parity** — the fundamental pricing relationship between calls and puts at the same strike. The formal proof uses calculus (Module 10 will cover put-call parity in depth). In the core European/non-dividend setup, the key result is delta_call − delta_put = 1.[^13][^14]

**Important qualifier:** This relationship holds exactly for European options on non-dividend-paying stocks. For American-style options or dividend-paying stocks, it is approximate — typically within a few hundredths. Your "Try It Yourself" verification may show small deviations for this reason.[^13]

**Practical benefit.** You never need to look up put deltas separately. Know the call delta at any strike and you instantly know the put delta. This also explains why a **protective put** (long stock + long put) behaves like a synthetic call — their net deltas match.[^14][^15]

*The reader is encouraged to verify: pick any strike in a real options chain and add the absolute values of the call and put deltas. The sum should be very close to 1.00.*

## Delta as Hedge Size

So far, we have used delta to predict how our option moves. Now let's use it to *cancel* that movement entirely.

This is delta's second job: the **hedge size**. Delta tells you exactly how many shares to hold to offset the directional risk of an option position.[^16][^17]

Suppose you are a market maker. A customer buys 10 ACME $100 calls from you. You are now short 10 calls with delta 0.54 — your position delta is −540 (equivalent to being short 540 shares). Every $1 rally costs you roughly $540.

**Solution:** Buy 540 shares of ACME. Stock delta: +540. Option delta: −540. Net delta: **zero**. A $1 stock move does not affect your portfolio (to a first approximation). This is a **delta-neutral** portfolio.[^16][^17]

### Dynamic Hedging — The Autopilot

There is a catch. Delta changes with every tick, every passing hour, and every volatility shift. Your hedge drifts out of alignment constantly.

Think of the autopilot on a sailboat. You set your heading to due north (delta = 0). But wind shifts, currents push, waves jostle. The autopilot makes constant small steering corrections — overshooting slightly, then correcting — to maintain the heading. In calm waters it barely intervenes. In rough seas (high gamma), it works frantically.[^16][^18]

**Dynamic hedging** works the same way. The market maker monitors delta and rebalances continuously — buying more shares when delta drifts negative, selling when it drifts positive.[^17][^18]

<video controls width="100%"><source src="media/delta-deep-dive/DynamicDeltaHedgeLoop.mp4" type="video/mp4">Your browser does not support video.</video>

**What delta hedging does NOT remove.** Delta-neutral eliminates first-order directional risk only. Gamma, theta, and vega exposures remain. The market maker is not risk-free — they still face losses from large sudden moves (gamma), time decay (theta), and volatility swings (vega).[^16][^17]

There is a flip side worth previewing: if you are delta-neutral and *long* gamma, you can actually *profit* from rebalancing. Each rebalance locks in small gains from the stock oscillating. This is called **gamma scalping** — the subject of Module 6.[^19]

*Before reading on, think: after you sell options and delta-hedge, which Greek determines how often you need to rebalance? (Answer: gamma — the higher the gamma, the faster delta drifts.)*

### Worked Hedging Example

You sold 10 ACME $100 calls (delta 0.54, gamma 0.04). You bought 540 shares to hedge.

**Day 1:** Stock rises $2 to $102. Gamma shifts delta from 0.54 to approximately 0.62 (0.54 + 2 × 0.04). Your option position delta is now −620. You hold +540 shares. Net delta: −80.

**Rebalance:** Buy 80 more shares. New stock: +620. Net delta: 0.

**Day 2:** Stock drops $1 to $101. Delta shifts from 0.62 to approximately 0.58. Option delta: −580. Stock: +620. Net delta: +40.

**Rebalance:** Sell 40 shares. Net delta: 0.

Each trade costs commissions and crosses the bid-ask spread. Professional market makers may rebalance dozens of times per day in liquid markets.[^17][^18]

## Portfolio Delta and Dollar Delta

Delta is additive. Sum deltas across all positions on the same stock to get one number — your **portfolio delta** — expressing total directional exposure in equivalent shares.[^20][^21]

**Position delta formula:**

Position delta = option delta × 100 × number of contracts × direction (+1 long, −1 short)

**Multi-position example.** You hold three ACME positions:
- Long 5 calls, delta 0.60 → +300 delta
- Short 3 calls, delta 0.30 → −90 delta
- Long 2 puts, delta −0.40 → −80 delta

**Net portfolio delta: +130** — equivalent to owning 130 shares of ACME.

*The reader is encouraged to verify: what happens to the net delta if you add a short position of 1 put contract at delta −0.50? (Answer: short a put = +50 delta, so new net is +180.)*

### Dollar Delta — The Missing Context

Raw delta has a blind spot. Being "+100 delta" on a $5 stock means $500 of exposure. Being "+100 delta" on a $500 stock means $50,000 of exposure. Same delta, very different risk.

**Dollar delta** fixes this: dollar delta = position delta × stock price.[^22]

| Position | Delta | Stock Price | Dollar Delta |
|----------|-------|-------------|-------------|
| 10 contracts, 0.30 delta | +300 | $50 | $15,000 |
| 2 contracts, 0.30 delta  | +60  | $500 | $30,000 |

The second position has one-fifth the raw delta but *double* the economic exposure. Dollar delta reveals what raw delta hides.[^22]

When comparing positions across different stocks, **beta-weighted delta** goes further — it adjusts each position's delta by the stock's beta relative to a common benchmark (typically SPY). This lets you express your entire portfolio as a single number: "I'm long 200 SPY-equivalent delta." More on this in advanced portfolio modules.[^21]

### Covered Calls, Protective Puts, and Strike Selection

Delta makes common strategies transparent:

**Covered call** = long 100 shares (delta +100) + short 1 call (−delta × 100).
- Sell a 0.30-delta call: net delta = 100 − 30 = **+70**. You have reduced directional exposure by 30%.[^15][^23]
- Sell a 0.15-delta call: net delta = 100 − 15 = **+85**. Less premium collected, but you keep more upside.

**Protective put** = long 100 shares (delta +100) + long 1 put (delta × 100, negative).
- Buy a −0.50-delta put: net delta = 100 − 50 = **+50**. You have cut exposure in half.[^15][^23]

Traders use delta as a **strike selection tool**. Selling a 0.30-delta covered call is a common starting point — it collects meaningful premium while leaving room for the stock to rise. Selling a 0.16-delta call is often used as a "lower assignment-risk" choice. As always, treat delta as a pricing sensitivity first and only a rough probability proxy. The choice comes down to the trade-off between premium income and upside participation.[^23]

## Delta vs Probability — Where the Shortcut Breaks

Module 4 introduced the heuristic: "delta approximates the probability of finishing ITM." Now let's see where it breaks.

For call options in the Black-Scholes framework, the key insight is: **delta overstates the probability of finishing in the money.** For calm stocks with short-dated options, the overstatement is tiny. For volatile stocks with long-dated options, the gap can be enormous.[^24][^25]

Model shorthand used below: for calls, \(\Delta = N(d_1)\). For puts, \(\Delta = N(d_1)-1\).

Why? Delta is a hedge-size number, not a probability meter. When deciding how many shares to hedge, the market cares about two things: *whether* the option finishes ITM and *by how much*. A lottery ticket analogy helps: if you are insuring a lottery, you need to know not just the odds of winning (probability) but also the jackpot size (magnitude). Delta bundles both together. Pure probability does not.[^25]

Here is the gap in practice, computed for an ATM call (S = K = $100):

| Scenario | IV | Time | Delta | Prob ITM | Gap |
|----------|-----|------|-------|----------|-----|
| Calm, short-dated | 15% | 7 days | 0.523 | 0.514 | 0.009 |
| Moderate | 30% | 30 days | 0.536 | 0.502 | 0.034 |
| Volatile, long-dated | 60% | 1 year | 0.649 | 0.414 | 0.235 |

That last row is striking. A 0.65-delta LEAPS call has only a 41% chance of finishing ITM — a gap of 24 percentage points. For a biotech with even higher vol, the divergence can be more extreme: the brief notes cases where a 0.50-delta call has only ~10% probability of finishing ITM.[^5][^24]

**A technical note for the curious.** In the Black-Scholes model, delta equals N(d1) while the model-implied ITM probability is N(d2), where d2 = d1 − σ√T. Since σ√T is always positive, d1 > d2, and so N(d1) > N(d2). The gap grows with σ√T. One further caveat: both N(d1) and N(d2) are **risk-neutral** quantities — they assume investors are indifferent to risk. Real-world probabilities may differ. We will revisit this distinction in Module 13 (Black-Scholes Assumptions).[^24][^25]

**Practical takeaway.** The delta-probability shortcut works well for short-dated options on stocks with normal volatility. Do not build a trading thesis on "my delta says 70% chance of profit" — especially for LEAPS and high-vol names.[^5][^24]

<video controls width="100%"><source src="media/delta-deep-dive/DeltaVsProbabilityGap.mp4" type="video/mp4">Your browser does not support video.</video>

## Seeing It in Code

We can compute the delta S-curve across strikes and watch time reshape it. The point is not "coding for coding's sake" — it is to numerically verify the intuition from the earlier sections. This code uses only Python's built-in `math` module:

```python
import math

def norm_cdf(x):
    """Cumulative normal distribution using math.erf."""
    return 0.5 * (1 + math.erf(x / math.sqrt(2)))

def call_delta(S, K, T, sigma, r=0.05):
    """Black-Scholes call delta = N(d1)."""
    d1 = (math.log(S / K) + (r + 0.5 * sigma**2) * T) / (sigma * math.sqrt(T))
    return norm_cdf(d1)

# ACME stock at $100, IV = 30%
S, sigma = 100, 0.30

for days in [90, 30, 7]:
    T = days / 365
    print(f"\n--- {days} days to expiration ---")
    for K in [80, 90, 100, 110, 120]:
        d = call_delta(S, K, T, sigma)
        print(f"  Strike ${K}: delta = {d:.3f}")

# Output:
# --- 90 days to expiration ---
#   Strike $80: delta = 0.951
#   Strike $90: delta = 0.806
#   Strike $100: delta = 0.562
#   Strike $110: delta = 0.315
#   Strike $120: delta = 0.143
#
# --- 30 days to expiration ---
#   Strike $80: delta = 0.996
#   Strike $90: delta = 0.906
#   Strike $100: delta = 0.536
#   Strike $110: delta = 0.154
#   Strike $120: delta = 0.021
#
# --- 7 days to expiration ---
#   Strike $80: delta = 1.000
#   Strike $90: delta = 0.995
#   Strike $100: delta = 0.517
#   Strike $110: delta = 0.012
#   Strike $120: delta = 0.000
```

The step-function collapse is unmistakable. The $110 call goes from 0.315 (90 days) to 0.012 (7 days) — nearly dead — without the stock moving a cent. Meanwhile, ATM barely budges (0.562 → 0.536 → 0.517).[^6][^7]

Now let's verify that delta overstates probability. We compute both N(d1) (delta) and N(d2) (probability of ITM) for the same ATM option under three volatility-time scenarios:

```python
def delta_vs_prob(S, K, T, sigma, r=0.05):
    """Return N(d1) and N(d2) for comparison."""
    d1 = (math.log(S / K) + (r + 0.5 * sigma**2) * T) / (sigma * math.sqrt(T))
    d2 = d1 - sigma * math.sqrt(T)
    return norm_cdf(d1), norm_cdf(d2)

scenarios = [
    ("Low vol, 7 days",   0.15,   7/365),
    ("Moderate, 30 days", 0.30,  30/365),
    ("High vol, 1 year",  0.60, 365/365),
]

for label, sigma, T in scenarios:
    nd1, nd2 = delta_vs_prob(100, 100, T, sigma)
    print(f"{label}: delta={nd1:.3f}, prob_ITM={nd2:.3f}, gap={nd1-nd2:.3f}")

# Output:
# Low vol, 7 days:   delta=0.523, prob_ITM=0.514, gap=0.008
# Moderate, 30 days: delta=0.536, prob_ITM=0.502, gap=0.034
# High vol, 1 year:  delta=0.649, prob_ITM=0.414, gap=0.235
```

The volatile LEAPS case is dramatic: delta says 0.65, but the actual probability of finishing ITM is only 0.41.[^24][^25]

*The reader is encouraged to modify the code: try sigma=0.80 (a volatile biotech) with T=2 years. The gap becomes enormous.*

## Common Mistakes

**1. Treating delta as constant.** Delta changes with every stock move, every passing day, and every volatility shift. The delta you saw yesterday morning may not be your delta this afternoon. Monitor it regularly, especially as expiration nears.[^6][^8]

**2. Confusing delta with probability.** For calls in Black-Scholes, delta overstates the probability of finishing ITM. And even the correct probability is not a probability of *profit* — profit also requires clearing the premium you paid.[^5][^24]

**3. Ignoring delta drift from time.** You buy a slightly OTM call with delta 0.30. Two weeks pass with no stock movement. Delta has quietly drifted to 0.15 — purely from the passage of time. You lost half your directional exposure without the stock doing anything.[^6][^7]

**4. Ignoring delta drift from volatility.** IV spikes and your deep ITM call's delta drops from 0.97 to 0.80. The option now behaves less like stock. If you were relying on near-stock behavior, you are underhedged.[^9][^10]

**5. Assuming delta-neutral means risk-free.** Zero delta removes only first-order directional risk. Gamma, vega, and theta exposures remain. A delta-neutral portfolio can still lose significant money from a large move or a vol shift.[^16][^17]

**6. Comparing deltas across different expirations.** A 0.30-delta call expiring in 7 days and a 0.30-delta call expiring in 90 days have identical delta but completely different risk profiles. The 7-day option has far more gamma, far more theta, and far less vega.[^8]

## Try It Yourself

This exercise connects the module's concepts to live market data. You need an options chain — Yahoo Finance or any brokerage platform.

**Step 1.** Pick a liquid stock (AAPL, MSFT, SPY). Pull up calls expiring in roughly 30 days.

**Step 2.** Write down delta for 5 strikes: two ITM, one ATM, two OTM. Verify the S-curve: do deltas sweep from near 1.0 (deep ITM) to near 0 (deep OTM) with the steepest change near ATM?

**Step 3.** For each strike, look up the put delta at the same strike. Verify |call delta| + |put delta| ≈ 1.00. (Small deviations are normal for American-style options on dividend-paying stocks.)

**Step 4.** Compare deltas across expirations. Pick the ATM strike and compare its delta at the nearest weekly vs. 90+ days. ATM should be ≈ 0.50 in both. Now do an OTM strike — the shorter-dated delta should be lower (closer to 0).

**Step 5.** Calculate your portfolio delta. Imagine you hold: long 3 ATM calls + long 2 ATM puts. Using deltas from Step 2, what is your net position delta? Are you net long or net short? Compute dollar delta by multiplying by the stock price.

## Glossary

- **S-curve (sigmoid)** — The characteristic S-shaped graph of delta across strikes: near 0 for deep OTM, near 0.50 at ATM, near 1.0 for deep ITM.
- **Step function** — The binary shape delta approaches at expiration: exactly 1.0 for ITM, exactly 0.0 for OTM.
- **Delta-neutral** — A portfolio with net delta of zero, insulated from small directional moves.
- **Dynamic hedging** — Continuously rebalancing a delta hedge as the stock moves, time passes, and volatility changes.
- **Hedge size (hedge ratio)** — The number of shares needed to offset directional risk; equals delta per share (delta × 100 per contract).
- **Position delta** — Total delta of an options position: option delta × 100 × contracts × direction.
- **Dollar delta** — Position delta × stock price; the true economic exposure in dollar terms.
- **Beta-weighted delta** — Position delta adjusted by the stock's beta to a benchmark (e.g., SPY), enabling cross-underlying portfolio comparison.
- **Gamma scalping** — A strategy where a delta-neutral, long-gamma trader profits from repeated rebalancing as the stock oscillates. Covered in Module 6.
- **Vanna** — Second-order Greek: how delta changes with implied volatility (dDelta/dIV). Explored in Module 8.
- **Charm** — Second-order Greek: how delta changes with the passage of time (dDelta/dTime). Related to the S-curve steepening near expiry.
- **N(d1)** — In the Black-Scholes model, the cumulative normal distribution value that equals call delta.
- **N(d2)** — The cumulative normal distribution value representing the risk-neutral probability of finishing ITM. Always less than N(d1).

## Quiz

**Q1.** You hold a long call with delta 0.40. The stock drops $3. The call's theta is −0.05. Using delta for the stock move (ignoring gamma), what is the approximate change in option value after one day?

<details>
<summary>Answer</summary>

Delta loss from $3 drop: 0.40 × (−$3) = −$1.20. Theta cost for one day: −$0.05. Approximate total change: **−$1.25**.

In reality, gamma would slightly cushion the loss — delta decreases as the stock falls, so the second and third dollars of decline contribute less than $0.40 each. But the linear estimate captures the magnitude.
</details>

**Q2.** A call has delta +0.65. Without looking at a chain, what is the put delta at the same strike? If the stock drops $2, approximately how much does the put gain?

<details>
<summary>Answer</summary>

Put delta = 0.65 − 1.00 = **−0.35**. The absolute values sum to 1.00.

If the stock drops $2, the put gains approximately |−0.35| × $2 = **$0.70** per share (before gamma effects).
</details>

**Q3.** You sell 5 call contracts (delta 0.54 each). How many shares do you buy for delta neutrality? After the stock rises $2 and delta increases to 0.62, what adjustment do you make?

<details>
<summary>Answer</summary>

Initial hedge: 5 × 100 × 0.54 = 270 shares short from options. Buy **270 shares**.

After the rally, new option delta: 5 × 100 × 0.62 = 310 short. You hold 270 long. Net: −40. Buy **40 more shares** to restore neutrality.

This is dynamic hedging — you rebalance as gamma shifts your delta.
</details>

## Recap and What Comes Next

Delta has three faces:

- **Price sensitivity** — the S-curve sweeping from ~0 (OTM) to ~1.0 (ITM), steepest at the money
- **Hedge size** — the exact number of shares that neutralize directional exposure
- **Probability proxy** — useful but imperfect; delta overstates the probability, especially for volatile, long-dated options

Two forces reshape the S-curve. Time steepens it toward a step function. Volatility flattens it. Both effects reflect uncertainty: more uncertainty → deltas cluster near 0.50; less → deltas polarize.

The mirror relationship ties calls to puts: for European options on non-dividend stocks, put delta = call delta − 1 exactly; in American/dividend cases it is usually a close approximation.

But we have glossed over something. Every time we said "delta changes," we were describing **gamma** — the rate at which delta shifts. Gamma is what makes hedging expensive, what causes P&L explosions near expiry, and what separates option behavior from stock behavior.

**Module 6: Gamma Deep Dive** takes that next step.

---

## Sources

[^1]: OIC Options Education, "Delta" — Delta by moneyness, S-curve shape, comprehensive overview.
[^2]: Options Playbook, "Option Greeks" — Delta's three interpretations and share equivalence.
[^3]: Brilliant.org, "Option Greeks: Delta" — S-curve inflection point, gamma connection at ATM.
[^4]: Britannica Money, "Delta & Gamma Explained" — Step function at expiry, gamma acceleration.
[^5]: Moontower/Kris Abdelmessih, "Lessons From the .50 Delta Option" — Why 0.50 delta ≠ ATM, probability divergence.
[^6]: ProjectFinance, "3 Examples of Time Changes Option Deltas" — Numerical time-decay examples with real options data.
[^7]: Elearnmarkets, "Delta and Time to Expiry" — ITM/OTM delta behavior as time passes.
[^8]: Options Playbook, "Option Greeks" — Gamma-delta relationship near expiry.
[^9]: Blue Collar Investor, "Impact of Implied Volatility on Delta" — IV flattening/sharpening with numerical examples.
[^10]: Elearnmarkets, "Delta and Volatility" — Volatility-delta relationship.
[^11]: OIC, "Volatility & the Greeks" — Comprehensive volatility-Greeks relationships.
[^12]: Quantsapp, "Vanna Explained" — Second-order delta-vol sensitivity.
[^13]: Macroption, "Delta of Calls vs Puts" — Put-call delta relationship, parity derivation.
[^14]: Structured Retail Products, "Delta: Put-Call Parity Rule" — Formal derivation of delta_call − delta_put = 1.
[^15]: AnalystPrep, "Delta of Covered Call and Protective Put" — Strategy-level delta analysis.
[^16]: Wikipedia, "Delta Neutral" — Delta-neutral portfolio definition and properties.
[^17]: Macroption, "Delta Hedging Calculations" — Multi-leg examples, rebalancing mechanics.
[^18]: MenthorQ, "Delta Hedging Guide" — Dynamic hedging mechanics, rebalancing frequency.
[^19]: MenthorQ, "Gamma Scalping and Delta Hedging" — Gamma scalping concept.
[^20]: Options Playbook, "Position Delta" — Position delta formula, multi-leg examples.
[^21]: Tastytrade, "Options Greeks: Analyzing Exposure" — Beta-weighted delta, portfolio-level Greeks.
[^22]: SteadyOptions, "Why Dollar Delta Will Change Your Trading" — Dollar delta concept with worked examples.
[^23]: CME Group, "Options Delta" — Exchange-level delta education, strike selection.
[^24]: Macroption, "Black-Scholes Formulas" — N(d1) vs N(d2) mathematical relationship.
[^25]: FinanceTrainingCourse, "N(d1) vs N(d2)" — Conditional expectation interpretation.

