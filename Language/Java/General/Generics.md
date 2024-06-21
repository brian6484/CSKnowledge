# Generics
## What is generics?
Well the pure English meaning that we know is 'general', which hints that this value can have various data types **without depending on the data format**.
It also means it is specified *outside the class* rather than internally.

What does the above sentences mean? Let's take example of an arraylist. We assign the data type **outside the class** by the user (us) as we are instantiating the object.

```java
ArrayList<Integer> list1 = new ArrayList<Integer>();
ArrayList<String> list2 = new ArrayList<Integer>();
```

Like we declare the type enclosed by these arrow brackets<type>. 

## Why generics?
But what if we want to support *multiple* data types and not be restricted to just one **SPECIFIC** data type per class? Making 1 class for each data type is inefficient so the solution to that is using **GENERIC**. So as mentioned, it is the users that declare the data type **outside the class**. 

## More precise explanation 
To be more precise, generics is a compile-type feature that checks for type safety **during compile time** and if it passes this test, the type info is not rememebered **during runtime** via the process of **type erasure**. For example, when compiling, Java compiler checks if you wrongly put a String inside a List<Integer> or not. Then at runtime, this generic type info is *erased*. The bytecode just contains the raw types and some casts. For example, List<Integer> is changed to List in bytecode.

## Advantages
### diff data type but same function
If there are 2 or more classes that have different data types (e.g. String, float, etc) but have same operating function, then they can be grouped into 1 single common class. This improves code reusability

### able to check for errors at **compile time**
This helps check for errors.



