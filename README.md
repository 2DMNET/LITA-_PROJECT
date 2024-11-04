# LITA_PROJECT_Documentation
---
### Project Title : Sales Record Analysis
---
### Project Overview
Project Overview
This project focuses on analyzing sales and customer data to uncover actionable insights that can drive business decisions. Using data on product sales, customer demographics, subscription trends, and regional performance, the goal is to create an interactive dashboard that highlights key metrics and trends, helping stakeholders understand overall business health and identify growth opportunities.

Objectives
Analyze Sales Performance: Provide a high-level overview of total sales, revenue growth, and top-performing products.
Understand Customer Segments: Segment customers by demographics to identify high-value segments and target markets.
Monitor Subscription and Retention Trends: Analyze subscription types, average subscription duration, and cancellation rates across regions.
Regional Insights: Breakdown sales and cancellations by region to guide targeted marketing and retention strategies.

### Data Sourses
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
### Exploratory Data Analysis

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
 ~~~ SQL

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


