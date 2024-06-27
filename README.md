# Liquor Inventory Restocking Predictions Project

![image](https://github.com/joeldmott/inventory_project/assets/51928528/39641d3f-b415-4528-bd5d-f1317a1b8dfb)
photo by Nikolay Loubet on [Unsplash](https://unsplash.com/photos/three-bottles-of-whisky-are-lined-up-on-a-shelf-O6QTgEbwimc)

## project summary ('elevator pitch')

Inventory restocking walks a fine line between overstocking and a stockout while navigating shifting levels in demand all year.

This project uses ARIMA and SARIMA time-series models to predict liquor inventory volume throughout 2024 for Iowa’s [busiest liquor retailer](https://www.hy-vee.com/stores/detail.aspx?s=49) using data from [Iowa’s open data platform](https://data.iowa.gov/Sales-Distribution/Iowa-Liquor-Sales/m3tr-qhgy/about_data). I first optimize a model on the overall liquor inventory for this store.

Next, category-specific models predict which types of liquor to restock throughout the year via streamlined functions. While accuracy varies depending on the category, all but one category predictions are above 94% accurate.

##  dataset

Iowa's open data project has kept track of all liquor retailer sales since 2012. As of June 2024, this data contains well over 29 million records (each record is a B2B or B2C transaction) with 24 columns pertaining to the date, store, location, liquor category & item, amount and volume of bottles per sale, and sale amount.

As eager as I was to work with all this data, the limitations of my own hardware and Google Colab as well as the relatively slower pace of the time-series models used here ((S)ARIMA) kept the scope of this project to one store (the busiest one, a Hy-Vee Grocery Supermaket on 14th Street in Des Moines):

![image](https://github.com/joeldmott/inventory_project/assets/51928528/a99cca9a-3b91-42cc-9d77-7592edfdce6c)

Subsequently, the data used here is [a snapshot of this store's sales as of June 2024 with some columns that were not relevant to iventory removed](https://www.kaggle.com/datasets/joelmott/liquor-inventory-dataset). Instead of going all the way back to 2012, I went to 2017. In order to keep the inventory forecasting on an annual scale, the data is cut off at the end of 2023 and then forecasts are made for all of 2024.

## reproducing the project

The [project notebook](https://colab.research.google.com/drive/1l3t-A3192pO0R2OuVuXn5PY61f7J7d84#scrollTo=ioRm9ijRls5G) will download [my June 2024 snapshot of the data](https://www.kaggle.com/datasets/joelmott/liquor-inventory-dataset), which I retrieved by querying the [Iowan Open Data page](https://data.iowa.gov/Sales-Distribution/Iowa-Liquor-Sales/m3tr-qhgy/about_data) for data just at the Hy-Vee Supermarket #3 (on 14th Street in Des Moines) from 2017-2023 with a few select columns. 

## deep-dive pre-work

This project is my first foray into time-series modeling, which I learned about through a number of online resources including Dr. Jason Brownlee's [site on Machine Learning Mastery](https://machinelearningmastery.com/time-series-seasonality-with-python/) as well as some more general posts on using (S)ARIMA [here](https://medium.com/@sophiamoullin_sac/arima-vs-sarima-vs-sarimax-03dd04fc7c66#:~:text=ARIMA%2C%20SARIMA%2C%20and%20SARIMAX%20models,accuracy%20by%20incorporating%20exogenous%20variables.) and [here](https://zerotomastery.io/blog/arima-sarima-sarimax-explained/).

I also learned more about different kinds of liquor (I confess I had no idea what a 'liqueur' was before this project or all the different types of whiskey there are) as well as some general information and terms on inventory management [here](https://www.shipbob.com/blog/demand-forecasting/).

## preprocessing

While there are definitely multiple factors that influence restocking, this project focuses on what we can learn from this dataset: historical inventory data. Subsequently, I focus on the 'Date' and 'Bottles Sold' columns. After a broader model that examines all liquor inventory, I also examine popular individual categories.

Specifically, I group the data by date and resample by week (since some dates are missing for holidays). Therefore, the forecasted deliverable will feature 52 predictions, one for every week of 2024.

This dataset contains 52 unique categories of liquor, which is somewhat impractical for this inquiry. I generalized them into 18 categories in an attempt to make broader predictions with more robust data without sacrificing too much granularity.

Before modeling, each subset of the data undergoes a time-series split with five of the six total years used to train the models and the last year (2023) set aside as a test set as seen here in the first model evaluation on the overall volume (all categories): 

![image](https://github.com/joeldmott/inventory_project/assets/51928528/cb9e23ce-1efe-4949-bf44-3cde3a078521)

## modeling

Time and hardware constraints limit this project to univariate modeling. With some seasonality keeping this data from being stationary, I would normally have just used a StatsModels SARIMA model, but running a parameter grid to optimize SARIMA put a strain on Colab. Subsequently, I used ARIMA to determine the optimal parameters for SARIMA's order *and* seasonal order parameters.

## evaluation

Both the Akaike Information Criterion (AIC) and root mean squared error (RMSE) are important metrics for (SA)ARIMA models. At first, I priveledged the AIC since it balances accuracy with model complexity. However, it proved to be a bigger help in terms of optimizing the models rather than evaluating them. Therefore, the primary evaluation metric used here is RMSE. The test set RMSE for the whole inventory model as well as all but one of the category models is above 94% accurate.

## repository structure
