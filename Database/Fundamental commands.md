## update
```sql
update table set column1=value1, column2=value2 where condition

update users set email='brian',status='active' where user_id=123;
```

## insert
```sql
insert into table_name (col1,col2,col3)
values (val1,val2,val3);

insert into users (name,status)
values ('brian','active');
```

## change column name
```sql
alter table table_name
change old_col new_col type;

alter table users
change email enamil_address varchar(255);
```

## altering table
```sql
alter table users
add column phone varchar(20);

## or
drop column phone;
```

## create index
```sql
create index index_name on table_name(col);
create idx_email on users(email);
```
