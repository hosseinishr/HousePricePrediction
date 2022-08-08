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

<img src="/images/num-eda.png" width = 500>

As can be seen:
- There is a linear correlation between some of the numeric columns and the target column. Hence, a linear regression model can be considered and trained.
- Also, it can be observed that there are correlations between different numeric columns as well, e.g. between 'GrLivArea' and 'TotRmsAbvGrd', or between '1stFlrSF' and 'GrLivArea'. However, let's RFE decides to choose the most important features for the regression model rather than dropping the columns that have some degree of correlation between themselves.
- The month of selling, 'MoSold', was dropped since there is not much of an impact of different months of selling on the target variable.

### Categorical columns EDA

<img src="/images/cat1-eda.png" width = 500>
<img src="/images/cat2-eda.png" width = 500>
<img src="/images/cat3-eda.png" width = 500>
<img src="/images/cat4-eda.png" width = 500>
<img src="/images/cat5-eda.png" width = 500>

As can be seen, the categorical columns have contribution to the target variable since moving across different categories of each of these columns leads to changes in the range of the target variable.  


### Visualising the target variable 'SalePrice'
<img src="/images/target-eda.png" width = 500>

### Heatmap of the correlations of the numeric features
<img src="/images/heatmap.png" width = 500>

As can be seen from the heatmap:
- The 'SalePrice' is highly correlated with several features including 'MasVnrArea', 'TotalBsmtSF', '1stFlrSF', 'GrLivArea', 'FullBath', 'GarageCars', and 'GarageArea'.
- There are high correlations of more than 0.50 between some of the features, e.g. between 'TotRmsAbvGrd' and '2ndFlrSF', 'GrLivArea', 'FullBath', and 'BedroomAbvGr'. However, let RFE selects the most important features.  
  
 

- 
- Also, the type of the columns are as expected, based on the observation of the head() of the dataframe.
- 
- independent variable: the rest of the variables (features)
- No duplicated rows were observed.
- Some of the columns that were not needed for the analysis and model building were dropped ('instant', 'dteday', 'casual', 'registered').



