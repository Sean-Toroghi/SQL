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

Expressions using window functions have access to values from other rows.
Without window functions, this can only be achieved via subqueries.

Window functions can only be used in SELECT and ORDER BY clauses exclusively, after the query's data set is finalized.

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

Filter limits the scope that _function_ lookthrough over the provided _window_ with logical predicates. Here the FILTER performing only inside the _Window function_ and (unlike WHERE clasue) it has no effect on the syntax outside the window function.

A blank OVER () clause provides access to the query’s entire data set. All the other subclauses within the OVER clause limit the window to only a subset of the query’s data set.

```sql
--- Apply window function to the entire dataset
Function (expressions)
  OVER ()
```

__OVER clause__

It defines the window over the dataset, for the _function_ to be applied to. 
- PARTITION BY is the second filter option in the window function.
- ORDER BY clause in window function perform different task based on the function used. For some functions it is used to limit visibility and for other it define how to evaluate the function.


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

__Example__: given a dataset for an inventory, retrieve number of items in each category entered the enventory prior to '2024-01-01'. Here the aggregate function requires to be applied to each 'Product_category' seperately.

If using subquery, the syntax becomes mroe complecated. We requrie to use two aliaces for the table (one inside the subquery, and one for main query) and use a WHERE clause inside subquery to apply the partitioning filer.

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
 
## ORDER BY clasue

In window function, ORDER BY clause performs task depending on the function:
- For order agnsotic functions, _aggregate and frame offset_, the ORDER BY limits the row visible to the function.
- For _rank, row offset, and distribution_ window functions, the ORDER BY defines how the function is evaluated.



[UP](#up)

[UP](#up)

--- <br>

# <a name = 'rad'>Logical process order in SQL</a>



[UP](#up)
