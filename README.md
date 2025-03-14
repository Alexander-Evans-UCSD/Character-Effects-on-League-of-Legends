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

## Here is a plot visualizing LoL game length

<iframe
  src="assets/gamelengths_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>