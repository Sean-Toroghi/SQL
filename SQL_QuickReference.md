<h1> SQL quick reference</h1>

<a name = 'up'>Table of contents</a>

__General topics__

- [SQL logical process order in SQL](#logicorder)

__Specific topics__
- [Aggregation function](#agg)
- [Common table expression (CTE)](#cte)
- [Windows function](#window)

- [Subquery](#subq)
- [Retrive/analyze data](#rad)
- [Date-Time manipulation](#date)




Resources
- [link 1](https://mode.com/sql-tutorial)
- [link 2](https://www.w3schools.com/sql/sql_case.asp)
- [link 3](https://www.techonthenet.com/sql/index.php)
- [Book: SQL Query Design Patterns and Best Practices](https://learning.oreilly.com/library/view/sql-query-design/9781837633289/)


--- 
# General topics

<br>
## <a name = 'logicorder'>Logical process order in SQL</a>

Every SQL query is processed in the following order: `FROM` -> `WHERE`   -> `GROUP BY`   -> `HAVING`    ->  `SELECT`   -> `ORDER BY`   -> `OFFSET FETCH` (`LIMIT FETCH`)  

---
# Specific topics
---

## <a name = 'window'>Window function</a>

The window function performs a query over a subset of the database (window). It can be used in SELECT and ORDER BY clauses exclusively.

```SQL
[Window function](operated_column)
  OVER ([PARTIOTION BY Grouping_columns]  [ORDER BY Hierarchy_columns])

---

Function (expressions)
FILTER (WHERE predicates)
  OVER (
        [PARTITION BY expressions]
        [ORDER BY expression [NULLS FIRST | LAST]]
        [frame type BETWEEEN frame start AND frame end]
        [EXCLUDE frame exclusion]
      )
```

__Function in Window function__ are categorized into three categories:
1. Aggregation functions: `SUM(), AVG(), MIN(), MAX(), COUNT()`
2. Ranking functions: `ROW_NUMBER(), RANK(), DENSE_RANK(), PERCENT_RANK(), NTILE()`
3. Value functions: `LAG(), LEAD(), FIRST_VALUE(), LAST_VALUE(), NTH_VALUE()`

__Filter__ limits the scope the _function_ looks through. It performs only inside the Window function with no effect on the syntax outside the window function.

__OVER__ limits the window to only a subset of the query’s data set. A black _OVER()_ applies the window function to the entire dataset.
- `PARTITION BY` is the second filter option in the window function.
- `ORDER BY` clause performs different tasks based on the function used: limit visibility, or define how to evaluate the function.


__Window function vs subquery__
In many cases, subquery and window functions can be used interchangeably. The following examples show the use of the two methods to perform the same task. The first example, just requires employ an aggregate function. The second example, adds a condition. With the subquery, we require to duplicate the predicates (using it in both main and sub-queries). However, with a window function, we can just use the condition on the main query.  This makes the window function much more efficient.

__Example 1__: the goal is to add the total number of products to the query. 
```sql
--- Employ subquery
SELECT
  Product_name,
  Product_price,
  Product_category,
  Product_barcode,
  Inverntory_date,
  (SELECT COUNT (*) FROM product_info) AS count_all_products
FROM
  product_info
ORDER BY Product_name

---Window function
SELECT
  Product_name,
  Product_price,
  Product_category,
  Product_barcode,
  Inverntory_date,
  COUNT (*)
    OVER ()
      AS count_all_products
FROM
  product_info
ORDER BY Product_name
```
 
__Example 1__: the goal is to add total number of product to the query, for products added to the inventory before '2024-01-01'.
```sql
--- Employ subquery
SELECT
  Product_name,
  Product_price,
  Product_category,
  Product_barcode,
  Inverntory_date,
  (SELECT COUNT (*) FROM product_info WHERE Inventory_date <= '2024-01-01') AS count_all_products
FROM
  product_info
WHERE Inverntory_date <= '2024-01-01'
ORDER BY Product_name

---Window function
SELECT
  Product_name,
  Product_price,
  Product_category,
  Product_barcode,
  Inverntory_date,
  COUNT (*)
    OVER ()
      AS count_all_products
FROM
  product_info
WHERE Inverntory_date <= '2024-01-01'
ORDER BY Product_name
```

## Window function: PARTITIN BY in OVER clause
This is the second filter option in the window function. This method divides the dataset into parts and limits the function's visibility to rows a portions of dataset that satisfies the condition in PARTITION BY clause. 

__Example__: given a dataset for an inventory, retrieve the number of items in each category entered the inventory prior to '2024-01-01'. Here the aggregate function requires to be applied to each 'Product_category' separately.

If using subquery, the syntax becomes more complicated. We require to use two aliases for the table (one inside the subquery, and one for main query) and use a WHERE clause inside subquery to apply the partitioning filer.

```sql
SELECT
  Product_name,
  Product_price,
  Product_category,
  Product_barcode,
  Inverntory_date,
  COUNT (*)
    OVER (PARTITION BY Product_category)
      AS count_all_products
FROM
  product_info
WHERE Inverntory_date <= '2024-01-01'
ORDER BY Product_name


---Employ subquery to perform the same task. 
SELECT
  p1.Product_name,
  p1.Product_price,
  p1.Product_category,
  p1.Product_barcode,
  p1.Inverntory_date,
  (SELECT COUNT (*)
    FROM product_info as p2
    WHERE (p2.Inverntory_date <= '2024-01-01')
          AND
          (p2.Product_category = p1.Product_category)
    ) AS count_all_products
FROM
  product_info as p1
WHERE Inverntory_date <= '2024-01-01'
ORDER BY p1.Product_name
```
 
## Window function: ORDER BY in OVER clause

In the window function, the ORDER BY clause performs different tasks depending on the function:
- For order agonistic functions, _aggregate and frame offset_, the ORDER BY limits the row visible to the function.
- For _rank, row offset, and distribution_ window functions, the ORDER BY defines how the function is evaluated.

## Functions in Window function

### Functions: aggregate functions

1. __Arithmatic__
    - average
    - count
    - sum
    - min/max 
2. __Statistical__
    - variance
    - deciation
    - regression
    - inverse distribution
    - hypothetical
3. __Boolean__
    - EVERY
    - ANY
    - SOME
4. __Array aggregate__
    - Support JSON and array formats.
5. __Proprietary__
    Such as 'bitwise' and 'approximate'


### Aggregate functions: GROUP VS WINDOW 

__GROUP aggregate function__
- can be used in HAVING, SELECT, and ORDER BY
- group aggregate function sees all rows within each groups, separately. Meaning they cannot see any rows in groups other than the on it is processing at each time.
- The aggregate function is defined by GROUP BY
- In GROUP BY, the following steps are performed by SQL to deliver the results:
  1. The FROM clause gets all rows from the specified table/s, and sends them to GROUP BY
  2. GROUP BY marks rows by the specified expression, and sends them to SELECT
  3. SELECT evaluates its expressions per row group. The valid options are either values that are equal for all rows in a group, or using an aggregate function to get a single  value for each group

__WINDOW aggregate function__
- can be used in SELECT and ORDER BY
- aggregate function is defined by OVER calsue
- aggregate function only sees row/rows that are defined by the window specification.



##  Function: Rank function

For _RANK functions_, ORDER BY clause is mandatory and used to determine the basis for the rank. _PARTITION BY_ is optional for rank functions.

```sql
RANK Function ()
  OVER (
    [PARTITION BY expression]
    ORDER BY expression
      )
```

List of rank functions:
- `ROW_NUMBER`: assigns a number to each row in a partition. It increases monotonically, beginning with one. One of the classical use of _ROW_NUMBER_ is top per group retrieval task.
  ```sql
  ROW_NUMBER ()
    OVER( [PARTITION BY expression]
          ORDER BY expression
        )
  ```
  
  Example: show top 3 product categories that have the largest number of purchases, including categories wuth less than 3 different product types.

  ```sql
  WITH product_purchases AS (
                            SELECT i.category, s_.product_name, COUNT (s.purchase_date) AS count_purchase
                              FROM inventory AS i
                                LEFT OUTER JOIN sale_log AS s
                                  ON i.category = s.category
                              GROUP BY i.category, s.product_name
                            )
  , row_number_by_purchase_count AS (
          SELECT *,
                  ROW_NUMBER()
                  OVER( PARTITION BY category
                        ORDER BY count_purchase, product_name ASC
                      ) AS row_number
          FROM product_purchase
          )
  SELECT *
    FROM row_number_by_purchase_count
    WHERE row_number <= 3
  ORDER BY category ASC,
           count_purchase DESC;
  ```
  
- `NTILE`: segment a partition into equal-sized segments of size $n$ plus a segment of remaining rows if less than $n$ rows remains for the last segment. It assigns a monotonically increasing number to each segment (called tiles).
  
  ```sql
  NTILE (n)
    OVER( [PARTITION BY expression]
          ORDER BY expression
        )
  ```
  
- `RANK`
- `DENSE_RANK`


## Function: offset functions

[UP](#up)

[UP](#up)

--- <br>




[UP](#up)

---

# <a name = 'date'>Date - time manipulationL</a>

SQL is enhanced with date operators to manipulate date and time, either in the form of extracting date ad time fetures (day, week, month, year) or calculating the difference betwen two dates. some of these opertators are: `CURDATE(), DATEDIFF(), YEAR(), MONTH(), DAY()`, and `DATEFORMAT()`. 


[UP](#up)
