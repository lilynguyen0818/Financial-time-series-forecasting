# Project: Financial time series forecasting

The aim of the project is to predict the prices of a stock one day ahead. The forecasting output is compared adopting a number of regression algorithms, based on that analytical decision will decide the investment choices of either buying or selling the stock. There are five main parts in the project and in each part an additional technical analysis is added and data transformation is performed to observe the change in the output of the regression. The project employs different Python libraries including pandas, numpy, matplotlib and sklearn. 

## Description of the data

There are more than 3.7 million entries in the dataset covering the prices of a stock during period from 01.01.2010 to 31.12.2019.

There are six features including timestamp, open (the opening price of the stock), high (the highest price of the stock), low (the lowest price of the stock), close (the closing price of the stock), volume (the total amount of the stock in the recorded transactions).

The prices of the stock were recorded at different time of the day, therefore, the data will be subsampled to one day transaction for prediction.

## Prediction process

The process of stock price prediction is as follows.
- Subsampling the data to one day timestep
- Pre-splitting the data into train and test set based on 80/20 ratio for standardization process to avoid information leakeage to the test set
- Applying standardization technique using StandardScaler to scale the sample into unit norm
- Merging the standardized train and test set to assign X (the features the prediction) and y (the label of the prediction). y is created by shifting the Close value one step
- Splitting X and y into train and test sets using 80/20 ratio
- Performing the regression models in the train set of X and y
- Predicting the stock price using the test set of X and y
- Comparing the coefficients of determination (R-Squared) to determine the realiabilty of the models

## Part 1:
The ratio of Larry William's %R (LW%R) is calculated and added as a feature of the stock price for prediction.
Following the prediction process above, R2 score of the training model is 0.996 and R2 score of the testing model is 0.990. R2 ratios of both models approach closer to 1 meaning that the model might well fit the data.

A test is done by dropping LW%R from the input datasets before running the prediction model. We can see that R2 of both in-sample and out-of-sample test do not change. This is because in time series, R2 presents how well the model predicts future values not how well it fits the past values. This result quite reflects a well-known fact that R2 is non-decreasing even after we keep adding up regressors into the models. Even with the unrelated regressors to the dependent variable, we can still obtain a perfect fit.

## Part 2:
The ratio of Stochastic slow %D is added as a feature of the stock price. 

A OHLC candles firgure is created using plotply together with the line of the label (actual data), the forecast (predicted data), LW%R, and Stochastic slow %D.

From the graph, we can see that forecast values fit quite well with the actual data. There is a downward trend in the price of stock, notably with a deep decrease around mid-April of 2018 to near end of May of 2018. After that the prices steadily went downward, and even lower than 1.1USD in September of 2019. The further downward of stock prices can be predicted. As along with the fall of stock prices, if we look at LW%R line, the indicator moved below -20 during the same period. It indicates the continuation of downward trend. Slow Stochastic followed a similar trend as L%R, however, it can be seen that L%R is quite more sensitive to price changes.

## Part 3:
The ratio of Relative Strength Index (RSI) is added as a feature of the stock price. The line indicated this ratio is also added to the OHLC figure above as a subplot.

An ElasticNet model is set up for prediction in this part. The line of the forecast from this model is also created in the OHLC figure. The best pair of hyperparameters (alpha and l1_ratio) to control the model is found by performing a GridSearchCV.

R-squared of test and train models from ElasticNet regression is the same as when we performed linear regression. Elastic net regression provides the 
advantage of finding the good model that makes use of information of variables and avoids bias. The above result indicates that linear regression model we have set up has good prediction of future values.

## Part 4: 
In this part, On Balance Volume is added as a feature of the stock price.
The sliding windows for the input data are created before feeding the data into a Polynomial regression. The window length is 10, 5, and 2 samples equivalently. For comparison purpose, Linear and ElasticNet regression models are also performed on the sliding window data.

Table 1: The score tables of different sliding windows length data

| Model/window | no_window | 2_window | 5_window | 10_window |
| --- | --- | --- | --- | --- | 
| Linear regression | 0.990 | 0.989 | 0.989 | 0.989 |
| ElasticNet regression | 0.989 | 0.989 | 0.990 | 0.990 |
| Polynomial regression | 0.989 | 0.985 | 0.958 | 0.513 |

As can be seen from the score_table,  without window information Linear Regression has higher R2 than ElasticNet Regression and Polynomial Regression.
For models adopting polynomial regression, R2 starts to decrease as the number of window sliding expands.
In linear regression, R2 slightly decreases with the window information attached.

## Part 5:
The implementation of investment decision is performed by calculating the hit ratio of different setups. The hit ratio is the number of times a correct prediction has been made in relation to the total number of predictions.

The forecast of closing price for one week ahead is also done for comparision with the hit ratio of one-day forecast.

Table 2: The hit ratio table of different setups

| Linear_Step/Forecast | no_window | 2_window | 5_window | 10_window |
| --- | --- | --- | --- | --- | 
| one_day| 0.522 | 0.536 | 0.502 | 0.507 |
| one_week | 0.559 | 0.535 | 0.513 | 0.522 |

| Polynomial_Step/Forecast | no_window | 2_window | 5_window | 10_window |
| --- | --- | --- | --- | --- | 
| one_day| 0.514 | 0.494 | 0.508 | 0.476 |
| one_week | 0.580 | 0.537 | 0.495 | 0.527 |

| ElasticNet_Step/Forecast | no_window | 2_window | 5_window | 10_window |
| --- | --- | --- | --- | --- | 
| one_day| 0.520 | 0.533 | 0.523 | 0.522 |
| one_week | 0.524 | 0.498 | 0.497 | 0.496 |

From the hit_table, we can see that the hit ratios for data of one_week forecast is mostly higher than those of one_day forecast. Prediction with longer stretch of time usually impoves forecast accuracy. As more information aggregating over time, the same effect of larger changes in prices restraining the impact of random variation can be seen.
This might explain why the setup of one_week forecast produces better hit ratios than that of one_day forecast. Overall, forecast for one week ahead adopting linear regression provides more stable and higher hit ratios, which seem to be the best setup of all.




