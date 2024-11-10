# LITA_PROJECT_Documentation 
---
### Project Title : Sales Record Analysis 
---
[Project Overview](#project-overvieww)

[Data Sources](#data-sources)

[Tools and Technologies](#tools-and-Technologies)

[Data Cleaning and Preparations](#data-cleaning-and-preparations)

[Steps for Data Cleaning and Preparation](#steps-for-data-cleaning-and-preparation)

[Questions to be answered after initial exploration](#questions-to-be-answered-after-initial-exploration)

[ Data Analysis Using SQL](#data-analysis-using-sql)

[SQL OUTPUT](#sql-output)

[Customer Data Sql Code](#customer-data-sql-code)

[SQL OUTPUT 2](#sql-output-2)

[POWER BI VISUALS](#power-bi-visuals)

[Recommendations](#recommendations)

Project Overview
---
This project focuses on analyzing sales and customer data to uncover actionable insights that can drive business decisions. Using data on product sales, customer demographics, subscription trends, and regional performance, the goal is to create an interactive dashboard that highlights key metrics and trends, helping stakeholders understand overall business health and identify growth opportunities.

Objectives
Analyze Sales Performance: Provide a high-level overview of total sales, revenue growth, and top-performing products.
Understand Customer Segments: Segment customers by demographics to identify high-value segments and target markets.
Monitor Subscription and Retention Trends: Analyze subscription types, average subscription duration, and cancellation rates across regions.
Regional Insights: Breakdown sales and cancellations by region to guide targeted marketing and retention strategies.

### Data Sources
The Main data source used was LITA Capstone Dataset which is an excel workbook with SalesData and CustomerData worksheets.

---
### Tools and Technologies
- Microsoft Ecxel
   1. for Data Cleaning,
   2. Data Analysis,
   3. and Visualisation.
- SQL: Structured Query Language
   1. for data extraction,
   2. transformation,
   3. and exploratory analysis.
- Power BI
   1. for building an interactive,
   2. user-friendly dashboard.
- GitHub
  1. to document,
  2. share,
  3. and Portfolio Building.
 
---
### Data Cleaning and Preparations
The data cleaning and preparation phase involves transforming raw data into a structured format suitable for analysis. This process includes handling missing values, correcting data types, removing duplicates, and creating new features that can aid in analysis.

---
### Steps for Data Cleaning and Preparation
- Data Loading and Inspection
- Handling Missing Values:
  
---
- Exploratory Data Analysis

  1.  Identify columns with missing values and assess their impact on analysis.
  2.  Data correction:

    -   Check each column’s data type to ensure it aligns with its expected format (e.g., Date columns as DateTime, Sales as Decimal or Integer).
    -   Parse date fields if they’re formatted inconsistently, ensuring they follow a standard date format.
    -   Removing Duplicates

- Total Sales: Create a calculated field for total sales, such as Total Sales = Quantity * Unit Price.
- Subscription Duration: Calculate the length of each customer’s subscription based on Start Date and End Date.
---
### Questions to be answered after initial exploration
 - Excel:
   - Perform an initial exploration of the sales data. Use pivot tables to summarize 
     total sales by product, region, and month.

![Summarization using Pivot](https://github.com/user-attachments/assets/cba52f2f-cc09-45ea-8523-941c38fd674b)

   - Use Excel formulas to calculate metrics such as average sales per product and 
     total revenue by region.

![Average sales by product](https://github.com/user-attachments/assets/79ba69e6-b72a-49b0-be60-2b363a0da5b5)

   
   - Create any other interesting report
![SalesData Visuals](https://github.com/user-attachments/assets/40c23314-7333-4d7a-9bb1-a9525bb5fa6a)

   -Analyze customer data using pivot tables to find subscription patterns. 
![Customers Pivot](https://github.com/user-attachments/assets/9d6f2e8c-109c-4917-bbdf-5021e65e31d6)
   -Calculate the average subscription duration and identify the most popular 
     subscription types. 

![Subscrition Duration](https://github.com/user-attachments/assets/c4a71833-d3ae-4d84-8d9e-63dc66087f69)
   -Create any other interesting reports.

![Subscription Visuals](https://github.com/user-attachments/assets/1c511d18-830d-4019-8d68-2462063b338b)


### Data Analysis Using SQL
---
 ~~~ SQL SALES DATA

CREATE TABLE SalesData (
    OrderID INT,
    CustomerId VARCHAR(50),
    Product VARCHAR(50),
    Region VARCHAR(50),
    OrderDate DATE,
    Quantity INT,
    UnitPrice DECIMAL(10, 2)
);





BULK INSERT SalesData
FROM 'C:\Users\Olabamidele\Desktop\salesdata.txt'
WITH (
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
    FIRSTROW = 2,
    DATAFILETYPE = 'char',  
    TABLOCK
);
--1. Retrieve the Total Sales for Each Product Category
SELECT 
    Product, 
    SUM(Quantity * UnitPrice) AS TotalSales
FROM 
    SalesData
GROUP BY 
    Product;
--2. Find the Number of Sales Transactions in Each Region

SELECT 
    Region, 
    COUNT(OrderID) AS NumberOfTransactions
FROM 
    SalesData
GROUP BY 
    Region;

--3. Find the Highest-Selling Product by Total Sales Value
SELECT TOP 1
    Product, 
    SUM(Quantity * UnitPrice) AS TotalSales
FROM 
    SalesData
GROUP BY 
    Product
ORDER BY 
    TotalSales DESC;


--4. Calculate Total Revenue Per Product
SELECT 
    Product, 
    SUM(Quantity * UnitPrice) AS TotalRevenue
FROM 
    SalesData
group by 
    Product;

--5. Calculate Monthly Sales Totals for the Current Year

SELECT 
    FORMAT(OrderDate, 'yyyy-MM') AS Month, 
    SUM(Quantity * UnitPrice) AS MonthlySales
FROM 
    SalesData
WHERE 
    YEAR(OrderDate) = YEAR(GETDATE())  -- Assuming current year is derived from the system
GROUP BY 
    FORMAT(OrderDate, 'yyyy-MM')
ORDER BY 
    Month;

--6. Find the Top 5 Customers by Total Purchase Amount

SELECT TOP 5
    CustomerId, 
    SUM(Quantity * UnitPrice) AS TotalPurchaseAmount
FROM 
    SalesData
GROUP BY 
    CustomerId
ORDER BY 
    TotalPurchaseAmount DESC;



--7. Calculate the Percentage of Total Sales Contributed by Each Region

WITH RegionSales AS (
    SELECT 
        Region, 
        SUM(Quantity * UnitPrice) AS RegionTotalSales
    FROM 
        SalesData
    WHERE 
        Region IS NOT NULL
    GROUP BY 
        Region
)
SELECT 
    Region, 
    RegionTotalSales, 
    (RegionTotalSales * 100.0) / (SELECT SUM(Quantity * UnitPrice) FROM SalesData) AS PercentageOfTotalSales
FROM 
    RegionSales;

--8. Identify Products with No Sales in the Last Quarter
SELECT 
    DISTINCT Product
FROM 
    SalesData
WHERE 
    Product NOT IN (
        SELECT 
            DISTINCT Product
        FROM 
            SalesData
        WHERE 
            OrderDate >= DATEADD(QUARTER, -1, GETDATE())
    );
~~~
### SQL OUTPUT
---
 -  Retrieve the total sales for each product category.
 ![retrieve the total number of customers from each region](https://github.com/user-attachments/assets/7af095e7-d409-4492-ac0b-ee2b7858a98d)

 - Find the most popular subscription type by the number of customers.

![ind the most popular subscription type by the number of customers](https://github.com/user-attachments/assets/77eb29a2-6d7d-4b57-b46e-c23ab2d441bb)
 - Find the highest-selling product by total sales value.

![find the highest-selling product by total sales value](https://github.com/user-attachments/assets/e31c80b3-8b40-4489-adc8-e7e4d802dcf1)
 -  calculate total revenue per product.

![calculate total revenue per product](https://github.com/user-attachments/assets/0f9962da-175f-49dc-ae25-2ad9374d8343)
 - calculate monthly sales totals for the current year

![calculate monthly sales totals for the current year](https://github.com/user-attachments/assets/a3c44f16-2f75-4d9f-a2b4-76c9bc737db2)
 - find the top 5 customers by total purchase amount.
   
![find the top 5 customers by total purchase amount](https://github.com/user-attachments/assets/80331b05-f987-4ff8-8f1e-ccdf0675ef4c)

 - calculate the percentage of total sales contributed by each region.

![calculate the percentage of total sales contributed by each region](https://github.com/user-attachments/assets/22b38ba2-9fac-4ff2-abca-82346ebd84b8)

- identify products with no sales in the last quarter.

![identify products with no sales in the last quarter](https://github.com/user-attachments/assets/0670fcf2-ee30-4041-8a6d-3e4093ad1268)

### Customer Data Sql Code
---
~~~ SQL CUSTOMER DATA
CREATE TABLE Customers (
    CustomerID INT, 
	CustomerName VARCHAR(50),
    Region VARCHAR(50),                     
    SubscriptionType VARCHAR(50),             
    SubscriptionStartDate DATE,               
    SubscriptionEndDate DATE,                 
    Cancelled VARCHAR(20),           
    Revenue DECIMAL(10, 2)                    
);
--- Reading the dataset into Table
BULK INSERT Customers
FROM 'C:\Users\Olabamidele\Documents\CustomerSub.txt'
WITH (
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
    FIRSTROW = 2,
    DATAFILETYPE = 'char',  
    TABLOCK
);
----1. Retrieve the total number of customers from each region:
SELECT 
    Region, 
    COUNT(CustomerID) AS TotalCustomers
FROM 
    Customers
GROUP BY 
    Region;

----2.Find the most popular subscription type by the number of customers:

SELECT Top 1
    SubscriptionType, 
    COUNT(CustomerID) AS CustomerCount
FROM 
    Customers
GROUP BY 
    SubscriptionType
ORDER BY 
    CustomerCount DESC

----3. Find customers who canceled their subscription within 6 months:

SELECT 
    CustomerID, 
    SubscriptionType, 
    DATEDIFF(MONTH, SubscriptionStartDate, SubscriptionEndDate) AS DurationInMonths
FROM 
    Customers
WHERE 
    Cancelled = 'True' 
    AND DATEDIFF(MONTH, SubscriptionStartDate, SubscriptionEndDate) <= 6; 

---4. Calculate the average subscription duration for all customers:

SELECT 
    AVG(DATEDIFF(MONTH, SubscriptionStartDate, 
        ISNULL(SubscriptionEndDate, GETDATE()))) AS AverageSubscriptionDurationInMonths
FROM 
    Customers;

----5. Find customers with subscriptions longer than 12 months:

SELECT 
    CustomerID, 
    SubscriptionType, 
    DATEDIFF(MONTH, SubscriptionStartDate, 
        ISNULL(SubscriptionEndDate, GETDATE())) AS DurationInMonths
FROM 
    Customers
WHERE 
    DATEDIFF(MONTH, SubscriptionStartDate, 
        ISNULL(SubscriptionEndDate, GETDATE())) > 12;

---6. Calculate total revenue by subscription type:

SELECT 
    SubscriptionType, 
    SUM(Revenue) AS TotalRevenue
FROM 
    Customers
GROUP BY 
    SubscriptionType;

----7. Find the top 3 regions by subscription cancellations:

SELECT Top 3
    Region, 
    COUNT(CustomerID) AS CanceledSubscriptions
FROM 
    Customers
WHERE 
    Cancelled = 'True'
GROUP BY 
    Region
ORDER BY 
    CanceledSubscriptions DESC


----8. Find the total number of active and canceled subscriptions:

SELECT 
    Cancelled SubscriptionStatus, 
    COUNT(CustomerID) AS TotalSubscriptions
FROM 
    Customers
GROUP BY 
    Cancelled;
~~~
### SQL OUTPUT 2
---
 - retrieve the total number of customers from each region.
![1](https://github.com/user-attachments/assets/9a47a4d0-fb6a-4591-8a14-89fe813faafa)

 - find the most popular subscription type by the number of customers.
![2](https://github.com/user-attachments/assets/0d4693d0-9620-4f39-8267-225ad09a8479)

 - find customers who canceled their subscription within 6 months.
![3](https://github.com/user-attachments/assets/8bc6bf05-5a60-4ae6-a328-537c5ad81776)

 - calculate the average subscription duration for all customers.

![4](https://github.com/user-attachments/assets/b0936681-4e15-4601-9fb7-271f1690f1d2)

 - find customers with subscriptions longer than 12 months.

![5](https://github.com/user-attachments/assets/ea5e531a-e794-44d8-90b7-4a639ad01ea5)

 - calculate total revenue by subscription type.

![6](https://github.com/user-attachments/assets/5ff4b775-a86a-465e-9f6b-ea5b08c92bef)

 - find the top 3 regions by subscription cancellations.

![7](https://github.com/user-attachments/assets/1d45edc6-0997-4e95-9d73-06dc09c96101)

 - find the total number of active and canceled subscriptions.

![8](https://github.com/user-attachments/assets/3ddf585b-c227-4162-a8d5-fbb07d37ee2b)

### POWER BI VISUALS
- Power BI:
  - The dashboard should include a sales overview, top-performing products, and 
    regional breakdowns.

![Lita Sales Report](https://github.com/user-attachments/assets/ce72597c-311a-4402-ab5c-5b4b1a75958c)
  - Build a Power BI dashboard that visualizes key customer segments, 
    cancellations, and subscription trends. Include slicers for interactive analysis.

![Customers Dashboard ](https://github.com/user-attachments/assets/0fc24d87-527b-4738-806e-f730e2aadde7)


### Recommendations
 - Sales Data
    - Based on the insights gathered from the exploratory data analysis and interactive dashboard, here are several key recommendations aimed at improving customer engagement, 
      optimizing product performance, and increasing revenue across regions.

    - Recommendations for Business Decisions
      Enhance Marketing Efforts for High-Performing Regions:

    - Insight: The analysis shows that specific regions contribute significantly to overall revenue.
      Recommendation: Allocate more marketing resources to top-performing regions to maximize sales in areas with proven customer demand. Regional marketing campaigns can 
      further.
    - Recommendation: Use targeted campaigns to anticipate and capture peak periods for these products. Strategic promotions or bundled offers can be timed during high-demand 
      months to maximize sales.
 - Customer Data 
    - boost engagement in these areas.
      Focus on Retention for Popular Subscription Types:
    
    - Insight: Some subscription types are highly popular, while others have lower adoption rates.
      Recommendation: Invest in customer retention strategies for the most popular subscription types to reduce churn. Additionally, evaluate feedback on less popular 
      subscriptions to refine offerings or promotions that encourage conversions.

    - Implement Loyalty Programs for Long-Term Subscribers:

    - Address High Cancellation Regions:
      Insight: The analysis indicates that certain regions have higher cancellation rates.
      Recommendation: Investigate factors driving cancellations in these regions through customer feedback or surveys. Tailored retention programs or improved customer service 
      in these regions could help reduce churn and improve customer satisfaction.
      Offer Subscription Discounts or Upgrades for At-Risk Customers:


 - Conclusion
   These recommendations are designed to support data-driven decision-making, improve customer retention, and enhance revenue growth. Implementing these strategies can help the 
   business capitalize on key insights and achieve sustained success.




