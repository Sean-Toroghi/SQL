# Sub-Queries - Examples

## Example 1. Given two tables 'Departments' and 'Employees', compute the average salary for each department and identify employees who have a salary higher than the average salary in their respective department.

```SQL

--- 1. explore tables
SELECT *
FROM Departments
LIMIT 3;
--->>> features: DepartmentID	DepartmentName
SELECT *
FROM Employees
LIMIT 3;
--->>>features: EmployeeID	FirstName	LastName	DepartmentID	Salary	HireDate
 

--- 2. department ID: average salary in each department
SELECT DepartmentID, AVG(Salary) as Average_salary
FROM Employees
GROUP BY DepartmentID 

--- 3. department name: average salary in each department
SELECT d.DepartmentName, AVG(e.Salary) as Average_salary
FROM Employees as e
    LEFT JOIN Departments as d 
    ON d.DepartmentID == e.DepartmentID
GROUP BY d.DepartmentName 

--- 4. in Employees table, add avg_dep_salary feature
SELECT  e.DepartmentID, e.EmployeeID, e.FirstName, e.LastName, e.Salary, a.Average_salary
FROM Employees as e
LEFT JOIN (SELECT DepartmentID, AVG(Salary) as Average_salary
            FROM Employees
            GROUP BY DepartmentID) as a
ON e.DepartmentID = a.DepartmentID

--- 5. Pick employees with salary higher than the avg salary in their department (add where clause)
SELECT  e.DepartmentID, e.EmployeeID, e.FirstName, e.LastName, e.Salary, a.Average_salary
FROM Employees as e
LEFT JOIN (SELECT DepartmentID, AVG(Salary) as Average_salary
            FROM Employees
            GROUP BY DepartmentID) as a
ON e.DepartmentID = a.DepartmentID
WHERE e.Salary > a.Average_salary
```
