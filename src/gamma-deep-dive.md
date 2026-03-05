# Gamma Deep Dive

## The Force Behind the Drift

Module 5 taught you how to build a delta-neutral hedge. You sold calls, bought shares, and brought your net delta to zero. Problem solved?

Not for long. Within hours — sometimes minutes — the hedge drifted. You had to rebalance, then rebalance again, and again. Something kept pushing delta off course.

That something is **gamma**. Gamma is the rate at which delta changes, and understanding it explains the deepest asymmetry in options: why buyers can sleep at night while sellers watch the screen.[^1][^2]

## Prerequisites

This module assumes you are comfortable with:

- **Delta's S-curve** across moneyness, and how time and volatility reshape it (Module 5)
- **Delta-neutral hedging** and dynamic rebalancing (Module 5)
- **The gamma-theta tradeoff** at survey level: high gamma comes with high theta (Module 4)
- **Share equivalence** and position delta (Module 5)

If any feel shaky, revisit Module 5 first. Gamma is delta's next chapter.

## Gamma as Curvature — Why Options Are Not Stocks

A stock moves $1 and your profit is $1 per share. Always. The relationship is a straight line. No surprises.

An option moves differently. A $1 stock move might earn you $0.54. The next $1 earns you $0.58. The one after that earns $0.63. Each successive dollar is *more* profitable than the last. The relationship is a curve, not a line.[^1][^3]

**Gamma** is the curvature. Formally, it is the change in delta for a $1 move in the stock — but what that really means is: your option's sensitivity *accelerates* as the stock moves in your favor.[^1][^2]

In calculus language, Module 5 gave us \(\Delta = \partial V / \partial S\). Gamma is the next derivative:

\\[
\Gamma = \frac{\partial \Delta}{\partial S} = \frac{\partial^2 V}{\partial S^2}
\\]

So for a small stock move \(dS\), the delta shift is approximately:

\\[
d\Delta \approx \Gamma \, dS
\\]

That one line explains hedging drift: even if you start delta-neutral, delta moves as soon as stock price moves.

<video controls width="100%"><source src="media/gamma-deep-dive/GammaAsCurvature.mp4" type="video/mp4">Your browser does not support video.</video>

### The Convexity Bonus

The extra profit from curvature has a name and a formula. When the stock moves by an amount ΔS, the option's gain is approximately:

**Option gain ≈ Δ × ΔS + ½ × Γ × (ΔS)²**

The first term (Δ × ΔS) is the straight-line prediction from delta alone. The second term (½Γ(ΔS)²) is the **convexity bonus** — extra profit that comes purely from the curve.[^3][^4]

Here is what that looks like for our ACME $100 call (delta 0.536, gamma 0.046, 30 days, IV 30%):

| Stock Move | Delta-Only | With Gamma | Convexity Bonus |
|-----------|-----------|-----------|----------------|
| +$1 | +$0.54 | +$0.56 | +$0.02 |
| +$2 | +$1.07 | +$1.16 | +$0.09 |
| +$3 | +$1.61 | +$1.82 | +$0.21 |
| +$5 | +$2.68 | +$3.25 | +$0.57 |

The bonus grows with the *square* of the move. A $5 move produces a $0.57 bonus — more than 20% of the total gain. Options reward big moves disproportionately.[^3][^4]

<video controls width="100%"><source src="media/gamma-deep-dive/ConvexityBonus.mp4" type="video/mp4">Your browser does not support video.</video>

**Key property.** The convexity bonus is always positive for long options, regardless of direction. Stock up $3? Bonus +$0.21. Stock *down* $3? The loss is *less* than delta predicts — also by $0.21. This does **not** guarantee positive total P&L, because theta can still dominate on quiet days.[^1][^4]

*The reader is encouraged to verify: use the formula ½ × 0.046 × 3² to confirm the $0.21 bonus. Then try it for a $10 move — the bonus grows dramatically.*

### Why Calls and Puts Have Identical Gamma

A surprising fact: call gamma and put gamma are exactly the same at the same strike and expiration. This follows from put-call parity — the same relationship that gave us "put delta = call delta − 1" in Module 5. Differentiating a second time shows the second derivatives (gamma) must be equal.[^1][^5]

## Gamma Across Moneyness — The Bell Curve

Where does gamma live? Not evenly across strikes. Gamma forms a **bell curve**, peaking sharply at the money and tapering off on both sides.[^1][^6]

<video controls width="100%"><source src="media/gamma-deep-dive/GammaBellCurveSweep.mp4" type="video/mp4">Your browser does not support video.</video>

Gamma works the same way. ATM is the middle register where delta is most sensitive to stock movement. Deep ITM options already have delta near 1.0 — nowhere left to go, so gamma is low. Deep OTM options have delta near 0 — barely responding, so gamma is also low.[^1][^6]

| Strike | Delta | Gamma | Why |
|--------|-------|-------|-----|
| $80 (Deep ITM) | 0.996 | 0.001 | Delta already at ceiling |
| $90 (ITM) | 0.906 | 0.020 | Some room to move |
| $100 (ATM) | 0.536 | 0.046 | Maximum sensitivity |
| $110 (OTM) | 0.154 | 0.028 | Some room to move |
| $120 (Deep OTM) | 0.021 | 0.006 | Delta near floor |

*The reader is encouraged to check: on a real options chain, does gamma peak near the ATM strike? Compare ATM gamma to a strike $20 away — the bell curve should be obvious.*

## Gamma and Time — The Explosion

The gamma bell curve is not static. As expiration approaches, it transforms dramatically.

Far from expiry, gamma is a gentle hump — modest and spread across strikes. Near expiry, the hump becomes a spike. ATM gamma *explodes*.[^7][^8]

| Time to Expiry | ATM Gamma | Change from 90 Days |
|---------------|-----------|---------------------|
| 90 days | 0.027 | — |
| 30 days | 0.046 | 1.7× |
| 7 days | 0.096 | 3.6× |
| 1 day | 0.254 | 9.6× |

From 90 days to 1 day, ATM gamma increases nearly tenfold.

Why? Remember from Module 5: at expiration, delta must be exactly 1.0 (ITM) or 0.0 (OTM). An ATM option sitting at delta 0.50 needs to jump to one extreme or the other.

With 90 days left, that transition is gradual — low gamma. With 1 day left, delta must make the same jump in a fraction of the time. The rate of change must be enormous.[^7][^8]

Numerically, this is the same story as Module 5's step-function intuition: as expiry approaches, delta has less time to transition from ~0.50 to either 0 or 1, so each $1 move forces a much larger delta jump.

**The tradeoff.** While ATM gamma explodes near expiry, OTM and ITM gamma *decrease*. The bell curve gets taller but narrower — gamma concentrates at a single point, making near-the-money options extraordinarily sensitive while everything else goes quiet.[^7][^8]

*Before reading on, estimate: if ATM gamma is 0.254 at 1 day to expiry and the stock moves $2, by how much does delta change? (Answer: approximately 0.254 × 2 = 0.508 — delta could swing from 0.50 to nearly 1.0 on a $2 move.)*

<video controls width="100%"><source src="media/gamma-deep-dive/GammaTimeExplosion.mp4" type="video/mp4">Your browser does not support video.</video>

## Gamma and Volatility

Volatility reshapes the gamma bell curve in the same way time does — but in the opposite direction.[^9][^10]

**Higher implied volatility flattens the bell curve** — reducing ATM gamma and spreading gamma more evenly across strikes. **Lower implied volatility sharpens it** — concentrating gamma at ATM.[^9]

| IV | ATM Gamma |
|----|-----------|
| 15% | 0.092 |
| 20% | 0.069 |
| 30% | 0.046 |
| 50% | 0.028 |

This is counterintuitive. You might expect "more volatility = more gamma risk." The opposite is true for ATM options: higher IV actually *reduces* ATM gamma. The most intense gamma environment is low vol + short-dated + ATM.[^9][^10]

The reason mirrors Module 5's delta-volatility section. Higher volatility means more uncertainty, which spreads outcomes across a wider range of strikes. Gamma distributes more evenly. Lower volatility concentrates outcomes near ATM, spiking gamma at that single point.[^9][^10]

*Think through the implication: if IV drops suddenly after earnings (a vol crush), ATM gamma spikes — even without a stock move. Short sellers just acquired more dangerous exposure without choosing to.*

<video controls width="100%"><source src="media/gamma-deep-dive/GammaVolatilityFlattening.mp4" type="video/mp4">Your browser does not support video.</video>

## Long Gamma vs Short Gamma — The Fundamental Asymmetry

Every options position is either long gamma or short gamma. This distinction matters more than any other in options risk management.[^11][^12]

**Long gamma** (option buyers): delta shifts in your favor on every move. Stock goes up? Your call delta increases — you make more on the next dollar. Stock goes down? Your call delta decreases — you lose less on the next dollar.[^11][^12]

**Short gamma** (option sellers): the exact opposite. Stock goes up? The calls you sold gain delta against you — losses accelerate. Stock goes down? Your short puts gain negative delta — losses accelerate there too.[^11][^12]

Think of catching a ball. A fishing net stretches when the ball hits — the harder the throw, the more the net gives, absorbing the shock. Each increment of force is cushioned. That is long gamma: adverse moves are absorbed, decelerated.

Now catch the same ball against a brick wall. The wall does not give. The harder the throw, the more destructive the impact. Each increment of force hits harder. That is short gamma: adverse moves hit with full force, accelerating.

<video controls width="100%"><source src="media/gamma-deep-dive/LongVsShortGammaAsymmetry.mp4" type="video/mp4">Your browser does not support video.</video>

| Position Type | Gamma Sign | Benefits From | Harmed By |
|--------------|-----------|---------------|-----------|
| Long calls/puts | + | Big moves (any direction) | Quiet markets |
| Short calls/puts | − | Quiet markets | Big moves (any direction) |
| Long straddle/strangle | + | Movement, any direction | Time decay |
| Short straddle/strangle | − | Time decay in quiet markets | Sudden moves |

**The asymmetry in one sentence:** Long gamma has limited downside (you paid the premium) and amplified upside. Short gamma has limited upside (you collected the premium) and amplified downside.[^11][^12]

*If you sold a straddle and the stock gaps 10% overnight, which direction do you want it to have moved? Neither — short gamma hurts in both directions. That is the danger.*

## Gamma Scalping — Profiting from Oscillation

Module 5 previewed gamma scalping. Now let's see the full mechanics.[^13][^14]

The idea: you buy options (long gamma), then delta-hedge with stock. Recall from Module 5 that selling shares offsets positive delta, and buying shares offsets negative delta. With gamma in your favor, you systematically buy shares when the stock dips and sell when it rallies — locking in small profits each round trip.[^13][^14]

Think of a market stall vendor at a bazaar. They buy goods when prices drop and sell when prices rise. The vendor does not need to predict which direction prices will move — they profit from the oscillation itself. But they pay rent for the stall space every day. If prices stay flat, the rent eats them alive.

### Walkthrough

**Setup.** Buy 1 ACME $100 straddle (1 call + 1 put) at 30 days, IV 30%. Combined delta ≈ +0.07 (call 0.536 + put −0.464). Sell 7 shares to flatten. Net delta: ~0. Total gamma: 0.092 (0.046 per option × 2).[^13][^14]

<video controls width="100%"><source src="media/gamma-deep-dive/GammaScalpingLoop.mp4" type="video/mp4">Your browser does not support video.</video>

**Round 1.** Stock rises $2 to $102. Gamma shifts delta positive by approximately 0.092 × 2 = +0.18. You sell 18 shares at $102.

**Round 2.** Stock falls back $2 to $100. Delta shifts back by ~0.18. You buy 18 shares at $100.

**Result.** You sold 18 shares at $102 and bought 18 at $100. Approximate profit: 18 × $2 = **$36** per round trip. (This is a linear approximation — actual gamma varies continuously during the move, so the real number differs slightly.)

**The catch.** You are bleeding theta every day. If the stock sits still, theta drains your straddle while gamma earns nothing. Gamma scalping only works when the stock moves *enough* to make rebalancing profits exceed the theta cost.[^13][^14]

## The Gamma-Theta Tradeoff — The Real Bet

Module 4 introduced the gamma-theta tradeoff: high gamma costs high theta. Now let's make it precise.[^15][^16]

Your daily P&L from owning a delta-hedged option is approximately:

**Daily P&L ≈ ½ × Γ × (ΔS)² + Θ**

The first term — **gamma P&L** — is always positive, regardless of direction. It grows with the *square* of the stock move. The second term is theta contribution. For long options, \(\Theta\) is negative, so it is a fixed daily drain.[^15][^16]

For our ACME $100 call (gamma 0.046, theta −$0.064/day):

| Daily Stock Move | Gamma P&L | Theta Cost | Net P&L |
|-----------------|-----------|-----------|---------|
| $0 (flat) | $0.00 | −$0.064 | −$0.064 |
| $1 | $0.023 | −$0.064 | −$0.041 |
| $1.67 | $0.064 | −$0.064 | $0.000 |
| $2 | $0.092 | −$0.064 | +$0.028 |
| $3 | $0.207 | −$0.064 | +$0.143 |

**Breakeven daily move: $1.67.** If the stock moves more than $1.67 per day (in either direction), gamma profits exceed theta cost. If less, theta wins.[^15]

### The Rule of 16

A quick shortcut: divide annualized implied volatility by 16 to estimate the expected daily percentage move.[^17]

IV = 30% → expected daily move ≈ 30/16 = 1.875% → $1.88 on a $100 stock.

If the stock *actually* moves more than $1.88/day on average, the option was cheap (realized vol > implied vol). If less, the option was expensive (realized vol < implied vol).[^17]

This is the real bet behind every long gamma position: **you are betting that realized volatility will exceed implied volatility.** Conversely, every short gamma position bets on quiet markets — that realized vol falls short of implied.[^15][^17]

## Gamma Risk and Blowups

Short gamma is where financial disasters happen. The pattern: losses accelerate non-linearly, hedging creates feedback loops, and the worst outcomes occur when markets are most stressed.[^18][^19]

**Volmageddon (February 5, 2018).** Short-volatility products like XIV were effectively massive short gamma positions. When the VIX spiked from 17 to 37 in a single day — far exceeding the expected ~3-point daily move — XIV lost 96% of its value overnight. The product was liquidated.[^18][^19]

**Black Monday (October 19, 1987).** Portfolio insurance strategies — mechanically selling stock as prices fell — were effectively short gamma. The selling accelerated the decline, which triggered more selling, creating a feedback loop. The Dow dropped 22.6% in one day.[^19][^22]

**LTCM (1998).** The hedge fund's short volatility positions carried massive negative gamma. When markets dislocated, losses accelerated beyond their models' predictions. Total losses: $4.6 billion in four months. The Federal Reserve coordinated a private-sector bailout to prevent systemic collapse.[^19][^23]

**GameStop (January 2021).** The reverse: a *positive* gamma feedback loop. Retail call buying forced market makers to delta-hedge by buying stock. The buying pushed GME higher, increasing call deltas, requiring more hedging, driving the stock higher still. GME rose over 2,000% in weeks — a **gamma squeeze**.[^20]

**Pin risk at expiration.** When a stock hovers near a strike at expiration with extreme gamma, tiny moves swing delta between 0 and 1. A seller cannot know whether they will be assigned until the final minutes. This is **pin risk**.[^21]

## Practical Gamma Management

Understanding gamma is one thing. Managing it is another. Here are the tools professionals use.[^11][^15]

### Dollar Gamma

Raw gamma has the same problem raw delta has — it ignores the stock price. **Dollar gamma** normalizes exposure:

Dollar gamma = Γ × S × 100 × contracts

**Example.** You have two positions:
- 5 contracts on Stock A ($50, gamma 0.08): dollar gamma = 0.08 × 50 × 100 × 5 = **$2,000**
- 2 contracts on Stock B ($200, gamma 0.02): dollar gamma = 0.02 × 200 × 100 × 2 = **$800**

Stock A carries 2.5× more gamma exposure despite having a lower raw gamma number. Dollar gamma reveals the true risk.[^15]

### Near-Expiry Protocol

Short ATM options in the final week carry extreme gamma. A practical rule: **close or roll short ATM positions before the final 5–7 trading days.** Before holding through expiration, assess: if the underlying moves 5–10%, how much does delta change? If the answer makes you uncomfortable, close the position.[^7][^21]

### When to Choose Long vs Short Gamma

| Scenario | Choose | Why |
|----------|--------|-----|
| Expecting a large move (earnings, FDA, macro event) | Long gamma | Convexity profits from the move |
| Expecting rangebound, quiet market | Short gamma | Collect theta while stock sits still |
| Realized vol running above implied vol | Long gamma | Rebalancing profits exceed theta cost |
| Implied vol elevated vs historical norms | Short gamma | Options are expensive; sell premium |

The decision always comes back to the gamma-theta tradeoff: will the stock move enough to justify the theta cost?[^15][^17]

### Gamma Exposure Index (GEX)

At the market level, **GEX** measures the aggregate gamma exposure of options dealers. When GEX is positive, dealers are long gamma — their hedging *absorbs* market moves (stabilizing). When GEX is negative, dealers are short gamma — their hedging *amplifies* moves (destabilizing). Many traders monitor GEX as a market-regime indicator.[^24]

## Seeing It in Code

We can compute the gamma bell curve and watch it reshape with time. This uses the Black-Scholes gamma formula: Γ = N'(d₁) / (S × σ × √T).

```python
import math

def norm_pdf(x):
    """Standard normal probability density function."""
    return math.exp(-0.5 * x**2) / math.sqrt(2 * math.pi)

def gamma(S, K, T, sigma, r=0.05):
    """Black-Scholes gamma."""
    d1 = (math.log(S / K) + (r + 0.5 * sigma**2) * T) / (sigma * math.sqrt(T))
    return norm_pdf(d1) / (S * sigma * math.sqrt(T))

# ACME stock at $100, IV = 30%
S, sigma = 100, 0.30

# Bell curve across strikes
print("--- Gamma bell curve (30 days) ---")
for K in [80, 90, 95, 100, 105, 110, 120]:
    g = gamma(S, K, 30/365, sigma)
    bar = "#" * int(g * 1000)
    print(f"  ${K:3d}: {g:.4f}  {bar}")

# Gamma explosion across time
print("\n--- ATM gamma vs time to expiry ---")
for days in [90, 30, 7, 1]:
    g = gamma(S, 100, days/365, sigma)
    print(f"  {days:2d} days: gamma = {g:.4f}")

# Convexity bonus
print("\n--- Convexity bonus (ATM call, 30 days) ---")
d, g = 0.536, 0.046
for move in [1, 2, 3, 5]:
    bonus = 0.5 * g * move**2
    total = d * move + bonus
    print(f"  +${move}: delta-only ${d*move:.2f}, "
          f"with gamma ${total:.2f}, bonus ${bonus:.2f}")

# Output:
# --- Gamma bell curve (30 days) ---
#   $ 80: 0.0013  #
#   $ 90: 0.0195  ###################
#   $ 95: 0.0366  ####################################
#   $100: 0.0462  ##############################################
#   $105: 0.0414  #########################################
#   $110: 0.0276  ###########################
#   $120: 0.0059  #####
#
# --- ATM gamma vs time to expiry ---
#   90 days: gamma = 0.0265
#   30 days: gamma = 0.0462
#    7 days: gamma = 0.0959
#    1 days: gamma = 0.2540
#
# --- Convexity bonus (ATM call, 30 days) ---
#   +$1: delta-only $0.54, with gamma $0.56, bonus $0.02
#   +$2: delta-only $1.07, with gamma $1.16, bonus $0.09
#   +$3: delta-only $1.61, with gamma $1.82, bonus $0.21
#   +$5: delta-only $2.68, with gamma $3.25, bonus $0.57
```

The bell curve is unmistakable — gamma peaks at $100 (ATM) and tapers in both directions. The time series shows the explosion: gamma nearly doubles each time you cut remaining days by two-thirds.[^6][^7]

*The reader is encouraged to modify the code: try `sigma = 0.15` and re-run the bell curve. ATM gamma nearly doubles — lower vol concentrates gamma at the money.*

## Common Mistakes

**1. Thinking gamma only matters near expiration.** Gamma matters always — it determines how quickly delta shifts. It just becomes *extreme* near expiry, which is when it demands the most attention.[^7][^8]

**2. Assuming long gamma guarantees profits.** Long gamma gives convexity, but you still pay theta every day. If realized movement stays below what was priced, theta can overwhelm the convexity bonus.[^11][^12][^15]

**3. Believing call gamma and put gamma are different.** They are identical at the same strike and expiry. This follows from put-call parity.[^1][^5]

**4. Treating gamma scalping as free money.** Gamma scalping profits require realized vol to exceed implied vol. If the stock is quieter than priced, theta eats the position alive. It is a bet, not an arbitrage.[^13][^14]

**5. Ignoring gamma when selling options.** Beginners sell options for theta income and forget they are short gamma. The income is steady — until a large move turns a month of profits into a single-day loss.[^18][^19]

**6. Comparing gamma across different expirations.** A 7-day ATM option has gamma 0.096; a 90-day ATM has 0.027. But the 7-day option's gamma can swing wildly, while the 90-day is stable. Raw gamma does not capture full risk.[^7]

**7. Believing short gamma is always bad.** Short gamma is a legitimate strategy when properly sized and risk-managed. The key is understanding the tradeoff: you collect theta in exchange for tail risk. The disasters happen when position sizing ignores the non-linear loss potential.[^11][^15]

## Try It Yourself

**Step 1.** Pick a liquid stock and pull up the options chain for ~30 days out. Write down gamma for 5 strikes (two ITM, ATM, two OTM). Verify the bell curve: does gamma peak at ATM?

**Step 2.** Compare ATM gamma for two expirations — one near-term (7–14 days) and one longer-term (60–90 days). The near-term gamma should be significantly higher.

**Step 3.** Pick the ATM call. Using its delta and gamma, calculate the convexity bonus for a hypothetical $3 stock move: ½ × gamma × 9. How much extra does curvature add beyond delta alone?

**Step 4.** Find the ATM call's theta. Calculate the breakeven daily move: √(2 × |theta| / gamma). Compare to the Rule of 16 estimate (IV ÷ 16 × stock price ÷ 100). Are they in the same ballpark?

**Step 5.** Imagine you sold a straddle at the ATM strike. If the stock moves $5, estimate your gamma-driven loss: ½ × gamma × 25 × 2 contracts. Compare to a week of theta income. Is the tradeoff clear?

## Glossary

- **Curvature (convexity)** — The property that makes option payoffs bend rather than run in straight lines. Measured by gamma. Long options have positive convexity.
- **Convexity bonus** — Extra gain (or reduced loss) from gamma's curvature, equal to ½ × Γ × (ΔS)². Always positive for long holders.
- **Gamma explosion** — The dramatic increase in ATM gamma as expiration approaches. Caused by delta needing to jump from ~0.50 to 0 or 1.
- **Long gamma** — Positive net gamma (option buyer). Benefits from large moves in either direction.
- **Short gamma** — Negative net gamma (option seller). Harmed by large moves in either direction.
- **Gamma scalping** — A delta-neutral, long-gamma strategy that profits from repeated rebalancing as the stock oscillates. Requires realized vol > implied vol.
- **Gamma-theta tradeoff** — Owning gamma costs theta. Daily P&L ≈ ½Γ(ΔS)² + Θ (and for long options, Θ is negative).
- **Rule of 16** — IV ÷ 16 ≈ expected daily percentage move. Estimates whether gamma or theta will dominate.
- **Realized volatility** — Actual stock price movement, as opposed to implied volatility priced into the option.
- **Gamma squeeze** — A feedback loop where hedging forces stock purchases, driving prices higher, increasing deltas, requiring more hedging. GameStop (2021) is the canonical example.
- **Pin risk** — Risk of a stock hovering near a strike at expiration, where extreme gamma causes unpredictable assignment.
- **Dollar gamma** — Γ × S × 100 × contracts. Normalizes gamma exposure across different stock prices.
- **GEX (Gamma Exposure Index)** — Aggregate gamma exposure of options dealers. Positive GEX = stabilizing; negative GEX = destabilizing.

## Quiz

**Q1.** You hold a long call with delta 0.50 and gamma 0.04. The stock rises $3. What is the convexity bonus, and what is the total estimated gain?

<details>
<summary>Answer</summary>

Convexity bonus = ½ × 0.04 × 9 = **$0.18**.

Delta-only prediction: 0.50 × $3 = $1.50. With gamma: $1.50 + $0.18 = **$1.68**. The bonus is 12% of the total gain — and it grows with the square of the move.
</details>

**Q2.** You sold a straddle. Is your gamma positive or negative? If the stock gaps $5 in either direction, describe what happens qualitatively.

<details>
<summary>Answer</summary>

**Negative gamma.** You sold both a call and a put.

If the stock gaps $5 upward: the short call's delta moves against you (toward −1.0 from your perspective). If $5 downward: the short put's delta moves against you. Either way, losses *accelerate* — each successive dollar hurts more. This is the fishing net vs brick wall asymmetry working against you.
</details>

**Q3.** An ATM call has gamma 0.046 and theta −$0.064/day. What daily stock move breaks even? If the stock only moves $1/day on average, should you be long or short this option?

<details>
<summary>Answer</summary>

Breakeven = √(2 × 0.064 / 0.046) = √2.78 = **$1.67**.

At $1/day: gamma P&L = ½ × 0.046 × 1 = $0.023. Theta cost = $0.064. Net: −$0.041/day. You lose money being long.

**Be short** — sell the option and collect theta. The stock is not moving enough to make the convexity bonus worth the daily time decay.
</details>

## Recap and What Comes Next

Gamma is the curvature beneath your option:

- **It creates the convexity bonus** — options gain more from favorable moves than they lose from equal unfavorable ones
- **It peaks at ATM** and forms a bell curve across strikes
- **It explodes near expiration** — the bell curve becomes a spike
- **It defines the buyer-seller divide** — long gamma benefits from movement, short gamma from stillness
- **It is paid for with theta** — the gamma-theta tradeoff is the central tension of options trading

Every time we mentioned "theta cost" in this module, we were describing the daily rent for owning gamma. But what determines that rent? How does theta behave across strikes, time, and volatility?

**Module 7: Theta Deep Dive** takes that next step.

---

## Sources

[^1]: OIC Options Education, "Option Greeks: Gamma" — Gamma definition, always positive for longs, identical for calls/puts.
[^2]: Options Playbook, "Option Greeks" — Gamma as rate of delta change, sign conventions.
[^3]: Macroption, "Option Gamma" — Convexity bonus formula ½Γ(ΔS)², numerical example.
[^4]: Ryan O'Connell CFA, "Option Gamma Explained" — Cumulative profit acceleration from gamma.
[^5]: Brilliant.org, "Option Greeks: Gamma" — Mathematical derivation, call-put gamma equivalence.
[^6]: Barchart, "Understanding Gamma" — Gamma bell curve across strikes, ATM peak.
[^7]: ProjectFinance, "Option Gamma and Time" — Gamma explosion across expirations with real data.
[^8]: Elearnmarkets, "Gamma and Time to Expiry" — ATM gamma increase, OTM/ITM decrease near expiry.
[^9]: Elearnmarkets, "Gamma and Volatility" — Higher IV flattens gamma curve, lower IV sharpens.
[^10]: Interactive Brokers, "Gamma and Implied Volatility" — Higher IV reduces ATM gamma.
[^11]: Schwab, "Understanding Gamma Risk" — Long vs short gamma, practical risk management.
[^12]: MenthorQ, "Long Gamma vs Short Gamma" — Asymmetry of convexity.
[^13]: MenthorQ, "Gamma Scalping and Delta Hedging" — Step-by-step gamma scalping mechanics.
[^14]: Schwab, "Gamma Scalping Primer" — Setup, rebalancing, and profit conditions.
[^15]: SpotGamma, "Gamma-Theta Tradeoff" — Daily P&L formula, breakeven analysis.
[^16]: Quant-Next, "Option Greeks and P&L Decomposition" — Gamma P&L = ½Γ(ΔS)², theta offset.
[^17]: SpotGamma, "Rule of 16" — IV/16 = daily move, realized vs implied vol.
[^18]: CFA Institute, "Volmageddon Analysis" — XIV collapse, short-vol products, feedback loops.
[^19]: Swan Global Investments, "Short Gamma Risk" — Historical blowups, systemic gamma risk.
[^20]: Nasdaq, "GameStop Gamma Squeeze" — Retail call buying, dealer hedging feedback.
[^21]: OIC, "Pin Risk" — Near-strike expiration risk, extreme gamma.
[^22]: Federal Reserve History, "Stock Market Crash of 1987" — Portfolio insurance and feedback loops.
[^23]: Federal Reserve History, "LTCM Crisis" — Short vol losses, systemic risk, bailout.
[^24]: SpotGamma, "GEX Explained" — Dealer gamma exposure, stabilizing vs destabilizing regimes.


