## SQL Injection

It is a security vulnerability where attacker manipulates a SQL query to gain *unauthorised* access to db, exposing sensitive data,
modifying data or executing harmful db commands

## Real life example in Korea
![download (1)](https://github.com/brian6484/CSKnowledge/assets/56388433/a9c3764d-8a4d-461a-91fd-6041f49bd474)
Actually happened in 2017 lol in 여기어떄

## Type of SQL Injection
### Error-based
![download (2)](https://github.com/brian6484/CSKnowledge/assets/56388433/286885a2-1c79-4f22-9071-ef2fffec7d94)

Most commonly used attack, that above query is the most commonly used for login. But in that user input, what if we type
```
` OR 1 = 1 --
```

Well this OR statement is True so it will execute and we comment out the remaining statement with this --. And now, attacker has
all the info of users and can do harmful stuff

### tbc 


## Solution
### Parameterised statement
Using Parameterised statement, also known as prepared statement, allows DBMS (DB management system) to **compile in advance** and waits
for user input. Even if user input is SQL injection, it will just be a string.

