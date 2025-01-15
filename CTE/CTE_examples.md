# Common table expression - Examples

## Example 1. Given 4 tables (Categories, Suppliers, Products, and Orders), identify suppliers who provide products with a unit price above the average unit price across all products. Retrieve the supplier name and the number of such high-value products they supply.


```sql
--- 1. overview of dataset
SELECT *
FROM Categories
LIMIT 3
--->> CategoryID	CategoryName
SELECT *
FROM Suppliers
LIMIT 3
--- >> SupplierID	SupplierName	ContactName	ContactEmail
SELECT *
FROM Products
LIMIT 3
--- >> ProductID	ProductName	CategoryID	SupplierID	UnitPrice	StockQuantity
SELECT *
FROM Orders
LIMIT 3
--- >> OrderID	CustomerName	OrderDate	TotalAmount

--- 2. generte average unt price over all products
SELECT AVG(UnitPrice) as Average_unit_price
FROM Products

--- 3. SELECT supplierID with their unit price above avg unit price
WITH avg_unit_price_tbl AS (
    SELECT AVG(UnitPrice) as Average_unit_price
    FROM Products
)
SELECT Distinct(SupplierID)
FROM Products
WHERE UnitPrice > (SELECT Average_unit_price FROM avg_unit_price_tbl)

--- 4. Join to get supplier name
WITH avg_unit_price_tbl AS (
    SELECT AVG(UnitPrice) as Average_unit_price
    FROM Products
)

SELECT Distinct(s.SupplierName), MAX(p.UnitPrice), MIN(p.UnitPrice), 
(SELECT Average_unit_price FROM avg_unit_price_tbl) as avg_unit_price
FROM Products as p
LEFT JOIN Suppliers as s
ON p.SupplierID = s.SupplierID
WHERE UnitPrice > avg_unit_price
```

---

## Example 2. Given 4 tables (Categories, Suppliers, Products, and Orders), identify products with a stock quantity below a specified threshold, 50 units. Also, retrieve the product name, category name, and the remaining stock quantity.

```sql
 --- 1. overview of dataset
SELECT *
FROM Categories
LIMIT 3
--->> CategoryID	CategoryName
SELECT *
FROM Suppliers
LIMIT 3
--- >> SupplierID	SupplierName	ContactName	ContactEmail
SELECT *
FROM Products
LIMIT 3
--- >> ProductID	ProductName	CategoryID	SupplierID	UnitPrice	StockQuantity
SELECT *
FROM Orders
LIMIT 3
--- >> OrderID	CustomerName	OrderDate	TotalAmount

--- 2. Select product with qty below 50
SELECT *
FROM Products
WHERE StockQuantity < 50

--- 3. Retrive 
WITH low_qty AS (
    SELECT *
    FROM Products
    WHERE StockQuantity < 50
)

SELECT p.ProductName, c.CategoryName, l.StockQuantity
FROM low_qty as l
LEFT JOIN Categories as c
    ON l.CategoryID = c.CategoryID
LEFT JOIN Products as p
    ON p.ProductID = l.ProductID

```

