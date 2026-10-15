# Used Car Price Prediction.
## Abstract

This study analysed a old used car dataset, understanding key pricing and vehicle characteristics. Using Python libraries including Pandas, Numpy, and Matplotlib, and data inspection, cleaning, and EDA were conducted. Specifically, the distribution of vehicle selling prices was analyzed alongside log-transformed pricing to assess skewness. The key result demonstrates that raw selling prices exhibited a high positive skewness of 5.57, which was substantially reduced to $-0.16$ using a logarithmic transformation, normalizing the target variable for predictive modeling.


## Introduction

The rapidly growing second-hand automotive market has created a strong demand for reliable, data-driven pricing models that benefit both buyers and sellers[cite: 1]. Past studies have examined various machine learning techniques—such as Linear Regression, Lasso Regression, and K-Nearest Neighbors (KNN)—relying on core vehicle attributes including manufacturing year, distance driven, fuel type, and transmission[cite: 1]. Comparative analyses show that while standard linear models capture general trends, regularized models or non-linear approaches like KNN can yield competitive test performance[cite: 1]. Furthermore, research emphasizes that meticulous data preprocessing, such as filtering outliers and refining categorical inputs, is vital for improving model evaluation metrics like $R^2$.

Building upon these foundational insights, this study investigates how machine learning algorithms perform under structured feature configurations and addresses the following specific research questions:

* **Predictive Performance:** How accurately can models such as Linear Regression and Random Forest predict a used car's selling price using core attributes like age, distance driven, fuel type, and transmission?
* **Feature Engineering Impact:** Does extracting the vehicle brand from raw names and explicitly generating a car age feature improve price prediction accuracy compared to using raw columns alone?
* **Key Pricing Drivers:** Which specific vehicle attributes matter most in determining a used car's final selling price?