**Question:**
Find the number of account registrations according to the signup date. Output the year months (YYYY-MM) and their corresponding number of registrations.

```sql
select date_format(started_at, '%Y-%m') as date, count(*) as number_of_registrations
from noom_signups
group by 1;
