+++
title = "Prediction market infrastructure: Kalshi and Polymarket"
+++

# Prediction market infrastructure: Kalshi and Polymarket

> two independently-derived fair-value models, three risk-control schemes, and an unattended
> data-collection system built to feed all of them continuously.

## Question

A prediction market that settles a short-horizon yes/no question, will Bitcoin be above a fixed
price at expiry, reduces to a barrier-crossing problem: estimate the probability that a price
process crosses a fixed level by a fixed time, then trade around that estimate. This project
built that estimate two separate ways, for two venues, Polymarket's 5-minute BTC up/down market
and Kalshi's 15-minute BTC up/down market, and layered a market-making strategy and a capital
allocator on top of each.

## Data

Two kinds of data were collected, one per venue. For Polymarket, a live BTC price tick from
Coinbase's WebSocket feed (`ticker` channel, `BTC-USD`), paired every few seconds with a snapshot
of the current 5-minute market itself, both token midpoints and time remaining until expiry, and
every model output the system ever produced (a volatility estimate, the market's implied
probability, the model's fair-value probability, the edge between them, and any trading signal
raised), so a live decision could be reconstructed after the fact rather than only existing in a
terminal at the time. For Kalshi, a tighter, one-second snapshot of the order book itself, bid,
ask, spread, and depth, alongside the live BTC price and its short-horizon momentum and
volatility, feeding a 34-field feature store built specifically to test whether that
microstructure predicts which way a 15-minute contract settles.

The collection system itself was built to run unattended, continuously, not just as a script to
run once and read from manually. On the Polymarket side, the Coinbase feed runs on its own daemon
thread with automatic reconnection on any dropped connection, while a separate poller tracks the
market on its own thread, computing the current 5-minute market's identifier directly from
wall-clock time rather than a hardcoded schedule, with an explicit fallback to the next window
for the boundary case where the current one hasn't opened yet. Both threads share state behind an
explicit lock, and a signal handler catches interrupt/terminate signals for a graceful shutdown
rather than an abrupt kill. Underneath both venues sits the same storage layer: SQLite in WAL
mode with foreign keys enforced, and a self-healing integrity check on startup that renames a
corrupted database file to a timestamped backup and rebuilds it fresh rather than crashing. The
intended final piece was a macOS `launchd` job to keep the collector running on a schedule with
no one watching it; that job never actually executed the collector, a broken script path meant it
failed identically on every scheduled run for the entirety of its recorded lifetime, which is the
reason the feature store above was never populated with more than a trivial amount of real data.

## Process

**Barrier-crossing fair value, version 1 (Polymarket, arithmetic diffusion):**
\[
P(\text{price} \ge \text{target at expiry}) = \Phi\!\left(\frac{S_0 - K}{\sigma\sqrt{t}}\right)
\]
\(S_0\) the current BTC price, \(K\) the target, \(\sigma\) a live-estimated instantaneous
volatility, \(t\) time remaining, \(\Phi\) the standard normal CDF, clipped to
\([0.001, 0.999]\).

**Barrier-crossing fair value, version 2 (Kalshi, geometric diffusion):**
\[
P(\text{price} \ge \text{target at expiry}) \approx \sigma_{\text{logistic}}\!\left(1.7 \cdot
\frac{\ln(S_0/K)}{\sigma_{\text{annual}}\sqrt{t / (365 \times 86400)}}\right)
\]
using a fixed annual volatility of 0.60 rather than a live estimate, and the standard \(1.7\)
constant for approximating a normal CDF with a logistic sigmoid. Same underlying question as
version 1, different diffusion assumption, different volatility source; the two were never
compared against each other.

**Avellaneda and Stoikov's (2008) market-making model,**[^avellaneda-stoikov] a second, more
academic implementation of the Kalshi strategy:
\[
r = s - q\gamma\sigma^2(T-t), \qquad \delta^* = \gamma\sigma^2(T-t) + \frac{2}{\gamma}
\ln\!\left(1 + \frac{\gamma}{k}\right)
\]
\(q\) current inventory, \(\gamma\) risk aversion, \(k\) order-arrival intensity, \(s\) the
mid-price. Conservative parameters (\(\gamma=0.3\), \(k=1.5\), a 10-contract position cap) were
set as the starting point, with a more aggressive set explicitly marked for use only after that
first version had been validated.

**Half-Kelly position sizing:**[^kelly]
\[
f^* = \frac{1}{2} \cdot \frac{pb - q}{b}, \qquad q = 1-p
\]
simplified for near-even-money contracts (\(b \approx 1\)), net of a 2% exchange fee and a flat
gas cost per trade. This ran as its own calculator; neither market maker actually imported it,
both sized trades as a flat fixed dollar amount instead.

**Rule-based risk controls**, the first Kalshi variant, all hard-coded rather than advisory: a
50-contract maximum inventory enforced by clamping trade size, a minimum required edge of 2 cents
before a fill fires, a freeze on new positions inside the final 5 minutes before expiry, and a
forced liquidation of any open position at market inside the final 3 minutes regardless of
current profitability, to avoid ever holding a position through settlement.

## Result

No backtest, no trained model, no settled trade exists for any of the above, for the reason
already given in Data: the collector that was supposed to feed all of it real market data never
ran unattended successfully. The engineering itself, two independently-derived pricing models, a
textbook market-maker risk scheme, an academic implementation of the same strategy, and a
resilient, self-healing collection system, is real and correctly built; it simply never got the
chance to be tested against an outcome.

[^avellaneda-stoikov]: Avellaneda, M. and Stoikov, S., "High-Frequency Trading in a Limit Order Book," *Quantitative Finance*, 2008.
[^kelly]: Kelly, J.L., "A New Interpretation of Information Rate," *Bell System Technical Journal*, 1956.

[Back to Research →](/research/)
