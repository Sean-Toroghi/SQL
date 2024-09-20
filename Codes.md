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





