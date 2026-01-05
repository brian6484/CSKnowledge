## explain
it explains the query but **doesnt run the query**

```sql
EXPLAIN SELECT * FROM permissions WHERE user_id = 123;
```

**Output:**
```
id | select_type | table       | type | possible_keys | key     | rows | Extra
1  | SIMPLE      | permissions | ref  | idx_user_id   | idx_user_id | 5 | Using where
```

we need to look at **type** as ref (ALL = bad, ref or index = good) which means its using index that is mentioned in **key column**. So this is to observe the query whether it uses index and whether
it is optimal

## analyse
it Recalculates table statistics and **actually updates the table stats** so EXPLAIN makes better decisions

```sql
ANALYZE TABLE permissions;
```
so we normally use after bulk inserts/updates or migrations where table is suddenly changed by a lot and need update.

## explain analyse
Shows the plan AND actually runs the query to show real performance
