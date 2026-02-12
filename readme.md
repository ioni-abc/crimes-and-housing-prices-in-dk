## Introduction

This code is part of a group assignment for the course "Data Mining" from ITU. I have included only the part that I worked on.

## Research Questions
1. To what extent does criminal activity influence the housing market in the Zealand and Capital Regions of Denmark?
2. Can we predict a house's price based on the local crime rate? What is/are the predictor/predictors?


---

## Data

For this hypothesis, we utilized two primary data sources:
* **housing_prices:** Includes data regarding the residential household sales during the period 1992 – 2024. It includes data such as the quarter of the sale, the house type, the construction year, the purchase price, the address, the square meters etc. The dataset is available on www.kaggle.com. It was provided and ini- tially cleaned by Martin Frederiksen. The raw data are available in the [Github](https://github.com/MartinSamFred/Danish-residential-housingPrices-1992-2024) repository.

* **non_danish_crime_per_capita:** A processed dataset merging crime data with population demographics (2014–2024). It is based on **crimes_data**. The data are also provided by the StatBank Denmark (Statistikbanken) [here](https://www.statbank.dk/statbank5a/SelectVarVal/Define.asp?MainTable=STRAF11&PLanguage=1&PXSId=0&wsid=cftree).


## Data Processing and Analysis Pipeline

This module performs data cleaning, feature engineering, and visualization on the Danish housing prices dataset. The workflow transforms raw transaction data into a clean format suitable for further analysis and generates publication-ready figures.

### 1. Data Cleaning and Filtering
- **Source:** Loaded raw data from `housing_prices.parquet`.
- **Filtering:**
  - **Timeframe:** Restricted data to the period between **2014 and 2024**.
  - **Region:** Filtered specifically for the **Zealand** region.
  - **Columns:** Selected relevant features: `date`, `year_build`, `purchase_price`, `city`, and `region`.
- **Formatting:** converted the `date` column to represent the transaction year only.

### 2. Feature Engineering
- **Municipality Mapping:** - Created a mapping dictionary to aggregate specific **Cities** into their respective **Municipalities** (e.g., grouping *København N*, *V*, *S*, etc., under *Copenhagen*).
  - Validated data integrity, confirming coverage for 45 distinct municipalities in the Zealand/Capital region.
- **Dimensionality Reduction:** Removed the original `region` column after processing.

### 3. Visualization
Generated high-quality plots using `matplotlib` and `seaborn` with LaTeX integration for typography. The following figures were saved as `.pgf` files:
- **`housing_trend.pgf`**: Line plot showing the median housing price trend from 2014 to 2024.
- **`price_distribution_by_municipality.pgf`**: Boxplot displaying price distributions, sorted by median price per municipality.
- **`number_of_sales_per_municipality.pgf`**: Count plot illustrating transaction volume per municipality.

### 4. Output
- The final processed dataset was exported to CSV format: `datasets/housing_data_clean.csv`.


## Statistical Analysis and Modeling Pipeline

This module extends the project by integrating socioeconomic data (crime rates and demographics) with housing prices to identify patterns and predictive relationships. The analysis employs unsupervised learning (Clustering) and supervised learning (Regression).

### 1. Data Integration and Validation
- **Sources:** Merged the cleaned housing dataset (`housing_data_clean.csv`) with `non_danish_crime_per_capita.csv`.
- **Validation:** - Identified a discrepancy in municipality coverage (Bornholm present in crime data but absent in housing data).
  - Performed an inner join on `municipality` and `year`, resulting in a consolidated dataset of **337,512** records.

### 2. Cluster Analysis (K-Means)
To group municipalities based on market behavior and safety:
- **Preprocessing:** Aggregated data by municipality (median price vs. crime rate) and applied `StandardScaler`.
- **Optimization:** Used the **Elbow Method** (via `KneeLocator`) to determine the optimal number of clusters ($k=4$).
- **Outlier Detection:** - Identified **Copenhagen** and **Tårnby** as significant outliers using Z-score thresholding ($|z| > 3$).
  - Re-ran clustering on the non-outlier dataset, resulting in more distinct and natural groupings.
- **Insights:** Identified distinct market segments, such as "High Value/Low Crime" (e.g., Gentofte) vs. "Low Value/Moderate Crime" (e.g., Lolland).

### 3. Predictive Modeling (Linear Regression)
Tested the hypothesis: *Can crime rates predict housing prices?*
- **Experiments:** Conducted comparative regression analysis across 5 feature sets and 2 dataset conditions (With vs. Without Outliers).
- **Key Findings:**
  - **Crime Rate Alone:** Poor predictor ($R^2 \approx 0.00$).
  - **Location Dominance:** Municipality identity is the strongest single predictor ($R^2 \approx 0.31$).
  - **Best Model:** A combined model using *Crime Rate + Demographics + Location* achieved the highest accuracy ($R^2 = 0.325$, MAE $\approx$ 1.14M DKK).

### 4. Visualization
Generated analytical figures to interpret complex relationships:
- **`elbow_method.pgf`**: Diagnostic plot to determine optimal cluster count.
- **`kmeans_clustering.pgf`**: Scatter plot visualizing municipality clusters based on price and crime.
- **`kmeans_clustering_no_outliers.pgf`**: Refined clustering plot after removing statistical outliers.
- **`residuals_histogram.pgf`**: Distribution of prediction errors for the best-performing regression model.

## Conclusion

This project investigated the relationship between housing prices and criminal activity in the Zealand and Capital Regions of Denmark (2014–2024). By integrating housing transaction data with socioeconomic indicators, we derived the following key insights:

### 1. Market Segmentation
* **Distinct Clusters:** The housing market is not uniform but segments into four distinct clusters based on price and safety.
    * **High Value / Low Crime:** Premium municipalities (e.g., Gentofte, Rudersdal) where safety strongly correlates with high property values.
    * **Low Value / Moderate Crime:** Economically less active areas (e.g., Lolland) where low prices persist despite varying crime levels.
    * **Outliers:** Urban centers like **Copenhagen** and **Tårnby** defy standard trends, exhibiting both high prices and high crime rates (likely due to population density and specific factors like the airport).

### 2. Predictive Power of Crime
* **Crime is a weak standalone predictor:** A simple linear regression using only crime rates yielded an $R^2$ of near zero, indicating that crime statistics alone cannot predict housing prices.
* **Location is the primary driver:** The municipality itself is the strongest predictor of price ($R^2 \approx 0.31$).
* **Marginal Gains:** Adding crime and demographic data to location-based models provided only a small improvement in predictive accuracy (increasing $R^2$ from 0.31 to 0.325).

### Summary
While a correlation exists between safe neighborhoods and higher property values, **crime rate is a secondary factor** in the Danish housing market. Location (Municipality) remains the dominant driver of price, with crime and demographics serving as minor adjusting variables rather than primary determinants.