# SQL Code examples

- [Date and time manipulation](#date)



---
<a id= 'date'># Date and time manopulation examples<\a>

[source link(https://www.linkedin.com/learning/practice-it-advanced-sql/introduction-to-datetime-manipulation?resume=false&u=95231449)

## Example 1. Given th'Eevents' table,  find the longest gap (in days) between two consecutive events. Retrieve the event names and the duration of the longest gap.
```sql
--- 1. overview data
SELECT *
FROM Events
LIMIt 3
--- >> EventID	EventName	EventDate	EventLocation	TicketPrice

--- 2. create lead of 1 event
SELECT *, 
    LEAD(EventDate, 1) OVER (ORDER BY EventDate) as lead_to_next
FROM Events

--- 3. Create gap features and sort by gap descending
WITH lead_tbl AS (
    SELECT EventDate,  LEAD(EventDate, 1) OVER (ORDER BY EventDate) as lead_date
    FROM Events
    )

SELECT e.EventID, e.EventName, e.EventDate, e.EventLocation, e.TicketPrice,
    l.lead_date as next_event_date, 
    JULIANDAY(l.lead_date) - JULIANDAY(e.EventDate) as gap

FROM Events as e
LEFT JOIN lead_tbl as l
    ON l.EventDate = e.EventDate
ORDER BY gap DESC
```

---
## Example 2. Given the 'Events' table, calculate the total revenue earned for each month in the database. Retrieve the month and year along with the total revenue.

```sql
--- 1. overview data
SELECT *
FROM Events
LIMIt 3
--- >> EventID	EventName	EventDate	EventLocation	TicketPrice

--- 2. extract year and month
SELECT EXTRACT(YEAR FROM EventDate) as year, EXTRACT(MONTH FROM EventDate) as month
FROM Events;
SELECT strftime('%Y', EventDate) as year, strftime('%m', EventDate) as month
FROM Events;

--- 3. 
SELECT strftime('%Y', EventDate) as year, strftime('%m', EventDate) as month, SUM(TicketPrice) as revenue
FROM Events
GROUP BY year, month

```
