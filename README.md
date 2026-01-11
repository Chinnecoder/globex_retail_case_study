## - BUSINESS INTRODUCTION
Globex Retail, a dynamic leader in the retail industry,
seamlessly integrates the convenience of
e-commerce with the traditional appeal of
brick-and-mortar stores. As the company continues
to expand its reach, it recognises the critical role that
data engineering plays in driving success. By
harnessing the power of advanced data engineering
techniques, Globex Retail is committed to
transforming raw data into actionable insights,
optimising inventory management, and ultimately
boosting revenue. This data-driven approach
positions Globex Retail at the forefront of innovation,
ensuring that they not only meet but exceed the
evolving demands of their diverse customer base.
## - PROBLEM STATEMENT
As a new Data Engineer at Globex Retail, a
company specialising in e-commerce and
brick-and-mortar sales, your first task involves
analysing a dataset to understand customer
purchase behavior. The goal is to identify
patterns in customer purchases that could
lead to targeted marketing strategies and
improved inventory management.
## - RATIONALE FOR THE PROJECT
#### Globex Retail seeks to:

-  Enhance customer retention.
-  Optimise inventory levels to meet customer demand. 
-  Increase revenue through targeted marketing. 
-  Make data-driven decisions that improve overall business performance.
## - DATA DESCRIPTION
The dataset includes information on customer purchases, such as:
1. Customer ID: Unique identifier for customers.
2. Order ID: Unique identifier for each order.
3. Order Date: Date the order was placed.
4. Product Category: Main category of the product.
5. Product Sub-Category: More specific category of the product.
6. Quantity: Number of units ordered.
7. Price: Price per unit of the product.
8. Discount: Discount applied to the product.
9. Customer Location: Location (state code) of the customer.
## - TECH STACK
#### To process and analyse the dataset, the following tools were utilised:

- Python: The core programming language for data manipulation.
- Pandas: Used for high-performance data structures and analysis.
- Jupyter: The interactive development environment for coding and visualisation.

## - PROJECT WORKFLOW

#### I followed a rigorous data engineering workflow to ensure the integrity and utility of the insights:

- Data Familiarisation: Exploring the schema and understanding data relationships.

- Data Cleaning: Scrubbing the dataset to handle missing or inconsistent values.

- Feature Engineering: Creating new metrics to drive deeper analysis.

- Exploratory Data Analysis (EDA): Visualising distributions and identifying initial patterns.

- Insight Generation: Using Pandas to extract specific answers for stakeholders.


### Importing Necessary Libraries
!pip install pandas
!pip install numpy
import pandas as pd
import numpy as np
### 1. Loading Dataset (Data Extraction)
globex_df = pd.read_csv(r'C:\Users\okafo\Desktop\Data_Engineering_10Alytics\SECTION6-AccessingDBwithPythonSQLGitHub\Capstone_Project_Pandas\globex_retail_data.csv')
# Display first 5 rows to understand the structure
globex_df.head()
# Display Columns
globex_df.columns
### 2. Inspecting the Dataset (Data Quality Checks and Assessment)
# Check for basic info and data types
globex_df.info()
# Check the dimension(number of rows and columns) of the dataset
globex_df.shape
# Check for missing values in each column
globex_df.isnull().sum()
# Check for duplicate entries
globex_df.duplicated()
# Total duplicate entries
duplicates = globex_df.duplicated().sum()
print(f"Number of duplicate rows = {duplicates}")
duplicates
# Check for inconsistent data entries
globex_df['Product_Category'].value_counts()
globex_df['Product_Sub_Category'].value_counts()
globex_df['Customer_Location'].value_counts()
### 3. Data Cleaning and Transformation
# Create a copy of original dataframe to clean
gclean_df = globex_df.copy()
# Convert Order_Date from string to datetime objects to enable time_series analysis 
gclean_df['Order_Date'] = pd.to_datetime(gclean_df['Order_Date'])
# Standardise text data to ensure no white spaces
gclean_df['Product_Category'] = gclean_df['Product_Category'].str.strip()
gclean_df['Product_Sub_Category'] = gclean_df['Product_Sub_Category'].str.strip()
gclean_df['Customer_Location'] = gclean_df['Customer_Location'].str.upper().str.strip()

# Sanity check on numerical columns to ensure quantity and price are positive to remove potential errors like negative or zero-unit orders
gclean_df = gclean_df[(gclean_df['Quantity'] > 0) & (gclean_df['Price'] >=0)]
print(f"Data Cleaning Completed.")

### 4. Feature Engineering
gclean_df.head()
# Create a Month column from Order_Date for trend analysis
gclean_df['Order_Date'].dt.to_period('M')
# Extract the name of day and month, and year from Order_Date
gclean_df['Order_Day'] = gclean_df['Order_Date'].dt.day_name()
gclean_df['Order_Month'] = gclean_df['Order_Date'].dt.month_name()
gclean_df['Order_Year'] = gclean_df['Order_Date'].dt.year
gclean_df['Order_Month_Period'] = gclean_df['Order_Date'].dt.to_period('M')
gclean_df.head()
# Calculate total revenue per customer to create segments
Customer_Revenue = gclean_df.groupby('Customer_ID')['Revenue'].sum().reset_index()
Customer_Revenue
# Divide the customers based on their spending power as Diamond(Top 10%), Gold(Next 45%), Silver(Bottom 45%)
Customer_Revenue['Segment'] = pd.qcut(Customer_Revenue['Revenue'],
                                      q = [0, 0.45, 0.9, 1.0],
                                      labels = ['Silver', 'Gold', 'Diamond']
                                      )
# Merge Segments back into the main dataframe
gclean_df = gclean_df.merge(Customer_Revenue[['Customer_ID', 'Segment']], on = 'Customer_ID')
gclean_df.head()
gclean_df.tail()
# Saving the cleaned dataframe
gclean_df.to_csv(r'C:\Users\okafo\Desktop\Data_Engineering_10Alytics\SECTION6-AccessingDBwithPythonSQLGitHub\Capstone_Project_Pandas\globex_cleaned_data.csv', index = False)
print(f"Cleaned data saved as 'globex_cleaned_data.csv'")
### 5. Exploratory Data Analysis
Exploratory Data Analysis (EDA) is a critical first step in the data analysis process where you investigate and summarize the main characteristics of a dataset. It's essentially about getting to know your data before applying more complex techniques.

#### Key Components of EDA:

1. **Understanding the structure of the data**
   - Examining data types
   - Checking dimensions (rows and columns)
   - Identifying missing values

2. **Descriptive statistics**
   - Measures of central tendency (mean, median, mode)
   - Measures of dispersion (standard deviation, range)
   - Percentiles and quartiles

3. **Data visualization**
   - Histograms to understand distributions
   - Box plots to identify outliers
   - Scatter plots to explore relationships between variables
   - Correlation matrices to quantify relationships
   - Time series plots for temporal data

4. **Pattern discovery**
   - Identifying trends
   - Detecting anomalies or outliers
   - Discovering relationships between variables

5. **Hypothesis generation**
   - Forming initial hypotheses based on observations
   - Setting up questions for deeper analysis

EDA helps you make informed decisions about data preprocessing, feature engineering, and which modeling techniques might be most appropriate for your specific dataset and problem.

# Importing additional libraries
import matplotlib.pyplot as plt
import seaborn as sns
# 1. Descriptive Statistics
print(gclean_df.describe())
#### The revenue per order varies significantly (from 6.164400  to 13819.840000) US Dollars, indicating a diverse range of low-cost and high-ticket items.
print(gclean_df.describe(include='object'))
# 2A. Univariate Analysis for Numerical Variables
# Histogram for Distribution of Revenue
plt.figure(figsize=(18, 10))
sns.histplot(gclean_df['Revenue'], bins=50, kde=True, color='orange')
plt.title('Globex Retail Distribution of Revenue')
plt.savefig('globex_revenue_distribution.png')
#### The data is heavily right_skewed, with most orders being under $1000. However, the presence of high-revenue outliers highlights the importance of "Diamond" customer segment
# Pie Chart - Proportion of Orders by Segment
plt.figure(figsize=(14, 8))
gclean_df['Segment'].value_counts().plot(kind='pie', autopct='%1.1f%%', colors=['#ff9999', '#66b3ff', '#99ff99'])
plt.title('Proportion of Orders by Customer Segment (Globex Retail)')
plt.savefig('globex_segment_pie.png')
#### While Diamond customers contibute the most revenue, they represent only 13.2% of total orders.
# 2B. Univariate Analysis for Categorical Variables
# Box Plot for Revenue Distribution by Product_Category
plt.figure(figsize=(18, 12))
sns.boxplot(x='Product_Category', y='Revenue', data=gclean_df)
plt.title('Globex Retail Revenue Distribution by Product_Category')
plt.xticks(rotation=45)
plt.savefig('globex_revenue_box_plot.png')
#### Electronics shows the widest range of revenue and the most high_value outliers, reinforcing its position as the top revenue-generating category
 # 2C. Bar Chart for Revenue Distribution by day-of-the-week
plt.figure(figsize=(10, 4))
sns.barplot(x='Order_Day', y='Revenue', data=gclean_df)
plt.title('Globex_Retail Revenue Distribution by Day_of_the_Week')
plt.xticks(rotation=60)
plt.savefig('globex_revenue_barchart.png')

#### Monday and Thursday show high revenue peaks, wich may suggest corporate or routine shopping at the start of the week and wrap up towards the weekend is a major business driver.
# 3. Correlation Analysis
# Correlation Matrix Heatmap
plt.figure(figsize=(8, 4))
correlation_matrix = gclean_df[['Quantity', 'Price', 'Discount', 'Revenue']].corr()
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm',linewidths=0.5)
plt.title('Globex Retail Correlation Heatmap')
plt.savefig('globex_correlation_heatmap.png')
#### Price and Revenue have a strong correlation, showing that the unit price of items is the primary driver of revenue rather than quantity.
#### Discount and Revenue have a slight negative correlation, confirming that while discounts may increase sales volume, they do not necessarily increase the dollar value per order.

### 6. Generating Insights with Pandas
- 1. Which product category has generated the most revenue?
- 2. Which product subcategories generate the most revenue?
- 3. Which customer segment has the highest average order value?
- 4. What is the average discount applied to high-value customers?
- 5. Which geographic locations have the highest concentration of high-value customers?
- 6. How does the discount rate impact the total sales across different product categories?
- 7. Identify trends in purchase behavior over time (e.g., monthly).
# 1. Which product category has generated the most revenue?

Category_Revenue = gclean_df.groupby('Product_Category')['Revenue'].sum().sort_values(ascending=False)
print(f"Top Revenue Category: {Category_Revenue.idxmax()} (${Category_Revenue.max():,.2f})")
# 2. Which product subcategories generate the most revenue

Subcategory_Revenue = gclean_df.groupby('Product_Sub_Category')['Revenue'].sum().sort_values(ascending=False)
print(f"Top Revenue Sub_Category: {Subcategory_Revenue.idxmax()} (${Subcategory_Revenue.max():,.2f})")

# 3. Which customer segment has the highest average order value

# Avg_Order_Val = Total Revenue / Number of Unique Orders
Segment_Status = gclean_df.groupby('Segment').agg(
    Total_Revenue=('Revenue', 'sum'),
    Unique_Orders=('Order_ID', 'nunique') 
)
Segment_Status['Avg_Order_Val'] = Segment_Status['Total_Revenue']/ Segment_Status['Unique_Orders']

print(f"\nAverage Order Value by Segment:")
print(Segment_Status['Avg_Order_Val'].sort_values(ascending=False))
observed=False
# 4.What is the average discount applied to high-value customers?

Avg_discount_high = gclean_df[gclean_df['Segment'] == 'Diamond']['Discount'].mean()
print(f"\nAverage Discount for Diamond Customers: {Avg_discount_high:.2%}")
# 5. Which geographic locations have the highest concentration of high-value customers?

High_value_locations = gclean_df[gclean_df['Segment'] == 'Diamond'].groupby('Customer_Location')['Customer_ID'].nunique().sort_values(ascending=False)
print("\nTop 5 Locations for High-Value(Diamond) Customers:")
print(High_value_locations.head(5))
# 6. How does the discount rate impact the total sales across different product categories?

# using correlation to quantify the relationship between Discount and Revenue per category
discount_impact = gclean_df.groupby ('Product_Category')[['Discount', 'Revenue']].corr().iloc[0::2, 1]
print("\nCorrelation between Discount and Revenue by Product_Category:")
print(discount_impact)
# 7. Identify trends in purchase behavior over time (e.g., monthly)

gclean_df['Month'] = gclean_df['Order_Date'].dt.to_period('M')
monthly_trends = gclean_df.groupby('Month')['Revenue'].sum()
print(f"\nMonthly Revenue Trends:")
print(monthly_trends)
# Visualisation for Q7

monthly_revenue = gclean_df.groupby('Month')['Revenue'].sum()

plt.figure(figsize=(20, 10))
monthly_revenue.index = monthly_revenue.index.astype(str) # Convert for plotting
plt.plot(monthly_revenue.index, monthly_revenue.values, marker='o', color='red', linewidth=2)

plt.title('Globex Retail Monthly Revenue Trends', fontsize=14)
plt.xlabel('Month', fontsize=12)
plt.ylabel('Total Revenue ($)', fontsize=12)
plt.grid(True, linestyle='--', alpha=0.7)
plt.tight_layout()
plt.savefig('globex_retail_monthly_revenue_trend.png')
#### Key Observations include:
#### The highest revenue was recorded in July 2023 (approx. 200,000), followed by a strong performance in 2024 (approx. 185,000) and February 2024 (approx. $183,000).
#### A significant deep occurred in August 2023 (approx. 102,000), which was the lowest in the two-year period.
#### However, there is a bit of consistency as the revenue tends to oscillate between 130,000 and 180,000 for most months, indicating a relatively stable but seasonal business for Globex Retail.

## - RECOMMENDATION

#### Based on the comprehensive analysis and Exploratory Data Analysis (EDA) of the Globex Retail dataset, here are the strategic recommendations to improve business performance:

### 1. High-Value Customer Retention
The analysis showed that the high-value segment (Diamond customers) has an Average Order Value of $2625.187194, which is nearly 4x that of the medium segment (Gold customers).

### - Recommendation: 
- Implement a VIP Loyalty Program specifically for this top 10% of customers. Since their average discount is currently only 5.53%, offering exclusive "early access" to new electronics or personalised concierge services could increase retention without further eroding margins through deep discounts.

### 2. Optimise Electronics Inventory
Electronics is the flagship category, driving the most revenue ($2.07M) and containing the highest-value sub-categories like Smart Watches and Smartphones.

### - Recommendation: 
- Ensure stock levels for the top 5 sub-categories are prioritised, especially during peak months like July and January. Use the Electronics category as a "lead magnet" to cross-sell lower-performing categories like Beauty or Books.

### 3. Review Discounting Strategy
The Exploratory Data Analysis (EDA) revealed a negative correlation (-0.084) between discounts and revenue. This suggests that high discounts are not significantly driving higher volume; they are simply reducing the profit on sales that likely would have happened anyway.

### - Recommendation: 
- Shift from broad-based discounting to targeted, behaviour-based offers. Instead of flat discounts, try "Bundle Deals" (e.g., buy a Smartphone and get a discount on Headphones). This protects the AOV while still providing value to the customer.

### 4. Geographic Expansion in Top States
Colorado (CO), New Jersey (NJ), and Louisiana (LA) were identified as having the highest concentration of high-value shoppers.

### - Recommendation: 
- Focus digital marketing spend and localised social media campaigns in these specific states. If Globex Retail plans to open more brick-and-mortar stores, these locations should be at the top of the list due to the established high-spending customer base.

### 5. Capitalise on Early-Week Shopping Trends
The data showed that Mondays and Thursday are high-revenue days, contrary to the common assumption that weekends drive retail.

### - Recommendation: 
- Launch "Monday Morning Flash Sales" or "Weekly Refresh" email campaigns on Sunday evenings. Since customers are already active at the start of the week, capturing their attention early can secure the week's revenue goals before competition heats up on the weekend.

### 6. Improve Mid-Value Migration
A large portion of the customer base falls into the "medium-value" segment (Silver customers) with an AOV of around $500.

### - Recommendation: 
- Use upselling techniques to move medium-value customers into the high-value tier. For example, offering free shipping on orders over $1,000 or a "Buy Now, Pay Later" (BNPL) option could encourage these customers to increase their basket size.

## - CONCLUSION

The data engineering and analysis project for Globex Retail successfully transformed raw transaction data into a strategic roadmap for business growth. By leveraging Python and Pandas, I uncovered critical patterns in customer behaviour and product performance that were previously hidden.

#### - Key Project Takeaways:
- Revenue Engines: The Electronics category, specifically Smart Watches and Smartphones, is the cornerstone of Globex’s revenue. These high-ticket items drive the business and should be the focus of inventory and promotional priority.

- The Power of Segmentation: We identified that 10% of the customer base (Diamond segment) contributes a disproportionately high Average Order Value ($2,625.19). Recognising and rewarding this group is the most efficient path to increasing profitability.

- Discounting Realities: The analysis revealed that broad discounting is not a primary driver of revenue growth. In fact, a slight negative correlation suggests that the current discount strategy may be eroding margins without significantly increasing sales volume.

- Strategic Geography & Timing: High-value customers are concentrated in specific states like Colorado and New Jersey, and shopping activity peaks unexpectedly at the start of the work week (Monday).


Thus, Globex Retail is well-positioned to evolve from a traditional retail model to a data-centric powerhouse. By shifting focus from generic promotions to targeted, segment-specific marketing and optimising inventory based on high-revenue subcategories, the company can significantly enhance its customer retention and operational efficiency.

This project demonstrates that Data Engineering is not just about processing numbers — it is about providing the clarity needed to make decisions that drive sustainable innovation and revenue.
