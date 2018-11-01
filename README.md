# Batting for Dollars
Using linear regression to determine which batting performance statistics are most important to major league baseball player salaries.

## Summary
A linear regression model was developed using batting performance as features and salary data as the target for major league baseball players scraped from the baseball-reference web site https://www.baseball-reference.com/leagues/MLB/.  The initial goal was to develop a linear regression model to predict player salaries based upon the average batting performance from the previous three years and to identify the batting performance features that were most important to increasing salary in that model.  A secondary goal was to improve the performance of the initial model by exploring and applying feature engineering.

Python code for the initial goal for this project is found in a Jupyter Notebook titled **"salaries_simple_batting.ipynb"** that can be found in the **"Notebooks"** folder.  Python libraries/modules needed to run the code are listed in the first lines of the notebook.  

Presentation slides of the findings from the project can be found in the **Presentation** folder in Keynote and pdf formats.

## Future
There are a wide range of measures and statistics available for the baseball-reference web site.  These provide input for additional application of the machine learning process and offer an opportunity for further learning and discovery.  The notebook and functions for the linear regression model could be easily adapted to apply other batting data from the website.

## Data Description
There are several batting performance measures available on the baseball-reference website. These are:  
Rk - Rank - Count from top to bottom for selected parameter (only relevant within the web interface)  
Name - Player Name - * means left hand batter, # means switch hitter  
Age - Player Age - at midnight of June 30th  
G - Games Played, times player appeared on the lineup card  
PA - Plate Appearances  
AB - At bats  
R - Runs scored  
H - Hits  
2B - Doubles Hit  
3B - Triples Hit  
HR - Home Runs Hit  
RBI - Runs Batted In  
SB - Stolen Bases  
CS - Caught Stealing  
BB - Bases on Balls / Walks  
SO - Strikeouts  
BA - Batting Average - Hits / At Bats  
OBP - On Base Percentage (H + BB + HPB) / (At Bats + BB + HPB + SF)  
SLG - Slugging Percentage - (1B + 2*2B + 3*3B + 4*HR) / AB  
OPS - On Base Plus Slugging - OBP + SLG  
OPS+ - Normalized On Base Plus Slugging- 100(OBP / League OBP + SLG / League SLG - 1)  
TB - Total Bases, (1B + 2*2B + 3*3B + 4*HR)  
GDP - Double Plays Grounded Into  
HBP - Times Hit By Pitch  
SH - Sacrifice Hits (Sacrifice Bunts)  
SF - Sacrifice Flies  
IBB - Intentional Bases on Balls  

The web site provides batting parameters in a table on a web page for each year.  For example, data for the year 2015 can be found on the web page https://www.baseball-reference.com/leagues/MLB/2015-standard-batting.shtml.

Player salary data is provided by the web site in a table on a web page for each year.  For example, data for the year 2018 can be found on the web page https://www.baseball-reference.com/leagues/MLB/2018-value-batting.shtml. The salary data is provided along with data about the team the player played for, the position(s) the player played and various descriptive and performance statistics.  These are:  
Rk - Rank - Count from top to bottom for selected parameter (only relevant within the web interface)  
Name - Player Name - * means left hand   batter, # means switch hitter
Age - Player Age - at midnight of June 30th  
Tm - Team - team(s) player played for  
G - Games Played, times player appeared on the lineup card  
PA - Plate Appearances  
Rbat - Number of runs better or worse than average the player was as a hitter  
Rbaser - Number of runs better or worse than average the player was for all base running events  
Rdp - Number of runs better or worse than average the player was at avoiding grounding into double plays  
Rfield - Number of runs better or worse than average the player was for all fielding  
Rpos - Number of runs better or worse than average due to positional differences  
RAA - Runs better than average compared to a league average player  
WAA - Wins above average added by this player compared to a league average player  
Rrep - Number of runs an average player is better than a replacement player  
RAR - Number of runs this player is better than a replacement player  
WAR - The number of wins added by this player above what would be added by a replacement player  
waaWL% - win-loss ratio of an otherwise average team in only those games in which this player played  
162WL% - win-loss ratio of an otherwise average team for an entire season in only those games in which this player played  
oWAR - WAR with fielding excluded
dWAR - WAR with only defensive measures
oRAR - Offensive runs above replacement level
Salary - Player Salary
Acquired - How player was acquired by current team
Pos Summary - Position(s) played by player using standard 1-9 number system

This project created a model based on only the most basic batting performance measures and did not consider the more complex measures calculated from these or measures relating to base running.  The measures used were:  
R - Runs scored  
H - Hits  
RBI - Runs Batted In  
SO - Strikeouts  
GDP - Double Plays Grounded Into  
HBP - Times Hit By Pitch  
SH - Sacrifice Hits (Sacrifice Bunts)  
SF - Sacrifice Flies  
IBB - Intentional Bases on Balls  

## Code Narrative
Batting data was scraped from the web site for three years, 2015, 2016 and 2017 using the Selenium python library.  Code for this is found in the "get_batting_data" function in the project notebook.  Note that the batting data table is sourced from another web page via a javascript script so the data must be scraped from the page_source within the above page.  The table with batting performance for each individual player is the last table on the page.  Also note that this function supports scraping data for any number of years with years as input parameters so it would support exploring more and/or other years of player batting performance.

Salary data was scraped from the web site for the year 2018 using the Selenium python library.  Code for this is found in the "get_value_data" function in the project notebook.  Note that the salary data table is sourced from another web page via a javascript script so the data must be scraped from the page_source within the above page.  The table with salaries for each individual player is the last table on the page.

The dataframes created from the scraping process were cleaned using the "clean_batting_data" and "clean_salary_data" functions and combined using the "combine_data" function into a single dataframe that contained 2018 salary and 2015, 2016 and 1017 batting performance data for each player.  Average batting performance data was calculated for each player over the three year period and added to the dataframe.  Basic features (see "Data Description" above) were extracted along with salary data for each player into a new dataframe using the "simple_features" function.

The dataframe was split into features (batting performance data) and target (salary) dataframes.  These dataframes were then split into train and test dataframes, and a linear regression model fit to the train data using Scikit Learn.

Mean squared error (using the "mse" function) and mean absolute error (using the "mae" function) metrics were calculated with which to judge the model performance.

## Code Datails
The **"get_batting_data" function**:
* Specifies a list named "years" as an input parameter in which the user can specify the years for which to scrape batting data.
* Opens the Selenium web driver for the Google Chrome Browser and assigns it the name "driver".
* Creates a Template for the batting data web pages with which each year from the input list can be used to scrape the batting data from the web page for that year.
* Creates an empty list "df_list" in which dataframes of scraped batting data are listed.
* Loops through each year from the "years" list, scrapes the batting data for that year, puts it into a Pandas dataframe and puts the dataframe into the "df_list" list.
* Pauses after each iteration.
* Returns the list of batting data dataframes.

The **"get_value_data" function**:
* Opens the Selenium web driver for the Google Chrome Browser and assigns it the name "driver".
* Scrapes the data from the table containing the player salary data and puts it into a Pandas dataframe.
* Returns the dataframe.

The **"pickle_3yr_data" function**:
* Applies the "get_batting_data" function and the "get_value_data" function to scrape batting and salary data from the baseball-reference website.
* Puts this data into four Pandas dataframes (batting data for each year 2015, 2016, and 2017 in a list of three dataframes, and salary data for 2018 in a fourth dataframe).
* Saves the list of batting dataframes, and the salary dataframe as an object in a pickle file.

The **"clean_salary_data" function**:
* Opens the "batting_value_pickle" pickle file and assigns the second item (index 1) from the pickle object to dataframe name "df_value".
* Strips white space from column names.
* Drops columns except for Name, Salary and Pos Summary and renames them as Name, salary and position.  Name and salary are the only columns needed for this analysis.  The position column will be used to drop pitchers from the salary data (see below).
* Drops rows with any missing values.
* Converts salary string in $x,xxx,xxx format to integer format/type.
* Drops rows for pitchers.  Pitchers do not bat in the American League segment of major league baseball and typically do not bat very well in the National League segment.  Pitchers are paid primarily for their pitching skills so National League pitchers will not have batting performance that is consistent with batters in other positions where batting performance is much more important to their salaries (this presents opportunity for verification via application of data science).
* Drops players with salaries less than $1 million.  This represents primarily new players who are paid the league minimum salary or utility/replacement players that do not play in many games during the season.  Salaries for these players are not likely to be determined by their past major league batting performance.
* Drops the position column now that it has been used to drop pitchers and no longer serves a purpose for the analysis.
* Returns the clean dataframe.

The **"clean_batting_data" function**:
* Strips white space from column names.
* Drops rows with any missing values.
* Retains rows with string values in the "Name" column other than the string "Name".  Rows with values that are the column names are repeated throughout the table to make it easier to read the data on the web site.  These rows need to be deleted from the dataframe for analysis.
* Drops rows with duplicate string values in the "Name" column.  Batting data for players that played for more than one team is included in duplicate rows with the first row giving total batting performance values for the season and each following row giving batting performance values for each team.  Only total season batting performance values for the season are needed for the analysis so only the row that appears first in the table will be retained.
* Converts numeric data columns (all columns except "Name") to float type values.
* Returns the clean dataframe.

The **"combine_data" function**:
* Opens "batting_value_pickle" pickle file.
* Assigns list of batting data dataframes (first item in "pickle object" from pickle file) to a variable name.
* Puts data tables for the years 2015, 2016 and 2017 (items 0, 1 and 2 respectively from "data_list") into dataframes with meaningful names and cleans them using the "clean_batting_data" function.
* Cleans the dataframe containing the salary data using the "clean_salary_data" function.
* Merges the salary dataframe with the each of the three batting performance dataframes.
* Drops any rows with missing data (these are rows for players that did not play all of the three years).
* Returns the merged dataframe.

The **"calculate_average_features" function**.
* Takes the dataframe returned by the "combine_data" function as an input parameter.
* Calculates the average value over the three years of data for each batting performance parameter and puts the average value into a new column in the dataframe.
* Returns the dataframe including the average bating performance values.

The **"simple_features" function**:
* Takes the dataframe returned by the "calculate_average_features" function as an input parameter.
* Puts only the columns including the player names, player salaries and the average basic batting performance data (see "Data Description" above) into a new dataframe.
* Returns the dataframe.

The **"linear_regression_model" function**:
* Takes the dataframe from the "simple_features" function** as an input parameter.
* Creates a new dataframe containing the player names and batting performance data columns to be used as the feature ("X") data for the model.
* Creates a new dataframe containing the player names and salaries to be use as the target ("y") data for the model.
* Splits the X and y dataframes into train and test dataframes (30% of the data in the test dataframe) to support model validation.
* Log transforms the target (y) train and test dataframes (see "Data Description" above for an explanation of why this was done)
* Fits a linear regression model to the train data.
* Returns the model and the train and test dataframes.

The **"mse" function**:
* Takes a model, features dataframe ("X"), and target dataframe ("y") from the "linear_regression_model" function.
* Makes predictions by applying the model to the features dataframe ("X").
* Calculates the mean squared error (mse) comparing predictions to target dataframe ("y") values.
* Returns mean squared error (mse).

The **"mae" function**:
* Takes a model, features dataframe ("X"), and target dataframe ("y") from the "linear_regression_model" function.
* Reverses the log transformation done in the "linear_regression_model" function by taking the exponent of the target dataset ("y") values.
* Makes predictions by applying the model to the features dataframe ("X").
* Takes the exponent of the predicted values to allow them to be compared to the target exponent values.
* Calculates the mean absolute error (mae) comparing predictions to target dataframe ("y") values.
* Returns mean absolute error (mse).

The first cell in the notebook following the function code runs the "pickle_3yr_data" function to create the initial raw dataframe.

The final cell in the notebook applies the remaining functions to calculate a mean squared error for the model based on the training data and on the test data, and to calculate mean absolute error on the test data.
