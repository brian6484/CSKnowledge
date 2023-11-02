# Why does Hibernate Need a NoArgsConstructor?

Chapter 3.2.3

## Explanation
Hibernate and JPA both require a NoArgsConstructor for every persistent class. If you don't provide them with one, they will
create one using a default Java constructor. But why?

Hibernate calls the persistent classes through Java Reflection API and creates instances of those classes using those constructors.
This is cuz Hibernate uses these runtime-generated proxy classes (that extend or implement your real classes) for performance optimisation.
For example, for lazy loading.
