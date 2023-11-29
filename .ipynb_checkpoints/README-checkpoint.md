# car-price

In this application, I explored a dataset from kaggle. The original dataset contained information on 3 million used cars. The provided dataset contains information on 426K cars to ensure speed of processing. My goal was to understand what factors make a car more or less expensive. As a result of my analysis provide clear recommendations to a client -- a used car dealership -- as to what consumers value in a used car.

Based on various attributes of the cars that were sold, is it possible to predict the selling price of a car?

Datraframe has the following columns
- id: A unique identifier for each listing.
- region: The geographical region of the listing.
- price: The listed price of the vehicle. (y feature)
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

Based on experiences I have had in the past buying and selling cars, there are many features in this dataset that can possibly be good predictors for what the car was sold for. The car model, year of manufacture, condition, transmission, odometer, etc., are characteristics that, in my experience, influence the selling price of the car. But  when analyzing the data, it is noticeable that the Dataset has serious data quality issues.

### Data Understanding
#### Issue 1
There are many features that have a high percentage of missing values. % of mising values by feature (descending order):
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

For features with a high percentage of missing values (size, cylinders, drive, paint_color and type), I din't find a reliable strategy to impute missing values so I decided to drop them.
For 'condition' feature I used the 'constant' imputation technique with fill_value='unknown' and for 'year' feature I used the 'mean' to fill the missing values.

#### Issue 2
The second problem is that the number of unique values for some features is very high, which makes creating the Regression model more complex and less effective as there is high cardinality in the data. For example, there are 29,649 different car models in the Dataset.

The third problem is that there are many inconsistencies in the data. The 60 most expensive cars in the Dataset have values ranging from 3,736,928,711 to 1,000,000 with strange values in this range such as 1,111,111 or 1,234,567. These values seem inconsistent to me when analyzed in isolation, when analyzed together with other information such as the car model, year of manufacture or condition, the values seem even more inconsistent. The same happens with cheaper cars, there are more than 35k sales records where the value of the car was less than $100. Both situations seem to be outliers or data entry errors. It makes predicting the sales price of a car more difficult as there are clearly inconsistencies in data entry.

In my analysis there is a chart with a set of three boxplots, each showing the distribution of vehicle sales prices from the dataset with different considerations for outliers:
- First Boxplot (Price > 0 with outliers): This plot shows the distribution of all vehicle prices greater than zero, including outliers. The plot appears to have several extreme outliers that are far removed from the rest of the data points, which significantly stretch the scale of the x-axis. These outliers make it difficult to observe the distribution of the majority of the data.
- Second Boxplot (Price > 0 and < 999999 with outliers): In this plot, the vehicle prices are limited to those greater than zero and less than 999,999, still including outliers. The scale of the x-axis is reduced, which provides a clearer view of the distribution compared to the first plot. However, there are still noticeable outliers present that are well above the upper quartile.
- Third Boxplot (Price > 0 without outliers): This plot restricts the data to prices greater than zero and excludes outliers. Without the extreme values, the scale of the x-axis is further condensed, allowing a much clearer view of the 'typical' price distribution.

The progression from the first to the third boxplot demonstrates the effect of removing outliers on the visualization of data. By excluding extreme values, the third boxplot offers a more informative view of where most of the data points lie, which is more useful for understanding the typical sales price of the vehicles.