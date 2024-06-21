## How to use generics
read [theory](https://github.com/brian6484/CSKnowledge/blob/main/Language/Java/General/Generics.md) before reading this.


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

## Using generic class
```java
class ClassName<K, V> {
    private K first;    // Generic type K
    private V second;   // Generic type V
    
    void set(K first, V second) {
        this.first = first;
        this.second = second;
    }
    
    K getFirst() {
        return first;
    }
    
    V getSecond() {
        return second;
    }
}

class Main {
    public static void main(String[] args) {
        ClassName<String, Integer> a = new ClassName<String, Integer>();
        
        a.set("10", 10);
 
        System.out.println("  first data : " + a.getFirst());
        // Printing the runtime type of the returned variable
        System.out.println("  K Type : " + a.getFirst().getClass().getName());
        
        System.out.println("  second data : " + a.getSecond());
        // Printing the runtime type of the returned variable
        System.out.println("  V Type : " + a.getSecond().getClass().getName());
    }
}

```

the output will be
```
  first data : 10
  K Type : java.lang.String
  second data : 10
  V Type : java.lang.Integer
```

So as you see, when you instantiate a generic class from an **external class** (Main class in this case), you declare the type parameter in <>. 

Next we go to a more complicated case - generic methods.




