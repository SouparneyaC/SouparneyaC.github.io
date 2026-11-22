+++
title = "Designing a low-latency market data system"
description = "A live order book service for Kalshi's prediction markets: one Python process on one small server, with a timer on every stage from the exchange's timestamp to the browser."
+++

# Designing a low-latency market data system

> A book is updated a median 0.01 ms after the process receives a message, in one Python process on a $12-a-month server.

## Abstract and introduction

Kalshi publishes the state of its prediction markets as a stream of text messages. A trader who wants to act on one market needs that market's order book, the list of standing offers to buy and sell, kept current from the stream, and a lost update leaves the book wrong until a fresh copy arrives. This project is a service that does that job. At a small scale it does what a trading firm's market data system does: it reads the feed, keeps the books in memory, repairs a book when it notices a lost update, and sends each change to the browsers that are open.

It is one Python process with one event loop, a single worker that runs one task at a time and switches whenever a task waits. It runs on a cloud server with 2 virtual CPUs and 2 GiB of memory in the same Amazon region as Kalshi's API host, for about $12 a month, and it has been in service since 21 October 2026, which is 32 days as of 22 November. Its code is open source.[^repo]

The numbers here come from three tests on the live service: a 48-hour test with a timer on every stage from Kalshi's timestamp to the browser's paint, an overnight test that raised the books held from 400 to 1,000, and an evening of profiling with perf and py-spy. In the 48-hour test the service took in 5,852 messages a second on average, and a book was updated a median 0.01 ms after the process received a message.

## Data

Kalshi runs markets that each ask a yes or no question about a future event. A YES contract pays $1 if the answer is yes and a NO contract pays $1 if it is no. The order book of a market lists the prices at which traders have offered to buy, each with the total quantity offered, and one such price and quantity is a level. Kalshi sends YES bids and NO bids and no asks, because a bid for YES at \(X\) is the same offer as an ask for NO at \(1-X\).[^bids] With a best YES bid of $0.39 and a best NO bid of $0.58, YES can be sold at $0.39 and bought at $0.42.

A snapshot carries every level of both sides, and the receiver replaces its copy of the book with it. An update, which Kalshi calls a delta, carries one price, a side and a signed quantity, and the receiver adds the quantity to that level. In the 48-hour test 133,884 snapshots arrived, against 740,095,376 updates. Kalshi numbers the messages of a subscription in order, so a gap in the numbers shows a lost message.

| Channel | A message carries | Sequence number | Share |
|---|---|---|---|
| order book | one change to one level | yes | 73.2% |
| ticker | the latest price of one market | no | 23.5% |
| trade | one executed trade | yes | 3.2% |

The service takes the ticker and trade channels for every market and the order book channel for its books. It received 1,010,892,111 messages in the 48-hour test, 11,300 a second in the busiest minute.

## Literature review

Most work on order books takes the book as given. Gould and colleagues review limit order books without covering how the book is obtained from a feed,[^gould] and for prediction markets Wolfers and Zitzewitz set out the economics[^wolfers] while the one study of Kalshi I found uses 41.6 million trades.[^bartlett]

Exchanges describe one rule for keeping a book correct. Binance's procedure fetches a snapshot, discards the updates it already contains, checks that each update continues the one before and starts again from a new snapshot when one does not,[^binance] and the service applies that rule to Kalshi's sequence counter. Hardware feed handlers report times in nanoseconds, such as 42 ns inside an FPGA,[^denholm] and the service is software reading one copy of the feed, so I make no comparison. Tene shows that a recorded latency can look better than the system's when slow events are missing from the record,[^tene] so the service times every message, and Dean and Barroso show that at scale the tail decides what users see,[^dean] so the 99th percentile sits beside the median.

## Design decisions

I set eight requirements, among them an exact book, repair of lost updates, delivery that a slow browser cannot hold up, proximity to the exchange and a timer on every stage, and checked each against a measurement.

An update follows one path from Kalshi to a browser. The process receives the message from the WebSocket library, which has already read, decrypted and parsed it, stamps the time, decodes it, applies it to the book and wakes any tab watching that market. The path then splits: the market list on the home page receives batches of changed markets, and the page for one market receives frames with the full book.

![Path of one order book update from Kalshi to a browser, with the timed intervals marked](/images/research/prediction-market-infrastructure/path-light.svg)

I put the server next to the exchange. Kalshi does not publish where it runs, so I checked the host the service connects to: its reverse name is an Amazon EC2 instance in us-east-2, in Ohio, so I chose one EC2 t4g.small instance there. A TCP connection to Kalshi took a median 2.08 ms in 100 trials, and an order book message arrived a median 8.0 ms after Kalshi's timestamp. The cost is a burstable instance, which is slowed when its CPU credits run out, and one availability zone.[^place]

The usual alternative is separate ingest and serving processes joined by a message queue, which keeps a slow browser from holding up ingestion. One process avoids a second program, the queue service and a copy of every update. The cost is that one long task delays everything else: the event loop ran late by 36.5 ms at the 99th percentile and 1,172 ms at the worst, and a full garbage collection, the runtime's pass over memory for unused objects, paused it for a median 35 ms.

The service holds 200 books, so it has to choose which markets get one. Parlays, bets that combine several outcomes, are 95.5% of Kalshi's tickers and 37.3% of its dollars, so ranking by contracts traded fills the list with them: 152 to 162 of the top 200 markets in a 5-minute window, against 81 to 89 by dollars. I rank by dollars. Fifty books go to the markets with the highest lifetime dollar volume and 150 to those with the most recent dollars, a score that loses 15% every 30 seconds. Books chosen from one 5-minute window cover a median 46% of the next window's dollars at 200 books, 54% at 1,000 and 60% at 5,000. The curve flattens near 62%, because about 37% of the next window's dollars are in markets with no trade in the window before.

![Median share of the next 5 minutes' dollars covered against the number of books held, rising from 36% at 50 books to 62% at 10,000](/images/research/prediction-market-infrastructure/coverage-light.svg)

A book keeps each side as two lists in step, prices ascending and quantities at the same positions, so the best bid is the last element and an update finds its price by binary search. Prices are whole numbers of ticks of $0.0001 and quantities whole hundredths of a contract, because 28.1% of recorded trades are at prices that are not a whole number of cents and floating-point numbers would drift. My first version used a dictionary of exact decimals, which stays in the test suite as the reference that random streams of updates are checked against. The cost is memory: a level takes at least 72 bytes in lists where a typed array of 64-bit integers takes 16.[^layout]

Kalshi keeps one counter for the whole order book subscription, and the service checks that each number is one more than the last. A gap means a message was lost but not which market it belonged to, so the service fetches a fresh snapshot of every book and replaces each one. The counter showed 0 gaps in 740,095,376 order book messages in the 48-hour test. A second check, the audit, asks Kalshi every 5 seconds for a snapshot of one book and compares every level, but asking changes which updates Kalshi sends, so a mismatch can come from the check itself.[^audit]

Each open tab has its own queue of 20 messages and its own sender, and a push is encoded once and offered to every tab without waiting for any of them. A tab that falls 20 messages behind has its queue emptied and receives one full copy of the market list, and a tab whose send takes over 5 seconds is closed. My first version awaited each send in turn, and on 25 October one stuck tab froze all of them.[^stuck] Pushes now leave at most every 100 ms, and the cost is delay: a median 99 ms from receipt to send, most of it the throttle.

The service also summarizes the stream in fixed memory for three dashboard panels, with algorithms from Chapter 6 of Blum, Hopcroft and Kannan:[^blum] a heavy-hitters summary, a count of different markets and a concentration index. The heavy-hitters summary, Misra-Gries, holds \(K\) counters and is certain to hold every market above a share of \(1/(K+1)\) of the stream, a promise about a share that gives no ranking. For choosing books I use exact counters. Replaying 18.1 million recorded trades, a summary with 5,000 counters found 99.5% of the exact top 200 markets, so it would work, and memory was never the limit.

## How the system measures itself

The service times seven intervals on the path above, and every sample goes into a histogram with logarithmic buckets, 16 to each doubling of the value, so a value read back is at most 2.2% from the sample.[^ddsketch] The service keeps one histogram per stage for each minute, and because percentiles of different minutes cannot be averaged, minutes are combined by adding bucket counts. Once a minute the event loop hands the histograms to a writer thread, which adds the process's CPU and memory and writes the rows with the code commit, so comparing two versions of the code is a query. The store checks itself: the histogram counts must equal the stage totals and the minutes must be contiguous, and both held for all 2,880 minutes of the 48-hour test.

## Results

In the 48-hour test a message reached the process a median 8.0 ms after Kalshi's timestamp and 73 ms at the 99th percentile. The book was updated a median 0.01 ms after receipt and 0.065 ms at the 99th percentile. From receipt to the end of the send the median was 99 ms and the 99th percentile 153 ms, mostly the 100 ms throttle. Where a tab reported back, it saw 19 ms at the median and 2,661 ms at the 99th percentile from the server's stamp to the paint, but only 103 of the 2,880 minutes have such a report, and the intervals use different clocks, so they do not add.

![Median and 99th percentile of four intervals on a log scale: 8.0 and 73 ms from Kalshi's timestamp to receipt, 0.01 and 0.065 ms from receipt to the book updated, 99 and 153 ms from receipt to send, 19 and 2,661 ms from send to paint](/images/research/prediction-market-infrastructure/latency-hops-light.svg)

In the 48-hour test the process used 33.0% of one core on average, 65.0% in its busiest minute, and 318.9 MB of memory. The named handlers account for 38.0% of its CPU, so no timer of mine covers the other 62.0%.

![Share of the process's CPU time by message handler: order book messages 21.1%, all named handlers 38.0%, and 62.0% attributed to no handler timer](/images/research/prediction-market-infrastructure/cpu-by-handler-light.svg)

A profile showed where the rest goes. perf samples the CPU's cycle counter and py-spy samples the Python stacks, and I ran both on the live service for two 120-second runs in an evening, at 400 books with no browser open. The Python interpreter running Python code took 77% of the cycles. TLS decryption, decompression, JSON decoding and the kernel's TCP receive path took 2 to 4% each.

![Share of CPU cycles by where they were spent, in two perf runs: Python interpreter 77.3 and 76.5%, libc 4.7 and 5.0%, kernel TCP receive 3.7 and 4.2%, JSON decoding 3.3%, TLS 3.0 and 2.4%, decompression 2.3 and 2.4%](/images/research/prediction-market-infrastructure/perf-split-light.svg)

In the overnight test every step from 400 to 1,000 books passed all eleven pass-or-fail budgets written down beforehand, including no sequence gaps, with CPU between 25.8% and 31.5% of one core.[^capacity] Going from 200 to 1,000 books adds 8 points of coverage.

![Three panels against books held from 200 to 1,000: average CPU between 26 and 31% of one core, event loop lag at the 99th percentile falling from 47 to 16 ms, and messages per second falling from 6,420 to 4,646](/images/research/prediction-market-infrastructure/capacity-light.svg)

## Limitations

I cannot say how accurate the books are, because the audit changes what it measures. The sequence check covers only the order book subscription. The concentration estimator I deployed had a typical error of 58% on recorded trades, where the version Alon, Matias and Szegedy describe, which I tested offline, had 10% with 48 counters, and the count of different markets read a median 1.62 times the exact count, so I treat those two dashboard panels as indicators. The timers stop when the server sends, so they cannot say how long an event at Kalshi takes to reach a screen.

The profile is one evening at 400 books with no browser open, so it covers ingest only, and delivery to browsers was not profiled. In one capacity step the event loop stalled for about a second at 600 books, and I have not explained it. A C++ port of the book matched the Python book on 385,631 live updates, and I have not timed it end to end. The service runs on one machine in one availability zone and is deployed by hand, and it has no order path: it only subscribes to the feed.

[^repo]: The code is at [github.com/SouparneyaC/kalshi-orderbook-dashboard](https://github.com/SouparneyaC/kalshi-orderbook-dashboard) under the MIT license, and the numbers behind this page are in its folder `numbers/`. The server runs the commit that was measured, and the public `app/` folder is the same code apart from comments, import lines and five file paths.
[^bids]: Kalshi's order book documentation states that it returns YES bids and NO bids only, for this reason.
[^gould]: Gould, M.D. et al., "Limit order books," *Quantitative Finance*, 2013.
[^wolfers]: Wolfers, J. and Zitzewitz, E., "Prediction markets," *Journal of Economic Perspectives*, 2004.
[^bartlett]: Bartlett, R. and O'Hara, M., "Adverse selection in prediction markets: evidence from Kalshi," SSRN 6615739, 2026.
[^binance]: Binance developer documentation, "How to manage a local order book correctly," read 15 November 2026.
[^denholm]: Denholm, S. et al., "Network-level FPGA acceleration of low latency market data feed arbitration," *IEICE Transactions on Information and Systems*, 2015.
[^tene]: Tene, G., "Coordinated omission," Mechanical Sympathy list, 2013.
[^dean]: Dean, J. and Barroso, L.A., "The tail at scale," *Communications of the ACM*, 2013.
[^place]: Kalshi does not publish its location, so the region is an inference from the host's reverse name and Amazon's published address ranges. A TCP connection time is one round trip and says nothing about the delay of a message. At $0.0168 an hour, 730 hours cost $12.30.
[^layout]: I have not timed lists, binary search and typed arrays against each other on the server.
[^audit]: In the 48-hour test 19,278 audits came back with 423 wrong levels among 1,493,750 compared, and 41.7% of requests got no reply, for a reason I did not find. The counts show that the audit ran and give no accuracy rate.
[^stuck]: The code comment written that day records 97,018 dropped updates.
[^blum]: Blum, A., Hopcroft, J. and Kannan, R., *Foundations of Data Science*, Cambridge University Press, 2020, Chapter 6.
[^ddsketch]: Masson, C. et al., "DDSketch," *Proceedings of the VLDB Endowment*, 2019, use the same bucket index.
[^capacity]: The market's own message rate fell from 6,489 a second at 400 books to 4,646 at 1,000 overnight, so CPU per book is not separated from the quieting market. Each step ran 9 to 26 minutes after a 10-minute warm-up.
