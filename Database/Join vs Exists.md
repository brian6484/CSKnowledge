Unlike join which returns all matching rows
```sql
-- Returns permission details
SELECT gp.* FROM group_permissions gp
JOIN user_groups ug ON gp.group_id = ug.group_id
WHERE ug.user_id = 123;

-- Result: All permission rows
permission | group_id | resource_id
-----------|----------|------------
read       | 1        | PROJ-123
write      | 1        | PROJ-123
admin      | 2        | PROJ-456
```

exists just checks if there is the data **exists**. It doesnt return the data but just checks if it exists.
```sql
-- Returns permissions WHERE user has that group
SELECT gp.* FROM group_permissions gp
WHERE EXISTS (
    SELECT 1 FROM user_groups ug 
    WHERE ug.group_id = gp.group_id 
    AND ug.user_id = 123
);

-- Same result, but EXISTS stops as soon as it finds a match
```

for example,
