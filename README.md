# **Coffee Shope Analysis**
A comprehensive data analytics project on Coffee Shope Sales using **MySQL for database management and Power BI for data visualization and validation**
## Table Of Content


## Data Souce
**Data Link :** (https://drive.google.com/file/d/1Amf_LHmf-boXQNeImxNv6cSobAbmlG5p/view?usp=sharing)
## Introduction
This data analytics project analyzes Coffee Shop Sales to uncover key performance indicators such as total sales, orders, and quantity sold per month. We explore trends month-over-month and segment sales data by weekdays, weekends, and store locations. The insights gained will help optimize sales strategies and improve business performance.

## Problem Statement
1. **Total Sales**
2. **Total Orders**
3. **Total Qyantity Sold**
4. **Sales Analysis By Weekdays & Weekends**
5. **Daily Sales Analysis**
6. **Sales Analysis By Product Category & Dayli Hours**

## MySQL QUERIES

- Creating a Table with the same field name in the original dataset
```
CREATE TABLE Coffee_shop_sales (
    transaction_id INT,
    transaction_date DATE,
    transaction_time TIME,
    transaction_qty INT,
    store_id INT,
    store_location TEXT,
    product_id INT,
    unit_price DOUBLE,
    product_category TEXT,
    product_type TEXT,
    product_detail TEXT
);
```
- Importing the data using the **LOAD INFILE** Statement
```
LOAD DATA INFILE "C:/Coffee_sale.csv"
INTO TABLE Coffee_shop_sales
FIELDS TERMINATED BY ','
OPTIONALLY ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 LINES;
```
### Data Cleaning and Formating
- ALTER DATE (transaction_date) COLUMN TO DATE DATA TYPE
```
ALTER TABLE coffee_shop_sales
MODIFY COLUMN transaction_date DATE;
```
- CONVERT TIME (transaction_time)  COLUMN TO PROPER DATE FORMAT
```
UPDATE coffee_shop_sales
SET transaction_time = STR_TO_DATE(transaction_time, '%H:%i:%s');
```
- ALTER TIME (transaction_time) COLUMN TO DATE DATA TYPE
```
ALTER TABLE coffee_shop_sales
MODIFY COLUMN transaction_time TIME;
```
-  DATA TYPES OF DIFFERENT COLUMNS
```
DESCRIBE coffee_shop_sales;
```
### Exploratory Data Analysis
**Total Sales**
```
SELECT ROUND(SUM(unit_price * transaction_qty)) as Total_Sales 
FROM coffee_shop_sales 
WHERE MONTH(transaction_date) = 5; -- for month of (CM-May) CM = current month
```
![TOTAL SALES](https://github.com/user-attachments/assets/041ad754-56a9-4311-94f7-9167bcaf86f8)

**Total SALES KPI - MOM DIFFERENCE AND MOM GROWTH**
```
SELECT 
    MONTH(transaction_date) AS month,
    ROUND(SUM(unit_price * transaction_qty)) AS total_sales,
    (SUM(unit_price * transaction_qty) - LAG(SUM(unit_price * transaction_qty), 1)
    OVER (ORDER BY MONTH(transaction_date))) / LAG(SUM(unit_price * transaction_qty), 1) 
    OVER (ORDER BY MONTH(transaction_date)) * 100 AS mom_increase_percentage
FROM 
    coffee_shop_sales
WHERE 
    MONTH(transaction_date) IN (4, 5) -- for months of April and May
GROUP BY 
    MONTH(transaction_date)
ORDER BY 
    MONTH(transaction_date);
```
![TOTAL SALES KPI - MOM DIFFERENCE AND MOM GROWTH](https://github.com/user-attachments/assets/c993377c-b189-4fea-8da2-09a7196b5a61)

**TOTAL ORDERS**
```
SELECT COUNT(transaction_id) as Total_Orders
FROM coffee_shop_sales 
WHERE MONTH (transaction_date)= 5; -- for month of (CM-May)
```
![TOTAL ORDERS](https://github.com/user-attachments/assets/2e792a6c-eb0d-4349-ab92-d5e528ffa16a)

**TOTAL ORDERS KPI - MOM DIFFERENCE AND MOM GROWTH**
```
SELECT 
    MONTH(transaction_date) AS month,
    ROUND(COUNT(transaction_id)) AS total_orders,
    (COUNT(transaction_id) - LAG(COUNT(transaction_id), 1) 
    OVER (ORDER BY MONTH(transaction_date))) / LAG(COUNT(transaction_id), 1) 
    OVER (ORDER BY MONTH(transaction_date)) * 100 AS mom_increase_percentage
FROM 
    coffee_shop_sales
WHERE 
    MONTH(transaction_date) IN (4, 5) -- for April and May
GROUP BY 
    MONTH(transaction_date)
ORDER BY 
    MONTH(transaction_date);
```
![TOTAL ORDERS KPI - MOM DIFFERENCE AND MOM GROWTH](https://github.com/user-attachments/assets/37b015a1-e18a-40a8-acb4-49b348bed093)

**TOTAL QUANTITY SOLD**
```
SELECT SUM(transaction_qty) as Total_Quantity_Sold
FROM coffee_shop_sales 
WHERE MONTH(transaction_date) = 5; -- for month of (CM-May)
```
**TOTAL QUANTITY SOLD KPI - MOM DIFFERENCE AND MOM GROWTH**
```
SELECT 
    MONTH(transaction_date) AS month,
    ROUND(SUM(transaction_qty)) AS total_quantity_sold,
    (SUM(transaction_qty) - LAG(SUM(transaction_qty), 1) 
    OVER (ORDER BY MONTH(transaction_date))) / LAG(SUM(transaction_qty), 1) 
    OVER (ORDER BY MONTH(transaction_date)) * 100 AS mom_increase_percentage
FROM 
    coffee_shop_sales
WHERE 
    MONTH(transaction_date) IN (4, 5)   -- for April and May
GROUP BY 
    MONTH(transaction_date)
ORDER BY 
    MONTH(transaction_date);
```
![TOTAL QUANTITY SOLD KPI - MOM DIFFERENCE AND MOM GROWTH](https://github.com/user-attachments/assets/82f6a1e6-c645-432e-bd92-e8056784385d)

**CALENDAR TABLE – DAILY SALES, QUANTITY and TOTAL ORDERS**
```
SELECT
    CONCAT(ROUND(SUM(unit_price * transaction_qty)/ 1000, 1), 'K') AS total_sales,
    CONCAT(ROUND(SUM(transaction_qty)/ 1000, 1), 'K') AS total_quantity_sold,
    COUNT(transaction_id) AS total_orders
FROM 
    coffee_shop_sales
WHERE 
    transaction_date = '2023-03-26'; -- For 26 Mars 2023
```
![CALENDAR TABLE – DAILY SALES, QUANTITY and TOTAL ORDERS](https://github.com/user-attachments/assets/d808aeb6-772b-4d73-857c-cb08ba6d89f0)

**SALES TREND OVER PERIOD**
```
SELECT AVG(total_sales) AS average_sales
FROM (
    SELECT 
        SUM(unit_price * transaction_qty) AS total_sales
    FROM 
        coffee_shop_sales
	WHERE 
        MONTH(transaction_date) = 5  -- Filter for May
    GROUP BY 
        transaction_date
) AS internal_query;
```
![SALES TREND OVER PERIOD](https://github.com/user-attachments/assets/25ce042d-0ed4-409b-b3de-ce294fd7f0c2)

**DAILY SALES FOR MONTH SELECTED**
```
SELECT 
    DAY(transaction_date) AS day_of_month,
    CONCAT(ROUND(SUM(unit_price * transaction_qty)/ 1000,1), 'K') AS total_sales
FROM 
    coffee_shop_sales
WHERE 
    MONTH(transaction_date) = 5  -- Filter for May
GROUP BY 
    DAY(transaction_date)
ORDER BY 
    DAY(transaction_date);
```
![DAILY SALES FOR MONTH SELECTED](https://github.com/user-attachments/assets/98b4183b-b2ba-4f6d-a8ba-a9bff04d1a48)

**COMPARING DAILY SALES WITH AVERAGE SALES**
```
SELECT 
    day_of_month,
    CASE 
        WHEN total_sales > avg_sales THEN 'Above Average'
        WHEN total_sales < avg_sales THEN 'Below Average'
        ELSE 'Average'
    END AS sales_status,
    total_sales
FROM (
    SELECT 
        DAY(transaction_date) AS day_of_month,
        SUM(unit_price * transaction_qty) AS total_sales,
        AVG(SUM(unit_price * transaction_qty)) OVER () AS avg_sales
    FROM 
        coffee_shop_sales
    WHERE 
        MONTH(transaction_date) = 5  -- Filter for May
    GROUP BY 
        DAY(transaction_date)
) AS sales_data
ORDER BY 
    day_of_month;
```
![COMPARING DAILY SALES WITH AVERAGE SALES](https://github.com/user-attachments/assets/cd8c2d7e-5690-4ab0-8e75-b77ce191b553)

**SALES BY WEEKDAY / WEEKEND:**
```
SELECT 
    CASE 
        WHEN DAYOFWEEK(transaction_date) IN (1, 7) THEN 'Weekends'
        ELSE 'Weekdays'
    END AS day_type,
    CONCAT(ROUND(SUM(unit_price * transaction_qty)/ 1000,1), 'K') AS total_sales
FROM 
    coffee_shop_sales
WHERE 
    MONTH(transaction_date) = 5  -- Filter for May
GROUP BY 
    CASE 
        WHEN DAYOFWEEK(transaction_date) IN (1, 7) THEN 'Weekends'
        ELSE 'Weekdays'
    END;
```
![SALES BY WEEKDAY  WEEKEND](https://github.com/user-attachments/assets/1a146224-4e84-40c8-a9bd-dc86dad73c56)

**SALES BY STORE LOCATION**
```
SELECT 
	store_location,
	CONCAT(ROUND(SUM(unit_price * transaction_qty)/ 1000, 2), 'K') as Total_Sales
FROM coffee_shop_sales
WHERE
	MONTH(transaction_date) =5 
GROUP BY store_location
ORDER BY 	SUM(unit_price * transaction_qty) DESC;
```
![SALES BY STORE LOCATION](https://github.com/user-attachments/assets/8a4d947e-31d5-4409-9f07-8ef37802b2ab)

 **SALES BY PRODUCT CATEGORY**
```
SELECT 
	product_category,
	CONCAT(ROUND(SUM(unit_price * transaction_qty)/ 1000,1), 'K') as Total_Sales
FROM coffee_shop_sales
WHERE
	MONTH(transaction_date) = 5 
GROUP BY product_category
ORDER BY SUM(unit_price * transaction_qty) DESC;
```
![SALES BY PRODUCT CATEGORY](https://github.com/user-attachments/assets/4574e460-32e7-4cf1-987b-21b06993fcea)

**SALES BY PRODUCTS (TOP 10)**
```
SELECT 
	product_type,
	ROUND(SUM(unit_price * transaction_qty),1) as Total_Sales
FROM coffee_shop_sales
WHERE
	MONTH(transaction_date) = 5 
GROUP BY product_type
ORDER BY SUM(unit_price * transaction_qty) DESC
LIMIT 10;
```
![SALES BY PRODUCTS (TOP 10)](https://github.com/user-attachments/assets/c3afb0b7-9370-4967-bbf8-c9029cf1f3fe)

**SALES BY DAY | HOUR**
```
SELECT 
    CONCAT(ROUND(SUM(unit_price * transaction_qty)/1000, 1), 'K') AS Total_Sales,
    SUM(transaction_qty) AS Total_Quantity,
    COUNT(*) AS Total_Orders
FROM 
    coffee_shop_sales
WHERE 
    DAYOFWEEK(transaction_date) = 3 -- Filter for Tuesday (1 is Sunday, 2 is Monday, ..., 7 is Saturday)
    AND HOUR(transaction_time) = 8 -- Filter for hour number 8
    AND MONTH(transaction_date) = 5; -- Filter for May (month number 5)
```
![SALES BY DAY  HOUR](https://github.com/user-attachments/assets/934a18ed-01e1-4659-92b6-bfa8712e0179)

**TO GET SALES FROM MONDAY TO SUNDAY FOR MONTH OF MAY**
```
SELECT 
    CASE 
        WHEN DAYOFWEEK(transaction_date) = 2 THEN 'Monday'
        WHEN DAYOFWEEK(transaction_date) = 3 THEN 'Tuesday'
        WHEN DAYOFWEEK(transaction_date) = 4 THEN 'Wednesday'
        WHEN DAYOFWEEK(transaction_date) = 5 THEN 'Thursday'
        WHEN DAYOFWEEK(transaction_date) = 6 THEN 'Friday'
        WHEN DAYOFWEEK(transaction_date) = 7 THEN 'Saturday'
        ELSE 'Sunday'
    END AS Day_of_Week,
    CONCAT(ROUND(SUM(unit_price * transaction_qty)/ 1000, 1), 'K') AS Total_Sales
FROM 
    coffee_shop_sales
WHERE 
    MONTH(transaction_date) = 5 -- Filter for May (month number 5)
GROUP BY 
    CASE 
        WHEN DAYOFWEEK(transaction_date) = 2 THEN 'Monday'
        WHEN DAYOFWEEK(transaction_date) = 3 THEN 'Tuesday'
        WHEN DAYOFWEEK(transaction_date) = 4 THEN 'Wednesday'
        WHEN DAYOFWEEK(transaction_date) = 5 THEN 'Thursday'
        WHEN DAYOFWEEK(transaction_date) = 6 THEN 'Friday'
        WHEN DAYOFWEEK(transaction_date) = 7 THEN 'Saturday'
        ELSE 'Sunday'
    END;
```
![TO GET SALES FROM MONDAY TO SUNDAY FOR MONTH OF MAY](https://github.com/user-attachments/assets/cbb50e8d-80c6-4fce-b0d5-6d45f6c34956)

**TO GET SALES FOR ALL HOURS FOR MONTH OF MAY**
```
SELECT 
    HOUR(transaction_time) AS Hour_of_Day,
    ROUND(SUM(unit_price * transaction_qty)) AS Total_Sales
FROM 
    coffee_shop_sales
WHERE 
    MONTH(transaction_date) = 5 -- Filter for May (month number 5)
GROUP BY 
    HOUR(transaction_time)
ORDER BY 
    HOUR(transaction_time);
```
![TO GET SALES FOR ALL HOURS FOR MONTH OF MAY](https://github.com/user-attachments/assets/93c6f456-40d7-4df4-8785-cab101745e1b)

## Power BI Dashboard
You can interact with the report [here](https://app.powerbi.com/view?r=eyJrIjoiMDcxMDM1NjEtZjU4Ny00NmExLThjYjMtNzcwNWMxODRkOTU3IiwidCI6ImZjZjMyMWUxLTU1OGQtNGQzMi1iZWI1LTk2MDAwNDRhZDBjNiJ9)

## Key Insights

**Let’s weave these insights into a narrative that highlights their significance:**
- Month-over-month changes in KPIs reveal a consistent increase, with the exception of February, which shows a dip due to fewer days in the month.
- **Sales Drop After 1 PM**: There’s a significant drop in sales **after 1 PM**. This information is crucial for staffing and inventory management.

## Recommendations
- **Promote Tea Sales:** Implement marketing strategies to boost tea sales, as it’s currently underperforming.
- **Off-Peak Promotions:** Consider promotional activities during low-sales hours to increase revenue throughout the day.
