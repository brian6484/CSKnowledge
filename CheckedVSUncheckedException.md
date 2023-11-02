# Checked and unchecked exception
## Checked exception
They are exceptions that Java compiler needs you to explicity handle them in your code. They are checked during compilation time.
You can handle by either:

1) catching them with try/catch 
2) declare the exception in the method's throws clause

For example
* IOException - error with input/output operations 
* ParseException - error when parsing data like dates or numbers

## Unchecked exception
Also known as *runtime exceptions*, these exceptions literally happen at runtime cuz of programmer's mistakes, logic issues, etc.
So we can't be Dr.Strange and expect them to happen in the future so we don't have to handle explicitly like checked exception.

For example
* NullPointerException - trying to access a method/field on a null object
* IllegalArgumentException - when trying to put invalid argument into a method
