<h1> Advance SQL</h1>
References
- PostSQL
- [link](https://www.linkedin.com/learning/advanced-sql-for-data-scientists-13972889)

# user-defined functions
Beyound pre-defined funtions in SQL (e.g. SELECT, UPDATE, DELETE), users can define new functions (such as creating a function to roleback or commit). There are couple of ways to define customized function is SQL
1. employ `CREATE FUNCTION` statement
2. employ `CREATE OR REPLACE FUNCTION `, which first check if a function exist it will update the definition, and if not exist it creates it
3. employ `DROP FUNCITON` and `CREATE FUNCTON`, here we first delete an existing function and create new function (that has a  new identifier)

__Example__ Create a harmonic mean $\frac{2xy}{x+y}$ function.

```SQL

```