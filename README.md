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
