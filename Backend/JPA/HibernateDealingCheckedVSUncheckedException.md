# Checked and unchecked exception and how Hibernate deals with them
## Checked exception
They are exceptions that Java compiler needs you to explicity handle them in your code. They are checked during compilation time.
Literally they are erros that need to be checked.
You can handle by either:

1) catching them with try/catch 
2) declare the exception in the method's throws clause

For example
* IOException - error with input/output operations 
* ParseException - error when parsing data like dates or numbers

## Unchecked exception
Also known as *runtime exceptions*, these exceptions literally happen at runtime cuz of programmer's mistakes, logic issues, etc.
So we can't be Dr.Strange and expect them to happen in the future so we don't have to handle explicitly like checked exception.
They are called unchecked cuz there is no way to check whether or not they will occur before they actually occur.

For example
* NullPointerException - trying to access a method/field on a null object
* IllegalArgumentException - when trying to put invalid argument into a method

## How Hibernate treats them
For unchecked exception (i.e. runtime exceptions), Hibernate treats them as runtime exceptions and roll back the transaction so that the
DB remains unchanged (consistent state). This exception propogates all the way to the code that causes this issue and you can catch it there or
let it propagate further up in the call stack.

For checked cexception, Hibernate actually wraps them into a `HibernateException` or a `PersistenceException`, which are runtime exceptions.

### Why does Hibernate make all exceptions, icnluding checked excpetions, as runtime exceptions?
To maintain a consistent and simplified way of dealing with exceptions.  
