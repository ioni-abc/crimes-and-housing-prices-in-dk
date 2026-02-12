1) What knowledge/insight have you tried to extract from what data?

The primary objective of this research was to investigate the relationship between public safety (measured by criminal activity) and economic value (measured by housing prices) across the municipalities of Zealand, Denmark.

** The Core Research Question **
We aimed to determine if local crime rates serve as a primary driver for housing market segmentation. Specifically, we wanted to answer:
- Does a lower crime rate consistently correlate with higher property values?
- Can we identify distinct "market profiles" based on the interaction of these two variables?
- To what extent can we predict the median purchase price of a home using safety statistics versus other features like location, building age, and demographics?

While our intuition was suggesting that safer areas should be more expensive, urban centers like Copenhagen often have high crime rates alongside the highest property values due to density and economic activity. We aimed to extract how these factors interact to shape the Zealand real estate landscape.

** Data Sources and Integration **
To extract these insights, we merged several distinct datasets provided by Statistics Denmark and Kaggle.



2) What methods have you used?

** Unsupervised Learning: Clustering Analysis **
We used clustering algorithms to discover natural groupings of municipalities without prior labeling.

Initial Approach (Discarded): K-Means Clustering. We initially attempted to divide the market using K-Means. However, this method assumes spherical clusters and struggled with the continuous "gradient" nature of the housing market, drawing arbitrary vertical lines through the data.

Final Approach (Selected): Agglomerative Hierarchical Clustering. We shifted to a bottom-up hierarchical approach using Ward’s linkage method. This algorithm was a bit better for our dataset as it builds clusters based on local proximity rather than global centroids, allowing it to capture irregular market shapes and the distinct outlier behavior of Copenhagen and Gentofte.

** Supervised Learning: Regression Analysis **
To quantify the predictive power of crime rates versus other variables, we utilized Multiple Linear Regression.

The Models: We designed five different experimental setups, from simple univariate models (Crime Rate $\rightarrow$ Price) to multivariate models (Crime + Demographics + Location $\rightarrow$ Price).



3) Why have you chosen this data set and these features? How are those related to your initial goal?
   
Regarding crimes we chose the corresponding dataset from statbank. The provided dataset contained how many total crimes
were reported for each quarter for a specific range of years. We chose to keep only data from the past decade, i.e from the first quarter of 2014 to the last quarter of 2024.

Regarding housing prices, the provide dataset was from Kaggle, and it contains danish residential house prices from 1992 to 2024. Again, we chose to keep records from the past decade.

Those datasets were related to our initial goal to investigate individual municipalities of Zealand. The crimes dataset
had already this feature implemented. On the other hand the residential house prices dataset did not, but it did contain
a "city" and a "region" column which we used to categorize the records into municipalites.



4) How do the selected algorithms work?

1. Agglomerative Hierarchical Clustering
This is a bottom-up unsupervised learning algorithm. Unlike K-Means, which requires pre-defined centroids, this method starts by treating every municipality as its own individual cluster. In each step, it merges the two most similar clusters based on Ward’s Linkage, which minimizes the increase in variance within clusters. This creates a hierarchy of natural groupings based on feature proximity rather than rigid shapes.

2. Multiple Linear Regression (MLR)
This supervised learning algorithm models the linear relationship between our target variable ($Y$: Purchase Price) and multiple independent features ($X$: Crime Rate, Location, Year Built). The model calculates a specific "weight" (coefficient) for each feature to create a mathematical equation that best predicts the price. It optimizes these weights by minimizing the sum of squared errors between the predicted and actual values.

3. Spearman’s Rank Correlation
A non-parametric statistical measure used to assess the strength of the relationship between two variables. Unlike standard Pearson correlation, Spearman does not use raw values (which are sensitive to outliers). Instead, it converts data into ranks (e.g., ranking cities from "Highest Crime" to "Lowest Crime") and calculates the correlation between these rankings, making it robust for non-linear distributions like housing data.



5) How have you extracted, stored, and managed the data?

The data lifecycle was managed entirely within a Python (Jupyter Notebook) environment, utilizing the Pandas library for manipulation and storage.

** Housing Dataset **

- Filtering & Cleaning
To ensure the analysis focused on relevant recent trends in the target area, we applied the following filters:

Temporal Filter: We restricted the dataset to transactions occurring between 2014 and 2024.

Geographical Filter: We explicitly filtered for `df["region"] == "Zealand"`.

Dimensionality Reduction: We dropped irrelevant columns, retaining only `date`, `year_build`, `purchase_price`, and `city`. The precise date was converted to a simple integer (year) to facilitate broader trend analysis.

- The "City-to-Municipality" Transformation (Crucial Step)
A major challenge was that real estate data is recorded by Postal City (e.g., "Valby", "Brønshøj"), whereas official crime statistics are recorded by Municipality (e.g., "Copenhagen").

The Problem: A direct merge was impossible because one municipality (e.g., Gentofte) contains multiple cities (Hellerup, Charlottenlund, Klampenborg).

The Solution: We constructed a custom Mapping Dictionary assigning over 100 specific city names to their correct parent municipality.

Example: {"Hellerup": "Gentofte", "Søborg": "Gladsaxe", "Valby": "Copenhagen"}.

Execution: We applied this mapping using the `.map()` function to create a new, standardized municipality column. This effectively "normalized" the location data, allowing it to be joined 1-to-1 with the external Crime and Demographic datasets.



6) Which parameters were used with the algorithms? Have you done any tuning?




