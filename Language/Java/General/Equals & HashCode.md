## Equals
By default, the equals() method in Object checks for reference equality. But if truly want to compare if 2 objects are same with field values, we need to override
this method

```java
class Person {
    String name;
    int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) {
            return true; // Same instance
        }
        if (obj == null || getClass() != obj.getClass()) {
            return false; // Null or different class
        }
        Person other = (Person) obj;
        return this.name.equals(other.name) && this.age == other.age;
    }

    // We'll talk about hashCode() later
}
```

## Hashcode
It produces an integer hash for an object to **optimise performance of hash-based collections** like hashmap and hashset.

## diff
So if 2 objects are equal via equal(), then the MUST have the same hash code. 
But if 2 objects have same hash code, its not guaranteed that the 2 obejcts are the same. (hash collision may occur)

This is cuz number of possible hash codes is finite but the number of obejcts that can be made are infinite. So mathematically in theory,
different objects can have the same hash code at some possibility. (hash collision)



