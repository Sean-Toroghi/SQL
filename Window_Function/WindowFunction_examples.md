# Window Function - Examples

---
## Example 1. Given table 'Orders' with 6 features (OrderID, CustomerID, OrderDate, Product, Quantity, Amount), find the top 3 customers who have made the highest total purchases and their ranking based on total purchases. 

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
---
## Example 2. Given table 'Orders' with 6 features (OrderID, CustomerID, OrderDate, Product, Quantity, Amount), retrieve the quantity of the previous order, as well as calculate the difference between the previous and current order quantities for each order.

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
WITH quanity_lag AS 
    (SELECT *,
    LAG(Quantity, 1) OVER (PARTITION BY CustomerID ORDER BY OrderDate) as Previous_quantity
    FROM Orders)
SELECT *,
    (Quantity - Previous_quantity) as Difference_quantity
    FROM quanity_lag
```

