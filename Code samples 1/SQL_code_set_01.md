Table of content:
- subquery 
- CTE
- WINDOW function

---

__Subquery__

Problem 1: Retrieve orders whose order total is higher than the average total price of all other orders. (Table: OnlineRetailSales)

```sql
SELECT Top (5) * FROM [Red30Tech].[dbo].[OnlineRetailSales$] 

SELECT AVG([Order Total]) FROM [Red30Tech].[dbo].[OnlineRetailSales$] 

SELECT OrderNum, OrderDate, [Order Total], (SELECT AVG([Order Total]) FROM [Red30Tech].[dbo].[OnlineRetailSales$]) AS Average_total
  FROM [OnlineRetailSales$]
  WHERE [Order Total] > (SELECT AVG([Order Total]) FROM [Red30Tech].[dbo].[OnlineRetailSales$])

```

Problem 2: Retrieve the following information. Session name, start date, end date, and room the employees of Trees Olive Oil have a presentation at (SpeakerInfo table)

```sql
SELECT TOP (4) * FROM [SpeakerInfo$]
SELECT TOP (4) * FROM [SessionInfo$]

SELECT [Speaker Name],[Session Name], [Start Date], [End Date], [Room Name]
FROM [Red30Tech].[dbo].[SessionInfo$]
WHERE [Speaker Name] IN 
	(SELECT [Name] 
		FROM [Red30Tech].[dbo].[SpeakerInfo$] 
		WHERE [Organization] = 'Two Trees Olive Oil')
```



Problem 3 (Correlated subquery). Retrieve the following information of the conference attendees who come from states that there is no online retail sales in those states: Name, email address, and phone number.


```sql

SELECT TOP (4) * FROM [Red30Tech].[dbo].[OnlineRetailSales$]
SELECT TOP (4) * FROM [Red30Tech].[dbo].[ConventionAttendees$]

-- Approach 1: uncorrelated subquery
SELECT [First name], [Last name], [Email], [Phone Number], [State]
FROM [Red30Tech].[dbo].[ConventionAttendees$] 
WHERE [State] NOT IN (SELECT DISTINCT([CustState]) FROM [Red30Tech].[dbo].[OnlineRetailSales$])

--Alternative appeoach using correlated subquery
SELECT [First name], [Last name], [Email], [Phone Number], [State]
FROM [Red30Tech].[dbo].[ConventionAttendees$] as c
WHERE NOT EXISTS
	(SELECT [CustState] FROM [Red30Tech].[dbo].[OnlineRetailSales$] as o
		WHERE c.[State] = o.[CustState])
```

Problem 4: Retrieve the following information from the “Inventory” table of items with less than average amount of product left in stock: ProdCategory, ProdNumber, and ProdName.

```sql
SELECT TOP (4) * FROM [Red30Tech].[dbo].[Inventory$]

SELECT AVG([In Stock]) FROM [Red30Tech].[dbo].[Inventory$]

SELECT [ProdName], [ProdNumber],  [ProdCategory], [In Stock], (SELECT AVG([In Stock]) FROM [Red30Tech].[dbo].[Inventory$]) AS "Average_Storck"
FROM [Red30Tech].[dbo].[Inventory$]
WHERE [In Stock] <= (SELECT AVG([In Stock]) FROM [Red30Tech].[dbo].[Inventory$])
```


---

__Common table expression__

Problem 1:  Retrieve orders whose order total is higher than the average total price of all other orders. (Table: OnlineRetailSales)

```sql

SELECT TOP (5) * FROM [Red30Tech].[dbo].[OnlineRetailSales$] 

WITH AVGERAGE_Sale_CTE (AVG_sale) AS (
	SELECT AVG([Order Total]) AS AVG_sale 
	FROM [Red30Tech].[dbo].[OnlineRetailSales$])

SELECT o.OrderNum, o.OrderDate, o.[Order Total], cte1.AVG_sale
FROM [Red30Tech].[dbo].[OnlineRetailSales$] as o, AVGERAGE_Sale_CTE as cte1
WHERE [Order Total] >= AVG_sale
```

Problem 2: Report the info/count of employees that are directly report to Grant Nguyen.

```sql
SELECT TOP (5) * FROM [Red30Tech].[dbo].[EmployeeDirectory$]

SELECT TOP (5) * FROM [Red30Tech].[dbo].[EmployeeDirectory$]

-- retrieved: Grant Nguyen EmployeeId = 42
WHERE [First name] = 'Grant' and [Last name] = 'Nguyen' 
-- Return how many employees directly report to Grant Nguyen
SELECT COUNT (EmployeeId)
FROM [Red30Tech].[dbo].[EmployeeDirectory$]
WHERE [Manager] = 42 

-- Return information of employees directly report to Grant Nguyen
SELECT [EmployeeId], [Last name], [Manager]
FROM [Red30Tech].[dbo].[EmployeeDirectory$]
WHERE [Manager] = 42 

-- Option 2 Recursive CTE
WITH Direct_report AS (
	-- The anchor cluse returns manager (Grant Nguyen) information
	SELECT [EmployeeId], [Last name], [Manager]
	FROM [Red30Tech].[dbo].[EmployeeDirectory$]
	WHERE [EmployeeID] = 42
	UNION ALL
	-- The recursive clause returns all the employees whom their manager is Grant Nguyen
	SELECT e.[EmployeeId], e.[Last name], e.[Manager] as [Manager Id]
	FROM [Red30Tech].[dbo].[EmployeeDirectory$] as e
	INNER JOIN Direct_report as cte1
		ON cte1.[EmployeeID] = e.[Manager]
	)
SELECT * 
FROM Direct_report
WHERE [EmployeeId] != 42 --remove first row (Grant Nguyen
```




Problem 3: Retrieve the following information with CTE from the “Inventory” table of items with less than average amount of product left in stock: ProdCategory, ProdNumber, and ProdName

```sql

SELECT TOP (4) * FROM [Red30Tech].[dbo].[Inventory$]

WITH average_stock_CTE (Avg_stock) AS (
	SELECT AVG ([In Stock])
	FROM [Red30Tech].[dbo].[Inventory$]
)
SELECT [ProdName], [ProdNumber], [ProdCategory], [In Stock], [Avg_stock]
FROM [Red30Tech].[dbo].[Inventory$], average_stock_CTE
WHERE [In Stock] <= [Avg_stock]
```

---
__Window function (ROW_NUMBER, LAG, LEAD, RANK, DENSE_RANK)__

Problem 1:  Retrieve the most recent order by customers from OnlineRetailSale$ table.

```sql
-- Note: Window function is executed after WHERE clause in the SELECT statement. 
-- To apply a condition on the output of a window function, we can wrap it inside a CTE.

SELECT TOP (4) * FROM [Red30Tech].[dbo].[OnlineRetailSales$]

WITH row_number_cte AS (
SELECT [CustName], [OrderDate], 
	ROW_NUMBER() OVER (PARTITION BY [CustName] ORDER By [OrderDate]) AS order_index
FROM  [Red30Tech].[dbo].[OnlineRetailSales$])

SELECT *
FROM row_number_cte
WHERE order_index = 1
```


Problem 2: Retrive the following information for the top 3 orders based on total amount purchased by "Boehm Inc":  OrderNum, OrderDate, ProdCategory, ProdName, and Order Total

```sql

WITH order_sorted_CTE AS (
	SELECT [OrderNum], [OrderDate], [ProdCategory], [ProdName], [CustName], [Order Total],
	ROW_NUMBER() OVER (PARTITION BY [ProdCategory] ORDER BY [Order Total] DESC) AS total_amount_index
	FROM [Red30Tech].[dbo].[OnlineRetailSales$]
	WHERE [CustName] = 'Boehm Inc.'
	)
SELECT * 
FROM order_sorted_CTE
WHERE total_amount_index <=3
```


Problem 3: Retrieve the following information for the product “drone”: OrderDate, Quantity, Quantities from the last five orders. Both daily and individual order.


```sql
SELECT TOP (4) * FROM [Red30Tech].[dbo].[OnlineRetailSales$] WHERE ProdCategory LIKE '%drone%'
-- ProdCategory is "Drones"

-- Individual order
SELECT OrderDate, Quantity, 
	LAG([Quantity], 1) OVER (Partition BY (ProdCategory) ORDER BY [OrderDate] DESC) AS previous1_quantity,
	LAG([Quantity], 2) OVER (Partition BY (ProdCategory) ORDER BY [OrderDate] DESC) AS previous2_quantity,
	LAG([Quantity], 3) OVER (Partition BY (ProdCategory) ORDER BY [OrderDate] DESC) AS previous3_quantity, 
	LAG([Quantity], 4) OVER (Partition BY (ProdCategory) ORDER BY [OrderDate] DESC) AS previous4_quantity, 
	LAG([Quantity], 5) OVER (Partition BY (ProdCategory) ORDER BY [OrderDate] DESC) AS previous5_quantity 

FROM [Red30Tech].[dbo].[OnlineRetailSales$]
WHERE ProdCategory = 'Drones'
ORDER BY OrderDate DESC

-- Daily orders (aggregated daily)
-- There are multiple orders in a day -> employ CTE to aggregate the orders' quantity that occur in a single day
WITH daily_aggregate_orders_cte AS (
SELECT OrderDate, SUM(Quantity) AS Quantity_agg_daily
FROM [Red30Tech].[dbo].[OnlineRetailSales$]
GROUP BY OrderDate, ProdCategory
HAVING ProdCategory = 'Drones'
)
SELECT *,
	LAG(Quantity_agg_daily, 1) OVER ( ORDER BY [OrderDate] DESC) AS previous1_quantity,
	LAG(Quantity_agg_daily, 2) OVER ( ORDER BY [OrderDate] DESC) AS previous2_quantity,
	LAG(Quantity_agg_daily, 3) OVER ( ORDER BY [OrderDate] DESC) AS previous3_quantity, 
	LAG(Quantity_agg_daily, 4) OVER ( ORDER BY [OrderDate] DESC) AS previous4_quantity, 
	LAG(Quantity_agg_daily, 5) OVER ( ORDER BY [OrderDate] DESC) AS previous5_quantity 
FROM daily_aggregate_orders_cte
```

Problem 4: Retrieve the first three attendees from each states (table ConventionAttendees$)

```sql
SELECT TOP (4) * FROM [Red30Tech].[dbo].[ConventionAttendees$]

WITH ranked_registeDate_CTE AS (
SELECT *,
	RANK() OVER (PARTITION BY (State) ORDER BY ([Registration Date])) AS ranked_registered
FROM [Red30Tech].[dbo].[ConventionAttendees$]
)
SELECT *
FROM ranked_registeDate_CTE
WHERE ranked_registered <=3
```


