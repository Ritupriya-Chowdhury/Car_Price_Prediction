# Used Car Price Prediction.
## 1. Research Questions

1. How accurately can Linear Regression and Random Forest predict a used car's selling price from its age, distance driven, fuel type, and transmission?
2. Does extracting the brand from the car name and creating a car age feature improve price prediction over using the raw columns alone?
3. Which factors matter most for a used car's price?

## 2. Dataset Description

### 2.1 Dataset
Dataset: `Car_details_v3.csv` (8,128 listings, 13 columns). After dropping 1,202 exact duplicates, **6,926 rows** remain.
Goal: predict `selling_price` (INR) from car attributes.

### 2.2 Two feature sets 

| Set | Columns | Shape |
|---|---|---|
| **Baseline** | `year`, `km_driven`, `fuel`, `transmission` | 6 |
| **Engineered** | `car_age`, `km_driven`, `fuel`, `transmission`, `brand_grouped` | 24 |

Baseline vs engineered isolates the effect of the brand and age features. Note that `mileage`, `engine_cc`, `max_power_bhp`, `seats`, `seller_type` and `owner` were cleaned or explored but are not in either final feature set.

### 2.3 Train/test split
- 80/20 split with `random_state=42`, done on the row index so **both feature sets and both models see identical train and test rows**.
- Train 5,540 rows, test 1,386 rows.

### 2.4 Limitations
- The prices are asking prices from listings, not final sale prices.
- It’s a snapshot that ends at 2020, so it ignores later market changes.
- Condition, accident history, and location are not recorded.
- Some brands have few listings, so the model may be weak for rare cars.

## 3. Data Cleaning

### 3.1 Remove duplicate values: 
1,199 duplicates removed

### 3.2 Check Unique values in string columns and change where require
- String columns, str_cols=['name', 'fuel', 'seller_type', 'transmission', 'owner', 'mileage',
       'engine', 'max_power', 'torque'] 
- `mileage`: 221 NaN. Values are text such as “18.9 kmpl”. CNG and LPG cars use km/kg instead. Convert NaN and 0 with median.
- `engine`: 208 NaN. Replace NaN values with median.
- `max_power`: 206 NaN and 3 zeros. Replace NaN and 0 values with median.
- `seats`: 208 NaN values. Replace NaN and 0 values with median.

## 4. Feature Engineering

- Car age (car_age). The raw year column records when a car was made, but what a buyer reacts to is how old the car is. Age was therefore computed as car_age = 2020 - year, where 2020 is the newest manufacturing year in the dataset (the data was collected around that time). A 2020 car has age 0, and an older car has a larger value. This makes the feature easier to interpret (each extra year of age is associated with a change in price) and makes the depreciation effect explicit. Because car_age is a linear transformation of year, it contains the same information, so it is not expected to improve Linear Regression or Random Forest by itself. It is included mainly to test whether this more interpretable form matters.

- Brand (brand). The name column combines brand, model and variant (for example, "Maruti Swift Dzire VDI"), which makes it too specific to use directly: almost every value is nearly unique. The brand was extracted as the first word of name. Two-word brands that the split cuts short (for example, "Land" for Land Rover and "Ashok" for Ashok Leyland) were corrected manually [FILL: confirm which brands were corrected]. Brand captures differences in price level that the raw columns cannot, such as the premium of luxury makes over mass-market ones. It also stands in for model quality, reputation and typical resale value, which are not recorded elsewhere in the baseline set.

- Rare brands. Brands with fewer than listings were grouped into a single "Other" category, so that one-hot encoding does not create columns based on only a few cars. 
- Encoding. brand, fuel and transmission are categorical and were one-hot encoded. The numeric features were left in their original units.

- Features considered but not used. torque was excluded because its units and formats are inconsistent. owner and seller_type were not used, to keep the comparison between feature sets focused on the questions asked. Other derived features (such as kilometres driven per year) were not created, so that any change in accuracy between Set A and Set B can be attributed to car_age and brand only.

## 5. Models and Why

### 5.1 Linear Regression
- Serves as the interpretable baseline. With a log target, coefficients approximate percentage changes in price.
- Fast, no hyperparameters, and a sanity floor: any more complex model must beat it to justify itself.

### 5.2 Random Forest Regressor
- `n_estimators=200`, `random_state=42`, `n_jobs=-1`, other settings default.
- Captures non-linear effects and interactions (for example, kilometres matter differently for old and new cars, brand effects differ by fuel) without manual feature crosses.
- Robust to outliers (extreme `km_driven` values were kept, not removed) and needs no scaling.

## 6. Evaluation Metrics and Why

All metrics are computed on the held-out test set. Rupee metrics use predictions converted back with `exp`.

| Metric | Why |
|---|---|
| **R² (log price)** | Variance explained on the scale the model was trained on. Standard goodness of fit, and comparable across models. |
| **R² (rupees)** | Same idea on the original price scale, where expensive cars dominate. Shows how well the model handles high-value listings. |
| **MAE (Rs)** | Average absolute error in rupees. Easy to explain to a seller or buyer and not dominated by a few large misses. |
| **RMSE (Rs)** | Penalises large errors more heavily. Comparing RMSE with MAE reveals whether a few big misses (likely luxury cars) drive the error. |
| **MAPE (%)** | Relative error, appropriate because prices vary over orders of magnitude. |
| **5-fold CV R²** | Run on the engineered training set for both models, checking that the difference between models is not an artefact of one random split. |
| **Confusion matrix on price bands** | Predictions and actuals binned into Budget (<3 lakh), Mid (3-7 lakh) and Premium (>7 lakh). Shows where band-level mistakes happen, which matters more for a user than a continuous error. |

## 7. Results (test set)

| Model | Features | R² (log) | MAE (Rs) | MAPE (%) |
|---|---|---|---|---|
| Linear Regression | baseline | 0.670 | 163,171 | 35.6 |
| Random Forest | baseline | 0.649 | 166,884 | 36.1 |
| Linear Regression | engineered | **0.773** | **131,289** | 29.0 |
| Random Forest | engineered | 0.768 | 131,420 | **28.1** |

- Engineered features improved both models (MAE down about 20%).
- Since `car_age` is only `year` reversed, a linear model gains nothing from it; the improvement is attributable to **brand**.
- Linear Regression and Random Forest are essentially tied (R² gap 0.004). Linear Regression had higher R² in all 5 CV folds, so there is no evidence that Random Forest's flexibility helps with this small feature set.
