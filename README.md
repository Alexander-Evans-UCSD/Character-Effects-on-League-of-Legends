# Character-Effects-on-League-of-Legends
This is a project for DSC80 at UCSD done by Seiichi Nakahira and Alexander Evans

# 1. Introduction
This is the 2025 League of Legends game dataset. In this project we will be looking at the effect 
of one character in particular, Yone, on game balance and match length. Our base dataset has 20148 rows as of 3/14/25 and 161 columns describing different
statistics within League of Legends (LoL). 

The columns we will be focusing on in particular today are the 'champion', 'gamelength', 'dpm', 'team kpm', 'patch', 'url', 'result', 'league', 'gameid' and 'pick{n}' columns 1-5.  

The 'champion' column describes which champion a player picked in the match. A champion is the character that is being used by that player.  

The 'gamelength' column stores the length of the game in seconds. We will attempt to predict this later in this report.  

The 'dpm' column is the damage per minute done by either a player or a team during the match. More later on how to determine if it's a singular player or a team.  

The 'team kpm' column shows the kills per minute of each team.  

The 'patch' column describes which patch the match was played on. 

The 'url' column contains a url to the match, however data from this column is frequently missing.  

The 'result' column contains a 1 if the match is a win, 0 if it is a loss.  

The 'league' column contains the league that the match was played in. There are many different leagues in LoL that depend on region or skill level. 

The 'gameid' contains the id of the match.  

The 'pick{n}' columns are a set of five columns named 'pick1' to 'pick5' that contain the champions picked. These are important because the 'champion' column is empty if the data is stored as a team.

# 2. Data Cleaning and Exploratory Data Analysis

A big issue with the raw dataset at first is that it double dipped and would show matches twice, from both a team and a player perspective. If we used this dataset as it is, we would get a lot of noise in our analyses from double dipping.  

To fix this, we split up our dataset into two different datasets: 'players_data' and 'team_data'. To do this, we noticed that there was no data in the 'champion' column for the team data, so we filtered by NaN to create our two datasets. Once we got these two datasets, we filtered out all the columns except the ones mentioned earlier, as they are the only relevant columns to our analysis. 

## Here is a sample of our teams data:

|   champion |   gamelength |   team kpm |     dpm |   patch |   url |   result | league   | gameid           | pick1   | pick2   | pick3    | pick4    | pick5    |
|-----------:|-------------:|-----------:|--------:|--------:|------:|---------:|:---------|:-----------------|:--------|:--------|:---------|:---------|:---------|
|        nan |         1592 |     0.1131 | 1889.81 |   15.01 |   nan |        0 | LFL2     | LOLTMNT03_179647 | Maokai  | Jinx    | Leona    | Hwei     | Gnar     |
|        nan |         1592 |     0.4899 | 2023.15 |   15.01 |   nan |        1 | LFL2     | LOLTMNT03_179647 | Varus   | Ivern   | Braum    | Renekton | Orianna  |
|        nan |         1922 |     0.6556 | 3125.24 |   15.01 |   nan |        1 | LFL2     | LOLTMNT06_96134  | Varus   | K'Sante | Ivern    | Azir     | Rell     |
|        nan |         1922 |     0.3122 | 2435.28 |   15.01 |   nan |        0 | LFL2     | LOLTMNT06_96134  | Corki   | Sejuani | Renekton | Orianna  | Nautilus |
|        nan |         1782 |     0.6061 | 2285.19 |   15.01 |   nan |        0 | LFL2     | LOLTMNT06_95160  | Corki   | Skarner | Alistar  | Aurora   | Aatrox   |

## Here is a sample of our player data:

| champion   |   gamelength |   team kpm |     dpm |   patch |   url |   result | league   | gameid           |   pick1 |   pick2 |   pick3 |   pick4 |   pick5 |
|:-----------|-------------:|-----------:|--------:|--------:|------:|---------:|:---------|:-----------------|--------:|--------:|--------:|--------:|--------:|
| Gnar       |         1592 |     0.1131 | 759.648 |   15.01 |   nan |        0 | LFL2     | LOLTMNT03_179647 |     nan |     nan |     nan |     nan |     nan |
| Maokai     |         1592 |     0.1131 | 187.048 |   15.01 |   nan |        0 | LFL2     | LOLTMNT03_179647 |     nan |     nan |     nan |     nan |     nan |
| Hwei       |         1592 |     0.1131 | 525.829 |   15.01 |   nan |        0 | LFL2     | LOLTMNT03_179647 |     nan |     nan |     nan |     nan |     nan |
| Jinx       |         1592 |     0.1131 | 259.975 |   15.01 |   nan |        0 | LFL2     | LOLTMNT03_179647 |     nan |     nan |     nan |     nan |     nan |
| Leona      |         1592 |     0.1131 | 157.312 |   15.01 |   nan |        0 | LFL2     | LOLTMNT03_179647 |     nan |     nan |     nan |     nan |     nan |

## Here is a histogram visualizing kills per minute in LoL

<iframe
  src="assets/kpm_histogram.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Here is a boxplot showing the relationship between Damage Per Minute and game length

<iframe
  src="assets/dpm_quartiles.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Note that DPM has been sorted into 4 quartiles as to make plotting a box plot possible. Q1 is the lowest quartile, while Q4 is the highest.  

# 3: Assessment of Missingness

We believe that the 'url' column has data missing depending on the data from another column. This missing data is Not Missing At Random (NMAR) and we believe that a column that it depends on is the 'league' column.  

To test this, we did a permutation test on both the 'url' column vs the 'league' column as well as the 'url' column vs the 'result' column as a control. Our null hypothesis was that the data in the 'url' column is missing completely by random, and our alternative hypothesis for both tests was that the data was missing due to the info in the column it was being tested against ('league' or 'result'). 

For our permutation test involving the 'league' column, we got a p-value of 0.0, meaning that we reject the null hypothesis.

For our permutation test involving the 'result' column, we got a p-value of 1.0, meaning that we fail to reject the null hypothesis.

## Here is a bar graph showing the amount of valid and invalid urls per league

<iframe
  src="assets/url_missingness.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

In fact, when we look at this plot we can see what is causing this NMAR issue! Only LPL matches have a recorded URL, all other matches have no URL!

# 4: Hypothesis Testing

Next, we wanted to perform a hypothesis test and see if the character 'Yone' had a positive effect on winrates or if he actually is a balanced character despite many feeling otherwise.

To determine this we set up a hypothesis test with the null hypothesis that Yone's win rate was the same as the average character at 50%. Our alternative hypothesis was that his win rate was greater than 50%. We ran 10,000 simulations of a character with a 50% win rate playing 251 games as Yone, which is how many games we have worth of data in our dataset. Yone's observed win rate is 56.57%, and in these 10,000 simulations, our perfectly balanced character was only able to achieve a win rate equal or better to Yone's 2.14% of the time. 

We chose a significance level of 0.05, meaning we find this p-value of 0.0214 to be significant, and as a result we reject the null hypothesis that Yone is a balanced character and instead consider that he has a positive influence on his team's winrate. We chose to run the hypothesis test on Yone's winrate, because that tells the biggest story in the game. Kills or Deaths could be determined by another factor such as playstyle or gamelength, whereas winning is the ultimate goal in LoL and as a result became our test statistic.

# 5: Framing a prediction problem

Next we decided to predict a value in our dataset based on the other values. For this section, we decided to predict 'gamelength' of a match based on the presence of our favorite character Yone, as well as the kills per minute (KPM) occuring in the match. This is a classic regression problem, with each of these variables becoming a feature in our prediction.

We decided these features because we figured they both would have a strong influence on a match. If Yone is a broken character like we claim, it stands to reason that he would make matches shorter, as he dominates other characters. We thought our other feature, KPM, could help predict match length because games with a higher KPM are a lot more aggressive and quick, whereas matches with lower KPM's are slower and more strategic, thus taking more time to complete. 

To determine the accuracy of our prediction, we are using Mean Square Error (MSE) which is the difference between our prediction and actual value for each prediction we make. We are using this over Root Mean Square Error (RMSE) because it punishes more for large prediction errors, which we want to avoid as much as possible. 

# 6: Baseline Model

Our baseline model to predict game length is a simple linear regression of the form:

### Gamelength = 1935.48 - 2.93(If Yone is present on either team) - 65.16 * the average kills per minute per team

This model means that both the presence of Yone and a high KPM match indicates that a match will likely be shorter.

KPM is a quantitative feature, meaning that it's numerical, continuous and has a natural order to it. As a result, this feature did not need to be manipulated in any manner, and we could just chuck it into the pipeline.

Is_Yone was a boolean value to mark the other feature, being True if Yone was present in the match, and False otherwise. This is an example of a nominal feature, because it is categorical, and there is no inherent ordering to this feature. Normally, we would have to one hot encode a nominal feature to get it working in a pipeline, however we were able to use the boolean value without modification because it was a single boolean, which is essentially a singular one hot encoded column. 

The MSE of this model is 109780.5 which is quite a large number. We think this isn't great, and can definitely be improved, which we aim to do so with an upgraded model.

# 7: Upgraded Model

While our baseline model gave us an ok prediction of game length, we can do much better by including more features and changing the model we use!

The first feature we added is the damage per minute for each team (DPM). It might be easy to confuse this measure with KPM, however they are very different. A high DPM suggests longer, more drawn out fights due to a key mechanic in LoL: healing. If a character wins a duel quickly, there won't be much damage done, and there will be no damage done in the time it takes the character to respawn. On the other hand, if a fight is drawn out over a longer period of time, there will most likely be healing done during that duel, and the DPM will be much higher than a clean quick kill.

Another feature we added was a quantile transform of KPM. This makes it so KPM is normalized and has a more uniform distribution, meaning that outliers won't skew the data as much. If you look at the histogram of KPM from part 2, you can see this data has a right skew to it, and adding a quantile transformation makes that skew affect our data less.

This time, instead of a linear regression model, we decided to use a Random Classifier Forest. This method creates a lot of different decision trees that are based off of bootstrapped samples of our data, which are then aggregated out to get the 'best' prediction. We tested multiple different hyperparameters such as number of trees in the forest, the max depth of each tree, how many samples required to split a node, and how many samples required in each leaf node.

Of these hyperparameters, we found that the optimal max depth was 10, the optimal amount of trees per forest was 200, the optimal minimum amount of samples required to split a node was 5, and the optimal minimum amount of samples per leaf node was 1. 

This model gave us a MSE of 71538.27, which is almost 40,000 less than our first model. This is a reduction of almost 35% compared to the first model.

## Visualization of the importance of each feature for our final model

<iframe
  src="assets/final_model_visualization.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This graph shows why our final model is so much better than our first! DPM is the most important feature for predicting gamelength that we have tested so far, and our first model was completely missing it!