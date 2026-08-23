+++
title = "France versus Morocco, World Cup quarterfinal"
+++

# France versus Morocco, World Cup quarterfinal

*A case study within [Crowd belief compression in a live forecasting competition](/research/crowd-belief-compression/).*

## Abstract

This case study prices fifteen questions for the France versus Morocco quarterfinal of the 2026 World Cup. It differs from all forty-nine prior matches in the campaign in one decisive respect: no crowd consensus and no sharp market price existed for any of the fifteen questions at pricing time. The campaign's usual anchor, an average of crowd consensus and a verified sharp market, couldn't be applied anywhere, so every estimate had to be built from first principles. It was also the first match priced with access to a newly constructed StatsBomb event-level panel spanning the 2018 and 2022 World Cups, including the two teams' one prior competitive meeting, the 2022 semifinal (France 2, Morocco 0). A much richer data surface than any previous match brought its own risk: fitting a tool to data that happened to exist, not data that was actually informative for this specific match.

## Data

The StatsBomb panel gives 128 matches, flattened into 6,131 player-match rows and 257 team-match rows.[^panel-validation] The two teams' only prior meeting, the 2022 World Cup semifinal, is StatsBomb-covered and was used directly: Mbappé recorded three shots and zero on target that day, Dembélé recorded zero shots, and Hakimi recorded one shot and zero on target. Team and player rates elsewhere in the panel are shrunk toward these World Cup-specific priors by empirical Bayes shrinkage, five pseudo-matches for team rates, three for player rates, since a team's World Cup version of itself doesn't always match its normal domestic one.

The absence of any market anchor mattered most on two of the fifteen questions, both team shot-count propositions with no market to check them against. Projecting a team's shot-on-target or corner count upward from a thin empirical sample, with no market to anchor the estimate, had already cost a net -155.64 relative Brier points across five prior matches, the campaign's single most expensive repeated error. This match faced that exact risk on exactly the two questions with no market to fall back on.

[^panel-validation]: Validated before use: total goals and top scorers both reproduce known tournament history.

## Models

Point-in-time Elo, with \(K = 60\) for knockout matches, margin-of-victory scaling, and a neutral-venue-adjusted home term, put France and Morocco's ratings ahead of the match at what turned out to be stale, pre-tournament values.[^elo-bug-forward-ref] A Poisson goal-rate regression, with a Dixon-Coles correction (\(\hat\rho = -0.06\)) and a Negative Binomial overdispersion term refit on 49,400 historical matches, gave expected goal rates of \(\lambda_{\text{France}} = 1.441\) and \(\lambda_{\text{Morocco}} = 0.855\). An ordered logit fit directly on match outcomes, rather than derived from the goals model, since backtesting elsewhere in the campaign shows the goals pipeline underpredicts favorites by five to eight percentage points, gave a raw \(P(\text{France win}) = 0.5485\).

A live Smarkets query, run after the initial pricing pass, supplied a liquid price for twelve of the fifteen questions and, most consequentially, raised the match-winner estimate from the ordered logit's 0.52 to a market-anchored 0.60, the figure actually submitted. Direct, liquid markets are this campaign's most reliable data tier by a wide margin, a 73.5% historical crowd-beat rate, so the market price took priority over the model's own output once it appeared.

Two errors were caught and corrected before submission. First, the JTC platform's own `current_value` API field was initially misread as a possible crowd consensus. Cross-checking it against 209 settled markets showed it only ever takes the values 0, 50, or 100, a binary open-or-settled flag, not a probability, confirming a separate, earlier finding that pre-close crowd consensus isn't observable through any known API endpoint on this platform. Second, and more consequential: the historical results file this project treats as ground truth records every 2026 World Cup score as missing, so the Elo engine had never processed a single 2026 result all tournament, silently freezing every in-progress team's rating at its pre-tournament value. A point-in-time replay, reconstructing each team's actual 2026 results through the Elo formula, corrected France's rating from 2129.05 to 2205.39 and Morocco's from 1984.85 to 2058.10.[^elo-bug-scope]

[^elo-bug-forward-ref]: A systemic bug, described below, meant the ratings actually used at pricing time understated both teams' form.
[^elo-bug-scope]: This bug was caught and corrected before this match's submission, but it's systemic, not specific to this fixture; it affects every in-progress World Cup match until it's fixed at the data source rather than patched match by match.

## Why not a learned blend

A richer data surface than any previous match made a learned model tempting: let it decide how to combine the crowd's estimate, my own estimate, and the newly available StatsBomb evidence. So I tested it. An L2-regularized logistic regression and a gradient-boosted blender, taking the crowd's estimate and my own as inputs, were evaluated by both a walk-forward split and a grouped six-fold cross-validation on 404 settled campaign questions. Both learned blenders lost to the simple, hand-built rule of trusting the crowd baseline, by between 148 and 1,760 RBP-equivalent points depending on the validation scheme. The existing rule-based pricing pipeline stayed. This generalizes beyond this one match: the campaign's question-level sample size isn't yet large enough for a multi-feature learned blend to be trustworthy, however appealing one looks in principle.

## Results

Fourteen of the fifteen questions were submitted. The fifteenth, a card-in-stoppage-time proposition with no exact historical precedent for its precise phrasing, was withdrawn rather than guessed. The match settled France 2, Morocco 0. The submitted slate gained +169.17 relative Brier points, a new campaign best at the time it settled: thirteen of fourteen questions beat the crowd (92.9%), and the mean Brier score came in at 0.1787 against the crowd's 0.2127.

The single biggest win was Brahim Díaz recording one or more shots on target, priced at 0.25 against a crowd of 0.46. The outcome was no, for +36.65 RBP, the third straight validation in this campaign of the rule that a player's own zero rate overrides generic team-level context. The only loss came on the match-winner question itself: France to win in regulation was submitted at 0.55, having drifted slightly below the 0.60 Smarkets-anchored figure in a final pre-submission pass, against a crowd of 0.63. The outcome was yes, for -8.75 RBP, a difference small enough to be worth auditing on its own.

The single biggest regret was the dropped fifteenth question. It settled no, and the discarded estimate of 0.28 would have won an estimated 220 or more RBP had it been submitted, more than the entire match's eventual net result. The lesson: "no exact historical precedent for this exact phrasing" should trigger a conservative, explicitly flagged estimate, not a skip. Skipping a priceable question is its own kind of unforced error.

## Discussion

This match tested the pricing pipeline under the one condition it hadn't yet faced: total absence of any crowd or market anchor, on a question set where nine of the fifteen questions became directly comparable to routine market coverage once the Smarkets revision arrived. It also tested, deliberately, whether a richer data surface should change the pipeline's own decision process. The answer was no, by the same margin the campaign's evidence already supported before this match began. The Spain versus Belgium quarterfinal repeated the same no-crowd, no-market exercise and fared far worse, the honest counterweight to this result. A market anchor stays the default whenever one exists; it doesn't become optional once a model looks good enough.

[Back to the project overview →](/research/crowd-belief-compression/)
