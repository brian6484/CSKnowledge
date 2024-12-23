## Where
It filters rows **before any aggregation**. For example
```sql
SELECT department, salary
FROM employees
WHERE salary > 50000;
```

It filters out rows that have salary less than 50k before any grouping or aggregation.

## Having
It filters rows **after all the grouping & aggregation has completed**. For example
```sql
SELECT department, AVG(salary) AS avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 60000;
```
this groups by department, calcs average, **and then** filters the rows out that have average salary less than 60k.

