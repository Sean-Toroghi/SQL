# SQL
A sample of some of the tasks I perform employing SQL.

## Language
  - SQL (PostgreSQL 12.4, compiled by Visual C++ build 1914, 64-bit) 
  
## Platforms
1. PostregSQL
2. MongoDB
3. SQL on Jupyter Notebook

# PostregSQL
## Dataset
- 3 tables: "staff", "company_divisions", and "company_regions"
- Columns: 
  - staff (id integer,
      last_name varchar(100),
      email varchar(200),
      gender varchar(10),
      department varchar(100),
      start_date date,
      salary integer,
      job_title varchar(100),
      region_id int,
      primary key (id))
  - company_regions (
   region_id int,
   company_regions varchar(20),
   country varchar(20),
   primary key (region_id)
  )
  - company_divisions (
    department varchar(100),
    company_division varchar(100),
    primary key (department)
  )
  
  
## Tasks

1. __TASK 1.1:__ check the data in the table "staff"
2. __TASK 1.2:__ Counting # of employees across the company and across different groups 
3. __TASK 1.4 - statistical descriptive:__ retrieve sum of the salary paid in each department by gender, and the average amount paid. Also compute the salary variance and standard deviation across the department/gender.
4. __TASK 1.5 - Filtering and Grouping:__  Find how many employee receives salary larger than 100,000 by gender in department that their name starts with "B". Also retrieve what is the average salary paid for that bracket.
5. __TASK 2.1 :__  Retrieve name of the department, in lower case.
6. __TASK 2.2 - concatenate :__  Report the highest salary paid, its department, and job title.
7. __TASK 2.3 - create a boolean feature :__  Add a column to identify jobs that are categorized as an assistant.
8. __TASK 2.4 - text extraction :__  Report the salary of all assistant jobs, and their category and department.
9. __TASK 2.5 - text editing :__  Replace the "Assistant" in job_title to "Asst.". For example "Assistant Manager" --> "Asst. Manager".
10. __TASK 2.6 - text editing :__  Report average salary for the "Assistant" at different levels: I, II, III, and IV.
11. __TASK 2.7 - truncate:__  Report average salary (with 2 decimal) in each department.
12. __TASK 3.1 - sub-query in SELECT clause :__  Report the salary of each job, the department, and the average salary paid in the corresponded department. 
13. __TASK 3.2 - sub-query in FROM clause:__ Retrieve the average salary above $100,000, by department.
14. __TASK 3.3 - sub-query in WHERE clause:__ report the department of the person with the highest salary.
15. __TASK 3.4 - OUTER JOIN:__ report the staff name and department, for whom the "company_division" has no value.
16. __TASK 3.5 - View:__ Create a view, for future use. It should contains all the tables from "staff" table, _company_region_ from "company_regions" table, and _company_division_ from "company_divisions" table. Employ this view to count number of employees in each region.
17. __TASK 3.6 - GROUPING SETS:__ Retrieve number of employees in each region, and in each division.
18. __TASK 3.7 - ROLLUP:__ Retrieve number of employees in each region, and each region, and country.
19. __TASK 3.8 - CUBE:__ Retrieve # of employees in each division, in each region, and the total of employees in each division.
20. __TASK 3.9 - FETCH FIRST:__ Retrieve the following: 1- top 10 salaries, and 2- top 5 number of divisions.
21. __TASK 4.1 - WINDOW + AGGREGATE function:__ Retrieve information about the maximum and minimum salary paid in each department.
22. __TASK 4.2 - WINDOW + first value__ Retrieve information about the maximum and minimum salary paid in each department, employing the first value extraction method.
23. __TASK 4.3 - Rank function__  Retrieve information of the top 10 paid salary, and their ranking among the salary paid in the country the job is located in. Also, provide the information about the department, and region for the job.
24.  __TASK 4.4 - LEAD function__  Retrieve information of the top 10 salary paid, and the second highest salary paid in the department associate with that highest salary.
25.  __TASK 4.5 NTILE function__ : Partition salaries in each department into 10 buckets and retrieve the salaries in the top first bucket, for each department. 








