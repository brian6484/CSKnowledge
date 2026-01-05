"Normalization organizes data into separate tables to eliminate redundancy - great for transactional systems where data integrity matters. Data integrity means data is accurate, consistent and reliable.
So this helps prevent anomalies.

like for example in this denormalised db
```
Orders table:
order_id | customer_name | customer_email  | product
1        | John Smith    | john@old.com    | Book
2        | John Smith    | john@old.com    | Pen
3        | John Smith    | john@old.com    | Laptop
```
when john updates his email, the query might update just the first row and not the other rows. So how do we know which is accurate? This is called **update anomaly**. But in normalised db
```
Customers table:
customer_id | name       | email
1           | John Smith | john@new.com  ← Update ONCE

Orders table:
order_id | customer_id | product
1        | 1           | Book
2        | 1           | Pen
3        | 1           | Laptop
```
u just update once and all orders can just ref that new correct value

Denormalization adds redundancy back for performance - useful in read-heavy scenarios like analytics. At Atlassian, you might normalize user data in Jira's core database but denormalize for reporting dashboards to avoid expensive JOINs."
Trade-off: Data integrity vs Query performance
