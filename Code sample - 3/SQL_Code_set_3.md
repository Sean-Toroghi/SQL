


# SQL code sample - set 3

## Dataset

![SQL_Excersize7](https://github.com/Sean-Toroghi/SQL/assets/50586266/4407c101-4b58-4bde-b55a-2c84534bb75a)



## SQL tasks

__Task: generate a table representing countries’ GDP for each year and their average gdp__

```sql
---1. examine data
SELECT * FROM "CountryStats" LIMIT 10
SELECT * FROM "CountryRegions" LIMIT 10

--- 2. extract gdp for each year for each country
	
SELECT country_id, EXTRACT(YEAR FROM date) as year_, gdp 
FROM "CountryStats"
ORDER BY country_id, year_
	
 
--- 3. extract average gdp for each country for each year
SELECT 
	AVG(gdp) OVER (PARTITION BY country_id) as avg_gdp
FROM "CountryStats"

---4. solution (combine 2 and 3 into a single table)
SELECT country_id, EXTRACT(YEAR FROM date) as year_, gdp,
	AVG(gdp) OVER (PARTITION BY country_id) as avg_gdp
FROM "CountryStats"
ORDER BY country_id, year_
```

---

__Task: partition data by region and showcase the most successful athletes in that region and sort them by total number of gold medals won by the athtete.__

```sql

```
