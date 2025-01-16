# SQL Code examples
- [Window functions](#window)
- [Date and time manipulation](#date)

---

# <a id= 'window'> Window Function<\a>

[refernce](https://github.com/LinkedInLearning/practice-it-advanced-sql-4412219/tree/main)

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
WITH quantity_lag AS 
    (SELECT *,
    LAG(Quantity, 1) OVER (PARTITION BY CustomerID ORDER BY OrderDate) as Previous_quantity
    FROM Orders)
SELECT *,
    (Quantity - Previous_quantity) as Difference_quantity
    FROM quantity_lag
```



---
# <a id= 'date'> Date and time manopulation examples</a>

[source link(https://www.linkedin.com/learning/practice-it-advanced-sql/introduction-to-datetime-manipulation?resume=false&u=95231449)

## Example 1. Given th'Eevents' table,  find the longest gap (in days) between two consecutive events. Retrieve the event names and the duration of the longest gap.
```sql
--- 1. overview data
SELECT *
FROM Events
LIMIt 3
--- >> EventID	EventName	EventDate	EventLocation	TicketPrice

--- 2. create lead of 1 event
SELECT *, 
    LEAD(EventDate, 1) OVER (ORDER BY EventDate) as lead_to_next
FROM Events

--- 3. Create gap features and sort by gap descending
WITH lead_tbl AS (
    SELECT EventDate,  LEAD(EventDate, 1) OVER (ORDER BY EventDate) as lead_date
    FROM Events
    )

SELECT e.EventID, e.EventName, e.EventDate, e.EventLocation, e.TicketPrice,
    l.lead_date as next_event_date, 
    JULIANDAY(l.lead_date) - JULIANDAY(e.EventDate) as gap

FROM Events as e
LEFT JOIN lead_tbl as l
    ON l.EventDate = e.EventDate
ORDER BY gap DESC
```

---
## Example 2. Given the 'Events' table, calculate the total revenue earned for each month in the database. Retrieve the month and year along with the total revenue.

```sql
--- 1. overview data
SELECT *
FROM Events
LIMIt 3
--- >> EventID	EventName	EventDate	EventLocation	TicketPrice

--- 2. extract year and month
SELECT EXTRACT(YEAR FROM EventDate) as year, EXTRACT(MONTH FROM EventDate) as month
FROM Events;
SELECT strftime('%Y', EventDate) as year, strftime('%m', EventDate) as month
FROM Events;

--- 3. 
SELECT strftime('%Y', EventDate) as year, strftime('%m', EventDate) as month, SUM(TicketPrice) as revenue
FROM Events
GROUP BY year, month

```


---
---

<h1>Sample code - Exercise set 1</h1>

- Datasets:
  - Library dataset
  - Restaturant dataset
- Engine: SQLiteStudio
- Source: [link](https://www.linkedin.com/learning/level-up-sql)

## Dataset 1: Library

__Problem 1__ : create  a list of customer: first name, last name, email address
Sort them alphabetically by last name
```sql
SELECT LastName as 'Last name' , FirstNAme as 'First name', Email, (COUNT(Email))
FROM Customers
GROUP BY Email
ORDER BY LastName
```


__Problem 2:__ Create a table to store customer response to invitation that includes
Associate customer ID and name, with the number of people they will bering

 ```sql

SELECT * FROM customers limit 2
SELECT * FROM reservations limit 2

SELECT r.CustomerID, c.FirstName, c.LastName, SUM (r.PartySize)
FROM Customers as c
RIGHT JOIN  reservations as r
    ON c.CustomerID == r.CustomerID
GROUP BY
    r.CustomerID
```

__Problem 3:__ Create a table to store the above information.
```sql
CREATE TABLE CustomerResponses (
CustomerID integer,
FirstName string,
LastName string,
PartySize integer)
```


__Problem 4__: Retrieve following three menus:
1. All items sorted by the price
2. Appetizers and beverages, by type
3. all items except beverages by type
```sql
SELECT * FROM dishes LIMIT 2

SELECT Type
FROM dishes
GROUP BY Type

SELECT Name, Price, Type
FROM dishes
ORDER BY type Asc, price Asc

SELECT Name, price, type
FROM dishes
WHERE type == 'Appetizer' or type == 'Beverage'
Order BY type ASc, Name Asc

SELECT Name, Price, Type
FROM dishes
WHERE type != 'Beverage'
Order By Type Asc, Name Asc
```

__Problem 5:__ Add new customer info into Customers table. Assume when Customers table was created the CustomerID was set to automatically be generated.

```sql
SELECT * FROM customers limit 2

INSERT INTO Customers ( FirstName, LastNAme, Address, City, State, Phone, Birthday, FavoriteDish)
Values (
    'Sean',
    'Toroghi',
    'Address',
    'Atlanta',
    'GA',
    '000-000-0000',
    '00/00/0000',
    Null)
```

__Problem 6__: Update a customer record in the Customers table.
```sql
SELECT *
FROM Customers
WHERE 
    FirstName == 'Sean' AND LastName == 'Toroghi'

UPDATE customers
SET 
    Address == 'New address'
WHERE 
    CustomerID == 25
```

__Problem 7:__ Remove a reservation from the dataset
```sql
--- Check the information of the customer
SELECT *
    FROM Customers
    WHERE firstname == 'Giraldo' and lastname == 'Algar'

--- check the reservation info of the customer
SELECT *
FROM Reservations
WHERE CustomerID == (
    SELECT CustomerID
    FROM Customers
    WHERE firstname == 'Giraldo' and lastname == 'Algar'
    )
ORDER BY date DESC

--- remove reservation
DELETE FROM Reservation
WHERE ReservationID == '' ---Get the reservation id from the retrieved info above
```

__Problem 8__: compute total cost of the last order placed by Yves Dell'Abbate.
```sql
SELECT * from customers where FirstName == 'Yves' and lastname == "Dell'Abbate"

SELECT * FROM orders 
WHERE CustomerID == 27
ORDER BY ORderdate Desc 
limit 2 


SELECT * FROM ORdersDishes WHERE ORDERID == 100

SELECT * 
FROM Dishes
WHERE DISHID IN (
SELECT DISHID FROM ORdersDishes WHERE ORDERID == 100
)

SELECT count(*) as "Total # of dishes", SUM(price)
FROM Dishes
WHERE DISHID IN (
SELECT DISHID FROM ORdersDishes WHERE ORDERID == 100
)
```

__Problem 9__: Retrieve list of customers with most number of orders.
```sql
SELECT * FROM orders limit 2
SELECT * FROM customers limit 2

SELECT CustomerID, COUNT(OrderID) as 'OrdersCount'
FROM Orders
GROUP BY CustomerID
ORDER By orders DESC
LIMIT 10

SELECT c.FirstName, c.LastName, c.Email, COUNT(o.OrderID) as 'OrdersCount'
FROM Orders as o
Left JOIN Customers as c
ON o.CustomerID == c.CustomerID
GROUP BY o.CustomerID
ORDER By OrdersCount DESC
LIMIT 15
```







