# MLB-Player-Digital-Engagement-Forecasting

## About The Project

The goal for this project is to make a time series data prediction model.
We have to predict 'the 4 fan engagement scores (target 1,2,3,4)' by each given MLB player time-serise data.
You can check the process in the Kaggle Notebook.

## Key Insight for the feature engineering ##

![image](https://user-images.githubusercontent.com/70621565/155867250-84fa8c7c-7397-4020-bfc5-10ea8aefa06d.png)
*4 Feature importances with a basic model*

 The result of a basic model with given data shows **'player_id'**  is a good feature for the model. When we think about the result, It makes sense when we consider that a popular MLB player is always focused by people while a non-popular MLB player is not. 

![image](https://user-images.githubusercontent.com/70621565/155508636-735da4dc-caa6-4612-902f-8842559eb816.png)

The Picture is an EDA result of 'AVG target values by Year-Month' from the ['Kaggle NB: Getting Started with MLB Player Digital Engagement'.](https://www.kaggle.com/ryanholbrook/getting-started-with-mlb-player-digital-engagement)
With the result, I assumed that there are some critical relationship between **4 target values and time-serise data(Year-Month-Day).**

By using these main two clues I made new features. I also realized that the process to make new features should **AVOID FUTURE DATA LEAK**!


### 1. Get mean values for each target value using 'Group-by' 'player_id' and same 'month' but 'previous-year'.



|Player|Year|Month|Date|&#x1F537;2020_target_1_Month_mean&#x1F537;|&#x1F537;2019_target_1_Month_mean&#x1F537;|target_1(*target variable*)|
|---|---|---|---|---|---|---|
|A|2021|5|16|**10**(*May 20. avg target_1 for player A*)|**12**(*May 19. avg target_1 for player A*)|13|
|A|2021|5|17|**10**(*May 20. avg target_1 for player A*)|**12**(*May 19. avg target_1 for player A*)|14|
|A|2021|5|18|**10**(*May 20. avg target_1 for player A*)|**12**(*May 19. avg target_1 for player A*)|15|

Player A(May. 15. 2021) : Player A target mean value (May 2020 , May 2019)
I added target mean values with the same month but the previous(to avoid future data leak) year each row for the train data.

![image](https://user-images.githubusercontent.com/70621565/155508689-a8e42faa-6d79-46f6-b7a2-3ab6b4f75939.png)

If there is a May 15th, 2021 train data row for player A, I calculated target mean values of May 2020 and May 2019 and put them into features for that row(15th, 2021) for the training model.
When we compare sales revenues by quarters for a company, usually economists compare present quarter revenue with not the previous quarter's revenues but same quarter revenues in a previous year. My first feature engineering idea came from this.

### 2. Get mean values for each target value using 'Group-by' player_id and previous month as well as next month but the previous years'


|Player|Year|Month|Date|&#x1F537;preYear_target_1_preMonth_mean&#x1F537;|&#x1F537;preYear_target_1_nextMonth_mean&#x1F537;|target_1(*target variable*)|
|---|---|---|---|---|---|---|
|A|2021|5|16|**9**(*Apr 20. avg target_1 for player A*)|**16**(*Jun 20. avg target_1 for player A*)|13|
|A|2021|5|17|**9**(*Apr 20. avg target_1 for player A*)|**16**(*Jun 20. avg target_1 for player A*)|14|
|A|2021|5|18|**9**(*Apr 20. avg target_1 for player A*)|**16**(*Jun 20. avg target_1 for player A*)|15|

Player A(May. 15. 2021) : Player A target mean value (April 2020 , Jun 2020)
If there is a May 15th, 2021 train data row for player A, I calculated target mean values of April 2020 and Jun 2020 for player A and put them into features for that row(15th, 2021) for the training model.

Based on the EDA graph, If I want to get one of May's target values, April and Jun data can be a good feature. However, in the test set, we cannot get future-month of statistical features. Therefore, instead of 2021 April and Jun mean target values, I put 2020 April and Jun mean target values in this example case. But these features are from the previous year one, therefore, I thought it require something that helps to connect the previous year (2020) and this year(2021)

### 3. A ratio of this year target mean values by previous year target mean values


|Player|Year|Month|Date|&#x1F537;preNow_Ratio_mean&#x1F537;|target_1(*target variable*)|
|---|---|---|---|---|---|
|A|2021|5|16|**0.91**(Player A target mean value(2021) **/** Player A target mean value(2020))|13|
|A|2021|5|17|**0.91**(Player A target mean value(2021) **/** Player A target mean value(2020))|14|
|A|2021|5|18|**0.91**(Player A target mean value(2021) **/** Player A target mean value(2020))|15|

Player A (May. 15. 2021): Player A target mean value(Jan+Feb+Mar 2021) / Player A target mean value(Jan+Feb+Mar 2020)
To find correlation between precious year and this year, I calculated a ratio of this year's target mean values by the previous year's target mean value.

The reason I use 'Jan+Feb+Mar' is that they are only perfect dataset on 2021 year data

### 4. Feature importance

![image](https://user-images.githubusercontent.com/70621565/155508898-180edacd-b7ca-4d12-adf7-2d8d82478c99.png)

We can check the feature importance result and new features are highly ranked on the result. 
