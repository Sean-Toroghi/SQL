
# <a id= 'window'> Window Functions</a>

- [Set 1](#set1)
- [Set 2](#set2)
- [Set 3](#set3)
---

## <a id= 'set1'>Exercise set 1</a>
[Reference](https://github.com/LinkedInLearning/practice-it-advanced-sql-4412219/tree/main)

[UP](#window)


### 1. Given table 'Orders' with 6 features (OrderID, CustomerID, OrderDate, Product, Quantity, Amount), find the top 3 customers who have made the highest total purchases and their ranking based on total purchases. 

```SQL
--- 1. Get an overview of data
SELECT * 
  FROM Orders
  LIMIT 2;

--- 2. Top 3 customers based on the highest total purchase amount
SELECT CustomerID,
    SUM(Amount) OVER (PARTITION BY CustomerID) as Total_purchase
 FROM Orders
 ORDER BY Total_purchase DESC
 LIMIT 3;

--- 3. add ranking
SELECT CustomerID,
       SUM(Amount) OVER (PARTITION BY CustomerID) as Total_purchase,
       RANK() OVER (ORDER BY SUM(Amount) DESC) as Rank
FROM Orders
GROUP BY CustomerID
ORDER BY Rank
LIMIT 3;
```


### 2. Given table 'Orders' with 6 features (OrderID, CustomerID, OrderDate, Product, Quantity, Amount), retrieve the quantity of the previous order, as well as calculate the difference between the previous and current order quantities for each order.

```SQL
--- 1. Get an overview of data
SELECT * 
  FROM Orders
  LIMIT 2;

--- 2. add quantity of previouse order
SELECT *,
    LAG(Quantity, 1) OVER (PARTITION BY CustomerID ORDER BY OrderDate) as Previous_quantity
    FROM Orders

--- 3. add difference of quantity of current and previous order    
WITH quantity_lag AS 
    (SELECT *,
    LAG(Quantity, 1) OVER (PARTITION BY CustomerID ORDER BY OrderDate) as Previous_quantity
    FROM Orders)
SELECT *,
    (Quantity - Previous_quantity) as Difference_quantity
    FROM quantity_lag
```

---
## <a id= 'set2'>Exercise set 2</a>
[Reference](https://github.com/LinkedInLearning/level-up-advanced-sql-4311094)

__Database tables__
- customer: customerId, firstName, lastName, address, city, zipcode, email
- employee: employeeId, firstName, lastName, title, startDate, managerId
- inventory: inventoryId, modelId, colour, year, isAvailable
- model: modelId, model, EngineType
- sales: salesId, inventoryId, customerId, employeeId, sakesAmount, soldDate
- 
[UP](#window)


### 1. Retrieve the list of sales people and rank them by number of cars they have sold.

```sql
---1. overview of table
SELECT * 
FROM sales 
LIMIT 2

---2. total sales by employeeId
SELECT COUNT(salesId) as count_sales, employeeId
FROM sales
GROUP BY employeeId

---3. add rank based on sale count
WITH sale_count_tbl AS (SELECT COUNT(salesId) as count_sales, employeeId
                        FROM sales
                        GROUP BY employeeId
                        )

SELECT employeeId, count_sales,
    RANK() OVER (ORDER BY count_sales DESC) as ranking,
    DENSE_RANK() OVER (ORDER BY count_sales DESC) as ranking_dense
FROM sale_count_tbl
ORDER BY count_sales DESC

---4. add name of employee
WITH sale_count_tbl AS (SELECT COUNT(salesId) as count_sales, employeeId
                        FROM sales
                        GROUP BY employeeId
                        )

SELECT s.employeeId, s.count_sales, e.firstName, e.lastName,
    RANK() OVER (ORDER BY count_sales DESC) as ranking
FROM sale_count_tbl as s
    LEFT JOIN employee as e
        ON s.employeeId = e.employeeId
ORDER BY count_sales DESC

```

### 2. Retrive 2 highest rank of car model each sales person have sold
