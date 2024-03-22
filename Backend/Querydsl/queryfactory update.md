## Precaution
I was wondering why update statement is not being sent properly.

You need to invoke **execute()** for your queryFactory’s update statement, 
just like you need fetch() for queryFactory’s select statement.
