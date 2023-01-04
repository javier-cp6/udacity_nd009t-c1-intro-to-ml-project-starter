# Report: Predict Bike Sharing Demand with AutoGluon Solution
#### Javier Castillo P.

## Initial Training
### What did you realize when you tried to submit your predictions? What changes were needed to the output of the predictor to submit your results?
The output of the predictors were floating values, so they were rounded to integer values in order to submit them. Furthermore, the outcome of some trials included negative predictions that were rejected by Kaggle, therefore it was necessary to convert them to zero. 

### What was the top ranked model that performed?
Using AutoGluon, three different models were trained: the first one with raw features, a second one with additional features and default hyperparameters (“best quality” presets), and a third model that included hyperparameter optimization. The latter was the top ranked since achieved the best test score (based on the RMSLE, according to Kaggle rules). 

This model (“WeightedEnsemble_L3”) was an ensemble of LightGBM and CatBoost models based on gradient boosting decision tree algorithms. 

## Exploratory data analysis and feature creation
### What did the exploratory analysis find and how did you add additional features?
  
The exploratory data analysis helped to detect the following findings:
-	No missing values were detected in train and test datasets.
-	The columns ‘casual’ and ‘registered’ were present in the train dataset but not in the test data, therefore they were ignored in the training process.
-	The columns ‘season’, ‘weather’, ‘holiday’, and ‘workingday’ were initially found as integer values, so they were set as categories.
-	The train and test datasets included only 3 events of weather type 4 (“Heavy Rain”), so they were changed as events of type 3 (“Light rain”) which was present in a higher number of events.

The following features were included:
-	‘hour’. The purpose of the model was to predict hourly demand, so it was important to include an hour feature which was extracted from the “datetime” column. In addition, AutoGluon automatically included 4 additional time features from the ‘datetime’ column, they were ‘year’, ‘month’, ‘day’, and ‘dayofweek’. 
-	‘time_of_day. It was observed different bike demand periods during a day which were classified as ‘morning’, ‘lunch’, ‘rush_hour’ (peak demand), ‘night’, and ‘other’. 
-	‘atempcat’ and “tempcat’. These categories were generated from the ‘atemp’ ("feels like" temperature in Celsius) and ‘temp’ features considering their normal distribution showed in the histograms. The values were classified as ‘very hot’, ‘hot’, ‘warm’, ‘cool’, and ‘cold’.
-	‘windcat’. Wind speed values were classified as ‘low’, ‘mild’, and ‘windy’.
-	‘humiditycat’. Humidity values were classified as ‘low’, ‘mild’, and ‘high’.

The new features were also set as category data types, except ‘hour’ (integer value).

### How much better did your model preform after adding additional features and why do you think that is?
Adding new features was key to improve the model performance significantly from 53.073174 to 30.554656 (RMSE) in the validation score and from 1.79200 to 0.65341 in the test score (RMSEL). It may seem that feature engineering helped the model to consider more information that was not explicitly listed in the dataset. For instance, new features such as ‘hour’ or ‘’time_of_day’ helped to relate the bike demand (‘count’) and other features in a specific period of time. Moreover, weather conditions classified in categories gave extra information to the model than continuous values only.

## Hyper parameter tuning
### How much better did your model preform after trying different hyper parameters?
When compared to the model with additional features and default hyperparameters (“best quality” presets), the hyperparameter optimization allowed to improve the test score from 0.65341 to 0.47562 (RMSEL); however, it is worth mentioning that the performance decreased based on the validation score which went from 30.554656 to 36.361050 (RMSE).
 
The optimization process required more time to try different hyperparameters. First, high level hyperparameters such as num_stack_levels, num_bag_folds, and  num_bag_sets were set to the maximum accepted values of 3, 10 and 20, respectively. However, the test score did not improve significantly. A second approach combining high level hyperparameters and individual model hyperparameters allowed the model to achieve the best test score.

### If you were given more time with this dataset, where do you think you would spend more time?
I consider there is more room for feature engineering. For instance, it could be explored whether the model improve adding past values of the target (demand of previous hours) as new features, which are also known as “lag features”. 

On the other hand, hyperparameter optimization may require more machine learning expertise to increase the performance efficiently.

### Create a table with the models you ran, the hyperparameters modified, and the kaggle score.
|model|num_stack_levels|num_bag_folds|num_bag_sets|score|
|--|--|--|--|--|
|initial|3|8|20|1.79200|
|add_features|3|8|20|0.65341|
|hpo|5|10|20|0.47562|

### Create a line plot showing the top model score for the three (or more) training runs during the project.

![model_train_score.png](img/model_train_score.png)

### Create a line plot showing the top kaggle score for the three (or more) prediction submissions during the project.

![model_test_score.png](img/model_test_score.png)

## Summary
The project allowed to develop a machine leaning model to forecast hourly bike rental demand using historical data (from Kaggle), AutoGluon and AWS Sagemaker. The deployment workflow was simplified by AutoGluon which automates some tasks such as data splitting, training, evaluation, and tuning. Additional hyperparameter optimization was performed to obtain the model with best performance.

Three different models were trained: a baseline model with raw features, a second one with additional features and default hyperparameters (“best quality” presets), and a third model with hyperparameter optimization. 

The third model that included a combination of high level hyperparameters and individual model hyperparameters allowed to achieve the best test score (RMSLE: 0.47562). This model (“WeightedEnsemble_L3”) was an ensemble of LightGBM and CatBoost models based on gradient boosting decision tree algorithms. 

## References
- AutoGluon Predictors. AutoGluon. https://auto.gluon.ai/stable/api/autogluon.predictor.html#autogluon.tabular.TabularPredictor.fit 
- Predicting Columns in a Table - In Depth. AutoGluon. https://auto.gluon.ai/0.0.15/tutorials/tabular_prediction/tabular-indepth.html 
- Default (fixed) hyperparameter values used in Gradient Boosting model. AutoGluon. https://github.com/autogluon/autogluon/blob/master/tabular/src/autogluon/tabular/models/lgb/hyperparameters/parameters.py#L43-L51 
- How to use AutoGluon for Kaggle competitions. AutoGluon. https://auto.gluon.ai/stable/tutorials/tabular_prediction/tabular-kaggle.html 
- Time-related feature engineering. Scikit-learn. https://scikit-learn.org/stable/auto_examples/applications/plot_cyclical_feature_engineering.html 
- Feature Engineering Examples: Binning Numerical Features. Towards Data Science. https://towardsdatascience.com/feature-engineering-examples-binning-numerical-features-7627149093d