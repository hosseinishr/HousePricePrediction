# House Price Prediction

## Table of Contents
* [Problem Statement](#problem-statement)
* [Step 1: Importing, Understanding the Data, and EDA](#step-1-importing-understanding-the-data-and-eda)
* [Step 2: Data Preparation for Building Model](#step-2-data-preparation-for-building-model)
* [Step 3: Model Building and Training](#step-3-model-building-and-training)
* [Step 4: Residual Analysis](#step-4-residual-analysis)
* [Step 5: Step 5: Summarising the Base, Ridge, and Lasso models, and Conclusion](#step-5-summarising-the-base-ridge-and-lasso-models-and-conclusion)
* [Answer to the Business Problem](#answer-to-the-business-problem)
* [Acknowledgements](#acknowledgements)

## Problem Statement
A real estate company wants to understand the factors that impact the price of the houses in a region, and basically want to predict the house prices. Specially, they want to know:
  
- which features are significant in predicting the price of the houses in that region?
- how well those variables can predict the price of the houses in that region?
  
For this purpose, a dataset of the house prices is provided containing the price of every house along with different features of the house including surface area, location and neighbourhood, garage, utilities, etc.

The objective is to analyse the data, develop a linear regression model, if applicable at all, do a Ridge and Lasso regression, and advise the business with feedback on the above business problems.

## Step 1: Importing, Understanding the Data, and EDA
The dataset was successfully imported, and inspected. The following were found:
- The dataset has 1460 rows and 81 columns
- There are 6 columns: 'PoolQC', 'MiscFeature', 'Alley', 'Fence, 'FireplaceQu', and 'LotFrontage', that have high number of missing values (99.5% to 17.7% of the rows of the column). These columns were dropped.
- Dependent variable: 'SalePrice' or the target variable
- 'Id' column was dropped since it is a randomly generated number, and has no contribution to the target variable.
- None of the rows were identified having a high percentage of missing values.
- There are no columns that have only one unique value, so to drop the whole column.
- No duplicated rows were observed.
- It was checked for the categorical columns that no numeric value is saved as a string (object) in those columns. No problems were detected in that respect, and hence all our categorical columns are genuinely categorical.

### Dealing with the missing values
- The missing values of 'MasVnrType' and 'Electrical' columns were dropped (total of 7 rows).
- The missing values of the columns 'BsmtQual', 'BsmtCond', 'BsmtExposure', 'BsmtFinType1' and 'BsmtFinType2', correspond to the houses without basement, and hence were replaced with 'NoBsmt'.
- The missing values of the columns 'GarageType', 'GarageYrBlt', 'GarageFinish', 'GarageQual', 'GarageCond' correspond to the houses without garage, and hence were replaced with 'NoGarage' (for all except 'GarageYrBlt'), and with the reference year for 'GarageYrBlt'.

### Data class fixing
- Numeric value of 'MSSubClass' and 'MoSold' columns were replaced with the advised categories based on the data dictionary.
- The columns below are ordinal, meaning they are ranking/comparing the quality/condition/functionality of different attributes of the house. Therefore, the state of quality/condition/rank are assigned integer values starting from 0: 'LandSlope', 'LotShape', 'ExterQual', 'ExterCond', 'BsmtQual', 'BsmtCond', 'BsmtExposure', 'BsmtFinType1', 'BsmtFinType2', 'HeatingQC', 'CentralAir', 'KitchenQual', 'Functional', 'GarageFinish', 'GarageQual', 'GarageCond', 'PavedDrive'

### New columns transformation
- Calculating Age based on Year: Changing 'YearBuilt' into 'HouseAge', 'YearRemodAdd' into 'RemodAddAge', 'GarageYrBlt' into 'GarageAge', and 'YrSold' into 'SoldAge', and finally dropping the original Year columns.

### Inspecting and dealing with potential outliers
For this purpose, we take 25% and 90% quantiles as the InterQuantile Range (IQR). Hence, no lower outliers identified. 9 outliers above the upper limit identified and removed from the data set.

### EDA
### Numeric columns EDA
Some of the numeric columns were selected for EDA and pair plot visualisation as below.

<img src="/images/num-eda.png" width = 1100>

As can be seen:
- There is a linear correlation between some of the numeric columns and the target column. Hence, a linear regression model can be considered and trained.
- Also, it can be observed that there are correlations between different numeric columns as well, e.g. between 'GrLivArea' and 'TotRmsAbvGrd', or between '1stFlrSF' and 'GrLivArea'. However, let's RFE decides to choose the most important features for the regression model rather than dropping the columns that have some degree of correlation between themselves.
- The month of selling, 'MoSold', was dropped since there is not much of an impact of different months of selling on the target variable.

### Categorical columns EDA

'SalePrice' vs 'MSZoning'  
<img src="/images/cat1-eda.png" width = 700>  

'SalePrice' vs 'Neighborhood'  
<img src="/images/cat2-eda.png" width = 700>  
'SalePrice' vs 'HouseStyle'  
<img src="/images/cat3-eda.png" width = 700>  
'SalePrice' vs 'Foundation'  
<img src="/images/cat4-eda.png" width = 700>  
'SalePrice' vs 'SaleCondition'  
<img src="/images/cat5-eda.png" width = 700>  

As can be seen, the categorical columns have contribution to the target variable since moving across different categories of each of these columns leads to changes in the range of the target variable.  


### Visualising the target variable 'SalePrice'
<img src="/images/target-eda.png" width = 800>

### Heatmap of the correlations of the numeric features
<img src="/images/heatmap.png" width = 1500>

As can be seen from the heatmap:
- The 'SalePrice' is highly correlated with several features including 'MasVnrArea', 'TotalBsmtSF', '1stFlrSF', 'GrLivArea', 'FullBath', 'GarageCars', and 'GarageArea'.
- There are high correlations of more than 0.50 between some of the features, e.g. between 'TotRmsAbvGrd' and '2ndFlrSF', 'GrLivArea', 'FullBath', and 'BedroomAbvGr'. However, let RFE selects the most important features.  
  
## Step 2: Data Preparation for Building Model
In this step the following have been implemented:  
- Dummy variables (columns) for the categorical columns are created and the original categorical columns were dropped.
- The dataframe has been split into train dataframe and test dataframes.
- The standard scaling is applied only to the numeric columns of the train dataframe, since the rest of the columns are 0/1 's from dummy variables.
- Dependent feature (y_train, y_test) and independant dataframe (x_train, x_test) has been created.

## Step 3: Model Building and Training
- RFE used to select the 40 most significant features.
- Independant dataframe (x_train, x_test) were amended to take only those 40 RFE selected features.

### Base Regression model
The LinearRegression() class of sklearn was fitted to x_train:
R2 score (Base model) on train set:  0.8686
R2 score (Base model) on test set :  0.7859

### Ridge Regression
$R^2$ score on the train set and test set were plotted against a range of lambda values for a Ridge regression model:
<img src="/images/ridge.png" width = 800>

I would choose $Lambda = 1000$ as the best value for lambda, since the values of $R^2$ score of train and test are close. Increasing $Lambda$ beyond this value, although gives similar values of $R^2$ score of train and test, however, the $R^2$ score values are really low and the model is under-fit.  
  
With $Lambda = 1000$:
R2 score (Ridge model) on train set:  0.6984
R2 score (Ridge model) on test set :  0.7040

The Ridge model has identified the following variables as the most significant ones:
- OverallQual (rating of the overall material and finish of the house)
- 1stFlrSF (First floor surface area in square feet)
- 2ndFlrSF (Second floor surface area in square feet)
- TotalBsmtSF (Total basement floor surface area in square feet)
- BsmtFinSF1 (Type 1 finished in square feet)
  
### Lasso Regression
$R^2$ score on the train set and test set were plotted against a range of lambda values for a Lasso regression model:
<img src="/images/lasso.png" width = 800>

I would choose $Lambda = 0.1$ as the best value for lambda, since the values of $R^2$ score of train and test are close. Increasing $Lambda$ beyond this value, although gives similar values of $R^2$ score of train and test, however, the $R^2$ score values are really low and the model is under-fit.  

With $Lambda = 0.1$:
R2 score (Ridge model) on train set:  0.7266
R2 score (Ridge model) on test set :  0.7348

The Lasso model has identified the following variables as the most important ones:
- OverallQual (rating of the overall material and finish of the house)
- 1stFlrSF (First floor surface area in square feet)
- 2ndFlrSF (Second floor surface area in square feet)
- BsmtFinSF1 (Type 1 finished in square feet)
- TotalBsmtSF (Total basement floor surface area in square feet)  

As can be seen, Lasso's 5 most significant features are exactly the same as the 5 most significant features of Ridge regressor, except the ranking of the last two are different.  
  
## Step 4: Residual Analysis
  
### Ridge model residual analysis

#### Ridge model residual analysis
<img src="/images/ridge-hist.png" width = 500>
<img src="/images/ridge-scatter.png" width = 500>

#### Lasso model residual analysis
<img src="/images/lasso-hist.png" width = 500>
<img src="/images/lasso-scatter.png" width = 500>

Analysis of the histogram and the scatter plot of the residuals reveals that:

- the residuals follow a normal distribution with a mean of zero, as expected as one of the conditions of the MLR assumptions.
- the residuals are scattered around 0, and are independent of each other.
- the residuals have constant variance (homoscedasticity).
  
  
# Step 5: Summarising the Base, Ridge, and Lasso models, and Conclusion
Comparison of $R^2$ score of the Base, Ridge, and Lasso models  
<img src="/images/r2.png" width = 300>

Coefficients of Ridge regression model  
<img src="/images/ridge-coef.png" width = 500>

Coefficients of Lasso regression model  
<img src="/images/lasso-coef.png" width = 500>

As can be seen, by comparing the values of $R^2$ score of different models, the Lasso model is chosen due to the following reasons:
- The higher $R^2$ score compared to the Ridge model, and the lower difference between $R^2$ score of train and test in comparison to the base model. In other words, the $R^2$ score of the base model is higher than the Lasso, however, the difference between the $R^2$ score of train and test sets of the base model is higher than that of the Lasso model, which means the base model is slightly over-fit.
- The Lasso model has only 5 non-zero coefficients for the features, and while having a higher $R^2$ score compared to the Ridge, seems to be simpler and more generalisable.

## Answer to the Business Problem
Accordingly, the most significant variables (of the selected Lasso model) describing the price of a house, with an **$R^2$ score of around 73%** are:
- OverallQual (rating of the overall material and finish of the house)
- 1stFlrSF (First floor surface area in square feet)
- 2ndFlrSF (Second floor surface area in square feet)
- BsmtFinSF1 (Type 1 finished in square feet)
- TotalBsmtSF (Total basement floor surface area in square feet)  
  
  
# Acknowledgements
- I would like to acknowledge the feedback, support and dataset provision by [upGrad](https://www.upgrad.com/gb) and The [International Institute of Information Technology (IIIT), Bangalore](https://www.iiitb.ac.in/).  
- Also, I would like to express my gratitude to [Nishan Ali](https://www.linkedin.com/in/nishan-ali-826552166/) and Eshan Tiwari for providing clarification and guidance to carry out this project.   
- Furthermore, the valuable feedback from [Dr Tayeb Jamali](https://www.linkedin.com/in/tayeb-jamali-b1a10937/) is highly appreciated.
