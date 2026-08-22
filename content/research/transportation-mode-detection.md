+++
title = "Detecting transportation mode from smartphone sensors"
+++

# Detecting transportation mode from smartphone sensors

> a 14-feature subset recovers essentially all the accuracy the full 36 features ever offered, and that same redundant, non-Gaussian structure is why every tree-based method beats every linear method by 25 to 30 percentage points.

## Abstract and introduction

Smartphones carry enough sensors to infer how someone is getting around, walking, driving, riding a bus, riding a train, without ever asking them. The open question is which of those sensors actually carry that information, and which model turns them into an accurate guess. Michelle Segura and I answered both, as a final project for STAT 385 at UIC, using the TMD dataset from Carpineti, Lomonaco, Bedogni, Di Felice, and Bononi (2018).[^tmd-versions]

We used the dataset's third and most complete release, \(D_3\), which draws thirty-six features from nine physical sensors, accelerometer, gyroscope, gyroscope uncalibrated, linear acceleration, orientation, rotation vector, game rotation vector, sound, and speed, each one reduced to a mean, minimum, maximum, and standard deviation over a five-second window.

Two questions organize the project. Which of the thirty-six features actually carry independent information about transportation mode? And which classification method identifies that mode best?

Six feature-selection methods, ANOVA, PCA, multinomial LASSO, Boruta, recursive feature elimination, and conditional permutation importance, answer the first question the same way: the thirty-six features are heavily redundant, and fourteen of them recover essentially all the accuracy the full set offers. Nine classification methods, benchmarked on the same training and test split, answer the second: every tree-based method beats every linear one, PCR and PLS-DA, by twenty-five to thirty percentage points, and a tuned XGBoost model reaches 97.29% test accuracy, the best of anything we tried.

A third question emerged from the first two rather than being asked at the outset. Every classifier's errors cluster the same way, confusing Bus, Car, and Train with each other. Unsupervised clustering, run without the true labels, finds these three classes sitting close together in sensor space no matter which representation is used: raw features, ordinary PCA, or a cumulant-based alternative. Whatever is causing that confusion lives in the data itself, before any classifier gets involved.

[^tmd-versions]: The dataset ships in three versions with increasing sensor coverage, \(D_1\) through \(D_3\); we use only \(D_3\) throughout.

## Data

\(D_3\) has 5,893 observations, thirty-six sensor features, a time field, and the target label. We split it 4,714 training rows to 1,179 test, an 80/20 split with a fixed seed used identically across every model in this report. The five classes sit close to an even 20% each. Carpineti et al.'s original collection was not balanced this way: 26% Walking, 25% Car, 24% Still, 20% Train, and only 5% Bus, a realistic reflection of how often people actually use each mode. Whoever prepared the public \(D_3\) release rebalanced it before we ever touched the file.[^rebalance]

One consequence follows directly. Every accuracy number in this report measures how well a model separates five equally likely classes. It says nothing about deployment accuracy in a world where Bus trips are rarer than the other four.

A single correlation motivates the rest of this section. `accelerometer.mean` and `linear_acceleration.mean` measure closely related physical quantities, and they correlate at 0.557. Thirty-six features drawn from only nine physical sensors are going to overlap like this throughout the dataset.

Several methods used later in this report, PCA, PCR, PLS-DA, lean on the data being roughly normal and roughly linear. Tree-based methods carry no such requirement. We checked which assumption the data actually supports, rather than reading it off after the fact from which methods happened to win. Every one of the thirty-six features rejects normality under an Anderson-Darling test, a result Shapiro-Wilk confirms on a random 5,000-row subsample.[^shapiro]

![Skewness of all 36 features, ranked](/images/research/transportation-mode-detection/skewness_all36.png)

Ranking all thirty-six features by skewness puts `linear_acceleration.max` at the top, 7.56, just ahead of `speed.std` at 7.01. Twenty-seven of the thirty-six carry |skewness| above 1. One correction belongs here: an earlier version of this project's own report named `speed.std` as the single worst-fitting feature. The full ranking says otherwise, `linear_acceleration.max` is worse. Most of the data is heavily right-skewed either way, so the broader point survives, but the specific claim was wrong, and this page uses the corrected one.

[^rebalance]: The dataset's own documentation doesn't say how; the direction and size of the shift is most consistent with downsampling the majority classes.
[^shapiro]: Shapiro-Wilk caps out at 5,000 rows, below \(D_3\)'s 5,893, so it runs here as a cross-check on a random subsample rather than a replacement for the full-sample Anderson-Darling test.

## Literature review

Carpineti et al. (2018) built TMD on a specific premise: no single smartphone sensor reliably identifies transportation mode, so several have to be combined.[^carpineti-title] Our redundancy finding is the flip side of that premise. Combining overlapping sensors on purpose produces exactly the redundancy we measure later in this report. Zhu et al. (2016), working with a different smartphone dataset and a different set of participants, report the same Bus/Car/Train confusion independently: "the behaviors of the car-bus and the train-metro are very similar, making mode classification difficult," with bus-to-car confusion reaching 11.27% under k-nearest-neighbors.

Kursa and Rudnicki (2010), introducing Boruta, distinguish two different feature-selection goals: all-relevant selection, which features carry any signal at all, however small or duplicated, and minimal-optimal selection, the smallest feature set that still reaches peak performance. Boruta answers the first question. Recursive feature elimination answers the second. That's why Boruta confirming all thirty-six features and RFE narrowing to fourteen are two different, compatible answers rather than a disagreement. Guyon et al. (2002) introduced RFE for a small-n, large-p gene-expression problem, ranking genes with a linear SVM. We borrow the elimination procedure and substitute a Random Forest, a change worth flagging directly: our data sits in the opposite regime, roughly 4,700 rows against 36 features, not the handful of samples against thousands of genes Guyon's method was built for. Strobl et al. (2007) motivates a sixth, stricter check on top of these five. Ordinary Random Forest importance is biased upward for correlated features, because scrambling one member of a correlated cluster still leaves its near-duplicate partners intact for the model to exploit.

On the classification side, Barker and Rayens (2003) formalized Partial Least Squares Discriminant Analysis. PLS originates in chemometrics, where it was built to construct useful components from very few observations, unlike PCA or PCR, which optimize for variance alone.[^pls-origin] Grinsztajn, Oyallon, and Varoquaux (2022) benchmarked tree-based models against deep learning across 45 tabular datasets and found trees remain state-of-the-art at medium scale, around 10,000 rows, close to this project's 5,893. Their explanation centers on rotation: tree-based methods gain nothing from a fixed linear rotation of the feature space, and lose nothing when one isn't available. That's the same rotation PCA, PCR, and PLS-DA all impose here before classifying anything, one direct explanation for why every tree-based method in this report outperforms every rotation-based linear one. Kittler et al. (1998) show that simple sum-and-average combination is the most robust fixed rule for combining classifiers' probabilities. Our own ensemble sweep found the same thing: a weighted Random Forest/XGBoost blend, tested across every mixing ratio, never beat plain 50/50 averaging by more than sampling noise.

[^carpineti-title]: *Custom Dual Transportation Mode Detection by Smartphone Devices Exploiting Sensor Diversity.*
[^pls-origin]: Chemometrics problems typically have far more wavelengths, the predictors, than samples, the same "few observations, many features" regime RFE above was built for, in a different original application.

## Models

Six methods select features, each asking a different question of the same thirty-six columns. One-way ANOVA, \(X_{ij} = \mu + \tau_k + \epsilon_i\), tests whether a feature's mean differs across the five classes: all thirty-six pass at \(p < 0.05\), which rules out pure noise but says nothing about redundancy between features. PCA, the leading eigenvector of the standardized covariance matrix and each further orthogonal component, needs only 12 of the 36 components to reach 90% of total variance, direct evidence that the redundancy ANOVA couldn't rule out is actually there. Multinomial LASSO, a grouped \(L_1\) penalty \(\lambda\sum_{j=1}^{36}\lVert\beta_j\rVert_2\) on \(P(Y=k\mid X)\propto\exp(\beta_{0k}+X\beta_k)\), selected by 10-fold cross-validation, keeps 30 of the 36 features at \(\lambda_{\min}\).[^lasso-dropped] Boruta, pairing each feature with a shuffled shadow copy across 200 runs, confirms all 36 as distinguishable from noise, the same conclusion ANOVA reached by a different route.

Recursive Feature Elimination is the one method here that measures accuracy directly instead of through a proxy. It drops the least important feature by out-of-bag Random Forest accuracy and refits, repeatedly, until one feature remains.[^rfe-ntree] Accuracy peaks at 14 features, 95.31% out-of-sample, then falls off sharply below 8 to 10. Conditional permutation importance (Strobl et al., 2007) corrects Random Forest importance for correlation bias by permuting each feature only within its correlated partners' subgroups, and it is far stricter than any of the previous five: only `sound.std`, `sound.min`, `accelerometer.std`, `sound.mean`, and `sound.max` register any importance above zero. A Random Forest trained on just those five reaches 76.17% test accuracy, well below the 14-feature model, because removing every individually-redundant feature at once removes the redundancy those features were backing each other up with.

Nine classification methods are then compared on the same training and test split. A classification tree grown to entirety (398 leaves) and pruned by 10-fold cross-validation down to 172 leaves reaches 89.14% test accuracy, up from 73.96% for `rpart`'s untuned default, the cost of skipping cross-validated pruning made concrete. Random Forest, restricting each split to \(\texttt{mtry}=\lfloor\sqrt{p}\rfloor\) candidate features to decorrelate its trees, reaches 86.01% on 9 hand-picked features, 95.42% on the RFE-selected 14, and 95.17% on all 36.

Principal Component Regression fits multinomial logistic regression on the top \(M\) principal components, with \(M\) chosen by 5-fold cross-validation over \(M \in \{2,5,8,10,12,15,20,25,30,36\}\). Accuracy climbed the entire grid without ever peaking early, so the cross-validation itself selected \(M=36\): no dimensionality reduction at all, and 72.77% test accuracy. PLS-DA builds each component to maximize covariance with a dummy-coded response instead of blind variance, and it does peak early, at \(M=10\), declining slightly past that point, reaching 65.31%.

XGBoost fits gradient-boosted trees against the gradient of multinomial log loss (\(\texttt{max\_depth}=4\), \(\texttt{eta}=0.1\)). A held-out validation set picked the boosting-round count, 497 of a 500 maximum, and the final model was refit from scratch on the complete training set at that count, reaching 97.29% test accuracy, the best of anything tried here. A cascaded classifier collapses the problem to three classes first, Still, Vehicle, and Walking, then routes every row predicted Vehicle to a second, independently-tuned XGBoost stage that decides between Bus, Car, and Train; it reaches 96.52%. An ensemble that averages Random Forest's and XGBoost's predicted probabilities, following Kittler et al.'s (1998) sum rule, reaches 97.20%. We also swept non-50/50 blending ratios; none of them beat plain averaging by more than sampling noise.

[^lasso-dropped]: The six dropped features: `game_rotation_vector.mean`, `linear_acceleration.min`, `orientation.mean`, `rotation_vector.mean`, `sound.max`, `sound.mean`.
[^rfe-ntree]: Each refit uses \(\texttt{ntree}=300\); a direct sweep from 10 to 1,000 trees showed out-of-bag error flattening by roughly 100, so 300 leaves margin without adding runtime for no benefit.

## Backtesting

Every model here is evaluated once, on an identical, untouched test set never touched during tuning. The harder validation question this project ran into wasn't which model wins. It was whether the winning number could be trusted at all.

The headline XGBoost result exists only because of a bug caught live, mid-project. Three separately hand-written XGBoost scripts had each fit their *final* model on the validation-excluded subset used only to pick the number of boosting rounds, and never refit on the full training set afterward. The result, 95.93%, was completely plausible. It didn't announce itself as broken the way an earlier, unrelated reshape bug had, at an obviously wrong ~19% accuracy.[^reshape-bug] It surfaced only because a fourth, independently-written ensemble script's own XGBoost block disagreed with the number the project had been citing as its benchmark. An audit across every XGBoost fit found where the bug actually lived: in copy-pasted logic. The cascade and ensemble scripts, which had each written their own full-refit step from scratch, were already correct. Three separately hand-written flat-XGBoost scripts, sharing a copied block, all carried the same error. Every number in this report reflects the correction, 97.29% up from 95.93%. The RFE-14 model carried the same gap in miniature, 95.42% instead of 95.76%, and an earlier conclusion that the 14-feature model "nearly matches" the full model had to be walked back once both were refit correctly: the corrected gap is 1.53 points, not the 0.5 first written down.

We also ran the test this site's other pages ask of themselves: should a more complex model replace the one we're recommending? Every one of the 36 raw features is non-Gaussian, several severely, but ordinary PCA is built entirely around the covariance matrix, a second-moment summary blind to that skewness. Huang, Lu, and Boudt's (2026) Higher-order multi-cumulant Factor Analysis replaces the covariance matrix with one built from third- and fourth-order cumulants, specifically to recover factors that are weak in variance but still non-Gaussian. Using the authors' own vendored R implementation on our standardized features, both their rules for choosing the number of factors agreed on \(R^*=2\).[^hfa-validation] Trusting that estimate requires the residuals left after removing the factors to be Gaussian. On our data, they aren't: all 36 residual series reject normality under both orders. We report the HFA comparison in the next section as descriptive rather than as a validated alternative, because that assumption is the one thing standing between it and being trustworthy as a recommendation, and it fails here.

[^reshape-bug]: A separate, earlier bug: a predict-step reshape error that produced an obviously wrong ~19% accuracy, caught the same way any broken number gets caught, by looking at it.
[^hfa-validation]: The paper's own validation data satisfies this residual-normality check almost exactly, 2.4% and 1.61% rejection under its two rules; ours does not.

## Results

Twelve of the thirty-six principal components capture 90% of the total variance in the data. A gap this large between the raw feature count and what's actually needed is the clearest single piece of evidence for the redundancy the rest of this report is built around.

![Cumulative variance explained by principal components](/images/research/transportation-mode-detection/pca_scree_plot.png)

`sound.std`, `sound.min`, `sound.mean`, `accelerometer.mean`, and `accelerometer.std` sit at the top of every feature-ranking method we tried, not just Random Forest importance below. The practical recommendation still isn't this ranking, it's the RFE-selected fourteen-feature subset, the only method among the six that measures predictive accuracy directly rather than through a proxy for it.

![Random Forest feature importance by Mean Decrease Accuracy](/images/research/transportation-mode-detection/rf_importance_plot.png)

Accuracy peaks at fourteen remaining features in the RFE sweep and falls off sharply below eight to ten. Refitting XGBoost on the same fourteen features, independently, reaches 95.76% test accuracy, close to XGBoost's own result on all thirty-six, confirmation from a second model family that this isn't a Random-Forest-specific artifact.

![Out-of-sample accuracy as features are eliminated, Recursive Feature Elimination](/images/research/transportation-mode-detection/rfe_accuracy_plot.png)

Every tree-based method clears every linear method by 25 to 30 percentage points: XGBoost at 97.29%, the Random Forest/XGBoost ensemble at 97.20%, the cascaded classifier at 96.52%, Random Forest between 95.17% and 95.42% depending on feature set, against PCR at 72.77% and PLS-DA at 65.31%, despite every method seeing the exact same information. Imposing a fixed linear rotation on a problem whose class boundaries don't separate linearly costs exactly this much.

![Final model comparison, test accuracy across nine methods](/images/research/transportation-mode-detection/model_comparison_plot.png)

The tuned tree, shown here to only its top three levels (the full tree runs to 172), splits first on low `linear_acceleration.mean`, isolating Still and Car, then on high `accelerometer.std` within that group, which separates Bus from Car. It's a readable illustration of what the tree-based methods are actually doing that PCR and PLS-DA structurally cannot.

![Top three levels of the pruned classification tree](/images/research/transportation-mode-detection/tree_plot.png)

Every classifier in this report makes most of its errors confusing Bus, Car, and Train with each other, while Walking and Still are classified almost perfectly. Hierarchical clustering on the five true class centroids, in standardized sensor space, shows why: Walking sits two to three times farther from every other class than those classes sit from one another, and Bus and Train are the single closest pair of all. The sensor signatures themselves are just close together for these three modes. A k-means elbow sweep across the raw 36 features corroborates this from a different angle: there's no sharp elbow at K=5, the true number of classes; within-cluster variance declines smoothly and only tapers around K=4.

At the matched \(R^*=2\) components, HFA3 reaches 51.48% test accuracy against 41.82% for ordinary PCA, a statistically significant gap by McNemar's test.[^hfa-scree-shape] Both stay far below the 97.29% XGBoost gets with a full model, since two factors, however chosen, can't capture what a full nonlinear model does.

![Scree comparison, ordinary PCA against HFA3 and HFA4](/images/research/transportation-mode-detection/hfa_scree_comparison.png)

Restricted specifically to Bus, Car, and Train, three different representations, raw features, PCA, and HFA3, land at statistically indistinguishable clustering purity: 0.4481, 0.4392, and 0.4403, all well above the 0.33 random floor but nowhere near clean separation. HFA finds no structure here that raw features or PCA don't already show. Its earlier accuracy gain most likely came from separating the easy classes, Walking and Still, not from resolving Bus, Car, and Train, which stays just as unresolved under a cumulant-based representation as under a covariance-based one. That's the direct reason the method that did improve on it, the cascaded classifier, had to be nonlinear and supervised rather than a smarter unsupervised representation.

![Clustering purity restricted to Bus, Car, and Train](/images/research/transportation-mode-detection/vehicle_hfa_clustering.png)

### What this doesn't show

This dataset carries no user or session identifier, so within-session correlation between train and test rows could be inflating every accuracy number here to some degree, a documented risk for this class of problem.[^session-id] The conditional permutation importance diagnostic, roughly 25 to 30 minutes per run, was reported from one verified run rather than recomputed alongside every other result. And the HFA comparison rests on a Gaussian-residual assumption this data does not satisfy, exactly why it's reported above as descriptive rather than as a competing recommendation.

[^hfa-scree-shape]: The scree shape itself, one strong factor then a sharp drop for HFA3/HFA4 against PCA's gentle decline, matches the pattern the original HFA paper reports on its own FRED-MD macroeconomic data.
[^session-id]: We cannot rule this out directly; the dataset provides no way to group rows by the session or user that produced them.

## Conclusion

Six feature-selection methods and nine classification approaches, evaluated on the same train/test split, add up to one story. The 36 raw sensor features are heavily redundant. `sound`- and `accelerometer`-derived features matter most consistently. Fourteen of the thirty-six recover essentially all the accuracy the full set offers, a result Random Forest and XGBoost reach independently of each other. That same redundant, non-Gaussian structure is why every tree-based method beats every linear one by 25 to 30 points: PCA, PCR, and PLS-DA all impose a fixed linear rotation, and this problem's class boundaries don't separate linearly. A correctly-tuned XGBoost model, refit on the complete training set after hyperparameter selection, reaches the best test accuracy of anything attempted, 97.29%, because it needed no such rotation. That number is trustworthy for a specific reason: a live, self-caught methodology bug was found and corrected before it shipped.

The more transferable finding sits one level up from any single dataset. Redundancy and non-linearity aren't separate things to stumble onto by trying nine models in turn. They're one diagnosis. Check how many components it takes to reach a given variance target, check whether the raw features are Gaussian, and both checks together tell you up front whether linear dimension reduction is worth trying at all, or whether the effort belongs in tuning a tree-based model directly.

## References

Barker, M., & Rayens, W. (2003). Partial least squares for discrimination. *Journal of Chemometrics*, 17(3), 166-173.

Breiman, L. (2001). Random Forests. *Machine Learning*, 45(1), 5-32.

Carpineti, C., Lomonaco, V., Bedogni, L., Di Felice, M., & Bononi, L. (2018). Custom Dual Transportation Mode Detection by Smartphone Devices Exploiting Sensor Diversity. *2018 IEEE International Conference on Pervasive Computing and Communications Workshops*, 367-372.

Chen, T., & Guestrin, C. (2016). XGBoost: A Scalable Tree Boosting System. *Proceedings of the 22nd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining*, 785-794.

Grinsztajn, L., Oyallon, E., & Varoquaux, G. (2022). Why do tree-based models still outperform deep learning on typical tabular data? *Advances in Neural Information Processing Systems (NeurIPS 2022), Datasets and Benchmarks Track*.

Guyon, I., Weston, J., Barnhill, S., & Vapnik, V. (2002). Gene Selection for Cancer Classification Using Support Vector Machines. *Machine Learning*, 46(1-3), 389-422.

Huang, G., Lu, W., & Boudt, K. (2026). Estimation of Factors Using Higher-Order Multi-Cumulants in Weak Factor Models. *Journal of Business & Economic Statistics*.

Kittler, J., Hatef, M., Duin, R. P. W., & Matas, J. (1998). On combining classifiers. *IEEE Transactions on Pattern Analysis and Machine Intelligence*, 20(3), 226-239.

Kursa, M. B., & Rudnicki, W. R. (2010). Feature Selection with the Boruta Package. *Journal of Statistical Software*, 36(11), 1-13.

Strobl, C., Boulesteix, A.-L., Zeileis, A., & Hothorn, T. (2007). Bias in random forest variable importance measures. *BMC Bioinformatics*, 8, Article 25.

Tibshirani, R. (1996). Regression Shrinkage and Selection via the Lasso. *Journal of the Royal Statistical Society: Series B*, 58(1), 267-288.

Zhu, Y., Zhang, Y., et al. (2016). Transportation Modes Classification Using Sensors on Smartphones. *Sensors*, 16(8), 1324.

[Repository →](https://github.com/SouparneyaC/transportation-mode-detection)
