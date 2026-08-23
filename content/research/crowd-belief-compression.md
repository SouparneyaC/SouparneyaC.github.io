+++
title = "Crowd belief compression in a live forecasting competition"
+++

# Crowd belief compression in a live forecasting competition

> the crowd's own consensus is systematically too cautious, and correcting for that was the actual source of the edge.

## Abstract and introduction

A forecasting competition that scores against the crowd's own consensus comes down to one question: not whether to report an honest belief, but where the crowd itself is wrong. The Probability Competition FIFA WC2026, run by Jump Trading and Sports Predict, scores every submitted probability with Relative Brier Points (RBP). A forecaster gains when a submission lands closer to the true outcome than the platform's own crowd consensus, and loses when it doesn't. RBP is a proper scoring rule, so the optimal strategy is always honesty, and the competitive edge has to come from somewhere else.[^solo-campaign]

I built a full statistical forecasting pipeline to find it: point-in-time Elo ratings over more than 49,000 historical international matches back to 1872, a Poisson goal-rate regression with a Dixon and Coles low-score correction and a Negative Binomial overdispersion term, a parallel ordered logit match-result model, and a StatsBomb event-level panel covering the 2018 and 2022 World Cups.

Two findings anchor the work. The competition's crowd consensus is structurally compressed toward 50%, a quantified effect, not a felt one, that held up when the sample size grew several times over; this page leads with it. Separately, four independent tests, run at different points in the campaign, all concluded that a learned model of my own estimate against the crowd's error wasn't trustworthy yet at this sample size. Every probability submitted in this campaign came from that hand-built pipeline.

[^solo-campaign]: This is solo research, not a team effort. The methodology rests on a systematic review of the goal-modeling, rating-based, calibration, and crowd-aggregation literatures, summarized below.

## Data

Every number below traces to a specific file in the project's private repository, current through the round of sixteen at the most recent full rebuild, July 7, 2026: 943 question rows across 85 settled matches, 921 of them carrying a settled RBP value. Later matches, including the case studies linked below, are tracked separately until the next rebuild folds them in; the campaign's most recent headline totals, in Results below, are reported alongside that master dataset, not instead of it.

The most distinctive data artifact here is a source reliability audit, built by tracing specific wins and losses back to whichever data source actually drove each pricing decision. Three tiers came out of that tracing.[^tier-method] Tier 1, weighted heavily: the ESPN roster API's per-player shot-on-target, foul, and offside history, which the campaign's four largest single wins (each worth 20 or more RBP) all trace back to; agreement among major sportsbooks, FanDuel, DraftKings, Betfair, bet365, within five percentage points, treated as near ground truth; referee career statistics for card and penalty questions; and liquid two-sided markets on Smarkets and Polymarket. Tier 2, useful only with adjustment: foul-count magnitude priors, and the model's own output, downweighted to ten percent or less whenever the Elo gap between two teams exceeds 350 points, since the market is trusted over the model past that point. Tier 3, avoided or discounted heavily: thin, one-sided odds from niche aggregators, and personal conviction overriding a researched figure, which independently produced two of the campaign's worst individual results.

A StatsBomb event-level panel, restricted to the 2018 and 2022 World Cups for full schema coverage, flattens 128 matches into 6,131 player-match rows and 257 team-match rows. Total goals reproduce known tournament history exactly, 169 in 2018 and 172 in 2022, and top scorers reproduce row for row. Portugal versus Croatia tested what the panel is actually worth: a pure StatsBomb history model scored +93.01 RBP equivalent on its own, while the submission that blended that history with live market and current tournament form scored +120.30.

One further fact matters throughout: ESPN and StatsBomb don't count the same events identically for the same match. Across matches covered by both, ESPN records roughly 20% fewer fouls, 27% fewer yellow cards, 38% more shots on target, and 30% more corners than StatsBomb. Counts from the two sources are never pooled without a source-specific correction; every count-based feature in this pipeline is tagged by which source produced it.

[^tier-method]: The tiers came from tracing outcomes back to sources empirically, not from ranking each source's reliability in advance.

## Literature review

Four literatures ground the modeling choices here, taken in the order they build on each other rather than by publication date.

Dixon and Coles (1997) treat home and away goals as two Poisson processes and introduce the tau correction, a multiplicative adjustment to the low-scoring cells that repairs independent Poisson's tendency to underpredict draws:

\[
\tau_{\lambda,\mu}(x,y) = \begin{cases}
1 - \lambda\mu\rho & x=0, y=0 \\
1 + \lambda\rho    & x=0, y=1 \\
1 + \mu\rho        & x=1, y=0 \\
1 - \rho           & x=1, y=1 \\
1                  & \text{otherwise}
\end{cases}
\]

Karlis and Ntzoufras (2003) model the goal correlation directly with a bivariate Poisson construction, though it can only capture positive correlation. Michels, Ötting, and Karlis (2023) show the Dixon-Coles correction achieves at most \(\rho \approx -0.05\) to \(-0.08\) at typical scoring rates, which directly bounds what re-fitting \(\rho\) could improve here.

Hvattum and Arntzen (2010) supply the direct template for half of this project's modeling stack: the standard Elo update feeding a proportional-odds ordered logistic regression that maps an Elo difference straight to Home, Draw, and Away probabilities. Applied to fourteen seasons of English league play, their approach beats naive baselines but falls short of bookmaker-implied probabilities. That result is why this project treats live market prices as a primary input wherever one exists, instead of a secondary check on the model.

Murphy (1973) supplies the calibration and resolution decomposition of the Brier score used throughout Results below: separating "I cannot tell strong teams from weak ones," low resolution, from "my confidence is systematically miscalibrated," poor calibration, two failure modes with different fixes. Constantinou and Fenton (2012) work through a concrete case for why an ordinal scoring rule matters: in their benchmark, a plain Brier score scores one forecaster's prediction worse than a rival's despite that forecaster's distribution being the more informative one for a Home-or-Draw bet, purely because Brier treats Home, Draw, and Away as unordered categories.[^cf-match5] Whether this competition's own scoring formula inherits that weakness is an open question, noted again under Limitations.

Peduzzi et al. (1996) and Riley et al. (2019) establish that logistic regression coefficients are provably unstable below roughly 10 to 20 settled outcomes per fitted parameter, a bar this campaign's own question-level sample sits well under for any multi-feature blending model. That's the direct statistical reason behind the rejection of learned blending discussed below. Harvey, Liu, and Zhu (2016) document that most published trading-rule discoveries fail to replicate out of sample, the same risk this project's own named situational rules carry until each is tested against new data. Lichtendahl, Winkler, and Bickel (2020), studying the 2018 World Cup's own public forecasting contest, found that simple unweighted crowd averaging beat most individual forecasters and most attempts to weight the crowd more cleverly. That's independent evidence, from a different platform and a different tournament, that this competition's own crowd compression is a structural pattern rather than a coincidence of one platform.

[^cf-match5]: Their Match 5 benchmark: a lay bet that the home team won't lose. Model α, 57/33/10, scores worse under Brier (0.3038) than model β's 60/20/20 (0.0240), even though α's distribution is the one that actually favors a Home-or-Draw outcome more strongly.

## Models

Two parallel approaches price the match-result question. Point-in-time Elo ratings update with the standard rule,

\[
R'_h = R_h + K \cdot (S_h - E_h), \qquad E_h = \frac{1}{1 + 10^{(R_a - R_h)/400}}
\]

with a goal-difference multiplier on \(K\), a flat home-advantage offset, and \(K = 60\) for knockout matches. The Elo difference feeds a Poisson goal-rate regression,

\[
\log(\lambda_{\text{home}}) = b_0 + b_1 \cdot \mathbf{1}[\text{home}] + b_2 \cdot \Delta R
\]

fitted on 49,400 historical matches with exponential recency weighting: \(b_0 = 0.1041\), \(b_1 = 0.2302\) (home advantage multiplies expected goals by about 1.26 times), \(b_2 = 0.00181\) per Elo point of advantage. The Dixon-Coles tau correction and a Negative Binomial overdispersion term sit on top (\(\hat\rho = -0.05\), \(\hat\alpha = 0.0992\)), giving a full scoreline grid that match result, total goals, and both-teams-to-score are all computed from analytically.

A proportional-odds ordered logistic regression runs in parallel, fitting the three-way outcome directly against Elo difference and a home-advantage indicator, following Hvattum and Arntzen: \(\beta = 0.005199\) per Elo point, \(\delta = 0.3771\) for home advantage, cutpoints \(\kappa_1 = -0.7702\) and \(\kappa_2 = 0.5549\). It's fit directly on the outcome it predicts, so it calibrates better than the goals-based pipeline in the high-confidence range, and it's the primary instrument for match-result questions.

Propositions outside either model's reach, fouls, cards, offsides, individual player props, run on a live Smarkets price as the primary input: a Poisson rate recovered from the market's own over/under thresholds by root-finding, and the Skellam distribution giving an exact head-to-head probability when two independent Poisson rates are compared. Compound questions with no direct market, whether both teams will receive at least one card, for example, are priced by decomposing each side's own independently measured rate. The single best individual result of the campaign came from exactly this method.

## Backtesting

Every statistical model here is validated by an expanding-window, time-ordered walk-forward backtest, deliberately not random k-fold cross-validation, since match outcomes are temporally ordered and a model must only ever see data that would have been available at the time.

Two further tests targeted the temptation to make the pipeline more complex rather than more correct. Platt scaling, a standard logistic recalibration, was tested at n=246: the fitted correction coefficient, b=0.51 with a 95% confidence interval of [0.12, 1.29], includes b=1, so "no correction needed" is statistically indistinguishable from the fitted correction itself, and a walk-forward holdout showed applying it anyway would have worsened the Brier score by 0.028. The decision was to apply no correction until the sample is large enough, roughly n≈350, for the correction's own parameters to be trustworthy.

Separately, a machine learning blend of my own estimate and the crowd's, run through logistic regression and gradient boosting, was tested more than once as the campaign progressed, under both a plain out-of-sample split and a grouped, match-level cross-validation. It lost to the simple, hand-built pipeline every time. That's consistent with the events-per-variable threshold from the literature review above: this campaign's question-level sample size isn't yet large enough to trust a multi-feature learned blend, however appealing one looks in principle.

### The crowd compression finding

The central, quantified claim of this project is a regression, not an impression. Fit on the competition's own revealed crowd consensus against my submitted estimate:

\[
\hat p_{\text{crowd}} \approx 0.511 + 0.60 \times (\hat p_{\text{mine}} - 0.5)
\]

A slope of 1.0 would mean the crowd tracks a well-researched estimate one for one. A slope of 0.60 means the crowd only moves 60 cents of consensus for every dollar of conviction the underlying evidence supports, compressing roughly 40% of the way back toward 50/50 regardless of how one-sided the evidence is. This isn't a small-sample artifact. The coefficient was first estimated at n=85 (slope 0.61, a wide confidence interval), then re-estimated at n=384 once the group stage finished (slope 0.60, 95% CI [0.559, 0.641], \(p < 2.2 \times 10^{-16}\)). The estimate barely moved across a 4.5-times increase in sample size while its confidence interval tightened sharply. Noise that only looked structural early on doesn't survive a sample increase that way; a stable effect does. The interval excludes both 0, no compression at all, and 1, perfect crowd tracking, comfortably.

## Results

Every settled question in the master dataset, 85 matches, 921 questions as of the July 7 rebuild, feeds the three figures below. The campaign's most recent full total, through the semifinal, stands at roughly +3,905 RBP across 993 questions, a 70.5% crowd-beat rate, up from 58.2% at an earlier snapshot. That moved the leaderboard position from the top 5.6% to the top 1.1% of an open field of nearly 3,900 registered participants.

![Cumulative RBP by match, with a drawdown panel showing distance from the running peak](/images/research/crowd-belief/cumulative_rbp_drawdown.png)

Across 83 settled matches (921 individually scored questions), the cumulative total at this snapshot sits at +3,246.04 RBP. The worst single match result, -58.35 against Switzerland versus Canada, stays in the chart rather than getting trimmed out. Separating forecasting skill from luck needs a lot of individually scored questions: MacKay (2025) derives roughly 100 for a one-standard-deviation separation and roughly 400 for 95% confidence. This campaign's question count clears that bar, but the chart above runs on the coarser match-level axis, a noisier unit than the question count the confidence claim is actually about, so the visual trend here suggests skill without proving it on its own.

![Reliability diagram comparing submitted probabilities to observed outcome frequencies, with a 1000-resample bootstrap band](/images/research/crowd-belief/calibration_reliability.png)

A reliability diagram asks a different question: of everything submitted at, say, 70% confidence, did those calls resolve true about 70% of the time? The isotonic regression curve above, CORP-style binning with no manually chosen bin count, following Dimitriadis, Gneiting, and Raftery (2021), tracks the perfect-calibration diagonal closely across the middle of the range, with a bootstrap band showing where the estimate could plausibly sit given the sample size (n=786 questions across 72 matches).

![Bar chart of RBP for every settled match, colored by sign, best and worst matches annotated](/images/research/crowd-belief/match_rbp_waterfall.png)

Every one of the 83 settled matches at this snapshot is in this chart, win or loss: the best, Brazil versus Norway at +179.27, and the worst, Argentina versus Austria at -42.23, are labeled, but nothing is left out. Grading every submitted question means showing every settled match. A track record built by omitting the losses is not a track record.

### What this doesn't show

The crowd compression regression pools every question type into one slope, match winners, player props, and count props alike, on the theory that a single pooled estimate is the most defensible one available now. It's plausible the compression differs meaningfully by question type; a stratified regression is the natural next step, not yet done. The calibration diagram answers a pooled, question-level question too. It doesn't by itself prove the skill shown in the cumulative chart is durable. That claim rests on the question count clearing MacKay's threshold, not on any single chart.

## Case studies

Two matches from this campaign got written up in full, at the same depth as a standalone pricing paper, because each one tests the methodology above against a hard case, not a routine one.

- [France versus Morocco, World Cup quarterfinal](/research/crowd-belief-compression/france-morocco/): the first match all campaign priced with no crowd or market anchor available for any question, and the campaign's single match best result at the time it settled.
- [England versus France, third place play-off](/research/crowd-belief-compression/england-france-bronze/): a full validated pricing paper, tested out of sample by the actual result, in a ten-goal match that was the extreme case the model's own validation had already flagged as its weak point.

## Limitations

Live-record calibration is still an open question. The Platt scaling diagnostic can't yet statistically distinguish overconfidence from sampling noise, and the natural checkpoint is a larger submission count. Whether this competition's own scoring formula behaves like plain Brier or like the Ranked Probability Score, which respects the ordinal Home, Draw, Away structure, hasn't been independently confirmed against the live platform, and it has strategic implications for how much to hedge toward Draw on direction-uncertain questions. This is single-investigator research, conducted under competitive time pressure, without peer review. Every named situational rule should be read as provisional until it's been tested against more than the one or two matches that first produced it.

## Conclusion

Two findings anchor this project, and they hold together. The competition's own crowd consensus is systematically compressed toward 50%, and that compression is the dominant source of positive RBP, more than any model's raw accuracy advantage. Four independent tests, run at different points and different sample sizes, all concluded that a learned blend of my own estimate and the crowd's isn't trustworthy yet. Every probability in this campaign came from a hand-built, individually falsifiable rule. Neither finding claims learned models are wrong in general. Both are quantified statements about where the sample size actually binds for this specific problem, right now.

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
