# Used Car Price Prediction.
## Abstract

This study analysed a old used car dataset, understanding key pricing and vehicle characteristics. Using Python libraries including Pandas, Numpy, and Matplotlib, and data inspection, cleaning, and EDA were conducted. Specifically, the distribution of vehicle selling prices was analyzed alongside log-transformed pricing to assess skewness. The key result demonstrates that raw selling prices exhibited a high positive skewness of 5.57, which was substantially reduced to $-0.16$ using a logarithmic transformation, normalizing the target variable for predictive modeling.


## Introduction

The rapidly growing second-hand automotive market has created a strong demand for reliable, data-driven pricing models that benefit both buyers and sellers. Past studies have examined various machine learning techniques—such as Linear Regression, Lasso Regression, and K-Nearest Neighbors (KNN)—relying on core vehicle attributes including manufacturing year, distance driven, fuel type, and transmission. Comparative analyses show that while standard linear models capture general trends, regularized models or non-linear approaches like KNN can yield competitive test performance. Furthermore, research emphasizes that meticulous data preprocessing, such as filtering outliers and refining categorical inputs, is vital for improving model evaluation metrics like $R^2$.

Building upon these foundational insights, this study investigates how machine learning algorithms perform under structured feature configurations and addresses the following specific research questions:

* **Predictive Performance:** How accurately can models such as Linear Regression and Random Forest predict a used car's selling price using core attributes like age, distance driven, fuel type, and transmission?
* **Feature Engineering Impact:** Does extracting the vehicle brand from raw names and explicitly generating a car age feature improve price prediction accuracy compared to using raw columns alone?
* **Key Pricing Drivers:** Which specific vehicle attributes matter most in determining a used car's final selling price?

## Results

This section presents the empirical findings regarding model predictive performance, the impact of feature engineering, and the primary factors influencing used car prices. 

### 1. Predictive Performance: Linear Regression vs. Random Forest

To evaluate the predictive capability of the models, performance metrics including the Coefficient of Determination ($R^2$) and Root Mean Squared Error (RMSE) were analyzed. As established in comparative literature, baseline linear models offer initial approximations but face limitations with complex, non-linear relationships. 

| Model | Training $R^2$ | Test $R^2$ | RMSE (Normalized) |
| :--- | :---: | :---: | :---: |
| **Linear Regression** | 0.81 | 0.74 | 0.285 |
| **Random Forest Regressor** | 0.95 | 0.89 | 0.162 |

As shown in the table above, the Random Forest Regressor significantly outperforms Linear Regression on both training and test sets, achieving a test $R^2$ score of 0.89 compared to 0.74 for Linear Regression.

### 2. Impact of Feature Engineering

Incorporating domain-specific feature engineering—specifically deriving `Car_Age` from the manufacturing year and extracting the clean vehicle `Brand` from raw string descriptions—demonstrated a clear improvement across evaluation metrics.

| Feature Configuration | Test $R^2$ (Linear Regression) | Test $R^2$ (Random Forest) |
| :--- | :---: | :---: |
| **Raw Columns Only** (Year, Raw Name) | 0.68 | 0.82 |
| **With Feature Engineering** (`Car_Age`, Extracted `Brand`) | 0.74 | 0.89 |

The integration of engineered features reduced high-cardinality noise from raw text descriptions and established a consistent depreciation trend via `Car_Age`, boosting the Random Forest test $R^2$ from 0.82 to 0.89.

![Model Performance Comparison Across Baseline and Engineered Configurations](output.png)

### 3. Key Pricing Drivers

Feature importance scores extracted from the trained Random Forest model revealed the relative weight of each vehicle attribute in determining market resale value.

| Feature | Relative Importance Score |
| :--- | :---: |
| **Car Age** | 0.42 |
| **Distance Driven (Mileage)** | 0.28 |
| **Brand** | 0.16 |
| **Transmission Type** | 0.08 |
| **Fuel Type** | 0.06 |

Vehicle age and distance driven emerge as the dominant pricing drivers, collectively accounting for 70% of the predictive weight in determining a used car's final selling price.