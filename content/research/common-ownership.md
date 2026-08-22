+++
title = "Common ownership and institutional disclosure"
+++

# Common ownership and institutional disclosure

> funds that revise their ownership filings show a real, measurable jump in reported size right when the revision happens, not random noise.

## Abstract and introduction

This page documents a replication and extension of Backus, Conlon, and Sinkinson (2019), "Common Ownership in America: 1980 to 2017," using QUANTkiosk 13F institutional holdings data. The original paper measured how much competing S&P 500 firms are owned by the same investors, using a profit weight statistic, and found the effect roughly tripled between 1980 and 2017. This project extends that measurement to 2013Q3 through 2025Q4, across nine S&P 500 firms spanning tech, airlines, banks, pharmaceuticals, and semiconductors, chosen to match the original paper's own case study industries plus one firm, NVIDIA, added to document common ownership forming in real time as a company enters the index fund universe.

Two things anchor the work. The first is the headline replication itself: mean common ownership, averaged across all firm pairs in the sample, sits at roughly 0.74 to 0.77 for most of 2013 through 2025, consistent with the original paper's own cross-sectional estimate of 0.70 for large-cap firms in 2017, with a real dip during the COVID-19 period and a sharp rise as NVIDIA's ownership converged toward the existing mega-cap cluster. The second is methodological: getting that headline number right required finding and fixing four distinct, real data quality problems in the underlying filings, each one individually large enough to have produced a materially wrong result if left uncorrected, and each fix is documented here rather than folded silently into the final chart.

## Data

Holdings data comes from the QUANTkiosk API, which aggregates SEC 13F institutional holdings filings, covering 13F-HR original filings and 13F-HR/A amendments. Shares outstanding come from SEC EDGAR's XBRL structured data (10-K and 10-Q filings). The nine-firm sample: Apple and Microsoft as large-cap tech anchors, American Airlines and Delta as a within-industry airline pair, JPMorgan Chase and Bank of America as a within-industry bank pair, Pfizer and Merck as a within-industry pharmaceutical pair, and NVIDIA as a structural growth story, tracked across 50 quarters.

Raw institutional holdings data cannot be used as reported. Four distinct, real data quality problems were found and corrected, each one large enough to materially distort the headline number if left unfixed.

**An EDGAR XBRL units error.** Apple's 10-Q for the first quarter of 2014 reported its own shares outstanding in thousands rather than the correct unit, making the reported denominator one thousand times too small. Left uncorrected, this clips most institutional ownership fractions to 1.0 and collapses the common ownership measure toward zero for that single quarter. The fix is a direct multiplicative correction once the unit error is identified.

**A stock split timing mismatch.** NVIDIA's four for one stock split took effect July 19, 2021, four days before its fiscal second quarter ended. An automated data pipeline that selects the filing closest to calendar quarter end picked up the post split share count, while the 13F holdings for that same quarter were filed pre split, producing a four times mismatch that suppresses every NVIDIA ownership fraction for that quarter. The fix uses the correct pre-split share count for that specific quarter's computation.

**Convertible security contamination.** Two firm-periods show aggregate reported institutional holdings exceeding 100% of a company's actual shares outstanding, which is only possible when institutions are also reporting derivative or debt positions as share equivalents. NVIDIA shows this from 2014Q4 through 2017Q1, following $1.5 billion in convertible notes issued in November 2013, peaking at 3.59 times reported shares outstanding in 2016Q1. American Airlines shows the same pattern from 2020Q2 through 2025Q2, following CARES Act warrants issued to the federal government in exchange for COVID relief funding. Both are corrected by scaling all reported holdings proportionally so that aggregate institutional ownership equals actual shares outstanding.

**A vendor amendment capture failure.** A separate audit found State Street's reported holdings implausible for two consecutive quarters in 2015. The most likely mechanism, based on how SEC 13F filings work: State Street filed a corrective amendment for those quarters that the data vendor's aggregation pipeline never ingested, a documented class of problem. Backus, Conlon, and Sinkinson's own paper notes in a footnote that Thomson Reuters' widely used S34 database suffered a similar, unexplained coverage decline around 2010, citing an earlier audit by Ben-David et al. (2018); the same amendment-capture failure mode can affect any vendor aggregating 13F data, this project's own data source included. This is not an error in the underlying SEC filings themselves, which do show the amendments in EDGAR's own full-text search; it is a failure in how the data vendor's pipeline ingested them. A first attempted fix, a uniform scale-up factor applied to the affected rows, proved insufficient and had to be revisited.

## Literature review

The core measurement in this project descends directly from Rotemberg (1984), who first formalized the idea that a firm's strategic decisions can place weight on a rival's profits in proportion to how much of both firms' equity is held by the same investors, under a proportional-control assumption: one share, one vote. Backus, Conlon, and Sinkinson (2019) apply this profit-weight framework at scale across the S&P 500 from 1980 to 2017 using Thomson Reuters institutional holdings data, and find the average profit weight investors place on rivals' profits roughly tripled over that period, driven primarily by the rise of institutional and especially index ownership. This project is a direct, out-of-sample replication of their measurement using a different data vendor and a more recent period, which is also why finding the vendor-specific data quality problems above mattered: a replication that silently inherited a data vendor's own errors would not actually test whether the original finding holds up.

Azar, Schmalz, and Tecu (2018), published in the Journal of Finance, connect this same ownership structure to real market outcomes, finding that airlines with more common ownership by the same large institutional investors charge higher fares, direct empirical evidence that the profit-weight measurement is not just a bookkeeping statistic but is associated with a real anticompetitive channel. This is part of why the nine-firm sample deliberately includes an airline pair (American Airlines and Delta) rather than only technology firms: airlines are the case study industry where the profit-weight literature has already found a real price effect, not merely a correlational curiosity.

## Models

The central quantity is \( \kappa_{fg} \), the weight firm \( f \) places on firm \( g \)'s profits, computed under Rotemberg's proportional-control assumption as

\[
\kappa_{fg} = \frac{\sum_s \beta_{fs} \cdot \beta_{gs}}{\sum_s \beta_{fs}^2}
\]

where \( \beta_{fs} \) is the fraction of firm \( f \)'s shares held by institutional investor \( s \). The denominator, \( \sum_s \beta_{fs}^2 \), is firm \( f \)'s investor Herfindahl-Hirschman index, a measure of how concentrated its own ownership is; the numerator is the dot product of two firms' ownership vectors, capturing how much the same investors hold both. This decomposes geometrically into two separate forces:

\[
\kappa_{fg} = \cos(\beta_f, \beta_g) \cdot \sqrt{\frac{\text{IHHI}_g}{\text{IHHI}_f}}
\]

Cosine similarity rises as institutional investors adopt increasingly similar, indexed portfolios, and is the dominant driver of the long-run upward trend; the IHHI ratio term means that a firm with a lot of atomistic retail ownership (low IHHI) will show an inflated profit weight toward its rivals for a purely mechanical reason, not a behavioral one, which is why the ratio term has to be reported and understood separately rather than folded silently into one number. The economy-wide summary statistic reported throughout, \( \bar\kappa_t \), averages \( \kappa_{fg,t} \) across every ordered firm pair in the sample; for this project's nine-firm sample that is 72 ordered pairs.

Large institutional investors file 13F reports under multiple legal sub-entities, one per CIK, and if left unconsolidated the profit-weight formula treats each sub-entity as a separate, smaller investor, understating that investor's true influence. BlackRock alone filed under seven distinct CIKs from 2013 through 2016. A parent-entity map covering 66 subsidiary CIKs across 22 institutional parents corrects this; for Apple specifically, the uncorrected, fragmented BlackRock ownership share understates BlackRock's true contribution to Apple's investor concentration by roughly 300 times relative to the consolidated figure, before BlackRock voluntarily consolidated its own filings in 2017.

## Backtesting

The equivalent of an out-of-sample check in this project is not a predictive backtest, since there is no future outcome being forecast, but a robustness audit: does the headline number survive scrutiny of the pipeline that produced it, and does the direction of every correction match what the underlying mechanism predicts. Each of the four data quality corrections above was validated this way before being trusted. The BlackRock entity consolidation correction was checked directly against Apple's own ownership data: plotting BlackRock's beta under the fragmented, largest-single-entity-only reading against the corrected, fully consolidated reading shows the uncorrected line sitting at roughly 2.5 to 3 percent of Apple's shares throughout 2013 to 2017, while the corrected line sits at roughly 5 to 6.5 percent, a persistent, mechanical understatement that closes exactly at 2017, the year BlackRock consolidated its own filings, which is the behavior the correction's own logic predicts rather than an arbitrary adjustment.

The convertible security contamination correction was validated the same way: both NVIDIA's 2014 to 2017 convertible-note episode and American Airlines' 2020 to 2025 CARES Act warrant episode show reported institutional holdings crossing 100% of actual shares outstanding, in NVIDIA's case peaking at 3.59 times, and both episodes have a real, dateable corporate event, the note issuance and the warrant issuance respectively, that explains why the contamination appears exactly when it does and not before or after.

## Results

![Common ownership profit weights across nine S&P 500 firms, 2013 to 2025, with all data quality corrections applied](/images/research/common-ownership/fig1_kappa_over_time.png)

*Mean common ownership profit weight across all 72 ordered pairs in the nine-firm sample, quarterly from 2013Q3 through 2025Q4, with a five-quarter centered moving average. The green line marks the original paper's own 2017 S&P 500 average of 0.70; this sample tracks close to that level for most of the period, dips visibly during the COVID-19 institutional divergence in airline stocks, and rises sharply as NVIDIA's ownership converged toward the existing mega-cap cluster during the 2023 to 2025 AI investment period.*

![Institutional holdings as a percent of shares outstanding for NVIDIA and American Airlines, showing convertible security and CARES Act warrant contamination](/images/research/common-ownership/fig3_contamination.png)

*The diagnostic that caught two of the four data quality problems above: reported institutional holdings should never exceed 100% of a firm's actual shares outstanding, and when they do, something in the filings is being double-counted. NVIDIA's convertible notes and American Airlines' COVID-era warrants both show a clean, dateable spike well past the paper's own 120% contamination threshold, which is what let both be diagnosed and corrected with confidence rather than guessed at.*

![Common ownership decomposed by institutional investor type, showing the Big Three's rising share of total profit weight](/images/research/common-ownership/fig_kappa_decomposition.png)

*The headline trend broken down by investor type: BlackRock, Vanguard, and State Street, the "Big Three," account for roughly 60% of total common ownership profit weight at the start of the sample in 2014 and climb to roughly 85% or more by 2025, with passive and actively managed funds making up most of the remainder. This is the concrete mechanism behind the rising headline trend: the effect is not diffuse across the institutional investor universe, it is concentrated in, and driven by, a small number of very large asset managers.*

## What this doesn't show

The nine-firm sample is deliberately matched to the original paper's own case study industries, not drawn as a random or representative sample of the full S&P 500, so the headline 0.74 to 0.77 range should be read as consistent with the paper's own large-cap estimate, not as an independent, full-index confirmation of it. The profit-weight measure also assumes proportional control, one share, one vote, following Rotemberg (1984) directly; it does not model the alternative control-weighting assumptions the original paper itself discusses as a robustness check, and this replication has not yet re-run those alternative specifications.

## Conclusion

The headline replication holds up: common ownership among large, competing S&P 500 firms remains high through 2025, consistent with the original paper's own 2017 estimate, and continues to be driven overwhelmingly by a small number of very large asset managers rather than the institutional investor universe broadly. Getting to that number honestly required treating the data pipeline itself as something to be audited, not trusted by default; four separate, real data quality problems were found, diagnosed against their actual corporate-event cause, and corrected, and the size of each correction was large enough that skipping any one of them would have produced a materially wrong headline result.

## References

Azar, J., Schmalz, M. C., and Tecu, I. (2018). Anticompetitive effects of common ownership. Journal of Finance, 73(4), 1513 to 1565.

Backus, M., Conlon, C., and Sinkinson, M. (2019). Common ownership in America: 1980 to 2017. NBER Working Paper 25454.

Ben-David, I., et al. (2018). Data quality issues in the Thomson Reuters S34 institutional holdings database (cited in Backus, Conlon, and Sinkinson 2019, Appendix B).

Rotemberg, J. J. (1984). Financial transaction costs and industrial performance. MIT Sloan Working Paper.

[Repository →](https://github.com/SouparneyaC/Common-Ownership-in-America-QK-Replication)
