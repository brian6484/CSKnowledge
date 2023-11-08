# How to fine-grain domain models
Fine-grained means having more classes than tables. And we should make our domain model as fine-grained as possible for higher cohesion
and increased reusability of code.

For example, in the database (not domain model), user db table can have a homeaddress like home_street, home_city, home_zip. 
In the domain model, you *can* do the same approach, assigning 3 string variables of the User class but it is better to 
model it in an Address class, where User has a homeAddress property. 

Fine-grained classes also increases type safety and behaviour. For e.g, normally I have assigned email address
as a string value property of User class. But we can actually define a separate EmailAddress class. How does this improve type
safety? By using a dedicated EmailAddress instead of a string, you have created a distinct data type. So the compiler can check
type safety by ensuring that you only assign and compare objects of **same data type**. In 1 line, you can now only assign
email address to this specific data type and if you assign email address to a string, it will raise an error.

It also promotes encapsulation, where methods specific to that class can be implemented within that class.


