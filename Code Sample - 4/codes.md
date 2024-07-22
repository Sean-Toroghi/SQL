<h1> Advance SQL</h1>
References
- PostSQL
- [link](https://www.linkedin.com/learning/advanced-sql-for-data-scientists-13972889)

# Customized function

## user-defined functions
Beyound pre-defined funtions in SQL (e.g. SELECT, UPDATE, DELETE), users can define new functions (such as creating a function to roleback or commit). There are couple of ways to define customized function is SQL
1. employ `CREATE FUNCTION` statement
2. employ `CREATE OR REPLACE FUNCTION `, which first check if a function exist it will update the definition, and if not exist it creates it
3. employ `DROP FUNCITON` and `CREATE FUNCTON`, here we first delete an existing function and create new function (that has a  new identifier)

__Example__ Create a harmonic mean $\frac{2xy}{x+y}$ function.

Given a dataset of a hotel, compute harmonic mean for two occupancy metrics: 'bed_occupancy_rate' and  'room_occupancy_rate'.
```SQL
---define harmonic_mean function
CREATE FUNCTION harmonic_mean (x numeric, y numeric) RETURN numeric
AS
  $$
SELECT ((2*x*y)/(x+y) :: numeric)

$$ LANGUAGE SQL;

---Execute function 
harmonic_mean(22, 33)
```

## Function overloading
A customized function could be defined multiple times, with different set of parameters. One example is when we want to define a function, but not sure if the datatyypes is consistent. 

__Example__ define a function that computes harmonic mean of two variables. The data type for the variables could be either numeric or string
```sql
---define harmonic_mean function with numeric inputs
CREATE OR REPLACE FUNCTION harmonic_mean (x numeric, y numeric) RETURN numeric
AS
  $$
  SELECT ((2*x*y)/(x+y) :: numeric)
  $$ LANGUAGE SQL;

---define harmonic_mean function with string inputs
CREATE OR REPLACE FUNCTION harmonic_mean (x test, y test) RETURN numeric
AS
  $$
  SELECT ((2 * x::numeric * y::numeric)/(x:numeric + y::numeric) :: numeric)
  $$ LANGUAGE SQL;
```
