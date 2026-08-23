+++
title = "Common ownership and institutional disclosure"
+++

# Common ownership and institutional disclosure

> the same handful of large asset managers now own most of the stake competing S&P 500 companies place weight on each other's profits through, and that concentration keeps rising.

## Abstract and introduction

The same handful of large asset managers now hold stakes in nearly every company competing in a given industry. Whether that changes how those companies actually compete, through the profit weight one firm's investors place on a rival's earnings, is the empirical question Backus, Conlon, and Sinkinson (2019) set out to answer for the S&P 500 from 1980 to 2017, finding the effect roughly tripled over that period. This page replicates and extends their measurement using QUANTkiosk 13F institutional holdings data, from 2013Q3 through 2025Q4, across nine S&P 500 firms spanning tech, airlines, banks, pharmaceuticals, and semiconductors. The sample matches the original paper's own case study industries, plus one firm, NVIDIA, added to watch common ownership form in real time as a company enters the index fund universe.

Two things anchor the work. Mean common ownership, averaged across all firm pairs in the sample, sits at roughly 0.74 to 0.77 for most of 2013 through 2025, consistent with the original paper's own cross-sectional estimate of 0.70 for large-cap firms in 2017. It dips during COVID-19 and rises sharply as NVIDIA's ownership converges toward the existing mega-cap cluster. The second finding is methodological: getting that headline number right required finding and fixing four distinct data quality problems in the underlying filings, each one large enough on its own to produce a materially wrong result if left uncorrected. Each fix is documented here rather than folded silently into the final chart.

## Data

Holdings data comes from the QUANTkiosk API, which aggregates SEC 13F institutional holdings filings, covering 13F-HR original filings and 13F-HR/A amendments. Shares outstanding come from SEC EDGAR's XBRL structured data, 10-K and 10-Q filings. The nine-firm sample: Apple and Microsoft as large-cap tech anchors, American Airlines and Delta as a within-industry airline pair, JPMorgan Chase and Bank of America as a within-industry bank pair, Pfizer and Merck as a within-industry pharmaceutical pair, and NVIDIA as a structural growth story, tracked across 50 quarters.

Raw institutional holdings data can't be used as reported. Four data quality problems, each large enough to materially distort the headline number, had to be found and corrected first.

Apple's 10-Q for the first quarter of 2014 reported its own shares outstanding in thousands rather than the correct unit, making the reported denominator a thousand times too small. Left uncorrected, this clips most institutional ownership fractions to 1.0 and collapses the common ownership measure toward zero for that single quarter, fixed by a direct multiplicative correction once the unit error was identified.

NVIDIA's four-for-one stock split took effect July 19, 2021, four days before its fiscal second quarter ended. An automated data pipeline that selects the filing closest to calendar quarter end picked up the post-split share count, while the 13F holdings for that same quarter were filed pre-split, producing a four-times mismatch that suppressed every NVIDIA ownership fraction for that quarter. The fix uses the correct pre-split share count for that specific quarter's computation.

Two firm-periods show aggregate reported institutional holdings exceeding 100% of a company's actual shares outstanding, only possible when institutions are also reporting derivative or debt positions as share equivalents. NVIDIA shows this from 2014Q4 through 2017Q1, following $1.5 billion in convertible notes issued in November 2013, peaking at 3.59 times reported shares outstanding in 2016Q1. American Airlines shows the same pattern from 2020Q2 through 2025Q2, following CARES Act warrants issued to the federal government in exchange for COVID relief funding. Both are corrected by scaling all reported holdings proportionally so that aggregate institutional ownership equals actual shares outstanding.

A separate audit found State Street's reported holdings implausible for two consecutive quarters in 2015. The most likely mechanism, given how SEC 13F filings work: State Street filed a corrective amendment for those quarters that the data vendor's aggregation pipeline never ingested, a documented class of problem.[^tr-coverage-decline] The underlying SEC filings themselves aren't wrong, EDGAR's own full-text search shows the amendments; the failure is in how the vendor's pipeline ingested them. A first attempted fix, a uniform scale-up factor applied to the affected rows, proved insufficient and had to be revisited.

[^tr-coverage-decline]: Backus, Conlon, and Sinkinson's own paper notes the same failure mode in a footnote: Thomson Reuters' widely used S34 database suffered a similar, unexplained coverage decline around 2010 (Ben-David et al., 2018). The same amendment-capture failure can affect any vendor aggregating 13F data, this project's own source included.

## Literature review

Rotemberg (1984) first formalized the core idea this project measures: a firm's strategic decisions can place weight on a rival's profits in proportion to how much of both firms' equity is held by the same investors, under a proportional-control assumption, one share, one vote. Backus, Conlon, and Sinkinson (2019) apply this profit-weight framework at scale across the S&P 500 from 1980 to 2017 using Thomson Reuters institutional holdings data, and find the average profit weight investors place on rivals' profits roughly tripled over that period, driven primarily by the rise of institutional and especially index ownership. This project replicates their measurement out of sample, using a different data vendor and a more recent period. That's also why the vendor-specific data quality problems above mattered: a replication that silently inherited a data vendor's own errors wouldn't actually test whether the original finding holds up.

Azar, Schmalz, and Tecu (2018), published in the Journal of Finance, connect this same ownership structure to market outcomes: airlines with more common ownership by the same large institutional investors charge higher fares, direct empirical evidence that the profit-weight measurement tracks an anticompetitive channel, not just a bookkeeping statistic. That's part of why the nine-firm sample deliberately includes an airline pair, American Airlines and Delta, rather than only technology firms: airlines are the case study industry where the profit-weight literature has already found a price effect, not a correlational curiosity.

## Models

The central quantity is \( \kappa_{fg} \), the weight firm \( f \) places on firm \( g \)'s profits. It falls out of one assumption: a firm maximizes a weighted sum of its shareholders' portfolio profits, not just its own, following Rotemberg (1984). Under proportional control, one share, one vote, that assumption reduces to

\[
\kappa_{fg} = \frac{\sum_s \beta_{fs} \cdot \beta_{gs}}{\sum_s \beta_{fs}^2}
\]

where \( \beta_{fs} \) is the fraction of firm \( f \)'s shares held by institutional investor \( s \). The denominator, \( \sum_s \beta_{fs}^2 \), is firm \( f \)'s investor Herfindahl-Hirschman index, how concentrated its own ownership is; the numerator is the dot product of two firms' ownership vectors, how much the same investors hold both. This decomposes geometrically into two separate forces:

\[
\kappa_{fg} = \cos(\beta_f, \beta_g) \cdot \sqrt{\frac{\text{IHHI}_g}{\text{IHHI}_f}}
\]

Cosine similarity rises as institutional investors adopt increasingly similar, indexed portfolios, and is the dominant driver of the long-run upward trend. The IHHI ratio term is mechanical, not behavioral: a firm with a lot of atomistic retail ownership (low IHHI) will show an inflated profit weight toward its rivals for that reason alone, which is why the ratio term gets reported and understood separately rather than folded silently into one number. The economy-wide summary statistic reported throughout, \( \bar\kappa_t \), averages \( \kappa_{fg,t} \) across every ordered firm pair in the sample, 72 ordered pairs for this project's nine-firm sample.

Large institutional investors file 13F reports under multiple legal sub-entities, one per CIK. Left unconsolidated, the profit-weight formula treats each sub-entity as a separate, smaller investor, understating that investor's actual influence. BlackRock alone filed under seven distinct CIKs from 2013 through 2016. A parent-entity map covering 66 subsidiary CIKs across 22 institutional parents corrects this.[^blackrock-scale]

[^blackrock-scale]: For Apple specifically, the uncorrected, fragmented BlackRock ownership share understated BlackRock's actual contribution to Apple's investor concentration by roughly 300 times relative to the consolidated figure, before BlackRock voluntarily consolidated its own filings in 2017.

## Backtesting

This project's equivalent of an out-of-sample check isn't a predictive backtest, there's no future outcome being forecast. It's a robustness audit: does the headline number survive scrutiny of the pipeline that produced it, and does the direction of every correction match what the underlying mechanism predicts? Each of the four data quality corrections above was validated this way before being trusted.

The BlackRock entity consolidation correction was checked directly against Apple's own ownership data. Plotting BlackRock's stake under the fragmented, largest-single-entity-only reading against the corrected, fully consolidated reading shows the uncorrected line sitting at roughly 2.5 to 3 percent of Apple's shares throughout 2013 to 2017, while the corrected line sits at roughly 5 to 6.5 percent. That gap closes exactly at 2017, the year BlackRock consolidated its own filings, the behavior the correction's own logic predicts, not an arbitrary adjustment.

The convertible security contamination correction was validated the same way. Both NVIDIA's 2014-to-2017 convertible-note episode and American Airlines' 2020-to-2025 CARES Act warrant episode show reported institutional holdings crossing 100% of actual shares outstanding, in NVIDIA's case peaking at 3.59 times, and both episodes trace to a dateable corporate event, the note issuance and the warrant issuance respectively, that explains why the contamination appears exactly when it does and not before or after.

## Results

Mean common ownership profit weight, averaged across all 72 ordered pairs in the nine-firm sample and reported quarterly from 2013Q3 through 2025Q4 with a five-quarter centered moving average, tracks close to the original paper's own 2017 S&P 500 average of 0.70 for most of the period. It dips visibly during the COVID-19 institutional divergence in airline stocks and rises sharply as NVIDIA's ownership converges toward the existing mega-cap cluster during the 2023-to-2025 AI investment period.

![Common ownership profit weights across nine S&P 500 firms, 2013 to 2025, with all data quality corrections applied](/images/research/common-ownership/fig1_kappa_over_time.png)

Reported institutional holdings should never exceed 100% of a firm's actual shares outstanding; when they do, something in the filings is being double-counted. That's the diagnostic that caught two of the four data quality problems above. NVIDIA's convertible notes and American Airlines' COVID-era warrants both show a clean, dateable spike well past the paper's own 120% contamination threshold, which is what let both be diagnosed and corrected with confidence rather than guessed at.

![Institutional holdings as a percent of shares outstanding for NVIDIA and American Airlines, showing convertible security and CARES Act warrant contamination](/images/research/common-ownership/fig3_contamination.png)

Broken down by investor type, BlackRock, Vanguard, and State Street, the "Big Three," account for roughly 60% of total common ownership profit weight at the start of the sample in 2014 and climb to roughly 85% or more by 2025, with passive and actively managed funds making up most of the remainder. That's the concrete mechanism behind the rising headline trend: the effect concentrates in a small number of very large asset managers, not the institutional investor universe broadly.

![Common ownership decomposed by institutional investor type, showing the Big Three's rising share of total profit weight](/images/research/common-ownership/fig_kappa_decomposition.png)

## What this doesn't show

The nine-firm sample is deliberately matched to the original paper's own case study industries, not drawn as a random or representative sample of the full S&P 500. The headline 0.74-to-0.77 range should be read as consistent with the paper's own large-cap estimate, not as an independent, full-index confirmation of it. The profit-weight measure also assumes proportional control, one share, one vote, following Rotemberg (1984) directly. It doesn't model the alternative control-weighting assumptions the original paper itself discusses as a robustness check; this replication hasn't yet re-run those alternative specifications.

## Conclusion

The headline replication holds up. Common ownership among large, competing S&P 500 firms remains high through 2025, consistent with the original paper's own 2017 estimate, and continues to be driven overwhelmingly by a small number of very large asset managers, not the institutional investor universe broadly. Getting to that number required treating the data pipeline itself as something to be audited, not trusted by default. Four separate data quality problems were found, diagnosed against their actual corporate-event cause, and corrected, each one large enough that skipping it would have produced a materially wrong headline result.

## References

Azar, J., Schmalz, M. C., and Tecu, I. (2018). Anticompetitive effects of common ownership. Journal of Finance, 73(4), 1513 to 1565.

Backus, M., Conlon, C., and Sinkinson, M. (2019). Common ownership in America: 1980 to 2017. NBER Working Paper 25454.

Ben-David, I., et al. (2018). Data quality issues in the Thomson Reuters S34 institutional holdings database (cited in Backus, Conlon, and Sinkinson 2019, Appendix B).

Rotemberg, J. J. (1984). Financial transaction costs and industrial performance. MIT Sloan Working Paper.

[Repository →](https://github.com/SouparneyaC/Common-Ownership-in-America-QK-Replication)
