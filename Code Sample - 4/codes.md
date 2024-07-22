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

__Example__: create a function that get a string and returns True if the string is palindrome

Approach 1 - python
```sql
CREATE FUNCTION check_palindrome(x text) return boolean
AS $$
  if len(x) == 0 or len(x) == 1: return True
  elif len(x) == 2 and (x[0] == x[1]): return True
  elif x == x[::-1]: return True
  else:
    return False
      
  $$LANGUAGE plpython3u;
```
Approach 2 - SQL
```sql
CREATE FUNCTION check_palindrome(x text) return boolean
AS $$
  SELECT REVERSE(x) == x
      
  $$LANGUAGE SQL;
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

## Function volatility
A functiuon has three classifications: `VOLATILE` (DEFAULT), `STABLE`, AND `IMMUTABLE`, that can be used to optimize it. 
- in `VOLATILE` mode the function is evaluated each time it is run. This modeallows the function to have any type of operation, iuncluding changing the database. Ther eis no optimization assumption for this mode.
- in `STABLE` mode we cannot modify database. It guarantees to return the same results, given the same arguments for all rows within a single statement. Based on this guarantee, we can employ some optimization to perform this type of function
- in `IMMUTABLE` mode, the function cannot modify athe database, and guarantees to return the same results given the same arguemnts in all cases (not single statement). Also optimization can be applied to this type of functions.

__Note__: any function with side effect (such as changing dataset) should be `VOLATILE`. Also any function that use a function in it that can change vbalue in a query, also needs to be `VOLATILE`.

__Example__ create IMMUTABLE function that computes harmonic meean of two input variables.
```SQL
---define harmonic_mean function
CREATE FUNCTION harmonic_mean (x numeric, y numeric) RETURN numeric
AS
  $$
SELECT ((2*x*y)/(x+y) :: numeric)

$$ LANGUAGE SQL IMMUTABLE;
```

## Create Python function in SQL!
WE can define a Python function inside PostgreSQL by using PL/Python language (a Postgres externtion): 
- first create extension `CREATE EXTENTION  pypython3u` (requires sueoruser privilege: `CREATE USER pPostgres WITH superuser`)
- create function similar to creating a customized function, by specify the python language at the end.

__Example__ create a customized function in python to return the larger number among two inputs.
```sql
CREATE FUNCTION larger_value (x integer, y integer) RETURN integer
AS $$
  if x> y:
    return x
  else:
    return y
  $$ LANGUAGE pypython3u;
```










