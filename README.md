# MLB-Player-Digital-Engagement-Forecasting

## About The Project

this project is to make a time series data prediction model.
We have to predict 'the fan engagement scores (target 1,2,3,4)' by each date and player with given MLB data.
You can check the process in the Kaggle Notebook.

## Key Insight for the feature engineering ##

 The result of a basic model with given data shows **'player_id'**  is a good feature for the model. When we think about the result, It is common sense that a popular MLB player is always highlighted by people while a non-popular MLB player is not. 

The Picture of an EDA result of 'AVG target values by Year-Month' from the ['Kaggle NB: Getting Started with MLB Player Digital Engagement'.](https://www.kaggle.com/ryanholbrook/getting-started-with-mlb-player-digital-engagement)
I assumed that there are some critical relationship between **4 target values and time-serise data(Year-Month-Day).**

By using these main who clues I made some new features and the process should **AVOID FUTURE DATA LEAK**!


### 1. Get statistic values(Mean, Max, Min...) for each target value using 'Group-by' player_id and same month but previous year.
Player A(May. 15. 2021) : Player A target mean value (May 2020 , May 2019)


|Player|Year|Month|Date|20_target_A|Priv_|AmOfChanges_1_SnA_1|
|---|---|---|---|---|---|---|
|A|2021|5|16|0|Null|Null|
|A|2021|5|17|&#x1F538;1$|&#x1F538;30|&#x1F538;Null|
|A|2021|5|18|&#x1F537;**2$**|&#x1F537;**35**|&#x1F537;**1**|


I decided to add target mean values with the same month but the previous year each row for the train data.

If there is a May 15th, 2021 train data row for player A, I calculated target mean values of May 2020 and May 2019 and put them into features for the model.
When we compare sales revenues by quarters for a company, usually economists compare present quarter revenue with not the previous quarter's revenues but same quarter revenues in a previous year. My first feature engineering idea came from this.

### 2. Target mean values with previous month and next month but the previous years'
Player A(May. 15. 2021) : Player A harget mean value (April 2020 , Jun 2020)
If there is a May 15th, 2021 train data row for player A, I calculated target mean values of April 2020 and Jun 2020 for player A and put them into features for the model.

Based on the EDA graph, If I want to get one of May's target values, April and Jun data can be a good feature. However, in the test set, we cannot get future-month of statistical features. Therefore, instead of 2021 April and Jun mean target values, I put 2020 April and Jun mean target values in this example case. But these features are from the previous year one, therefore, I thought it require something that helps to connect the previous year (2020) and this year(2021)

### 3. A ratio of this year target mean values by previous year target mean values
Player A (May. 15. 2021): Player A target mean value(Jan+Feb+Mar 2021) / Player A target mean value(Jan+Feb+Mar 2020)
To connect precious year and this year, I calculated a ratio of this year's target mean values by the previous year's target mean value.
