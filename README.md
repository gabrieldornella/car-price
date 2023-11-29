# car-price

In this application, I explored a dataset from kaggle. The original dataset contained information on 3 million used cars. The provided dataset contains information on 426K cars to ensure speed of processing. My goal was to understand what factors make a car more or less expensive.
The end goal of my analysis was to provide clear recommendations to a client -- a used car dealership -- as to what consumers value in a used car.
Based on various attributes of the cars that were sold, is it possible to predict the selling price of a car?

### Dataset Overview
The dataset had 426k rows and the following columns (features):
- id: A unique identifier for each listing.
- region: The geographical region of the listing.
- price: The listed price of the vehicle. (y feature - dependent variable - value to be predicted)
- year: The manufacturing year of the vehicle.
- manufacturer: The manufacturer of the vehicle.
- model: The model of the vehicle.
- condition: The condition of the vehicle.
- cylinders: Information about the vehicle's engine cylinders.
- fuel: The type of fuel the vehicle uses.
- odometer: The vehicle's odometer reading.
- title_status: The status of the vehicle's title.
- transmission: The type of transmission in the vehicle.
- VIN: The Vehicle Identification Number.
- drive: The drive type of the vehicle (e.g., front-wheel, rear-wheel).
- size: The size of the vehicle.
- type: The type of vehicle (e.g., SUV, sedan).
- paint_color: The color of the vehicle.
- state: The state where the vehicle is listed.

### Initial inference
Based on experiences I have had in the past buying and selling cars, there are many features in this dataset that can possibly be good predictors for what the car was sold for. The car model, year of manufacture, condition, transmission, odometer, etc., are characteristics that, in my experience, influence the selling price of the car. 

### Data Quality Issues

When analyzing the data, I noted that the Dataset has serious data quality issues.

#### Issue 1
There are many features that have a high percentage of missing values.
% of missing values by feature (descending order):
- size           71.767
- cylinders      41.622
- condition      40.785
- VIN            37.725
- drive          30.586
- paint_color    30.501
- type           21.753
- manufacturer    4.134
- title_status    1.931
- model           1.236
- odometer        1.031
- fuel            0.706
- transmission    0.599
- year            0.282
region, id, price and state do not have missing values.

#### Issue 2
Some features have a very large number of unique values, which makes the process of creating Regression models more complex and less effective as there is high cardinality in the data.
'Models', for example, have more than 29,649 unique values in the Dataset. However, apparently, the same car model appears several times with different names. Among the 10 best-selling models, there are three lines that appear to be the same car model with different names: 'silverado 1500', '1500', 'silverado'.

#### Issue 3
There are many inconsistencies in the data. The 60 most expensive cars in the Dataset have values ranging from 3,736,928,711 to 1,000,000 with uncommon values in this range such as 1,111,111 or 1,234,567. These values seem inconsistent to me when analyzed in isolation, when analyzed together with other information such as the model of the car model, year of manufacture or overall car condition, the values seem even more inconsistent. The same happens with the cheapest cars, there are more than 35k sales records where the value of the car was less than $100. Both situations seem to be outliers or data entry errors.

All the three issues make predicting the sales price of a car more difficult as there are clearly inconsistencies in the data.

### Data Understanding

#### Price analysis
As price is the variable I want to predict, I started my analysis with it. In my analysis there is a chart with a set of three boxplots, each showing the distribution of vehicle sales prices from the dataset with different considerations for outliers:
- First Boxplot (Price > 0 with outliers): This plot shows the distribution of all vehicle prices greater than zero, including outliers. The plot appears to have several extreme outliers that are far removed from the rest of the data points, which significantly stretch the scale of the x-axis. These outliers make it difficult to observe the distribution of the majority of the data.
- Second Boxplot (Price > 0 and < 999999 with outliers): In this plot, the vehicle prices are limited to those greater than zero and less than 999,999, still including outliers. The scale of the x-axis is reduced, which provides a clearer view of the distribution compared to the first plot. However, there are still noticeable outliers present that are well above the upper quartile.
- Third Boxplot (Price > 0 without outliers): This plot restricts the data to prices greater than zero and excludes outliers. Without the extreme values, the scale of the x-axis is further condensed, allowing a much clearer view of the 'typical' price distribution.

The progression from the first to the third boxplot demonstrates the effect of removing outliers on the visualization of data. By excluding extreme values, the third boxplot offers a more informative view of where most of the data points lie, which is more useful for understanding the typical sales price of the vehicles.

After removing the inconsistencies and the outliers, the main statistics of 'price' was:
- mean     19560.349
- std      15332.461
- min       1000.000
- 25%       7990.000
- 50%      15990.000
- 75%      27990.000
- max     566567.000

Price distribution is right skewed, so in the Regression models I decided to use a transformation using log.

##### Price correlation with other numerical features
There is no strong statistical correlation between price and odometer or year of manufacture.

#### Average price by each categorical feature
For each categorical feature, I created charts comparing the average price and frequency of listings against the overall average price and overall frequency.

##### Average price by region and state
Certain regions or states have more expensive vehicles on average, which could be due to a variety of factors such as local economic conditions, the prevalence of certain types of vehicles, or regional preferences. A region os state with a high average price but low frequency of listings (Utah for instance) might suggest a niche market with luxury or specialized vehicles. In contrast, a region with a high frequency of listings but lower average prices (Ohio for instance) might indicate a more competitive market with a higher turnover of vehicles, potentially with more economical options available.

But what caught the most attention in the boxplot graph of prices by state and was repeated in all other analyses, is the number of Outliers in all states (and also in the other features). Clearly, this dataset has a clear outlier problem that is difficult to remove as it is repeated in all features.

##### Average price by year
The most recent years show a significant increase in average price, which could be due to a number of factors such as newer cars having more advanced technology, being in better condition, or simply inflation. Older cars also have a significant increase in average price. This pattern where both very new and very old vehicles have higher average prices could be due to classic or vintage cars that are often valued higher due to their rarity, collectability, or classic status.
It's also worth noting that the boxplots with outliers show extreme prices for certain years, which could represent special edition cars, luxury models, or errors in the data. The boxplots without outliers present a clearer picture of the central tendency and spread of prices for each year, which shows that while there is an increase in prices for newer cars, some older cars also maintain high prices.

##### Average price by transmission
Vehicles with an 'other' type of transmission have the highest average price, followed by 'automatic' and then 'manual'. The red line indicates the overall average price across all transmission types. Both 'other' and 'automatic' are above this average, while 'manual' is below it.
This might suggest that vehicles with 'other' transmissions include categories that are typically more expensive (such as vehicles with CVT, or electric vehicle transmissions), or that 'automatic' vehicles are generally priced higher than 'manual' ones, which could be due to consumer preferences or the prevalence of automatic transmissions in higher-end vehicles.

##### Average price by fuel
vehicles with 'diesel' fuel type have the highest average price, followed by 'other', 'gas', 'hybrid', and 'electric'. Diesel vehicles being priced highest on average might indicate that they are either larger vehicles (such as trucks or SUVs), more likely to be used for commercial purposes, or simply carry a premium over other fuel types. The 'gas' fuel type has the highest frequency of listings, significantly more than other types. The dominance of 'gas' fuel type in listings suggests that it is the most common fuel type available or preferred in the market. 'Electric' and 'hybrid' vehicles have a smaller range of prices, but with outliers indicating there are some high-priced models within these categories as well.
The charts suggest that fuel type is a significant factor in vehicle pricing, with 'diesel' vehicles having the highest average prices and 'gas' vehicles being the most common in the market.

##### Average price by title_status
Vehicles with a 'lien' title status have the highest average price, followed by those with a 'clean' title. The 'rebuilt', 'salvage', 'missing', and 'parts only' title statuses have progressively lower average prices. When outliers are removed, the 'clean' title status has the highest median price, and the boxplots show less variability, making the price differences between the title statuses more apparent.
Ttitle status is an important factor in vehicle valuation. Vehicles with 'clean' and 'lien' titles tend to be priced higher, likely due to being in better condition or free of legal or financial encumbrances. Vehicles with 'rebuilt' or 'salvage' titles are priced lower, as they may have had significant damage or may require more investment to become roadworthy. Those with 'missing' or 'parts only' titles are the lowest priced, which suggests they are likely being sold for parts rather than as functioning vehicles.

##### Average price by manufacturer
Manufacturer brand is a significant determinant of vehicle pricing, with luxury or premium brands having higher average prices and a wider range of prices due to high-end vehicle offerings. Mass-market manufacturers tend to have lower average prices and a narrower price range. The presence of outliers indicates that within each brand, there can be vehicles that are significantly more expensive than the average, potentially due to features, model variants, or limited editions.

##### Average price by condition
Cars in 'new' condition have the highest average prices, which is to be expected since they would typically be the latest models with little to no wear. Vehicles in 'like new' and 'excellent' condition follow in average price, suggesting that they are well-maintained and likely newer models. Cars in 'good', 'fair', and particularly 'salvage' condition have progressively lower average prices. This likely reflects the reduced value due to wear, potential issues, or the need for significant repairs. The 'good' condition has the highest frequency of listings, followed by 'excellent' and 'like new', suggesting these are the most common conditions in which cars are sold. 'New' and 'salvage' conditions are less common.
The condition of a car is a strong indicator of its price, with better conditions commanding higher prices. The significant number of outliers in all conditions except 'salvage' suggests that within each condition category, there is a range of vehicle types and qualities affecting price. The less frequent 'new' condition and the more common 'good' to 'excellent' conditions reflect typical market availability, with 'salvage' condition vehicles being relatively rare and valued much lower

#### Average price by each categorical feature - Summary
Vehicle prices are influenced by regional economics, vehicle age, transmission, fuel type, title status, brand, and condition. Newer and classic cars, as well as those with automatic or special transmissions, diesel fuel, clean or lien titles, from luxury brands, or in better condition, tend to fetch higher prices. Mass-market brands and vehicles with salvage titles or in poorer condition are generally cheaper. A significant number of outliers across all categories indicates potential extremes in vehicle features or prices that may warrant further investigation.

### Data Preparation

After doing the exploratory analysis, I performed a few steps before creating the regression models.
1. Drop Unnecessary Columns: I dropped the  columns 'id' and 'VIN' from the dataframe data because they are unique identifiers that do not provide any predictive power for the modeling process.
2. Drop Columns with Missing Values: I then further dropped columns 'size', 'cylinders', 'drive', 'paint_color', and 'type'. These columns were dropped because they have a large number of missing values, which may be challenging to impute or could potentially introduce bias if not handled correctly.
3. Remove Unreasonable Price Rows: I removed rows where the price is less than $1,000 or equal to or above $600,000. This step was taken to remove outliers or incorrect entries that could skew the analysis or model training.
4. Remove Extreme Odometer Values: Next, I eliminated rows where the odometer reading is equal to or exceeds 999,999, which are considered either data entry errors or true outliers that could diminish the model's accuracy.
5. Drop Rows with Missing Categorical Data: Finally, I dropped any rows that have missing values in the 'state', 'manufacturer', 'model', 'fuel', 'title_status', 'transmission', or 'year' columns. The rationale behind this decision is that these columns contain categorical data, and instead of imputing values (which might be subjective or unreliable), it's cleaner to remove these incomplete records altogether.

### Modeling

Car 'model' is very important in predicting car's price but it's a high-cardinality categorical features (it has large number of unique values). So I created two functions to streamline the 'model' feature, thereby making the dataset more manageable for modeling, and to improve the performance of predictive models that might otherwise struggle with a feature containing too many unique categories.

Function: model_reducer_fit
The model_reducer_fit function aims to identify the most frequent vehicle models in the train dataset, allowing for a focus on models that represent a significant portion of the data.

Function: model_reducer_transform
The model_reducer_transform function is used to apply the transformation to a new or the same dataset based on the list of top models identified by model_reducer_fit.

I then created the pipeline with a comprehensive approach to preprocessing data, dealing with missing values, normalizing numerical features, encoding categorical variables, and preparing the target variable for regression analysis.

I splited dataset into train and test and I then executed the 'same' pipeline with three different models: LinearRegression, Lasso and Ridge.
LinearRegression and Ridge were the best performing models with both achieving a R² of 0.43. Lasso achieved a R² of 0.11.

### Conclusion

In this project, the aim was to analyze a dataset from Kaggle containing information on used car listings to determine factors influencing car prices and assist a used car dealership in understanding consumer values. The dataset comprised over 400,000 entries with various attributes such as region, year, make, and condition of the car.

The data presented several quality issues, including a high percentage of missing values for several features, a vast number of unique model names indicating high cardinality, and numerous outliers that suggested data entry errors or genuine extreme values. Notably, the 'model' feature was especially challenging due to its many unique values and repeated 'models' with different names.

To prepare the data for modeling, unnecessary columns like 'id' and 'VIN' were dropped, columns with many missing values were removed, and entries with implausible prices or odometer readings were excluded. Rows with missing categorical data were also dropped to avoid imputation, which could introduce bias.

To address the issue of high cardinality in the 'model' feature, I created functions to retain the names of the most frequent models and label the rest as 'other'. This reduced complexity and improved the predictive model's performance.

I then established a pipeline to preprocess the data, which included normalization, one-hot encoding for categorical variables, and a logarithmic transformation for the target variable (price). This pipeline was applied to three different regression models: Linear Regression, Lasso, and Ridge. Both Linear Regression and Ridge performed comparably well, with an R² of 0.43, indicating that these models could explain about 43% of the variance in car prices. Lasso underperformed with an R² of 0.11.

In conclusion, the analysis showed that car prices are influenced by various factors, including the car's condition, manufacturer, and specifics such as model and year. The presence of outliers and data quality issues highlighted the importance of thorough data cleaning and preparation for accurate predictive modeling. The findings from this project could guide the dealership in pricing used cars and understanding market trends.
