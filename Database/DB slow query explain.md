1) see db slow query logs
```sql
-- First, see if slow query log is enabled
SHOW VARIABLES LIKE 'slow_query_log%';

-- Find the log file location
SHOW VARIABLES LIKE 'slow_query_log_file';
-- Returns something like: /var/lib/mysql/slow-query.log

-- Check threshold (queries slower than this get logged)
SHOW VARIABLES LIKE 'long_query_time';
```

2) see active queries that are still running
```sql
-- See what's running RIGHT NOW
SHOW PROCESSLIST;

-- Look for queries with high "Time" value
-- Example output:
Id | User | Time | State | Info
45 | jira | 8    | Sending data | SELECT * FROM user_permissions WHERE...
```

3) see if theres a table lock. One query is locking a table, blocking others from reading/writing. Normally read requests dont block each other but its a single write request that blocks
all the other read requests, making the query slow and long.

5) there could be missing indexes on the columns that is used by *where* clause
```sql
-- BAD: No index on user_id - scans 1 million rows
SELECT * FROM permissions WHERE user_id = 12345;

-- Should have index:
CREATE INDEX idx_user_id ON permissions(user_id);
```

we can check if theres indexes via **EXPLAIN**, which literally explains the sql query 
```
-- See what indexes exist
SHOW INDEX FROM permissions;

-- Explain a slow query to see if it uses index
EXPLAIN SELECT * FROM permissions WHERE user_id = 12345;
-- Look for "type: ALL" (bad - full table scan)
-- Want to see "type: ref" or "type: index" (good - using index)
```


