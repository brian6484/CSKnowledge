## Diagonsis
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

4) there could be missing indexes on the columns that is used by *where* clause
```sql
-- BAD: No index on user_id - scans 1 million rows
SELECT * FROM permissions WHERE user_id = 12345;

-- Should have index:
CREATE INDEX idx_user_id ON permissions(user_id);
```

we can check if theres indexes via **EXPLAIN**, [which literally explains the sql query](https://github.com/brian6484/CSKnowledge/blob/main/Database/Explain%20VS%20Analyse.md) 
```
-- See what indexes exist
SHOW INDEX FROM permissions;

-- Explain a slow query to see if it uses index
EXPLAIN SELECT * FROM permissions WHERE user_id = 12345;
-- Look for "type: ALL" (bad - full table scan)
-- Want to see "type: ref" or "type: index" (good - using index)
```

## Fixes for Slow JOIN Queries

### **1. Add/Optimize Indexes** (Most Common Fix)

**Problem:** Database scans entire tables instead of using indexes

**Solution:**
```sql
-- Check current indexes
SHOW INDEX FROM user_groups;
SHOW INDEX FROM group_permissions;

-- Add composite index on JOIN columns
CREATE INDEX idx_user_groups_lookup ON user_groups(user_id, group_id);
CREATE INDEX idx_group_perms_lookup ON group_permissions(group_id, resource_id);

-- For WHERE clause columns too
CREATE INDEX idx_resource ON group_permissions(resource_id, permission);
```

**Why it works:** Database can jump directly to relevant rows instead of scanning millions

---

### **2. Reduce Data Being Joined**

**Problem:** Joining too many rows (user has 50 groups, each with 1000 permissions)

**Solutions:**

**a) Filter earlier:**
```sql
-- BAD: Join everything, then filter
SELECT * FROM group_permissions gp
JOIN user_groups ug ON gp.group_id = ug.group_id
WHERE ug.user_id = 123;

-- GOOD: Filter first, then join
SELECT * FROM group_permissions gp
JOIN (SELECT group_id FROM user_groups WHERE user_id = 123) ug 
ON gp.group_id = ug.group_id;
```

**b) Limit group memberships:**
- Audit users with 40+ groups
- Clean up unused group assignments
- Set limits on groups per user

---

### **3. Cache Permission Results**

**Problem:** Checking same permissions repeatedly

**Solution:**
```java
// Application-level caching
@Cacheable(value = "userPermissions", key = "#userId")
public List<Permission> getUserPermissions(Long userId) {
    // Expensive DB query here
}
```

**How it helps:**
- First check: 3 seconds (DB query)
- Subsequent checks: <10ms (from cache)
- Set TTL (e.g., 5 minutes) to balance freshness

---

### **4. Denormalize/Pre-compute Permissions**

**Problem:** Computing permissions on every request is expensive

**Solution:** Create a flattened permission table

```sql
-- New table: pre-computed permissions
CREATE TABLE user_all_permissions (
    user_id INT,
    resource_id VARCHAR(255),
    permission VARCHAR(50),
    source VARCHAR(50), -- 'direct' or 'group_name'
    PRIMARY KEY (user_id, resource_id, permission)
);

-- Populate it (run as background job)
INSERT INTO user_all_permissions
SELECT ug.user_id, gp.resource_id, gp.permission, 'inherited'
FROM user_groups ug
JOIN group_permissions gp ON ug.group_id = gp.group_id
UNION
SELECT user_id, resource_id, permission, 'direct'
FROM user_permissions;

-- Now permission check is simple
SELECT * FROM user_all_permissions 
WHERE user_id = 123 AND resource_id = 'PROJECT-456';
```

**Trade-off:** Storage space for speed, must rebuild when permissions change

---

### **5. Query Optimization**

**Problem:** JOIN order or query structure is inefficient

**Check with EXPLAIN:**
```sql
EXPLAIN SELECT * FROM group_permissions gp
JOIN user_groups ug ON gp.group_id = ug.group_id
WHERE ug.user_id = 123;
```

**Optimizations:**
- Use `STRAIGHT_JOIN` to force join order
- Rewrite as EXISTS instead of JOIN
- Break complex query into simpler parts

```sql
-- Instead of complex JOIN
-- Use EXISTS (sometimes faster)
SELECT * FROM group_permissions gp
WHERE EXISTS (
    SELECT 1 FROM user_groups ug 
    WHERE ug.group_id = gp.group_id 
    AND ug.user_id = 123
);
```

---

### **6. Database-Level Fixes**

**a) Update statistics:**
```sql
ANALYZE TABLE user_groups;
ANALYZE TABLE group_permissions;
```

**b) Increase memory:**
```sql
-- MySQL: Increase join buffer
SET GLOBAL join_buffer_size = 256M;
```

**c) Partition large tables:**
```sql
-- If permissions table has 100M rows
ALTER TABLE group_permissions 
PARTITION BY HASH(group_id) PARTITIONS 10;
```

---

### **7. Application Architecture Changes**

**a) Lazy loading:**
- Don't check all permissions upfront
- Check only when user tries specific action

**b) Permission service:**
- Dedicated microservice for permissions
- Can scale independently
- Better caching strategy

**c) Event-driven:**
- When group membership changes, update cache
- Avoid checking DB every time

---

## Priority Order for Your Scenario

**Interview Answer Structure:**

> "For slow JOIN queries causing permission checks to lag, I'd approach it in this order:
>
> **Immediate (hours):**
> 1. Add indexes on JOIN columns (user_id, group_id)
> 2. If there are too many where conditions, its a waste to create index for each column. Instead u should do 
```
"For WHERE clauses with many columns, I'd:

Identify the most frequently run queries from slow query logs
Create composite indexes on the 2-3 most selective columns
Order columns by selectivity (most filtering first) like (user_id,group_id) or (user_id, order_id)
Verify with EXPLAIN that the index is being used

I'd avoid over-indexing - too many indexes slow down writes and waste space. Generally 2-3 columns per composite index is optimal."
```
> 3. Run ANALYZE TABLE to update statistics
> 4. Enable application-level caching
>
> **Short-term (days):**
> 4. Optimize the query itself - filter before joining
> 5. Audit users with excessive group memberships
>
> **Long-term (weeks):**
> 6. Consider denormalized permission table
> 7. Implement async permission recalculation
>
> I'd measure impact after each change using slow query logs and access logs to confirm improvement."

---



