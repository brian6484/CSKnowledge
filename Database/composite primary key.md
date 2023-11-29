# Composite Primary Key
## Diff between PK and CPK
PK is a single column where this single attribute can uniquely identify a record in db.

CPK consists of 2 or more columns and is used when a single column is insufficient to uniquely identify a record in db so multiple columns are needed.
These multiple columns can consist of FKs of other tables. For example, in enrollment table, student_id or course_id alone is not enough to uniquely identify
which student is enrolled in which course. But if we use a composite primary key that includes both sutdent_id and course_id, this key can.

### Composite Primary Key:

```sql
CREATE TABLE Enrollments (
    student_id INT,
    course_id INT,
    enrollment_date DATE,
    PRIMARY KEY (student_id, course_id),
    -- other columns
);
```

### Single-Column Primary Key (`enrollment_id`):

```sql
CREATE TABLE Enrollments (
    enrollment_id INT PRIMARY KEY,
    student_id INT,
    course_id INT,
    enrollment_date DATE,
    -- other columns
);
```

## why not just use PK?
Why don't we just create a enrollment_id pk instead of CPK? Well, it is qustion of using natural key with business logic (student_id + course_id) or
a surrogate key with no business logic (enrollment_id). But I think surrogate key is better? Cuz maybe student_id or course_id can change? Of course it is rare
cuz they are PK themselves but maybe but surrogate key has no busienss logic and is independent from other FKs.

