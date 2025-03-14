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

Next we decided to predict a value in our dataset based on the other values. For this section, we decided to predict 'gamelength' of a match based on the presence of our favorite character Yone, as well as the kills per minute (kpm) occuring in the match. This is a classic regression problem, with each of these variables becoming a feature in our prediction.

We decided these features because we figured they both would have a strong influence on a match. If Yone is a broken character like we claim, it stands to reason that he would make matches shorter, as he dominates other characters. We thought our other feature, kpm, could help predict match length because games with a higher kpm are a lot more aggressive and quick, whereas matches with lower kpm's are slower and more strategic, thus taking more time to complete. 

To determine the accuracy of our prediction, we are using Mean Square Error (MSE) which is the difference between our prediction and actual value for each prediction we make. We are using this over Root Mean Square Error (RMSE) because it punishes more for large prediction errors, which we want to avoid as much as possible. 
