## How to use generics

## Class & Interface declaration
![gen1](https://github.com/brian6484/CSKnowledge/assets/56388433/27321144-76a8-46a6-ac62-5094f5d1d062)

You can declare the type parameter like <your_type> and this type parameter's scope is valid within the { ... } curly braces.
This means T can be referenced anywhere inside these curly braces.

```java
public class ClassName <T> { ... }
public interface InterfaceName <T> { ... }
```

You can declare 2 generic types like a hashmap.
```java
public class ClassName <T, K> { ... }
public interface InterfaceName <T, K> { ... }
 
// HashMap의 경우 아래와 같이 선언되어있을 것이다.
public class HashMap <K, V> { ... }
```

### Using this generic class to instantiate object
Since we have the recipe (class is recipe to cook objects muahaaha), how do we use it to instantiate objects? Remember it is
the **users that declare the type** when instantiating.

```java
public class ClassName <T, K> { ... }
 
public class Main {
	public static void main(String[] args) {
		ClassName<String, Integer> a = new ClassName<String, Integer>();
	}
}
```

So here, T becomes String and K becomes Integer.

### Only **reference types** can be used as type parameters
We **cannot use primitive types** like int,char,double,etc. We can only use **reference types** like Integer, Double ,etc
which are wrapper classes of those primitive types. Since it is only reference type, we can declare a user-declared class
and put it as type parameter like <Student> or <Team>.



