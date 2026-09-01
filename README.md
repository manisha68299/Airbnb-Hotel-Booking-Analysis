# Airbnb Hotel Booking Analysis

This project is a data analysis of an Airbnb hotel booking/listing dataset. The main goal is to understand the listings, prices, room types, neighbourhoods, host information, reviews, and availability.

I worked through the dataset step by step in a Jupyter/Google Colab notebook. The analysis starts with understanding the raw data, then cleaning it, and finally using different charts and statistical methods to find useful patterns.

## Project Overview

The dataset contains information about Airbnb listings such as:

- Listing ID
- Listing name
- Host ID and host name
- Host identity verification status
- Neighbourhood group and neighbourhood
- Latitude and longitude
- Country and country code
- Instant booking availability
- Cancellation policy
- Room type
- Construction year
- Price
- Service fee
- Minimum nights
- Number of reviews
- Last review date
- Reviews per month
- Review rate
- Number of listings managed by the host
- Availability during the year
- House rules and license information

The original dataset contains **102,599 rows and 26 columns**.

After cleaning the data, the final dataset used for analysis contains **83,411 rows and 24 columns**.

## Objective

The purpose of this project is not just to make charts. The idea is to use the available data to answer practical questions about Airbnb listings.

The main questions explored in the notebook are:

1. What are the different property/room types in the dataset?
2. Which neighbourhood group has the highest number of listings?
3. Which neighbourhood group has the highest average listing price?
4. Is there a relationship between the construction year of a property and its price?
5. Who are the top 10 hosts based on calculated host listings count?
6. Are hosts with verified identities more likely to receive positive reviews?
7. Is there a relationship between listing price and service fee?
8. What is the average review rate for listings, and does it change depending on neighbourhood group and room type?
9. Are hosts with more listings more likely to maintain higher availability during the year?

These questions help turn the raw dataset into information that can be understood from a business and customer perspective.

## Tools and Technologies

The project is mainly built using Python.

### Python Libraries

- **NumPy** - used for numerical operations.
- **Pandas** - used for loading, cleaning, transforming, grouping, and analysing the dataset.
- **Matplotlib** - used for creating charts.
- **Seaborn** - used for statistical visualizations such as box plots and regression plots.
- **Plotly Express** - imported for interactive visualizations.

### Environment

The notebook was developed using **Jupyter/Google Colab**.

## Dataset

The notebook loads the CSV file using Pandas:

```python
df = pd.read_csv('/content/Airbnb Hotel Booking Analysis.csv', low_memory=False)
```

The CSV file used by the notebook is named:

`Airbnb Hotel Booking Analysis.csv`

The notebook itself is:

`Airbnb_Hotel_Booking_Analysis.ipynb`

## Project Workflow

The project follows a simple data analysis workflow:

```text
Raw CSV Dataset
       |
       v
Load Dataset
       |
       v
Understand the Data
       |
       v
Check Duplicates and Missing Values
       |
       v
Clean and Transform Data
       |
       v
Exploratory Data Analysis
       |
       v
Create Visualizations
       |
       v
Calculate Correlations and Group Statistics
       |
       v
Find Patterns and Insights
```

## 1. Importing the Libraries

The first step is importing the libraries required for the analysis.

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import plotly.express as px
```

Pandas is the main library used for working with the tabular data, while Matplotlib and Seaborn are used to visualize the results.

## 2. Loading the Dataset

The CSV file is loaded into a Pandas DataFrame called `df`.

```python
df = pd.read_csv(
    '/content/Airbnb Hotel Booking Analysis.csv',
    low_memory=False
)
```

Using `low_memory=False` helps Pandas read the large CSV consistently instead of making mixed-type assumptions in smaller chunks.

## 3. Understanding the Raw Dataset

The notebook first looks at the first few rows using:

```python
df.head()
```

The raw dataset contains **102,599 records and 26 columns**.

The `df.info()` output also shows that the dataset contains:

- 2 integer columns
- 9 float columns
- 15 object/string columns

There are missing values in several columns, especially fields such as `last review`, `reviews per month`, `house_rules`, and `license`.

## 4. Checking Duplicate Records

Duplicate records were checked using:

```python
df.duplicated().value_counts()
```

The initial result showed:

- **102,058 non-duplicate records**
- **541 duplicate records**

The duplicate records were then removed.

```python
df.drop_duplicates(inplace=True)
```

This is important because duplicate listings can affect counts, averages, and other statistical results.

## 5. Checking Data Quality Issues

The notebook also checked for a spelling inconsistency in the neighbourhood group.

The dataset contained:

```text
brookln
```

instead of:

```text
Brooklyn
```

The incorrect value was corrected using:

```python
df.loc[
    df['neighbourhood group'] == 'brookln',
    'neighbourhood group'
] = 'Brooklyn'
```

This prevents the same neighbourhood from being counted as two different groups.

## 6. Removing Columns with Insufficient Data

The `house_rules` and `license` columns were removed because they did not contain enough useful data for this analysis.

```python
df.drop(
    ['house_rules', 'license'],
    axis=1,
    inplace=True,
    errors='ignore'
)
```

The `license` column was especially incomplete, with only 2 non-null values in the original dataset.

## 7. Cleaning Price and Service Fee

The `price` and `service fee` columns were stored as strings containing dollar signs and commas.

For example:

```text
$1,200
```

These symbols were removed before converting the columns to numeric values.

```python
df['price'] = df['price'].str.replace('$', '', regex=False)
df['service fee'] = df['service fee'].str.replace('$', '', regex=False)

df['price'] = df['price'].str.replace(',', '', regex=False)
df['service fee'] = df['service fee'].str.replace(',', '', regex=False)
```

The columns were then renamed:

```python
df.rename(
    columns={
        'price': 'price_$',
        'service fee': 'service_fee_$'
    },
    inplace=True
)
```

Finally, they were converted to floating-point numbers:

```python
df['price_$'] = df['price_$'].astype(float)
df['service_fee_$'] = df['service_fee_$'].astype(float)
```

This makes it possible to calculate averages, correlations, and other numerical statistics.

## 8. Handling Missing Values

After removing the columns with insufficient data, the notebook removes rows containing missing values:

```python
df.dropna(inplace=True)
```

This leaves a complete dataset for the analysis.

After cleaning, the DataFrame contains:

**83,411 rows and 24 columns.**

The cleaned dataset has no remaining duplicate rows.

## 9. Correcting Data Types

Some columns were also converted to more appropriate data types.

```python
df['id'] = df['id'].astype(str)
df['host id'] = df['host id'].astype(str)

df['last review'] = pd.to_datetime(df['last review'])

df['Construction year'] = df['Construction year'].astype(int)
```

This is useful because IDs are identifiers rather than values that should be used for mathematical calculations, and `last review` needs to be treated as a date.

## 10. Removing an Availability Outlier

The notebook removes records where `availability 365` is greater than 500:

```python
df = df.drop(
    df[df['availability 365'] > 500].index
)
```

The purpose is to remove unusually high values from the availability field before analysing the relationship between availability and other variables.

## 11. Descriptive Statistics

The notebook uses:

```python
df.describe()
```

to understand the numerical columns.

Some important values from the cleaned dataset are:

| Metric | Mean | Minimum | Maximum |
|---|---:|---:|---:|
| Price | 626.21 | 50 | 1200 |
| Service Fee | 125.24 | 10 | 240 |
| Minimum Nights | 7.41 | -365 | 5645 |
| Number of Reviews | 32.28 | 1 | 1024 |
| Reviews per Month | 1.38 | 0.01 | 90 |
| Review Rate | 3.28 | 1 | 5 |
| Calculated Host Listings | 7.03 | 1 | 332 |
| Availability 365 | 141.74 | -10 | 426 |

The descriptive statistics are useful for understanding the overall range and distribution of the numerical variables.

## 12. Room Type Analysis

The notebook checks the number of listings for each room type.

```python
property_types = df['room type'].value_counts().to_frame()
```

The result is:

| Room Type | Number of Listings |
|---|---:|
| Entire home/apt | 44,163 |
| Private room | 37,494 |
| Shared room | 1,646 |
| Hotel room | 108 |

### Observation

`Entire home/apt` is the most common room type in the cleaned dataset, followed by `Private room`.

`Hotel room` is the least common category.

A bar chart is also created to make this difference easier to see.

## 13. Neighbourhood Group Analysis

The notebook calculates the number of listings in each neighbourhood group:

```python
hood_group = df['neighbourhood group'].value_counts().to_frame()
```

The result is:

| Neighbourhood Group | Listings |
|---|---:|
| Brooklyn | 34,636 |
| Manhattan | 34,566 |
| Queens | 11,126 |
| Bronx | 2,267 |
| Staten Island | 816 |

### Observation

Brooklyn has the highest number of listings in the cleaned dataset, with **34,636 listings**.

Manhattan is very close behind with **34,566 listings**.

This shows that Brooklyn and Manhattan account for the majority of listings in the dataset.

## 14. Average Price by Neighbourhood Group

The notebook calculates the average listing price for each neighbourhood group:

```python
avg_price = (
    df.groupby('neighbourhood group')['price_$']
      .mean()
      .sort_values(ascending=False)
      .to_frame()
)
```

A bar chart is then created to compare the average price between neighbourhood groups.

The purpose of this analysis is to identify which areas have relatively expensive listings and which areas have relatively lower average prices.

## 15. Construction Year vs Price

The notebook investigates whether the construction year of a property has a relationship with its average price.

```python
df.groupby(
    df['Construction year']
)['price_$'].mean().to_frame().plot()
```

The resulting line plot compares:

- Construction year on the x-axis
- Average price on the y-axis

This is an exploratory analysis. It helps identify whether newer or older properties tend to have different average listing prices.

It should not be interpreted as proof that construction year directly causes a change in price.

## 16. Top 10 Hosts by Listing Count

The notebook identifies the top hosts based on calculated host listings count.

```python
hosts = (
    df.groupby('host name')['calculated host listings count']
      .sum()
      .sort_values(ascending=False)
      .nlargest(10)
      .to_frame()
)
```

A bar chart is used to display the top 10 hosts.

This analysis is useful for understanding whether a small number of hosts manage a large number of listings.

## 17. Host Verification and Review Ratings

The notebook checks whether host identity verification is associated with review ratings.

```python
review = (
    df.groupby('host_identity_verified')
      ['review rate number']
      .mean()
      .sort_values(ascending=False)
      .to_frame()
)
```

The average review rates found in the notebook are:

| Host Verification Status | Average Review Rate |
|---|---:|
| Verified | 3.284 |
| Unconfirmed | 3.273 |

### Observation

Verified hosts have a slightly higher average review rate than unconfirmed hosts.

However, the difference is small. Therefore, the analysis does not show a strong difference in average ratings based only on host verification status.

The notebook also creates:

- A bar chart
- A box plot

to compare the review-rate distributions between verification groups.

## 18. Price vs Service Fee

The notebook checks the correlation between listing price and service fee:

```python
df['price_$'].corr(df['service_fee_$'])
```

The correlation obtained is:

```text
0.9999909074778258
```

This is an extremely strong positive correlation.

In simple terms, listings with higher prices also have higher service fees in this dataset.

A regression plot is created to visualize this relationship:

```python
sns.regplot(
    df,
    x='price_$',
    y='service_fee_$'
)
```

The plot provides a visual confirmation of the very strong relationship between the two variables.

## 19. Review Rate by Neighbourhood and Room Type

The notebook also studies review ratings using both neighbourhood group and room type:

```python
ARRN = (
    df.groupby(
        ['neighbourhood group', 'room type']
    )['review rate number']
    .mean()
    .to_frame()
)
```

The calculated averages are:

| Neighbourhood | Room Type | Average Review Rate |
|---|---|---:|
| Bronx | Entire home/apt | 3.382 |
| Bronx | Private room | 3.306 |
| Bronx | Shared room | 3.356 |
| Brooklyn | Entire home/apt | 3.242 |
| Brooklyn | Hotel room | 3.833 |
| Brooklyn | Private room | 3.275 |
| Brooklyn | Shared room | 3.323 |
| Manhattan | Entire home/apt | 3.269 |
| Manhattan | Hotel room | 3.500 |
| Manhattan | Private room | 3.286 |
| Manhattan | Shared room | 3.262 |
| Queens | Entire home/apt | 3.350 |
| Queens | Hotel room | 3.750 |
| Queens | Private room | 3.311 |
| Queens | Shared room | 3.327 |
| Staten Island | Entire home/apt | 3.333 |
| Staten Island | Private room | 3.497 |
| Staten Island | Shared room | 3.714 |

A grouped bar chart is used to compare the average review rate across neighbourhood groups and room types.

One thing to keep in mind is that some combinations have relatively few listings, especially hotel rooms. Their averages should therefore be interpreted carefully.

## 20. Host Listings vs Availability

The final relationship explored in the notebook is between the number of listings managed by a host and yearly availability.

A regression plot is created using:

```python
sns.regplot(
    df,
    x='calculated host listings count',
    y='availability 365'
)
```

The correlation calculated by the notebook is:

```text
0.1359855273675869
```

This indicates a **weak positive relationship**.

In practical terms, hosts with more calculated listings tend to have somewhat higher availability, but the relationship is not strong.

This means that listing count alone does not explain most of the variation in yearly availability.

## Key Findings

Based on the analysis performed in the notebook:

1. The original dataset contains **102,599 rows and 26 columns**.
2. After cleaning, the analysis uses **83,411 rows and 24 columns**.
3. `Entire home/apt` is the most common room type with **44,163 listings**.
4. `Private room` is the second most common room type with **37,494 listings**.
5. Brooklyn has the highest number of listings with **34,636**.
6. Manhattan is a close second with **34,566 listings**.
7. Verified hosts have a slightly higher average review rate than unconfirmed hosts.
8. Price and service fee have an almost perfect positive correlation of approximately **0.99999**.
9. Host listing count and availability have only a weak positive correlation of approximately **0.136**.
10. Review ratings vary across different combinations of neighbourhood groups and room types.

## Visualizations Used

The notebook uses several types of visualizations:

### Bar Charts

Used for:

- Room type counts
- Neighbourhood group listing counts
- Average price by neighbourhood
- Top 10 hosts
- Average review rate by host verification status

### Line Plot

Used for:

- Average price by construction year

### Box Plot

Used for:

- Review rate distribution by host verification status

### Regression Plot

Used for:

- Price vs service fee
- Host listings count vs availability

### Grouped Bar Chart

Used for:

- Average review rate by neighbourhood group and room type

These visualizations make the numerical analysis easier to understand and help identify patterns that may not be obvious from tables alone.

## Data Cleaning Summary

The main cleaning steps performed in this project were:

```text
Remove duplicate records
        ↓
Remove columns with insufficient data
        ↓
Clean price and service fee values
        ↓
Rename price-related columns
        ↓
Remove rows with missing values
        ↓
Correct data types
        ↓
Correct "brookln" to "Brooklyn"
        ↓
Remove high availability outliers
        ↓
Perform exploratory analysis
```

## Important Note About the Analysis

This project is an exploratory data analysis project.

The correlations and group averages show relationships in the available dataset, but they should not automatically be treated as causal relationships.

For example:

- A higher price does not necessarily mean that the service fee causes the price to increase.
- A verified host having a slightly higher average rating does not prove that verification causes better reviews.
- A weak positive relationship between host listing count and availability does not mean that increasing the number of listings will automatically increase availability.

The results should be viewed as patterns found in this particular dataset.

## Project Structure

A simple version of the project can be organised like this:

```text
Airbnb-Hotel-Booking-Analysis/
│
├── Airbnb_Hotel_Booking_Analysis.ipynb
├── Airbnb Hotel Booking Analysis.csv
└── README.md
```

### File Description

**Airbnb_Hotel_Booking_Analysis.ipynb**

The main Jupyter/Google Colab notebook containing:

- Data loading
- Data inspection
- Data cleaning
- Exploratory data analysis
- Statistical calculations
- Visualizations
- Findings

**Airbnb Hotel Booking Analysis.csv**

The dataset used for the analysis.

**README.md**

This document explains the project, methodology, analysis, and findings.

## How to Run the Project

### Option 1: Google Colab

1. Open Google Colab.
2. Upload `Airbnb_Hotel_Booking_Analysis.ipynb`.
3. Upload the CSV file:
   `Airbnb Hotel Booking Analysis.csv`
4. Make sure the CSV path matches the path used in the notebook.
5. Run the notebook cells from top to bottom.

### Option 2: Jupyter Notebook

Install the required libraries:

```bash
pip install numpy pandas matplotlib seaborn plotly
```

Then open the notebook:

```bash
jupyter notebook Airbnb_Hotel_Booking_Analysis.ipynb
```

Make sure the CSV file is available at the location expected by the notebook.

## Skills Demonstrated

This project demonstrates practical experience with:

- Python
- Pandas
- NumPy
- Data cleaning
- Missing-value handling
- Duplicate removal
- Data type conversion
- Date conversion
- GroupBy operations
- Aggregation
- Descriptive statistics
- Correlation analysis
- Exploratory Data Analysis (EDA)
- Data visualization
- Matplotlib
- Seaborn
- Basic statistical interpretation
- Business-oriented data analysis

## What I Learned From This Project

Working on this project helped me understand that data analysis is not only about creating graphs.

A large part of the work happens before visualization:

1. First understand the dataset.
2. Check its structure and data types.
3. Find duplicate and missing records.
4. Fix inconsistent values.
5. Convert columns into useful formats.
6. Remove data that cannot support the analysis.
7. Then start asking questions.
8. Use grouping, aggregation, visualization, and correlation to answer those questions.
9. Finally, interpret the results carefully.

This workflow is important because incorrect or messy data can produce misleading results even when the code itself is correct.

## Conclusion

The Airbnb Hotel Booking Analysis project uses Python and common data analysis libraries to explore Airbnb listing data.

The analysis covers listing distribution, neighbourhoods, pricing, property types, host behaviour, reviews, and availability. The cleaning process reduced the dataset from 102,599 records with 26 columns to 83,411 usable records with 24 columns.

The analysis shows that entire homes/apartments and private rooms make up most of the listings, Brooklyn and Manhattan contain most of the listings, price and service fee move very closely together, and host listing count has only a weak relationship with yearly availability.

Overall, this project is a practical example of taking a raw dataset, cleaning it, asking business-related questions, analysing the data, and communicating the results through visualizations and simple statistical measures.
