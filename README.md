# Early Jungler Advantage in Professional League of Legends

**Name:** Jonathan Adrian  
**Website:** https://jon-adrian.github.io/dsc80-proj4/

---

## Introduction

League of Legends is a 5v5 competitive game where each team tries to destroy the opposing team's base. One of the 5 on each team is the 'jungler' role. The jungler role is really important because they farm neutral monsters, help teammates through surprise attacks, and influence early-game fights.

My central question I am trying to answer is:

**How does early-game jungle advantage relate to winning in professional League of Legends?**

To answer this, I used the 2025 League of Legends esports match dataset from Oracle's Elixir. The original dataset has **120,456 rows** and **165 columns**. Because my project focuses specifically on junglers, I filtered the data to rows where `position == "jng"`, which gave me **20,076 jungler rows**.

The main response variable I used is `result`, which has value `1` when the jungler's team won and `0` when the jungler's team lost. The main explanatory variables I used are early-game statistics at 15 minutes, including `golddiffat15`, `xpdiffat15`, `csdiffat15`, `killsat15`, `assistsat15`, and `deathsat15`.

---

## Data Cleaning and Exploratory Data Analysis

For data cleaning, I filtered the original dataset to only include the jungler rows. I picked columns related to identifying which match was being played, identifying the player playing the match, the match result, and how the jungler performed early-game. I also created a more readable `result_label` column using the `result` variable where `1` is labeled as `"Win"` and `0` is labeled as `"Loss"`. Finally, I dropped rows with missing values in the main early-game columns used for my analysis.

After cleaning, the dataset now had **18,442 rows** and **20 columns**.

### Distribution of Jungler Gold Difference at 15 Minutes

<iframe src="assets/gold_diff_distribution.html" width="800" height="500" frameborder="0"></iframe>

The histogram shows the distribution of `golddiffat15`, which measures how much more or less gold a jungler has compared to the opposing jungler at 15 minutes. Positive values (above 0) mean the jungler is ahead, while negative values (below 0) mean the jungler is behind. The distribution is centered around 0, which makes sense because each game should have one jungler ahead and one jungler behind relative to the opponent.

### Jungler Gold Difference by Match Result

<iframe src="assets/gold_diff_by_result.html" width="800" height="500" frameborder="0"></iframe>

The box plot compares jungler gold difference at 15 minutes between winning and losing teams. Winning junglers tend to have higher `golddiffat15` values than losing junglers, suggesting that early jungle gold advantage is associated with winning.

### Grouped Summary Table

| Match Result | Gold Diff at 15 | XP Diff at 15 | CS Diff at 15 | Kills at 15 | Assists at 15 | Deaths at 15 |
|---|---:|---:|---:|---:|---:|---:|
| Loss | -277.85 | -265.44 | -4.66 | 0.98 | 1.52 | 1.12 |
| Win | 277.85 | 265.44 | 4.66 | 1.42 | 2.15 | 0.71 |

This grouped table shows that junglers on winning teams have higher average gold, XP, and creep score differences at 15 minutes. They also average more kills and assists while having fewer deaths. This suggests that early jungle advantage is not only about farming resources, but also about their impact in fights (more kills and assists) and avoiding dying in fights.

---

## Assessment of Missingness

I assessed the missingness of `golddiffat15`, one of the main variables in my analysis. This column measures the jungler's gold difference against the opposing jungler at 15 minutes. About **8.14%** of jungler rows were missing `golddiffat15`, `xpdiffat15`, and `csdiffat15`.

The missingness of `golddiffat15` could be NMAR if the value is missing because of the unobserved value of `golddiffat15` itself. For example, if extremely unusual 15-minute gold differences were more likely to be missing because of unusual games or data collection issues, then the missingness would depend on the missing value itself. However, the missingness may also be MAR if it can be explained by observed columns such as game length.

### Missingness vs. Game Length

I tested whether the missingness of `golddiffat15` depends on `gamelength`.

- **Null Hypothesis:** The distribution of `gamelength` is the same whether or not `golddiffat15` is missing.
- **Alternative Hypothesis:** The distribution of `gamelength` is different depending on whether `golddiffat15` is missing.
- **Test Statistic:** Difference in mean game length.

The observed difference was about **27.8 seconds**, meaning games with missing `golddiffat15` values were about 27.8 seconds longer on average. The permutation test produced a p-value of **0.001**. Since this is below 0.05, I reject the null hypothesis. This suggests that missingness of `golddiffat15` depends on `gamelength`.

### Missingness vs. Side

I also tested whether the missingness of `golddiffat15` depends on `side`.

- **Null Hypothesis:** The distribution of `side` is the same whether or not `golddiffat15` is missing.
- **Alternative Hypothesis:** The distribution of `side` is different depending on whether `golddiffat15` is missing.
- **Test Statistic:** Total variation distance.

The observed TVD was **0.0**, and the p-value was **1.0**. Since this is much greater than 0.05, I fail to reject the null hypothesis. This suggests that the missingness of `golddiffat15` does not depend on whether the jungler was on Blue side or Red side.

Overall, the missingness of `golddiffat15` appears to depend on `gamelength`, but not on `side`. This means the missingness is not completely random with respect to all observed variables.

---

## Hypothesis Testing

For my hypothesis test, I tested whether junglers who are ahead in gold at 15 minutes are more likely to be on the winning team. I split junglers into two groups: those with `golddiffat15 > 0` and those with `golddiffat15 <= 0`.

- **Null Hypothesis:** Junglers with a positive gold difference at 15 minutes and junglers with a non-positive gold difference at 15 minutes have the same win rate. Any observed difference is due to random chance.
- **Alternative Hypothesis:** Junglers with a positive gold difference at 15 minutes have a higher win rate than junglers with a non-positive gold difference at 15 minutes.
- **Test Statistic:** Difference in win rates, calculated as the win rate of junglers with `golddiffat15 > 0` minus the win rate of junglers with `golddiffat15 <= 0`.
- **Significance Level:** 0.05

<iframe src="assets/hypothesis_test.html" width="800" height="500" frameborder="0"></iframe>

The observed difference in win rates was about **0.286**, meaning junglers ahead in gold at 15 minutes had a win rate about **28.6 percentage points higher** than junglers who were not ahead. In 1000 permutation trials, none of the simulated differences were as large as the observed difference, so I report the p-value as **p < 0.001**.

Since this p-value is below 0.05, I reject the null hypothesis. This suggests that junglers who are ahead in gold at 15 minutes are more likely to be on the winning team. However, this hypothesis test shows an association, not necessarily that jungle gold advantage directly causes winning.

---

## Framing a Prediction Problem

For my prediction problem, I predict the `result` column for jungler rows. The `result` column is 1 if the jungler's team won and 0 if the jungler's team lost, so this is a binary classification problem.

The response variable is `result`. I chose this response variable because my project focuses on whether early-game jungle advantage is associated with winning. Predicting the match result allows me to test whether early-game jungle statistics contain useful information about the eventual outcome of the game.

The time of prediction is **15 minutes into the game**. Because of this, I only use features that would be available at or before the 15-minute mark, such as `golddiffat15`, `xpdiffat15`, `csdiffat15`, `killsat15`, `assistsat15`, and `deathsat15`. I avoid using end-of-game statistics such as total gold, final kills, dragons, barons, towers, or game length because those would leak information from after the prediction time.

I evaluate my model using accuracy. Accuracy is appropriate because the response variable is binary and the classes are exactly balanced: 50% losses and 50% wins. This means a simple baseline that always guessed one class would only achieve about 50% accuracy.

---

## Baseline Model

For my baseline model, I used logistic regression to predict whether a jungler's team wins. I used three quantitative features:

- `golddiffat15`
- `xpdiffat15`
- `csdiffat15`

These features measure the jungler's gold, XP, and creep score advantages over the opposing jungler at 15 minutes.

All three features are quantitative. There are no nominal or ordinal features in this baseline model. I used a `Pipeline` with a `StandardScaler` and a logistic regression classifier. Scaling is useful because gold difference, XP difference, and CS difference are measured on different scales.

The baseline model achieved:

- **Training accuracy:** 0.651
- **Test accuracy:** 0.664

Since the response variable is exactly balanced between wins and losses, a simple baseline that always guessed one class would have about 50% accuracy. Therefore, the logistic regression model performs better than chance using only three early-game quantitative features.

---

## Final Model

For my final model, I improved the baseline model by adding features that capture more parts of the jungle role. The baseline model only used resource-difference features. These are useful, but junglers also impact the game through early fights and ganks.

I added:

- `killsat15`
- `assistsat15`
- `deathsat15`

I also engineered two new features:

- `kda_at15`: early combat efficiency, calculated using kills, assists, and deaths at 15 minutes
- `resource_diff_score`: a combined resource advantage feature using gold, XP, and CS difference

I originally considered using objective-control variables such as `firstdragon` and `firstherald`, but these columns were not available for the jungler rows I used in my analysis. Because of this, my final model focuses on early-game resource advantages, fighting impact, and engineered features based on available jungler statistics.

I used a Random Forest classifier because it can capture nonlinear relationships between features. I tuned hyperparameters using `GridSearchCV`.

The best final model used:

- `max_depth = 5`
- `min_samples_leaf = 10`
- `n_estimators = 50`

The final model achieved:

- **Training accuracy:** 0.674
- **Test accuracy:** 0.684

This is an improvement over the baseline model, which had a test accuracy of about 0.664. The improvement is modest, but it suggests that adding early fighting features and engineered features gives the model more useful information than resource differences alone. The final model still does not perfectly predict winners, which makes sense because League of Legends outcomes depend on many team-level factors beyond the jungler's 15-minute statistics.

---

## Fairness Analysis

For my fairness analysis, I investigated whether my final model performs similarly for Blue side junglers and Red side junglers. In League of Legends, each game has one Blue team and one Red team, and map side could potentially affect how the game is played.

- **Group X:** Blue side junglers
- **Group Y:** Red side junglers
- **Evaluation Metric:** Accuracy

- **Null Hypothesis:** My model has the same accuracy for Blue side junglers and Red side junglers. Any observed difference in accuracy is due to random chance.
- **Alternative Hypothesis:** My model has different accuracy for Blue side junglers and Red side junglers.
- **Test Statistic:** Absolute difference in accuracy between Blue side junglers and Red side junglers.

<iframe src="assets/fairness_test.html" width="800" height="500" frameborder="0"></iframe>

The observed absolute difference in accuracy between Blue side junglers and Red side junglers was about **0.0017**, or **0.17 percentage points**. This means the model performed almost identically for the two groups. The permutation test produced a p-value of **0.914**.

Since the p-value is much greater than 0.05, I fail to reject the null hypothesis. This suggests that there is not enough evidence to conclude that the model's accuracy differs between Blue side junglers and Red side junglers. Based on this fairness test, the model appears to perform similarly for both sides.

---

## Conclusion

Overall, early-game jungle advantage is meaningfully associated with winning in professional League of Legends. Junglers who are ahead in gold at 15 minutes have a substantially higher win rate than junglers who are not ahead. Early-game jungle statistics can also predict match results better than chance, although the model is far from perfect because League of Legends depends on many team-level factors beyond the jungler.

The final model improved slightly over the baseline model by adding early fighting features and engineered features, and the fairness analysis suggests that the model performs similarly for Blue side and Red side junglers.
