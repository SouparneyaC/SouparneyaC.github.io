+++
title = "Crowd belief compression in a live forecasting competition"
+++

# Crowd belief compression in a live forecasting competition

> the crowd's own consensus is systematically too cautious, and correcting for that, not building a smarter model, was the actual source of the edge.

## Abstract and introduction

This page documents a solo research campaign inside the Probability Competition FIFA WC2026, run by Jump Trading and Sports Predict, a World Cup prediction tournament that scores every submitted probability with Relative Brier Points (RBP): a forecaster gains when a submission lands closer to the true outcome than the platform's own crowd consensus on the same question, and loses when it does not. RBP is a proper scoring rule, so the optimal strategy is always to report an honest belief; the competitive question is not whether to be honest but where the crowd itself is wrong.

I built a full statistical forecasting pipeline for this competition: point in time Elo ratings over more than 49,000 historical international matches back to 1872, a Poisson goal rate regression with a Dixon and Coles low score correction and a Negative Binomial overdispersion correction, a parallel ordered logit match result model, and a StatsBomb event level panel covering the 2018 and 2022 World Cups. The methodology rests on a systematic review of the goal modeling, rating based, calibration, and crowd aggregation literatures, summarized below.

Two findings anchor the work. The first, and the one this page leads with, is that the competition's crowd consensus is structurally compressed toward 50%, in a way that is quantified, not just felt, and that held up when the sample size grew several times over. The second is that four independent tests, run at different points in the campaign, all concluded that a learned model of my own estimate and the crowd's own error was not trustworthy at this sample size, so every probability submitted in this campaign came from the hand built pipeline rather than a fitted blend.

## The data

Every number below traces to a specific file in the project's private repository, current through the round of sixteen at the most recent full rebuild (July 7, 2026): 943 question rows across 85 settled matches, of which 921 carry a settled RBP value. Later matches, including the case studies linked below, are tracked separately until the next rebuild folds them in, and the campaign's most recent headline totals (Results, below) are reported alongside rather than in place of that master dataset.

The most distinctive data artifact in this project is a source reliability audit, built empirically by tracing specific wins and losses back to the data source that drove each pricing decision, not asserted in advance. Three tiers emerged. Tier 1, weighted heavily: the ESPN roster API's per player shot on target, foul, and offside history (the campaign's largest single wins, four propositions each worth 20 or more RBP, all trace here); agreement among major sportsbooks (FanDuel, DraftKings, Betfair, bet365) within five percentage points, treated as near ground truth; referee career statistics for card and penalty questions; and liquid two sided markets on Smarkets and Polymarket. Tier 2, useful only with adjustment: foul count magnitude priors, and the model's own output, downweighted to ten percent or less whenever the Elo gap between two teams exceeds 350 points, since the market is trusted over the model at that point. Tier 3, avoided or discounted heavily: thin, one sided odds from niche aggregators, and personal conviction overriding a researched figure, which independently produced two of the campaign's worst individual results.

A newly built StatsBomb event level panel, restricted to the 2018 and 2022 World Cups for full schema coverage, gives 128 matches flattened into 6,131 player match rows and 257 team match rows. It was validated before use, not assumed correct: total goals reproduce known tournament history exactly (169 in 2018, 172 in 2022), and top scorers reproduce row for row. Its value was also tested directly against a real match rather than assumed: for Portugal versus Croatia, a pure StatsBomb history model alone scored +93.01 RBP equivalent, while the actual submission, which blended that history with live market and current tournament form, scored +120.30, so every StatsBomb rate used in this campaign is treated as a prior to be blended, never a standalone replacement for current form.

One further data fact matters throughout: ESPN and StatsBomb do not count the same events identically for the same match. Across matches covered by both, ESPN records roughly 20% fewer fouls, 27% fewer yellow cards, 38% more shots on target, and 30% more corners than StatsBomb. Counts from the two sources are accordingly never pooled without a source specific correction, and every count based feature in this pipeline is tagged by which source produced it.

## Literature review

The modeling choices here rest on four literatures, organized by lineage rather than publication date.

**Goal based and Poisson family models.** Dixon and Coles (1997) treat home and away goals as two Poisson processes and introduce the tau correction, a multiplicative adjustment to the low scoring cells that repairs independent Poisson's tendency to underpredict draws:

\[
\tau_{\lambda,\mu}(x,y) = \begin{cases}
1 - \lambda\mu\rho & x=0, y=0 \\
1 + \lambda\rho    & x=0, y=1 \\
1 + \mu\rho        & x=1, y=0 \\
1 - \rho           & x=1, y=1 \\
1                  & \text{otherwise}
\end{cases}
\]

Karlis and Ntzoufras (2003) model the goal correlation directly with a bivariate Poisson construction, though it can only capture positive correlation; Michels, Ötting, and Karlis (2023) show the Dixon Coles correction achieves at most \(\rho \approx -0.05\) to \(-0.08\) at typical scoring rates, which directly bounds what re-fitting \(\rho\) could improve here.

**Rating based models and outcome regression.** Hvattum and Arntzen (2010) supply the direct template for half of this project's modeling stack: the standard Elo update feeding a proportional odds ordered logistic regression that maps an Elo difference straight to Home, Draw, and Away probabilities. Applied to fourteen seasons of English league play, their approach beats naive baselines but falls short of bookmaker implied probabilities, a result that is exactly why this project treats live market prices as a primary input wherever one exists, rather than a secondary check on the model.

**Scoring rule verification and calibration.** Murphy (1973) supplies the calibration and resolution decomposition of the Brier score used throughout the Results section below, separating "I cannot tell strong teams from weak ones" (low resolution) from "my confidence is systematically miscalibrated" (poor calibration), two failure modes with different fixes. Constantinou and Fenton (2012) show that a plain Brier score can rank a confidently wrong forecaster no worse than one who is only one category off, since it does not respect the ordinal structure of Home, Draw, Away; whether this competition's own scoring formula inherits that weakness is an open question, noted again under Limitations.

**Small sample model selection.** Peduzzi et al. (1996) and Riley et al. (2019) establish that logistic regression coefficients are provably unstable below roughly 10 to 20 settled outcomes per fitted parameter, a bar this campaign's own question level sample sits well under for any multi feature blending model, which is the direct statistical reason behind the rejection of learned blending discussed below. Harvey, Liu, and Zhu (2016) document that most published trading rule discoveries fail to replicate out of sample, the same risk this project's own named situational rules carry until each is tested against new data. Lichtendahl, Winkler, and Bickel (2020), studying the 2018 World Cup's own public forecasting contest, found that simple unweighted crowd averaging beat most individual forecasters and most attempts to weight the crowd more cleverly, evidence that this competition's crowd compression is a real, structural pattern rather than a fluke of one platform.

## Models

Two parallel approaches price the match result question. Point in time Elo ratings update with the standard rule,

\[
R'_h = R_h + K \cdot (S_h - E_h), \qquad E_h = \frac{1}{1 + 10^{(R_a - R_h)/400}}
\]

with a goal difference multiplier on \(K\), a flat home advantage offset, and \(K = 60\) for knockout matches. The Elo difference feeds a Poisson goal rate regression,

\[
\log(\lambda_{\text{home}}) = b_0 + b_1 \cdot \mathbf{1}[\text{home}] + b_2 \cdot \Delta R
\]

fitted on 49,400 historical matches with exponential recency weighting, giving \(b_0 = 0.1041\), \(b_1 = 0.2302\) (home advantage multiplies expected goals by about 1.26 times), and \(b_2 = 0.00181\) per Elo point of advantage. The Dixon Coles tau correction and a Negative Binomial overdispersion term are applied on top (\(\hat\rho = -0.05\), \(\hat\alpha = 0.0992\)), giving a full scoreline grid from which match result, total goals, and both teams to score are all computed analytically.

In parallel, a proportional odds ordered logistic regression fits the three way outcome directly against Elo difference and a home advantage indicator, following Hvattum and Arntzen: \(\beta = 0.005199\) per Elo point, \(\delta = 0.3771\) for home advantage, with cutpoints \(\kappa_1 = -0.7702\) and \(\kappa_2 = 0.5549\). Because it is fit directly on the outcome it predicts, this model calibrates better than the goals based pipeline in the high confidence range and serves as the primary instrument for match result questions.

For propositions outside either model's reach (fouls, cards, offsides, individual player props), a live Smarkets price is the primary input, with a Poisson rate recovered from the market's own over and under thresholds by root finding, and the Skellam distribution giving an exact head to head probability when two independent Poisson rates are compared. Compound questions with no direct market (for example, whether both teams will receive at least one card) are priced by decomposing each side's own independently measured rate rather than assuming a shared prior; the single best individual result of the campaign came from exactly this method.

## Backtesting

All statistical models are validated by an expanding window, time ordered walk forward backtest, deliberately not random k fold cross validation, since match outcomes are temporally ordered and a model must only ever see data that would genuinely have been available at the time.

Two further tests target the temptation to make the pipeline more complex rather than more correct. Platt scaling, a standard logistic recalibration, was tested at n=246: the fitted correction coefficient, \(b = 0.51\) with a 95% confidence interval of \([0.12, 1.29]\), includes \(b=1\), meaning "no correction needed" is statistically indistinguishable from the fitted correction, and a walk forward holdout showed applying it anyway would have worsened the Brier score by 0.028. The decision was to apply no correction until the sample is large enough, roughly \(n \approx 350\), for the correction's own parameters to be trustworthy.

Separately, a machine learning model (a blend of my own estimate and the crowd's, run through logistic regression and gradient boosting) was tested more than once as the campaign progressed, under both a plain out of sample split and a grouped, match level cross validation. It lost to the simple, hand built pipeline every time, consistent with the events per variable threshold from the literature review above: this campaign's question level sample size is not yet large enough to trust a multi feature learned blend, however appealing one looks in principle.

### The crowd compression finding

The central, quantified claim of this project is a regression, not an impression. Fit on the competition's own revealed crowd consensus against my submitted estimate:

\[
\hat p_{\text{crowd}} \approx 0.511 + 0.60 \times (\hat p_{\text{mine}} - 0.5)
\]

A slope of 1.0 would mean the crowd tracks a well researched estimate one for one. A slope of 0.60 means the crowd only moves 60 cents of consensus for every dollar of conviction the underlying evidence actually supports, compressing roughly 40% of the way back toward 50/50 regardless of how one sided the real evidence is. This is not a small sample artifact: the coefficient was first estimated at n=85 (slope 0.61, a wide confidence interval), then re-estimated at n=384 once the group stage finished (slope 0.60, 95% CI \([0.559, 0.641]\), \(p < 2.2 \times 10^{-16}\)). The estimate barely moved across a 4.5 times increase in sample size while its confidence interval tightened sharply, which is what a real, stable effect looks like, as distinct from noise that only looked structural early on. The interval comfortably excludes both 0 (no compression at all) and 1 (perfect crowd tracking), which is what makes this a statistically robust finding rather than a directional guess.

## Results

Every settled question in the master dataset (85 matches, 921 questions as of the July 7 rebuild) is summarized in the three figures below; the campaign's most recent full total, through the semifinal, stands at roughly +3,905 RBP across 993 questions at a 70.5% crowd beat rate, up from 58.2% at an earlier snapshot, moving the leaderboard position from the top 5.6% to the top 1.1% of an open field of nearly 3,900 registered participants.

![Cumulative RBP by match, with a drawdown panel showing distance from the running peak](/images/research/crowd-belief/cumulative_rbp_drawdown.png)

*Cumulative RBP across 83 settled matches (921 individually scored questions), with a drawdown panel showing how far the running total ever fell from its own peak. Total at this snapshot: +3,246.04 RBP. Worst single match result: -58.35 (Switzerland versus Canada), left in the chart rather than trimmed out.*

Separating genuine forecasting skill from luck needs a lot of individually scored questions: MacKay (2025) derives roughly 100 for a one standard deviation separation and roughly 400 for 95% confidence. This campaign's question count clears that bar, but the chart above uses the coarser match level axis, a different and noisier unit than the question count the confidence claim is actually about, so the visual trend here is suggestive of skill, not proof of it by itself.

![Reliability diagram comparing submitted probabilities to observed outcome frequencies, with a 1000-resample bootstrap band](/images/research/crowd-belief/calibration_reliability.png)

*A reliability diagram asks a different question from the chart above: of everything submitted at, say, 70% confidence, did those calls resolve true about 70% of the time? The isotonic regression curve (CORP style binning, no manually chosen bin count, following Dimitriadis, Gneiting, and Raftery 2021) tracks the perfect calibration diagonal closely across the middle of the range, with a bootstrap band showing where the estimate could plausibly sit given the sample size. n=786 individually scored questions across 72 matches.*

![Bar chart of RBP for every settled match, colored by sign, best and worst matches annotated](/images/research/crowd-belief/match_rbp_waterfall.png)

*Every one of the 83 settled matches at this snapshot, win or loss, shown in full. The best (Brazil versus Norway, +179.27) and worst (Argentina versus Austria, -42.23) are labeled, but nothing is excluded from the chart. Grading every submitted question means showing every settled match; a track record built by omitting the losses is not a track record.*

### What this doesn't show

The crowd compression regression pools every question type into one slope: match winners, player props, and count props alike, on the theory that a single pooled estimate is the most defensible one available now. It is plausible the real compression differs meaningfully by question type, and a stratified regression is the natural next step, not yet done. The calibration diagram similarly answers a pooled, question level question; it does not by itself establish that the skill shown in the cumulative chart is durable rather than lucky, a claim that rests on the question count clearing MacKay's threshold, not on any single chart.

## Case studies

Two matches from this campaign were written up in full, at the same depth as a standalone pricing paper, because each one tests the methodology above against a genuinely hard case rather than a routine one.

- [France versus Morocco, World Cup quarterfinal](/research/crowd-belief-compression/france-morocco/): the first match all campaign priced with no crowd or market anchor available for any question, and the campaign's single match best result at the time it settled.
- [England versus France, third place play-off](/research/crowd-belief-compression/england-france-bronze/): a full validated pricing paper, tested out of sample by the actual result, in a ten goal match that was the extreme case the model's own validation had already flagged as its weak point.

## Limitations

Live record calibration remains genuinely open: the Platt scaling diagnostic cannot yet statistically distinguish real overconfidence from sampling noise, and the natural checkpoint is a larger submission count. Whether this competition's own scoring formula behaves like plain Brier or like the Ranked Probability Score, which respects the ordinal Home, Draw, Away structure, has strategic implications for how much to hedge toward Draw on direction uncertain questions, and has not yet been independently confirmed against the live platform. This remains single investigator research, conducted under competitive time pressure, without peer review, and every named situational rule should be read as provisional until it has been tested against more than the one or two matches that first produced it.

## Conclusion

Two findings anchor this project, and they hold together rather than standing as separate results. The competition's own crowd consensus is systematically compressed toward 50%, and that compression, not any model's raw accuracy advantage, is the dominant source of positive RBP. At the same time, four independent tests, run at different points and different sample sizes, all concluded that a learned blend of my own estimate and the crowd's is not trustworthy yet, so every probability in this campaign came from a hand built, individually falsifiable rule instead of a fitted model. Neither finding claims that learned models are wrong in general; both are quantified statements about where the sample size actually binds for this specific problem, right now.

## References

Brier, G. W. (1950). Verification of forecasts expressed in terms of probability.

Constantinou, A., and Fenton, N. (2012). Solving the problem of inadequate scoring rules for assessing probabilistic football forecast models.

Dixon, M., and Coles, S. (1997). Modelling association football scores and inefficiencies in the football betting market.

Harvey, C., Liu, Y., and Zhu, H. (2016). ...and the cross section of expected returns.

Hvattum, L. M., and Arntzen, H. (2010). Using ELO ratings for match result prediction in association football.

Karlis, D., and Ntzoufras, I. (2003). Analysis of sports data by using bivariate Poisson models.

Lichtendahl, K., Winkler, R., and Bickel, J. E. (2020). Is it better to average probability forecasts or forecast a probability average?

Michels, R., Ötting, M., and Karlis, D. (2023). A copula based bivariate model for football scores.

Murphy, A. H. (1973). A new vector partition of the probability score.

Peduzzi, P., Concato, J., Kemper, E., Holford, T. R., and Feinstein, A. R. (1996). A simulation study of the number of events per variable in logistic regression analysis.

Riley, R. D., et al. (2019). Minimum sample size for developing a multivariable prediction model.

[Repository →](https://github.com/SouparneyaC/sportspredict-jtc)
