## NPE
It is raised when you try to *use* an object reference that has not been initialised yet (null). But there are impt cases
that you have to know when NPE is not raised, even when obj reference is null.

## Causes when NPE is raised
### Calling a method on a null reference
```java
String str = null;
int length = str.length();  // Throws NullPointerException
```

### accessing or modifying an attribute of null reference
```java
MyClass obj = null;
obj.attribute = 10;  // Throws NullPointerException
```

### accessing an element of an array whose reference is null
```java
int[] arr = null;
int value = arr[0];  // Throws NullPointerException
```

### throwing null like you were correctly throwing a throwable value
```java
Throwable t = null;
throw t;  // Throws NullPointerException
```

### using null in a collection
```java
List<String> list = null;
list.add("Hello");  // Throws NullPointerException
```

## when NPE is not caused (very impt)
### (VERY IMPT) passing a null reference as **argument of method**
```java
public void printLength(String s) {
    // If s is null and we don't try to use it here, no exception
}

printLength(null);  // No exception here
```

### assigning a null value
```java
String str = null;  // No exception here
```

### checking if object is null
```java
if (str == null) {
    System.out.println("str is null");  // No exception here
}
```

### (impt) storing a null in a collection
only when you try to dereference it (deref means accessing object's method/field through its reference) 

```java
List<String> list = new ArrayList<>();
list.add(null);  // No exception here
```

```java
String str = list.get(0);
str.length();  // Throws NullPointerException because str is null
```

