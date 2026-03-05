# Greeks Overview — The Mental Model

## The Fuel Gauge Problem

Module 3 answered *why* options have value. Volatility creates opportunity. Time gives that opportunity room. Interest rates nudge at the margin. You know the engine runs on fuel.

But knowing your car runs on fuel is different from having a fuel gauge.

If the stock jumps $1, does your call gain $0.10 or $0.80? If a quiet week passes, how much value did you silently lose? This module gives you the gauges. Five of them, collectively called **the Greeks**, each measuring one dimension of your option's sensitivity.[^1][^2]

Fair warning: five new quantities in one sitting is a lot. We will take them one at a time, all measured on the *same* option, so you can see how the numbers fit together like instruments on a dashboard.

## Prerequisites

This module builds directly on Modules 1 through 3. You should be comfortable with:

- **Calls and puts**, buyer and seller roles (Module 1)
- **Moneyness** (ITM, ATM, OTM) and premium decomposition into intrinsic value and time value (Module 2)
- **Why options have value** — volatility as the biggest driver, time decay via the sqrt(T) relationship, and interest rates as the smallest factor (Module 3)

If any of those feel shaky, revisit the relevant module first. Everything below assumes that foundation.

## What "Sensitivity" Means

Module 3 told you which direction option prices tend to move when volatility, time, or rates change. This module adds the missing part: **magnitude**.

Each Greek answers one question: if one input changes by one unit, how much should the option price change, approximately, with other inputs held fixed?[^1][^3]

We isolate one force at a time for learning. In live markets, everything moves together. Greeks are local estimates for small moves, so treat them as guideposts, not guarantees.[^4]

Make it concrete with one ACME call held overnight:

- If the stock is unchanged but the option opens lower, that clock-loss comes from **theta**.
- If the stock rises $1 and your option rises about $0.52, that response is **delta**.
- If you sold calls and buy shares to mute small stock moves, that share amount is your **hedge size** (computed from delta).

Where do you see the raw numbers? On an **options chain** — the table of option contracts by strike and expiration. Broker platforms show Greek columns on that table (delta, gamma, theta, vega, and sometimes rho), usually computed from a **pricing model** (a calculator that maps stock price, strike, time, volatility, and rates to theoretical option value and Greeks).

You do not need model derivations yet. In this module, formulas are compact labels for ideas you can already feel from Modules 1-3.

The same story written as bookkeeping:

\\[ \Delta V \approx \Delta \cdot \Delta S + \Theta \cdot \Delta t + \mathcal{V} \cdot \Delta \sigma + \rho \cdot \Delta r \\]

Translation: one line from stock move, one from time passing, one from volatility change, one from rates.

The five standard Greeks are:

- **Delta** — sensitivity to **the stock price**
- **Gamma** — sensitivity of **delta itself** to the stock price
- **Theta** — sensitivity to **time passing**
- **Vega** — sensitivity to **implied volatility**
- **Rho** — sensitivity to **interest rates**

We will use one anchor position for the entire module:

| Position Card | Value |
|---|---|
| Underlying | ACME stock |
| Stock Price | $100 |
| Option | 30-day $100 call (ATM) |
| Option Price | $3.50 |
| Delta | +0.52 |
| Gamma | +0.04 |
| Theta | -0.06/day |
| Vega | +0.12 per 1% IV |
| Rho | +0.03 per 1% rate |

## Delta — How Much Does Your Option Translate?

Delta answers the most natural question: if the stock goes up $1, how much does my option go up?

Our ACME call has **delta = 0.52**: for every $1 stock move, option price changes by about $0.52.[^1][^5] Deep ITM calls approach 1.00. Deep OTM calls approach 0.

**Sign.** Call delta is always positive (stock up, call up). Put delta is always negative (stock up, put down).[^5][^6]

**Range.** Delta lives between 0 and +1 for calls (0 and -1 for puts). Deep ITM calls approach +1.00 — nearly dollar-for-dollar with the stock. Deep OTM calls approach 0. ATM options sit near 0.50.[^5][^6]

**Share equivalence.** A call with delta 0.52 on one contract (100 shares) gives you the directional exposure of owning 52 shares of stock. This **share equivalence** framing is how professional traders compare options to stock positions.[^5]

**The probability shortcut.** You may hear "delta approximates the probability of expiring ITM." A 0.52-delta call has roughly a 52% chance of finishing in the money. Useful mental shortcut — but not what delta *measures*.

Delta measures price sensitivity. The probability interpretation breaks down for deep ITM options and near expiration.[^7][^8] Treat it as a heuristic, not a definition.

**Worked numbers.** ACME call (delta 0.52, price $3.50). Stock rises $1 to $101: option gains ~$0.52, price ~$4.02. Stock falls $1: option loses ~$0.52, price ~$2.98. Selling flips the sign — a short call has delta -0.52.[^1]

*The reader is encouraged to verify: if the stock drops $3 instead, what is the approximate option price using delta alone? (Answer: $3.50 - 3 x $0.52 = $1.94. In reality gamma would cushion the loss, but the linear estimate is a good starting point.)*

## Gamma — The Steepening Slope

Delta tells you your current translation rate. But that rate is not fixed. **Gamma** measures how much delta changes when the stock moves $1.[^1][^9]

Our ACME call has gamma of **0.04**. If the stock rises $1, delta increases from 0.52 to approximately 0.56. Another $1 up, delta climbs to ~0.60. Each successive dollar of stock movement produces a *bigger* option gain. This is why option payoffs curve rather than running in a straight line.[^9]

**Always positive for buyers.** Gamma is positive for all long options — calls and puts. Owning an option means delta always shifts in your favor. Stock goes up? Call delta increases, amplifying further gains. Stock goes down? Call delta decreases, cushioning further losses.[^1][^9]

**Highest at the money.** Gamma peaks for ATM options. Deep ITM and OTM options have low gamma — their deltas are near 1 or 0 with little room to move.[^5][^6][^9]

**Worked numbers.** ACME call: delta 0.52, gamma 0.04. Stock rises $1: option gains ~$0.52, delta becomes ~0.56. Stock rises another $1: option gains ~$0.56. Total from a $2 move: ~$1.08 — more than a simple 0.52 x $2 = $1.04 would predict. Gamma is why.

<video controls width="100%"><source src="media/greeks-overview/DeltaGammaMechanics.mp4" type="video/mp4">Your browser does not support video.</video>

**The misconception.** "Gamma is always good." For buyers, yes. But selling an option gives you *negative* gamma — delta moves *against* you on every stock move. Negative gamma is the reason selling naked options is dangerous.[^5][^9]

## Theta — The Expiring Subscription

Module 3 introduced the melting ice cube — time value erodes as expiration approaches. **Theta** puts a dollar amount on that erosion: how much your option loses each day, purely from the clock ticking.[^1][^10][^11]

Our ACME call has theta of **-0.06**. The option loses $0.06 per day. Hold it overnight with no stock movement and unchanged volatility: your $3.50 call is worth roughly $3.44 tomorrow. Time is quietly billing you.

**Negative for buyers, positive for sellers.** Buyers pay $0.06/day in time decay. Sellers *collect* $0.06/day as the option shrinks. This is the fundamental tension: buyers pay the subscription, sellers receive it.[^10][^11]

**Acceleration near expiry.** Remember the sqrt(T) relationship from Module 3? An option with 90 days left might lose $0.02/day. The same option with 10 days left might lose $0.10/day.

Theta accelerates as expiration approaches. Beginners thinking "I have 30 days, the bleed is small" are often caught off guard in the final two weeks.[^10][^11]

*Before reading on, estimate: how much would our ACME call lose over 5 quiet days? Then check below.*

**Worked numbers.** ACME call: price $3.50, theta -0.06. After 5 quiet days: approximately $3.50 - (5 x $0.06) = $3.20. In reality the loss would be slightly more because theta itself increases as expiration nears — but the approximation captures the feel.

## Vega — The Forecast Gauge

Module 3 established volatility as the biggest driver of time value. **Vega** quantifies the link: how much the option price changes when **implied volatility (IV)** shifts by 1 percentage point.[^1][^12][^13]

A key distinction: implied volatility is not the stock's past movement. It is the market's current *expectation* of how much the stock will move in the future, expressed as an annualized percentage.

A stock that has been quiet for weeks can still have high IV if the market expects a big event ahead. This distinction between past movement and future expectation matters enormously — Module 11 will explore it in depth.

Our ACME call has vega of **0.12**. IV rises from 30% to 31%: option gains ~$0.12. IV drops from 30% to 28%: option loses ~$0.24 (2 x $0.12).

**Positive for all long options.** Vega is positive for long calls *and* long puts. Rising volatility lifts every option you own, because higher expected movement means the asymmetric payoff (from Module 3) has more room to work. Short positions have negative vega.[^12][^13]

**The vol crush trap.** You buy a call. The stock goes up. You *lose money*. How?

Implied volatility dropped at the same time — a **vol crush**, common after earnings. Your delta gain was real, but your vega loss was bigger. Direction is not the only thing moving your P&L.[^12][^13]

**Worked numbers.** ACME call: vega 0.12, IV at 30%. Implied volatility routinely swings 5-10 percentage points around earnings announcements. Before earnings, anticipation pushes IV to 35%: gain ~5 x $0.12 = $0.60 from vega alone. After earnings pass quietly, IV collapses to 25%: loss ~10 x $0.12 = $1.20 on the round trip. Vega giveth and vega taketh away.

Side note: vega is technically not a Greek letter — it is a made-up name universally grouped with the real ones.[^3]

## Rho — Interest Rate Sensitivity (The Smallest Greek)

**Rho** measures how much the option price changes when the risk-free interest rate moves by 1 percentage point. Module 3 told you interest rates are the smallest pricing factor. Rho reflects that — it is the quietest gauge on the dashboard.[^1][^14][^15]

Our ACME call has rho of **0.03**. If rates rise by a full percentage point, the option gains roughly $0.03. Compare that to delta's $0.52 or vega's $0.12 — rho is an order of magnitude smaller.

**Sign.** Rho is positive for long calls (higher rates make calls slightly more valuable via the cost-of-carry argument from Module 3) and negative for long puts.[^14][^15]

**When it matters.** For a 30-day option, rho is almost negligible. But two situations wake it up.

First, long-dated options called **LEAPS** (Long-Term Equity AnticiPation Securities, one year or longer), where the interest rate effect accumulates over a much longer horizon. Second, periods of rapid rate changes, when the Federal Reserve is hiking or cutting aggressively.[^14][^15]

**Worked numbers.** ACME call: rho 0.03. Rates rise 0.50 percentage points. The option gains approximately 0.50 x $0.03 = $0.015 — less than two pennies. You can see why traders rarely lose sleep over rho for short-dated options.

For the rest of this module, we will keep rho on the dashboard but focus our attention on the four louder gauges.

## The Dashboard — All Gauges at Once

No Greek works alone. At every moment, your option is exposed to stock movement (delta + gamma), time passing (theta), volatility shifts (vega), and interest-rate changes (rho).[^16]

### Sign Convention Reference

Which Greeks are positive and which are negative for each position type? This table is your cheat sheet:

| Greek | Long Call | Long Put | Short Call | Short Put |
|-------|-----------|----------|------------|-----------|
| Delta |     +     |     -    |      -     |     +     |
| Gamma |     +     |     +    |      -     |     -     |
| Theta |     -     |     -    |      +     |     +     |
| Vega  |     +     |     +    |      -     |     -     |
| Rho   |     +     |     -    |      -     |     +     |

Notice the pattern. Gamma and vega are positive for buyers, negative for sellers — owning options benefits from stock moves and volatility increases. Theta follows the opposite logic: it is negative for buyers because time decay erodes value, and positive for sellers because that erosion benefits them. Delta and rho follow a call-vs-put pattern instead.

### The Tradeoff Triangle

The Greeks come with tradeoffs. Understanding these tradeoffs is a mark of growing sophistication:

**Gamma vs theta.** Options with the highest gamma (ATM, near expiration) carry the steepest theta. You cannot buy acceleration without paying the subscription. Convexity has a price.[^17]

**Theta vs vega.** Long positions bleed theta daily while waiting for a volatility spike that may never arrive. For sellers, the reverse: collect theta but risk a vega-driven explosion.[^10][^12][^17]

<video controls width="100%"><source src="media/greeks-overview/ThetaVegaTension.mp4" type="video/mp4">Your browser does not support video.</video>

### How Greeks Vary Across Strikes

The following table shows how Greek profiles differ across moneyness for three calls on the same stock ($100) at the same expiration (30 days):

| Greek | $90 Call (ITM) | $100 Call (ATM) | $110 Call (OTM) |
|-------|:--------------:|:---------------:|:---------------:|
| Delta |     ~0.85      |      ~0.52      |      ~0.20      |
| Gamma |     ~0.02      |      ~0.04      |      ~0.03      |
| Theta |    ~-0.03      |     ~-0.06      |     ~-0.04      |
| Vega  |     ~0.06      |      ~0.12      |      ~0.08      |

Notice the pattern: gamma, theta (in absolute value), and vega all peak at ATM. Delta increases steadily as you move from OTM to ITM.

Also notice that the ITM call has lower vega — it has more intrinsic value and less time value, and vega only affects the time value portion.[^12] Rho is omitted from this table because it varies primarily with expiration length, not moneyness.

### A Day in the Life — P&L Attribution

Let us put the gauges to work. You hold our ACME call (delta 0.52, gamma 0.04, theta -0.06, vega 0.12). Three things happen overnight:

1. The stock rises $2 (from $100 to $102)
2. One day passes
3. Implied volatility drops 0.5%

Approximate P&L breakdown:

**Delta/gamma gain.** First $1 earns ~$0.52 (starting delta). Gamma lifts delta to ~0.56, so the second $1 earns ~$0.56. Total: **+$1.08**.

**Theta cost.** One day passed: **-$0.06**.

**Vega loss.** IV dropped 0.5 points: 0.5 x $0.12 = **-$0.06**.

**Net change: +$1.08 - $0.06 - $0.06 = +$0.96**

You were right on direction, but time and a vol decline ate $0.12 of your gain. This is **P&L attribution**: decomposing profit or loss into each Greek's contribution. Professional traders do this daily.[^16][^17]

When traders say "explain today's P&L," this decomposition is exactly what they mean.

<video controls width="100%"><source src="media/greeks-overview/GreeksPnLAttribution.mp4" type="video/mp4">Your browser does not support video.</video>

### Which Greek Should I Watch?

The answer depends on your situation:

- **Holding a long call for a directional bet?** Watch **delta** (are you getting enough exposure?) and **theta** (how much are you paying per day to hold this position?).
- **Selling options for income?** Watch **gamma** (how much can a sudden stock move hurt you?) and **vega** (how much can a volatility spike hurt you?).
- **Holding through an earnings announcement?** Watch **vega** above all — IV routinely swings 5-10 percentage points around earnings, dwarfing other Greeks.
- **Holding LEAPS (one year or longer)?** Check **rho** periodically, especially during rate-change cycles.

### Greek Triage (Fast Decision Rules)

Use this when you need to decide what matters first:

| Situation | Primary Greeks | Why |
|---|---|---|
| Short-term directional trade | Delta + Gamma | Directional exposure and how fast that exposure changes |
| Waiting for event/earnings | Vega + Theta | IV move vs daily bleed |
| Premium-selling income setup | Theta + Gamma | Time-decay collection vs convexity risk |
| Long-dated LEAPS exposure | Delta + Vega + Rho | Long-horizon direction, vol, and rate sensitivity |

### Where to Find Greek Values

Most brokerage platforms display Greeks as columns on the options chain. Look for a "Greeks" toggle or settings panel — it is often hidden by default.

Yahoo Finance's options page shows delta, gamma, theta, and vega for each contract. Once you can see the columns, the patterns we described above (gamma peaking at ATM, theta increasing near expiration) will jump off the screen.

### Portfolio-Level Greeks

Professional traders sum Greeks across all positions to get portfolio-level numbers: "My book is long 250 delta, bleeding $400/day in theta, and would gain $650 if vol rises 1%." We will revisit portfolio Greeks in later modules.[^16]

## Greeks Change with Every Tick

The Greeks are not constants stamped onto your option at purchase. They change — constantly — as the stock moves, time passes, and volatility shifts.[^18]

Think of a patient's vital signs on a hospital monitor. Blood pressure and heart rate are live readings that update with every heartbeat. You cannot take one reading and assume it holds all day. The Greeks work the same way.

Specific ways they shift:

- **As the stock moves**, delta changes (driven by gamma), and gamma itself migrates. An OTM option with low delta can become ATM with much higher delta after a rally.
- **As time passes**, theta accelerates (especially for ATM options), gamma increases near expiry, and vega decreases (shorter-dated options are less sensitive to volatility changes).
- **As volatility changes**, delta itself moves — higher volatility pushes all deltas toward 0.50; lower volatility pushes them toward 0 or 1.[^18]

These profiles are not random — they follow from the structure of option pricing that we will unpack in Modules 5 through 9, where each Greek gets its own deep-dive chapter.

Next sequence:

1. Module 5: Delta Deep Dive  
2. Module 6: Gamma Deep Dive  
3. Module 7: Theta Deep Dive  
4. Module 8: Vega Deep Dive  
5. Module 9: Rho Deep Dive

## Common Mistakes

**1. Treating Greeks as guarantees.** Greeks are theoretical estimates for *small* moves in one input, holding everything else constant. In reality, multiple inputs move at once and large moves break the linear approximation. Use Greeks as guideposts, not GPS coordinates.[^4]

**2. Checking Greeks once and forgetting them.** Your delta three days and $4 ago is not your delta today. Greeks shift — possibly dramatically — especially as expiration nears. Monitor regularly.[^18]

**3. Ignoring theta while waiting for a move.** You bought a call expecting a breakout that has not arrived. Every day, theta silently drains your position. Do not focus only on delta and forget the daily cost.[^10]

**4. Confusing vega with stock movement.** Vega measures sensitivity to *implied* volatility — the market's forecast — not the stock's actual movement today. The stock can sit still while IV rises, or thrash around while IV falls.[^12][^13]

**5. Believing delta IS the probability of expiring ITM.** It is a useful approximation, but delta is a sensitivity measure first. The probability heuristic diverges from reality in specific cases. Do not build a trading thesis on "my delta says 70%."[^7][^8]

**6. Comparing Greeks across different expirations without adjusting.** A 7-day ATM option and a 180-day ATM option will have very different gamma and theta values. Comparing them directly is misleading — the 7-day option's gamma and theta are much larger because expiration is imminent. Always compare like with like.[^18]

**7. Over-optimizing one Greek while ignoring others.** You find an option with fantastic gamma. But it also has crushing theta. The Greeks are a system of tradeoffs, not a menu where you can pick all the best values. Looking at the full dashboard is essential.[^17]

## Try It Yourself

This exercise connects the module's concepts to real market data. You will need access to an options chain — Yahoo Finance (free) or any brokerage platform will work.

**Step 1.** Pick any stock you follow. Pull up its options chain and find a call that is approximately at the money (strike price close to the current stock price) with roughly 30 days to expiration.

**Step 2.** Write down the five Greek values for that option: delta, gamma, theta, vega, and rho. (If your platform does not show rho, skip it — most traders do.)

**Step 3.** Check the patterns:
- Is delta near 0.50?
- Is gamma higher for the ATM option than for deep ITM or deep OTM options on the same chain?
- Is theta negative, and does it get larger (in absolute value) for options closer to expiration?

**Step 4.** Calculate two things:
- (a) If the stock moves up $1, what is your estimated new option price? (Current price + delta)
- (b) If nothing happens for 5 days, what is your estimated new option price? (Current price + 5 x theta — remember theta is negative)

**Step 5.** Attempt a mini P&L attribution. Imagine the stock rises $2, one day passes, and IV drops 1 point. Use your option's actual Greek values to estimate the net change, the same way we did with the ACME call.

*The reader is encouraged to compare their results with the patterns described in this module. If the numbers do not match, re-read the relevant Greek section — the mismatch itself is a learning opportunity.*

## Glossary

- **Greeks** — A set of five standard sensitivity measures (delta, gamma, theta, vega, rho) that quantify how an option's price responds to changes in underlying factors.
- **Delta** — The change in option price for a $1 change in the stock price. Ranges from 0 to +1 for calls, 0 to -1 for puts.
- **Gamma** — The change in delta for a $1 change in the stock price. Always positive for long option positions.
- **Theta** — The dollar amount an option loses per day from the passage of time, all else equal. Negative for buyers, positive for sellers.
- **Vega** — The change in option price for a 1-percentage-point change in implied volatility. Positive for all long options.
- **Rho** — The change in option price for a 1-percentage-point change in the risk-free interest rate. Positive for long calls, negative for long puts.
- **Sensitivity** — A measure of how much one quantity changes in response to a change in another quantity.
- **Share equivalence** — Expressing an option's directional exposure as the equivalent number of shares of stock (delta x 100 for one contract).
- **Implied volatility (IV)** — The market's current expectation of future stock price movement, expressed as an annualized percentage. Not the same as past movement. The input that vega measures sensitivity to.
- **Vol crush** — A rapid drop in implied volatility, often after an anticipated event like earnings. Causes vega-driven losses for long option holders.
- **LEAPS** — Long-Term Equity AnticiPation Securities. Options with expiration dates one year or more in the future, where rho becomes more relevant.
- **P&L attribution** — Decomposing a position's total profit or loss into the contributions of each Greek (delta/gamma gain, theta cost, vega impact, etc.).

## Quiz

**Q1.** You hold a call with delta 0.45, gamma 0.03, theta -0.05, and vega 0.10. Overnight, the stock goes up $1, one day passes, and implied volatility drops 1%. What is the approximate net change in your option's value?

<details>
<summary>Answer</summary>

Delta gain from $1 stock move: 0.45 x $1 = +$0.45. Theta cost for one day: -$0.05. Vega loss from 1% IV drop: 0.10 x 1 = -$0.10. Net change: $0.45 - $0.05 - $0.10 = **+$0.30**.

With a $1 move, gamma's effect is small — delta would shift from 0.45 to ~0.48, but the gamma adjustment matters more for estimating what happens *next*.
</details>

**Q2.** You are comparing two calls on the same stock. One is ATM with gamma 0.05. The other is deep ITM with gamma 0.01. The stock moves $2. Which option sees a larger change in its delta, and why?

<details>
<summary>Answer</summary>

**The ATM option.** Its delta changes by approximately 0.05 x $2 = 0.10 (e.g., from 0.50 to 0.60). The deep ITM option's delta changes by only 0.01 x $2 = 0.02. Gamma peaks at the money, where delta is most sensitive to stock price changes. Deep ITM options have delta near 1 with little room to move, so gamma is low.
</details>

**Q3.** You sell a call. What is the sign of your position's theta? What about gamma?

<details>
<summary>Answer</summary>

**Theta is positive** — as a seller, time decay works in your favor. The option you sold loses value each day, which benefits you. **Gamma is negative** — the stock moving in either direction shifts delta against you. This is the seller's tradeoff: you collect theta but absorb gamma risk.
</details>

## Recap and What Comes Next

You now have the full dashboard:

- **Delta** — your translator (how much of the stock's movement the option captures)
- **Gamma** — the steepness of the slope (how quickly that translation rate changes)
- **Theta** — your expiring subscription (the daily cost of holding the position)
- **Vega** — your forecast gauge (sensitivity to the market's volatility expectation)
- **Rho** — the smallest Greek (interest rate exposure, rarely loud)

These five numbers give you a complete risk snapshot at any moment. They come with tradeoffs — gamma costs theta, vega benefit requires theta patience — and they change constantly as the stock moves, time passes, and volatility shifts.

In **Module 5**, we zoom into delta with the first real math layer. You will see delta as slope, as hedge size, and as a probability shortcut that can fail in specific regimes.

---

## Sources

[^1]: Option Alpha, "Options Greeks" — Comprehensive overview of all five Greeks with definitions and sign conventions.
[^2]: Options Playbook, "Option Greeks" — Introductory treatment with share equivalence framing.
[^3]: Wikipedia, "Greeks (finance)" — Formal definitions as partial derivatives; note that vega is not an actual Greek letter.
[^4]: OCC Options Education, "Understanding Options Greeks" — Emphasis that Greeks are theoretical guideposts, not guarantees.
[^5]: Charles Schwab, "Get to Know the Option Greeks" — Practical numerical examples; gamma as acceleration.
[^6]: Optiver, "Option Greeks" — Market maker perspective with concrete numerical examples.
[^7]: Nasdaq, "Understanding Delta" — Analysis of delta as sensitivity vs probability; where interpretations diverge.
[^8]: Gaveiga, "Is Delta the Same as ITM Probability?" — Mathematical analysis of why delta is not exactly ITM probability.
[^9]: Britannica Money, "Option Greeks: Delta, Theta, Gamma, Vega" — Gamma dynamics and Greeks interaction example.
[^10]: Charles Schwab, "Theta Decay in Options Trading" — Theta acceleration near expiration; theta-vega tension.
[^11]: Tastytrade, "Theta" — Theta as daily time decay rate; highest for ATM options.
[^12]: Tastytrade, "Vega" — Vega affects only extrinsic value; positive for long, negative for short.
[^13]: Britannica Money, "Theta, Vega, and Implied Volatility" — Vega and theta interaction.
[^14]: Corporate Finance Institute, "Rho" — Positive for calls, negative for puts; cost-of-carry reasoning.
[^15]: Steady Options, "Options Rho" — Rho range and practical worked example.
[^16]: HedgePoint Global, "Options Greeks: From Delta to Theta" — Portfolio Greeks and P&L decomposition framework.
[^17]: Quant-Next, "Option Greeks and P&L Decomposition" — Gamma-theta tradeoff; professional risk management.
[^18]: ChartMini, "Options Greeks Guide" — How Greeks change with stock price, time, and volatility.
