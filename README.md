<h1>Sample code - Exercise set 1</h1>

- Datasets:
  - Library dataset
  - Restaturant dataset
- Engine: SQLiteStudio

## Dataset 1: Library

__Problem 1__ : create  a list of customer: first name, last name, email address
Sort them alphabetically by last name
'''sql
SELECT LastName as 'Last name' , FirstNAme as 'First name', Email, (COUNT(Email))
FROM Customers
GROUP BY Email
ORDER BY LastName
'''
