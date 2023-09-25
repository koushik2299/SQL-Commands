**Question 1 :**
Find the number of account registrations according to the signup date. Output the year months (YYYY-MM) and their corresponding number of registrations.

```sql
select date_format(started_at, '%Y-%m') as date, count(*) as number_of_registrations
from noom_signups
group by 1;
```
**Question 2 :Hard Amazon**

Given a table of purchases by date, calculate the month-over-month percentage change in revenue. The output should include the year-month date (YYYY-MM) and percentage change, rounded to the 2nd decimal point, and sorted from the beginning of the year to the end of the year.
The percentage change column will be populated from the 2nd month forward and can be calculated as ((this month's revenue - last month's revenue) / last month's revenue)*100.

```sql
with cte as 
(select row_number() over() as current_id,
(row_number() over() -1) as past_id
,date_format(created_at,'%Y-%m') as date,sum(value) as total 
from sf_transactions
group by 3)
select a.date , ((a.total-b.total)/b.total)*100 as revnue_diff_pct
from cte as a
left join cte as b
on a.past_id=b.current_id
order by 1
```
The CTE (Common Table Expression) named cte:

It assigns a unique current_id to each row.
It calculates a past_id by subtracting 1 from the current_id.
It formats the created_at column as a date in 'YYYY-MM' format.
It calculates the sum of value for each unique formatted date (month) using sum(value).
The main query:

It selects the formatted date as date from the CTE.
It calculates the percentage difference in revenue (revenue_diff_pct) between the current month (a) and the previous month (b). This is done using the formula: ((a.total - b.total) / b.total) * 100.
The left join connects the CTE a (representing the current month) with CTE b (representing the previous month) using the past_id and current_id columns.

Alternate Method
```sql
select date_format(created_at,'%Y-%m'),
(sum(value)-(lag(sum(value),1) over(order by created_at asc)))/
(lag(sum(value),1) over(order by created_at asc))*100 as previous_month
from sf_transactions 
group by 1
order by 1
```