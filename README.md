# Car_Prices_Prediction

## What drives the price of a car?

## OVERVIEW

In this application, I will explore a dataset from kaggle. The original dataset contained information on 3 million used cars. The dataset used contains information on 426K cars to ensure speed of processing. Our goal is to understand what factors make a car more or less expensive. As a result of the analysis, I will provide clear recommendations to my client -- a used car dealership -- as to what consumers value in a used car.

To frame the task, we will refer back to a standard process in industry for data projects called CRISP-DM. This process provides a framework for working through a data problem. A brief overview of CRISP-DM is here.

The notebook is here ![Car_prices.ipynb](https://github.com/DomenicoTanzarella/Car_Prices_Prediction/blob/main/Car_prices.ipynb) 

### Business Understanding

    - Develop a regression model to forecast used car prices based
  
    - Employ feature importance analysis/engineering to quantify the relative impact of car attributes (make, model, mileage, condition, age, etc.) on the target variable (price).


### Data Understanding

After considering the business understanding, next step is to get familiar with the data. Some of the steps in this phase are:

    -load the data, print a sample of the dataset to check which features are provided and their type

    -Check for missing data or Duplicates

    -Remove Outliers (car priced too high or 0)

    -Drop redundant features

    -Drop useless features


Data is related to auto sold in USA so prices are in USD

There is also an intuition from a first glance to the data that this dataset might need a lot of massaging in order to be fed to a regression model, which requires no missing data and numerical features (or features engneering like one hot encoding).

We have 18 Features but only 4 are numerical:

    id
    year
    Odometer
    Price

Some features are useless hence we could drop them:

    id
    VIN
    paint_color

Two features are redundant:

    State
    region


## Data Preparation

After our initial exploration and fine tuning of the business understanding, it is time to construct our final dataset prior to modeling. Here, we want to make sure to handle any integrity issues and cleaning, the engineering of new features, any transformations that we believe should happen and general preparation for modeling with sklearn.

### Feature removal

These features don't help with the price forecast:

    - 'manufacturer' column has no value as the same manifacturer can have 15000 USD cars and 150000 USD cars (e.g. Chevrolet), hence it can be dropped.
    - 'title' value has only few of 'salvage' title that would distort the prediction. Every other entry has either 'clean' value or 'lien' but both of them have no effect on price, hence dropping it too.
    - 'region' feature is made redundant by the 'state' column as we can assume car prices withn a state are comparable.
    - 'size' feature has ~70% of that missing, so we will drop that too alongside the VIN column

Last step of the initial Data Preparation would be to drop the NA values: this will likely further lower the number of data, but before doing that I want to futher massage the data in the features we have left for the data analysis.


### Outliers removal

  -After dropping several columns and duplicates row in the initial data exploration phase, now let's do more targeted cleanup.

  -Based on common sense, domain knowledge and initial data exploration, the following kind or rows can be dropped:

    Cars with price above 350000 USD
    Cars with price equal to 0
    Cars with odoeter above 400000 miles

-Another option is to drop columns with high Na ratio:

    'type', 'drive', 'condition', 'cylinders'.

because there to do imputation on 23-37% of data might cause overfitting: we will rather create a model with less real data.

-Lastly, model feature has too many different values and will cause heavy memory usage during the modeling phase (especially using onehotencoding), so I need to drop that as well.

To summarize, the following columns will be dropped:

    'model', 'drive', 'condition' and 'cylinders' that have a percentage of NaN > 29%.

The final cleaned dataset will be:

     Column        Non-Null Count   Dtype  
---  ------        --------------   -----  
     price         209949 non-null  int64
 
     year          209949 non-null  float64
 
     fuel          209949 non-null  object 
 
     odometer      209949 non-null  float64
 
     transmission  209949 non-null  object 
 
     type          209949 non-null  object 
 
     state         209949 non-null  object 
 
We finally have a clean dataset. Let's see how the numerical values correlate:

![alt text](https://github.com/DomenicoTanzarella/Car_Prices_Prediction/blob/main/corr.png)

that shows a new car is correlated to higher price and higher value of odometer brings the value of the car down, which matches common sense when people purchase a car.

## Modeling

With the final dataset in hand, it is now time to build some models.

We use Linear Regression, Ridge and Lasso regressors with some pre processing that performs one hot encoding and scaling of the data:

![alt text](https://github.com/DomenicoTanzarella/Car_Prices_Prediction/blob/main/regression.png)

# Evaluation
With some modeling accomplished, we aim to reflect on what we identify as a high quality model and what we are able to learn from this. 
We will distill our findings and determine whether the earlier phases need revisitation and adjustment or if we have information of value to bring back to your client.

For the test set, we got these medim values:

    price     18356.54
    ridge     15249.95
    lasso     18451.75
    linear    18448.08


and:

    R-squared for linear regression: 0.4287
    R-squared for Ridge regression: 0.4287
    R-squared for Lasso regression: 0.4844

 
Graphically we see:

![alt text](https://github.com/DomenicoTanzarella/Car_Prices_Prediction/blob/main/data-perf.png)

Lasso seems to perform slightly better then ridge and linear. Overall however seem to have the same performances with this dataset. 

Also changing alpha values does not bring any significant change or improvment in the models capability to predict the car price based on their features.

So Lasso regularization appears to have the best performances with this dataset, hence we will use that for predicting price of cars based on the selected features.

Let's see what feature influence the most the price:

![alt text](https://github.com/DomenicoTanzarella/Car_Prices_Prediction/blob/main/coeff.png)

## Conclusions

Results are interesting: aside from the obvious that
            
            -higher value of the odometer lower the car value  
            -the newest the car is the higher is the price
            
other considerations apply:

            -Pickup trucks contribute significantly to higher price of the car, matching comon market price observations.
            -sedan, wagon and minivan contribute to lower the car price, which is in line with the national trend of consumers shifting to big cars.
            -diesel car have a positive influence to the car price while hybrid and gas tend to go against high price value.
            -buying a car in Utah might be more expensive compared to, for exmaple, California so dealers should avoid purchasing cars from there.
