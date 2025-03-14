# Character-Effects-on-League-of-Legends
This is a project for DSC80 at UCSD done by Seiichi Nakahira and Alexander Evans

# 1. Introduction
This is the 2025 League of Legends game dataset. In this project we will be looking at the effect 
of one character in particular, Yone, on game balance. Our base dataset has 20148 rows as of 3/14/25 and 161 columns describing different
statistics within League of Legends. 

The columns we will be focusing on in particular today are the 'champion', 'gamelength', 'dpm', 'patch', 'url', 'result' and 'league'.
The 'champion' column describes which champion a player picked in the match. A champion is the character that is being used by that player.
The 'gamelength' column stores the length of the game in seconds. We will attempt to predict this later in this report.
The 'dpm' column is the damage per minute done by either a player or a team during the match. More later on how to determine if it's a singular player or a team.
