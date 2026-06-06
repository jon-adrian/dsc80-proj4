# Early Jungler Advantage in Professional League of Legends

**Name:** Jonathan Adrian  
**Website:** https://jon-adrian.github.io/dsc80-proj4/

---

## Introduction

In this project, I am analyzing a role in the competitive esport League of Legends. League of Legends is a 5v5 esport, and the main objective is to destroy the other team's base. One of the 5 on each team is the 'jungler' role, which farms neutral monsters, helps teammates surprise attack, and heavily influences early game fights. I want to test the importance of jungler performance.

Thus, my central question I am trying to answer is: **How does early-game jungle advantage relate to winning in professional League of Legends?**

I answer this using the 2025 League of Legends esports match dataset from Oracle's Elixir. The original dataset has **120,456 rows** and **165 columns**. I filtered this data down to focus specifically on the junglers (`position == "jng"`). After filtering, I ended with **20,076** rows.

I used `result` as the response variable. It has a value of `1` when the jungler's team won, and a value of `0` when the jungler's team lost.

For explanatory variables, I used jungler statistics comparative to the opponent's jungler at the 15 minute mark, signalling early game performance. I used `golddiffat15`, `xpdiffat15`, `csdiffat15`, `killsat15`, `assistsat15`, and `deathsat15`.

---

## Data Cleaning and Exploratory Data Analysis

For data cleaning, I filtered the original dataset such that only junglers were included. In addition, I chose columns related to each match, matching players, the result, and how the jungler performed in the early-game. Since `result`, my response variable, is binary, I just created a new column `result_label` to label `1` as `"Win"` and `0` as `"Loss"` 

After this cleaning, the dataset now has **18,442 rows** and **20 columns**.

### Distribution of Jungler Gold Difference at 15 Minutes

<iframe src="assets/gold_diff_distribution.html" width="800" height="500" frameborder="0"></iframe>

This histogram shows the distribution of the variable `golddiffat15`, measuring how much gold a jungler has compared to the opposing jungler at the 15 minute mark. If `golddiffat15` is positive (above 0), then the jungler is ahead at 15 minutes in gold, and negative values (below 0) mean the jungler is behind. The distribution is centered around 0, which it should be because each match should have one jungler ahead and one jungle behind relative to each other, cancelling each other out at 0.

### Jungler Gold Difference by Match Result

<iframe src="assets/gold_diff_by_result.html" width="800" height="500" frameborder="0"></iframe>

This box plot compares jungler gold difference at the 15 minute mark between winning and losing teams. Junglers on winning teams tend to have higher `golddiffat15` values than losing junglers, suggesting an association between early jungle gold advantage and winning.

### Grouped Summary Table

| Match Result | Gold Diff at 15 | XP Diff at 15 | CS Diff at 15 | Kills at 15 | Assists at 15 | Deaths at 15 |
|---|---:|---:|---:|---:|---:|---:|
| Loss | -277.85 | -265.44 | -4.66 | 0.98 | 1.52 | 1.12 |
| Win | 277.85 | 265.44 | 4.66 | 1.42 | 2.15 | 0.71 |

The grouped table here shows that junglers on winning teams had higher average gold, XP, and creep score differences at the 15 minute mark. They also averaged more kills and assists, while also having fewer deaths, suggesting that an early jungle advantage is both about farming resources AND having greater impact in fights (more kills / assists, less deaths).

---

## Assessment of Missingness

I assessed the missingness of `golddiffat15` which was one of the main variables in my analysis. As stated many times before, `golddiffat15` just measures if the jungler is ahead or behind the opposing jungler in gold. Around **8.14%** of junglers rows were missing `golddiffat15`, `xpdiffat15`, and `csdiffat15` which were the main resource stats. 

The missingness of `golddiffat15` could be NMAR if it was missing because of some weird or unusual gold difference causing it to happen, or some collection issue. It could also be MAR if it could be explained by other columns (for example, if the game was less than 15 minutes).

### Missingness vs. Game Length

I tested whether the missingness of `golddiffat15` depends on `gamelength` (MAR).

- **Null Hypothesis:** The distribution of `gamelength` is the same whether or not `golddiffat15` is missing.
- **Alternative Hypothesis:** The distribution of `gamelength` is different depending on whether `golddiffat15` is missing.
- **Test Statistic:** Difference in mean game length.

The observed difference I found was about **27.8 seconds** meaning games with missing `golddiffat15` values were around 27.8 seconds longer on average. A permutation test for this gave a p-value of **0.001**, which is below 0.05. Thus, I can reject the null hypothesis, and this suggests that missingness of `golddiffat15` does depend on `gamelength`.

### Missingness vs. Side

I also tested whether the missingness of `golddiffat15` depends on `side`.

- **Null Hypothesis:** The distribution of `side` is the same whether or not `golddiffat15` is missing.
- **Alternative Hypothesis:** The distribution of `side` is different depending on whether `golddiffat15` is missing.
- **Test Statistic:** Total variation distance.

The observed TVD was **0.0**, and the p-value was **1.0**. Because this is much greater than 0.05, I fail to reject the null hypothesis, suggesting that the missingness of `golddiffat15` does NOT depend on whether the jungler was on Blue side or Red side.

Overall, the missingness of `golddiffat15` appears to depend on `gamelength`, but not on `side`. This means the missingness is not completely random with respect to all observed variables. Also, matches where `golddiffat15` was not tracked were longer on average, meaning that the main cause of the variable not tracking was most likely not matches ending too early, but something else related to games going on for too long.

---

## Hypothesis Testing


For my hypothesis test, I tested whether junglers who are ahead in gold at 15 minutes are more likely to be on the winning team. I split junglers into two groups: those with `golddiffat15 > 0` and those with `golddiffat15 <= 0`.

- **Null Hypothesis:** Junglers with a positive gold difference at 15 minutes and junglers with a non-positive gold difference at 15 minutes have the same winrate. Any observed difference in win rate is due to random chance.
- **Alternative Hypothesis:** Junglers with a positive gold difference at 15 minutes have a higher winrate than junglers with a non-positive gold difference at 15 minutes.
- **Test Statistic:** Difference in winrate: winrate of junglers with golddiffat15 > 0 minus winrate of junglers with golddiffat15 <= 0.
- **Significance Level:** 0.05

<iframe src="assets/hypothesis_test.html" width="800" height="500" frameborder="0"></iframe>

The observed difference in winrates (our test statistic) was around **0.286**, which means that junglers ahead in gold at the 15 minute mark had a win rate about **28.6% higher**  than junglers who weren't ahead. After running 1000 repetitions of a permutation test, there were no simulations as large as the observed difference, so simulated p-value was < 0.001. 

Thus, since p < 0.05, we reject the null hypothesis, suggesting that junglers who are ahead in gold at the 15 minute mark are more likely to be on the winning team. This doesn't necessarily prove directly that jungler gold advantages cause teams to win more, but shows a clear association.

---

## Framing a Prediction Problem

I predict the `result` column for my prediction problem, which is the binary variable `1` for a win for the jungler's team, `0` for a loss. Because it's a binary variable, this is a binary classification problem. `result` is my response variable because I want to predict whether early game jungler advantages are associated with winning, making the end result imperative, as we need to know the outcome of the game (who won).

We predict **15 minutes into the game**, using features that are available at the 15 minute mark, using these variables: `golddiffat15`, `xpdiffat15`, `csdiffat15`, `killsat15`, `assistsat15`, `deathsat15`. I don't use end game statsitics because those are from after the prediction time.

My model is evaluated using accuracy because `result` is binary, and there is 50% wins and 50% losses.

---

## Baseline Model

I used logistic regression to predict if the jungler's team using `golddiffat15`, `xpdiffat15`, and `csdiffat15`, measuring gold, XP, and creep score advantages over the opposing jungler at the 15 minute mark

These features are quantitative, and there are no nominal or ordinal features in the baseline model. 

I used a `Pipeline` with a `StandardScaler`, alongside a logistic regression classifier. The scaler is useful because the features are measured on different scales.

The baseline model achieved:

- **Training accuracy:** 0.651
- **Test accuracy:** 0.664

Since the response variable is 50/50 between wins and losses, a simple baseline that always guessed one class should have about 50% accuracy. The baseline logistic regression model performs better than chance using three early-game quantitative features.

---

## Final Model

Improving on my baseline model for the final model, I added features to get a better picture of the jungler role. Instead of just resource difference, I also wanted to capture how they impact early game fights by adding `killsat15` (kills), `assistsat15` (assists), and `deathsat15` (deaths). 

Since we now have 2 different impacts of the jungler (resource impact and fighting impact), I decided to engineer these into new features: `resource_diff_score` (combines the gold, XP, and CS differences) and `kda_at15` (combines the kills, deaths, and assists). 

I also was thinking of using objective control variables, like `firstdragon` and `firstherald` but these weren't available for junglers. So, my final model focuses in on resource advantages and fighting impact.

I used a Random Forest classifier because it can capture nonlinear relationships between features. I tuned hyperparameters using `GridSearchCV`.

The best final model used: `max_depth = 5`, `min_samples_leaf = 10`, and `n_estimators = 50`.


In the end, the final model achieved: **Training accuracy** = 0.674, **Test accuracy** = 0.684.

This was a small improvement over the baseline model, but it still probably means that adding fighting impact was useful. It still doesn't perfectly predict the winner based solely on early game jungler stats. This was pretty expected because League of Legends has so many factors at play besides the jungler's 15 minute stats. However, it being quite significantly better than a guess suggests that the features analyzed do have a significant impact on the end result of the game.

---

## Fairness Analysis

For the fairness analysis, I analyzed whether my final model performs similarly for both Blue side and Red side junglers, as which map side one is on could maybe affect how junglers play.

- **Group X:** Blue side junglers
- **Group Y:** Red side junglers
- **Evaluation Metric:** Accuracy

- **Null Hypothesis:** My model has the same accuracy for Blue side junglers and Red side junglers. Any observed difference in accuracy is due to random chance.
- **Alternative Hypothesis:** My model has different accuracy for Blue side junglers and Red side junglers.
- **Test Statistic:** Absolute difference in accuracy between Blue side junglers and Red side junglers.

<iframe src="assets/fairness_test.html" width="800" height="500" frameborder="0"></iframe>

The observed absolute difference in accuracy between Blue and Red side junglers was around **0.0017**, or **0.17%**. This means the model performed almost identically for the two groups. The permutation test produced a p-value of **0.914**.

Because the p-value is much greater than 0.05, I fail to reject the null hypothesis, suggesting that there's not enough evidence to conclude that the model's accuracy is different for Blue and Red side junglers. Based on this fairness analysis, the model performs similarly on both sides.


---

## Conclusion

In conclusion, early game jungle advantage DOES have a meaningful association with winning in professional League of Legends, as junglers who are ahead in gold have a much higher win rate than junglers who are not. 

I first created a baseline model that accounted for resource differences between junglers to predict the end result of the match.

I improved that model to add features surrounding the jungler's kills, deaths, and assists, so the model now measured 2 things: resource advantage and fighting impact. The model was far from perfect, but was still significantly better than guessing by chance, thus suggesting that although League of Legends depends on many team factors, and other teammates besides the jungler role, the jungler's early game play and advantage still plays an important part in the overall scheme of the entire game.
