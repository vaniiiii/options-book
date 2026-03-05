# Why Options Have Value — Volatility, Time, and Interest Rate Intuition

## The Question Module 2 Left Open

In Module 2, we split every option premium into two pieces: **intrinsic value** (the portion that is already "in the money") and **time value** (the rest). We said that time value shrinks as expiration approaches — traders call this the "melting ice cube" effect, and the metaphor is apt.

But we never answered the deeper question: *why does time value exist at all?*

Three forces drive time value — volatility, time, and interest rates — and by the end of this article you will understand each of them intuitively, without a single formula.

## Prerequisites

You should be comfortable with:

- **Calls and puts** — calls give the right to buy, puts give the right to sell, at a fixed strike price (Module 1)
- **Payoff diagrams** — the hockey-stick shape showing profit and loss at expiration (Module 1)
- **Moneyness** — in the money (ITM), at the money (ATM), out of the money (OTM) (Module 2)
- **Premium = Intrinsic Value + Time Value** — the decomposition we covered in Module 2
- **Time decay exists** — you know time value shrinks, but not yet *why*

If any of those feel unfamiliar, revisit Module 2 before continuing.

## Volatility — It Is About the Bounce, Not the Direction

**Volatility** measures how wildly a stock's price swings over time, regardless of which direction it moves.[^1]

Stocks work the same way. A stock trading at $100 with 20% annualized volatility (meaning the expected range of movement over a full year) is expected to land between roughly $80 and $120 about two-thirds of the time over a year.[^7] The same stock with 40% volatility? Its expected range widens to roughly $60 to $140.[^2]

Notice that volatility says nothing about direction. A stock that climbs steadily from $100 to $130 with small daily moves has *low* volatility. A stock that lurches between $70 and $130 but ends the year right back at $100 has *high* volatility.[^7]

This distinction matters enormously for options, as we will see next.

> **Common misconception**: "The stock went up 30%, so it must be volatile." Not necessarily. Volatility is about the size of the *swings*, not the final destination. A smooth, straight-line climb is low volatility even if the total return is large.

> **Before reading on, estimate this:** A stock trades at $100 with 30% annualized volatility. What is the rough range the stock is expected to stay within over one year, about two-thirds of the time? (Hint: apply the percentage directly to the stock price.)
>
> <details>
> <summary>Check your estimate</summary>
>
> 30% of $100 is $30, so the one-year range is roughly $70 to $130. If you got that, you already have a working feel for what volatility numbers mean.
>
> </details>

### A Note on What "Volatility" Means in Practice

One subtlety we are glossing over: the volatility that actually drives option prices is not what the stock did in the *past*, but what the market *expects* it to do in the *future*. This forward-looking measure is called **implied volatility**, and it is the subject of Module 11. For now, think of volatility as a relatively stable characteristic that some stocks have more of than others, and that the market prices accordingly.

Also keep in mind that a stock's volatility is not fixed forever — it can rise or fall over time, especially around events like earnings announcements, FDA decisions, or market crises. We will explore how and why in Module 11.

## The Asymmetric Payoff — Why Bigger Swings Help Option Buyers

This is the single most important idea in the entire module. Take your time with it.

<video controls width="100%"><source src="media/why-options-have-value/AsymmetricPayoffComparison.mp4" type="video/mp4">Your browser does not support video.</video>

Recall the payoff diagram from Module 1. A stockholder faces symmetric risk: if the stock rises $20, they gain $20. If it drops $20, they lose $20. Every dollar of movement matters equally in both directions.

An option buyer faces something fundamentally different. If the stock moves in their favor by $20, they capture that full $20 of profit (minus the premium). But if it moves $20 against them, they lose only the premium — say, $3. A $20 adverse move costs the same as a $2 adverse move: just the premium.[^9]

This is the **asymmetric payoff**. Downside is capped. Upside is not.

| Position | Stock -$20 | Stock +$20 |
|---|---|---|
| **Stockholder** | Lose $20 | Gain $20 |
| **Call buyer (premium = $3)** | Lose $3 (premium only) | Gain $17 ($20 - $3) |

Now think about what this means for volatility. If a stock barely moves, the option buyer loses their premium — the ATM option expires worthless. If the stock swings wildly, the buyer's losses stay capped but their potential gains grow. Bigger swings only add upside for the buyer.[^9]

This applies to **both** calls and puts. A put buyer benefits from large downward moves. If the stock instead surges upward, the put buyer loses only the premium — the same amount they would lose from a small upward move. More volatility means more chances of a big downward swing, which is pure upside for the put holder.[^9]

### A Tale of Two Stocks

Let us make this concrete. Two stocks, both trading at $100:

- **Stock A** — a stable utility company. Annualized volatility: 15%. Over three months, the stock will likely stay between roughly $92.50 and $107.50.
- **Stock B** — a biotech startup. Annualized volatility: 50%. Over three months, the stock could easily land anywhere between $75 and $125.

Both stocks have ATM call options with a $100 strike expiring in three months. Which call costs more?

Stock B's call — and it is not close. The buyer of Stock B's call has a wide range of prices above $100 where they profit. The buyer of Stock A's call has a narrow window. Since the downside for both buyers is capped at the premium paid, the wider the range of outcomes, the more valuable the option.[^1]

### The Seller's Perspective

Option sellers face the mirror image. They collect a fixed premium but are on the hook for potentially large payouts. When volatility is high, sellers demand more premium to compensate for the greater risk — just as an insurance company charges more to insure a house in a hurricane zone than one in a calm valley. And just like insurance, the premium depends not on whether the hurricane actually hits, but on the *chance* that it could. That chance is volatility.[^1][^2]

> **Common misconception**: "Higher volatility should hurt put buyers because the stock might go up." Wrong. The put buyer's loss from an upward move is capped at the premium. The increased chance of a large *downward* move more than compensates.[^9]

## Time — More Time, More Chances

Now that we understand why bigger swings help option buyers, the next question is natural: what gives the stock more chances to swing?

Here is the payoff from your patience in Module 2. We can finally answer: *why does time value exist?*

The answer connects directly to the asymmetric payoff. More time before expiration gives the stock more days to bounce around — more chances to make a big move in the option buyer's favor. A one-week option gives the stock five trading days to move. A one-year option gives it 252 trading days.[^1][^8]

Since bigger moves only help option buyers (the asymmetric payoff), more time is always beneficial. More time means more chances for a favorable outcome.

That is why time value exists. You are paying for the possibility that time and volatility will work in your favor.

Deeply ITM options already have large intrinsic value, so the "will it finish ITM?" question is mostly settled. Deeply OTM options are far from the strike and have little value of any kind. But ATM options sit right at the boundary of finishing ITM or OTM — maximum uncertainty. That is why time value is highest for ATM options.[^6]

## The Square Root of Time — Why Decay Accelerates

You might expect that doubling the time to expiration would double an option's time value. It does not. Here is why.

<video controls width="100%"><source src="media/why-options-have-value/SqrtTimeDecay.mp4" type="video/mp4">Your browser does not support video.</video>

### The Coin-Flip Walk

Imagine standing at the center of a football field. Every minute, you flip a coin: heads, you step one yard north; tails, one yard south. After one flip, you are exactly one yard from where you started.

After 100 flips, how far away are you? Not 100 yards. The steps partly cancel each other out — some north, some south. On average, you end up about 10 yards from your starting point. That is the square root of 100.[^5]

Stock prices work similarly. Each day's price movement is partly random — some days up, some days down. The total uncertainty after many days does not grow in a straight line. It grows with the square root of the number of days.[^5]

This means a 4-month option is not worth 4 times a 1-month option. It is worth roughly *2 times* (because the square root of 4 is 2). A 9-month option is worth roughly *3 times* a 1-month option (square root of 9 is 3).

> **The reader is encouraged to verify this:** Before reading the table below, try it yourself. If a 1-month ATM call has $3 of time value, estimate the time value for a 4-month and 9-month option using the square-root relationship. Then check your answers against the table.

### Worked Numbers

Let us put concrete numbers on this. Take an ATM call on a $100 stock with moderate volatility. Suppose the 1-month option has $3 of time value. Using the square-root relationship:

| Months to Expiration | Square Root Factor | Approximate Time Value |
|---|---|---|
| 9 months | sqrt(9) = 3 | $9 |
| 4 months | sqrt(4) = 2 | $6 |
| 1 month | sqrt(1) = 1 | $3 |

Now look at the decay pattern. From 9 months to 4 months (5 months passing), time value drops from $9 to $6 — a loss of $3. From 4 months to 1 month (3 months passing), time value drops from $6 to $3 — also a loss of $3. Same dollar amount of decay, but the second drop happens in *less* time.[^5]

As a rough rule of thumb, the last 30 days of an option's life often account for roughly two-thirds of total time-value loss.[^5]

Here is a Python snippet you can run to see the full decay curve — time value at each month from 9 down to 1:

```python
import math

base_tv = 3  # time value of the 1-month option
for months in range(9, 0, -1):
    tv = base_tv * math.sqrt(months)
    print(f"{months} months: ${tv:.2f}")
# Output:
# 9 months: $9.00
# 8 months: $8.49
# 7 months: $7.94
# 6 months: $7.35
# 5 months: $6.71
# 4 months: $6.00
# 3 months: $5.20
# 2 months: $4.24
# 1 month: $3.00
```

Notice how the drops between consecutive months get larger as you approach expiration. From month 9 to month 8: a loss of $0.51. From month 2 to month 1: a loss of $1.24 — more than double.

The curve is gentle on the left and steep on the right. Early months barely dent the time value. The final weeks devour it.

> **Common misconception**: "Time decay is constant — I lose the same amount each week." It is not constant. The square-root relationship means decay is slow at first and fast at the end. Traders who sell options six months out hoping for steady income from time decay often find the first few months disappointingly slow.[^5]

## Interest Rates — The Smallest Factor

Volatility and time do the heavy lifting. Interest rates play a smaller role — but they are real, and you should understand the logic.[^3]

### The Savings Account Argument

Suppose you want exposure to a stock trading at $100 per share and you have $10,000 to work with. You could buy 100 shares and tie up all your capital. Or you could buy one call option for, say, $500 total — and put the remaining $9,500 in a savings account.

At a 1% interest rate, that $9,500 earns about $95 per year. At 5%, it earns about $475 — a $380 difference. The call option lets you keep capital free to earn interest. When rates are higher, the freed-up capital earns *more*, making the call slightly more attractive. So call premiums rise a bit when rates rise.[^3]

For puts, the logic reverses. Buying a put is an alternative to short-selling the stock (selling borrowed shares, hoping to buy them back at a lower price). Short sellers receive cash proceeds that can earn interest. When rates rise, short-selling becomes relatively more attractive, and put premiums fall slightly.[^4]

How much does this matter in practice? For a one-month option, the interest-rate effect is tiny — maybe a few cents. For long-dated options called LEAPS (options with expiration dates more than one year away), the accumulated interest difference can be meaningful. In most trading situations, volatility and time dominate; interest rates are a footnote.[^4]

> **Common misconception**: Either "interest rates do not affect options at all" or "rate changes are a big deal for my trades." Both are wrong. The effect is real but usually small. It only becomes significant for LEAPS or when rates change dramatically.[^3][^4]

## Putting It All Together

We now have the three forces that create time value:

<video controls width="100%"><source src="media/why-options-have-value/ThreeFactorSensitivity.mp4" type="video/mp4">Your browser does not support video.</video>

1. **Volatility** — bigger swings help option buyers because of the asymmetric payoff. The most important factor.
2. **Time** — more time gives volatility room to work, creating more potential for favorable moves. The second most important factor.
3. **Interest rates** — cost-of-carry makes calls slightly more valuable (and puts slightly less) when rates are higher. Usually the smallest factor.

| Factor | Effect on Calls | Effect on Puts | Relative Impact |
|---|---|---|---|
| Volatility up | Price up | Price up | Largest |
| Time up | Price up | Price up | Large |
| Rates up | Price up | Price down | Smallest |

Notice something: volatility and time push calls and puts in the *same* direction. More volatility or more time makes both calls and puts more expensive. Interest rates are the exception — they push calls and puts in opposite directions.[^1]

In real markets, one more force matters: **supply and demand**. Theoretical models give a reference price, but actual trading prices depend on who wants to buy and who wants to sell. Research has shown that roughly one-third of the "expensiveness" of index options comes from demand pressure by large institutions that buy options to protect their stock portfolios, not from any pricing formula.[^11]

The chain of logic is:

1. **Volatility** creates larger potential moves.
2. **Asymmetric payoff** makes large moves favorable for option buyers.
3. **Time** gives volatility more opportunities to create those moves.
4. **Square-root scaling** explains why time-value decay accelerates near expiration.
5. **Interest rates** add a smaller cost-of-carry adjustment on top.

### A Tiny Math Preview (For Module 4)

Up to now, we kept everything verbal. Module 4 will put numbers on each force.

For small changes, traders often use this approximation:

\[
\Delta \text{Option Price} \approx
(\text{stock sensitivity} \times \Delta \text{Stock})
+ (\text{time sensitivity} \times \Delta \text{Time})
+ (\text{volatility sensitivity} \times \Delta \text{IV})
+ (\text{rate sensitivity} \times \Delta \text{Rates})
\]

Read it as a bookkeeping line, not a scary formula: one part from stock movement, one from time passing, one from volatility shifting, one from rates moving.

Quick example: if stock contributes +$1.08, time contributes -$0.06, and volatility contributes -$0.06, net is +$0.96. Module 4 gives each line item its Greek name and shows how to estimate it quickly.

In Module 4, each of these factors will get its own Greek letter name — collectively called **the Greeks** (so named because each sensitivity is represented by a Greek letter). Volatility sensitivity becomes **vega**. Time sensitivity becomes **theta**. Interest rate sensitivity becomes **rho**. But the intuition you just built is the foundation for all of them.

## Glossary

- **Volatility** — A measure of how much a stock's price swings over time, regardless of direction. Stated as an annualized percentage (meaning the expected range over one full year).
- **Annualized volatility** — Volatility expressed as a yearly percentage, making it comparable across stocks regardless of the time period measured.
- **Historical volatility** — Volatility calculated from a stock's past price movements over a specific period.
- **Implied volatility** — The market's expectation of future volatility, derived from current option prices. Covered in depth in Module 11.
- **Asymmetric payoff** — The property of options where the buyer's potential loss is capped (at the premium paid) but the potential gain is not capped.
- **Square root of time** — The relationship describing how uncertainty (and therefore time value) grows proportionally to the square root of time, not linearly.
- **Cost of carry** — The interest that could be earned on capital freed up by buying an option instead of the underlying stock.
- **LEAPS** — Long-term options with expiration dates more than one year away. Interest rate effects are more noticeable on these.
- **Supply and demand** — The real-world market forces that cause actual option prices to deviate from theoretical model prices.

## Quiz

**Question 1:** Stock A has 15% annualized volatility. Stock B has 40% annualized volatility. Both trade at $80. Both have 3-month ATM calls with an $80 strike. Which call option costs more, and why?

<details>
<summary>Answer</summary>

Stock B's call costs more. Higher volatility means a wider range of possible outcomes over three months. Since the call buyer's downside is capped at the premium (asymmetric payoff), the wider range only adds upside potential. Stock B might reach $90, $100, or higher — pure profit for the call buyer. Stock A is unlikely to move far from $80, giving its call buyer a much narrower window.

</details>

**Question 2:** You own a put option with a $5 premium. The underlying stock could move $20 in either direction. Why does higher volatility increase your put's value, even though the stock might go *up* $20?

<details>
<summary>Answer</summary>

If the stock rises $20, you lose your $5 premium — the same amount you would lose from a $2 rise. Your downside is capped. If the stock drops $20, you profit $15. Higher volatility increases the probability of that large downward move, which is where your profit comes from. The asymmetric payoff means the upside from a big drop outweighs the already-capped loss from a big rise.

</details>

**Question 3:** An ATM call option has $4 of time value with 4 months to expiration. Using the square-root-of-time relationship, approximately how much time value will it have with 1 month to expiration?

<details>
<summary>Answer</summary>

About $2. The square root of 4 is 2, and the square root of 1 is 1. The ratio is 1/2, so time value scales from $4 to approximately $2. It does not drop to $1 (that would assume a linear relationship). It drops to $2 because uncertainty grows with the square root of time.

</details>

**Question 4:** You are comparing two ATM calls. Call X has 60 days to expiry on a stock with 20% volatility. Call Y has 30 days to expiry on a stock with 40% volatility. Without calculating an exact price, which call likely has more time value, and why?

<details>
<summary>Answer</summary>

Call Y likely has more time value. Here is the reasoning: Call X has twice the time, which contributes a factor of sqrt(2) ~ 1.41 to its time value. But Call Y has twice the volatility, which contributes a factor of 2. Volatility enters the time-value calculation directly (not as a square root), so doubling volatility has a bigger effect than doubling time. Volatility is the dominant driver — when comparing two options, check volatility first.

</details>

**Question 5:** Interest rates rise from 3% to 5%. A trader holds a one-month ATM call. Should they expect a noticeable change in the option's price? What if they held a two-year LEAP instead?

<details>
<summary>Answer</summary>

For the one-month call, the effect is negligible — maybe a few cents. The freed-up capital earns interest for only one month, so the rate difference barely matters. For a two-year LEAP, the same rate increase accumulates over 24 months of freed-up capital, making a more meaningful difference. Interest rates matter most for long-dated options.

</details>

## Try It Yourself

These exercises use real market data. You do not need a brokerage account — free tools like Yahoo Finance work.

1. **Compare volatility and premiums.** Go to [Yahoo Finance](https://finance.yahoo.com/) and look up the option chains for two stocks: one you expect to be calm (a large utility like Duke Energy or Southern Company) and one you expect to be volatile (a biotech or a high-growth tech stock). Find the ATM calls expiring in roughly the same month. Compare the time value (premium minus intrinsic value) relative to the stock price. Is the volatile stock's ATM call substantially more expensive, as the theory predicts?

2. **Test the square-root relationship.** For any stock on Yahoo Finance, find the ATM call at two different expirations — say, one month out and four months out. Compute the time value for each (premium minus intrinsic value). Divide the longer-dated time value by the shorter-dated time value. Is the ratio close to sqrt(4/1) = 2? Try it for a few stocks and see how well the square-root approximation holds in practice.

3. **Run the Python snippet.** Copy the time-value decay code from the square-root section above into any Python environment (even an online one like [repl.it](https://repl.it/)). Modify the `base_tv` variable to match the 1-month time value you found in exercise 2. Do the predicted values roughly match the actual prices you see in the option chain?

## Recap and What Comes Next

Here is what we covered:

- **Volatility** measures how much a stock bounces around, not which direction it goes. It is the biggest driver of time value.
- The **asymmetric payoff** — capped downside, uncapped upside — is why bigger swings always help option buyers, for both calls and puts.
- **Time** gives volatility room to work. More time means more chances for a big favorable move. That is *why* time value exists.
- Time value does not decay at a constant rate. The **square-root-of-time** relationship means decay accelerates near expiration.
- **Interest rates** affect options through cost-of-carry: higher rates make calls slightly more valuable and puts slightly less. Usually a small effect.
- **Option prices in the real world** are also shaped by supply and demand. Models give a reference price; actual markets set the final price.

### Practical Heuristics

When you encounter real option prices, here are rules of thumb to carry with you:

1. **When comparing two options, check volatility first** — it is the single biggest driver of time value. A stock with 40% vol will have ATM options roughly twice as expensive as a stock with 20% vol, all else being equal.
2. **Use the square-root shortcut for time comparisons** — a 4-month option should have roughly 2x the time value of a 1-month option (sqrt(4) = 2), not 4x. If you see a ratio much larger or smaller, the market is pricing in a volatility change.
3. **Ignore interest rates for short-dated trades** — for options expiring within a few months, the rate effect is pennies. Only factor rates in when looking at LEAPS (1+ year expirations).

In Module 4, we give each of these sensitivities a precise measurement — the **Greeks**. Volatility sensitivity becomes *vega*. Time sensitivity becomes *theta*. Interest rate sensitivity becomes *rho*. Price sensitivity gets *delta*. And the rate of change of delta gets *gamma*. The intuition you just built is the foundation for all of them.

---



[^1]: AnalystPrep, "The Value of an Option," CFA Level 1 Exam Preparation.
[^2]: Optiver, "Options Volatility Explained."
[^3]: OCC, "How a Fed Rate Hike Can Affect Option Prices," Options Education.
[^4]: Fidelity, "Options and Interest Rates," Fidelity Learning Center.
[^5]: Gregory Gundersen, "The Square Root of Time Rule," 2022.
[^6]: Britannica Money, "Option Theta, Vega, and Implied Volatility."
[^7]: Options Playbook, "What Is Volatility?"
[^8]: Montreal Exchange, "Options — Time Decay."
[^9]: Motilal Oswal, "Why Does Volatility Impact Call and Put Options in the Same Way?"
[^11]: Garleanu, Pedersen, Poteshman, "Demand-Based Option Pricing," UC Berkeley Haas School of Business.
