# ACRE/CFx Commodity Price Forecasting Pipeline
A plug-and-play notebook for commodity price forecasting that uses standard ML Pipeline template and data ingestion methods for features coming from Barchart and Oilworld.


- #### Introduction

    - In this project, a number of tree-based regressors are deployed to predict the monthly price direction and concurrent median spot price of a chosen commodity contract over a select period of time (the client will need to specify the type of contract or averaging method across contracts that should be used). 


- #### Data compatibility and acquisition

    - This code will work for any market data ingested via Barchart to construct the target feature as well either Barchart or Oilworld physical commodity contract data for input features. 
    - In order to obtain the data pertaining to Oilworld data please contact XXX. The data should contain a monthly price column with timestamps as well as the following physical 1-month forward contract price provided as either floats or integers: 
        -  Soybean oil, US, fob Gulf	
        -  Soybean oil,U.S.,fob Decatur	
        -  Soybean oil,Dutch, fob ex-mill	
        -  Soybean oil, Brazil, fob	
        -  Soybean oil, Argentina, fob	Grnd'nt oil,any orig,cif Rott	
        -  Sunoil, EU, fob N.W.Eur. ports	
        -  Sunoil, Arg., fob	
        -  Sunoil, fob Black Sea	
        -  Rape oil,Dutch, fob ex-mill	
        -  Corn oil, U.S., fob Midwest	
        -  Corn oil, U.S., fob Gulf	
        -  Olive Oil,Spain,Extra Virgin	
        -  Palm oil crude, cif Rotterdam	
        -  Palm oil RBD, Mal, fob	
        -  Palm oil crude, Indonesia, fob	
        -  Palm olein RBD, Mal, fob	
        -  Palm olein RBD, Mal, cif Rott	
        -  Palm stearin RBD, Mal fob	
        -  Palm stearin RBD, Mal,cif Rott	
        -  PFAD, Mal fob	
        -  Palmkern oil,Mal/Indo,cif Rott	
        -  Coconut oil,Phil/Indo,cif Rott	
        -  Butter, Germany, 25kg, min 82%	
        -  Lard, EU, unrefined, 0.5%	
        -  Fish oil,any orig,cif N.W.Eur	
        -  Fish oil, Peru, fob	Lin oil,any orig,ex-tank Rott	
        -  Castor oil, ex-tank Rotterdam	
        -  Tung oil,S.America,ex-tank Rot	
        -  Tallow, Edible, US, fob Gulf

    - In order to obtain Barchart-related data, please download the price risk analytics code repo at the link below and apply the instructions to get economic data (also pasted below)

# Get Barchart economic data using price risk analytics repo)
Download the repo here at: https://github.com/McK-Private/acr-price_risk_analytics/tree/develop/price_risk_analytics

Once deployed, the following in the command line:
'price_risk_analytics get-economic-data --help' to view the options.

A few example commands below:
price_risk_analytics get-economic-data barchart C --market=CBOT --start=2019-01-01 --end=2020-01-01 --output=./data.csv
price_risk_analytics get-economic-data dtn QCL --market=NYMEX --start=2019-01-01 --end=2020-01-01 --output=./data.csv
price_risk_analytics get-economic-data quandl CME_C1 --market=CHRIS --start=2019-01-01 --end=2019-06-01 --output=./data.csv

Data issues - Please contact Market Data Support <mds@barchart.com> for data issues. Please list the following information:


- #### Exploratory data analysis

    - Standard pandas profiling reports are provided along with basic summary statistics to inform skew and missingness in the data.


- #### Data wrangling and pre-processing

    - The current approach converts time series data into a usable format for a supervised machine learning problem and applies the following standard transformations:
       - Handling missing data (represented as NaNs or zeros) using forward-backfill mean imputation. This method can also be substituted for general mean imputation if required
       - Removal of data skew using log transformation
       - Normalization with sklearn MinMax scaling


- #### Feature engineering

    - Automated feature engineering is included in the template of this code. Examples are also provided for information that may be helpful to engineer into additional input features (consult with your CST, the client, and potentially use ENS Navigator to set up expert interviews for identifying targets specific to your use case). 

- #### Feature transformation

    - The option is provided to apply dimensionality reduction using the PCA algorithm from sklearn. The number of dimensions to be used should be specified by the user.


- #### Feature selection 

    - Feature selection defaults to the Boruta method and reverts to F-test selection if no features are initially chosen. Setting a lower iteration rate may also help to reduce run-time and expand the selection, but may also reduce model accuracy.

- #### Model selection and evaluation approach

    - A tree-based model is required to run the code (any type of ensemble or boosted-tree model is compatible but may require additional updates to params grid and confidence interval generator). Currently the model will default to sklearn's RandomForestRegressor.

    - Backtesting is applied using a time series GridSearch expanding window cross-validation approach - the training period must be specified by the user. The model will train up to each point in an expanding window and then make an out-of-sample prediction. These out-of-sample predictions are then collected into a final dataframe along with confidence intervals (currently generated by forestci). This dataframe is then further transformed to convert predictions from % difference between current and future median contract price into spot price predictions.

    - The model is evaluation using standard regression metrics (MAE, MAPE) as well as directional accuracy (precision, recall, and F1 scores). For directional accuracy the option is also provided for both binary and multi-class confusion matrices; the client will need to provide information on the cut-off points to be used for constructing classes. 


- #### Feature importance

    - A gini-importance score is used to rank and plot feature importances (using sklearn package)


- #### Final output and visualization

    - The final predictions, confidence intervals, and information related to actual price as well as % change in price are saved in both .csv and .xlsx format. 

    - Additionally, an ouptut file is provided in both .csv and .xlsx format for populating an MVP PowerBI Dashboard. In order to fully utilize the dashboard, output may also be required from the ACRE Commodity FX hedging optimization support tool.

    - A version of the dashboard is also in development that will only require price forecasting output from this notebook.
