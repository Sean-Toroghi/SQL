<h1> SQL quick reference</h1>

<a name = 'up'>Table of contents</a>
- [SQL logical process order in SQL](#logicorder)
- [Windows fucntion](#window)
- [Retrive/analyze data](#rad)








--- 

<br>


# <a name = 'logicorder'>Logical process order in SQL</a>
Every SQL query is processed in the following order:
1. `FROM` clause to get source data (and evaluated based on the condition provided with `ON` and type of join). The data then are put into a single data source for the next processing task.
2. `WHERE` clause to fiter rows. At this step, each row is evaluated according the pradicate and filtered rows are passed for the next processing task.
3. `GROUP BY` clause changes structure of dataset from individual rows to row groups based on the grouping expresion. 
4. `HAVING` clause employs its logical pradicates to filter the row groups. 
5. `SELECT` clause recieves filtered rows and filtered row groups. `SELECT` evaluates each expression for every row or row group. The result is a dataset that is sent to the next precessing step.
6. `ORDER BY` clause sorts the dataset and transforms it to a cursor.
7. `OFFSET FETCH` (`LIMIT FETCH`) processes the cursor and slice it according to the provided information. 

# <a name = 'window'>Window function</a>


Window function general format:
```sql
Function (expressions)
FILTER (WHERE predicatess)
  OVER (
        [PARTITION BY expressions]
        [ORDER BY expression [NULLS FIRST | LAST]]
        [frame type BETWEEEN frame start AND frame end]
        [EXCLUDE frame exclusion]
      )
```

__Function__

__FILTER__

Filter limits the scope that _function_ lookthrough over the provided _window_ with logical predicates. Here the FILTER performing only inside the _Window function_ and has no effect on the syntax outside the window function.

If we want to apply the function to the whole dataset, the syntax will be as follow:
```sql
Function (expressions)
  OVER ()
```

__OVER clause__

It defines the window over the dataset, for the _function_ to be applied to. 
- PARTITION BY is the second filter option in the window function.


## Window function vs sub-querry
In many cases subquery and window function can be used interchagibly. Following examples show the use of the two methods, to perform the same task. The first example, just requires employ an aggregate function. The second example, adds a condition. With the subquery, we require to duplicate the predicatess (using it in both main and sub-queries). However, with window function, we can just use the condition on the main query.  This makes the window function much more efficient.

__Example 1__: the goal is to add total number of product to the query. 
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
  (SELECT COUNT (*) FROM product_info WHERE Inverntory_date <= '2024-01-01') AS count_all_products
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

## PARTITIN BY
This is the second filter option in the window function. This method divides the dtaset into parts and limits the function's visibility to rows a portions of dataset that satisfies the condition in PARTITION BY clause. 

__Example__: given a dataset for an inventory, retrieve number of items in each category entered the enventory prior to '2024-01-01'. Here the aggregate function requires to be applied to each 'Product_category_ seperately.

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
```

While for a simple task, window function and subquery are both can be used, subquery will become complicated as the task gets more 

[UP](#up)

[UP](#up)

--- <br>

# <a name = 'rad'>Logical process order in SQL</a>



[UP](#up)
