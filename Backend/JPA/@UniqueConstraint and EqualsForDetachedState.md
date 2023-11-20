# Overriding equals() method for comparing entities in detached state
So for entities in detached state, once the EM closes PC, the reference to the instance is still present.
But if it is made persistent again, the JVM now allocates a different in-memory address for this reference so object identity (==) will not work.
But as mentioned in [here](https://github.com/brian6484/CSKnowledge/blob/main/Backend/JPA/JavaIdentityAndEquality.md) in JVM section, the db equality and
object equality still works. So can we use either of these 2?

## DONT use db equality (i.e. same pk value)
What if we implement equals() to compare just the db identifier (pk)? Even if it is detached or not it will have the same pk value so can we do that?
One big problem with this is that id values are not assigned by Hibernate until the instances become persistent (unless it is post-insert strategy 
like TABLE or AUTO but still not a good way). So if a transient instance is added to a Set and we persist it, its hash value would change.

## Use business key
If you need reminder of business key, it is [here](https://github.com/brian6484/CSKnowledge/blob/main/Backend/JPA/@IdonNaturalKeyVSSurrogateKey.md). Business key is what we humans (not db that uses surrogate key) can use to uniquely identify an instance. Business key's role is to compare entities in detached state.

## Example
Let's say in User table, we can uniquely differentiate each user entity from one another via his username. It is always required and is unique db constraint and is rarely changed. We set the business key with @UniqueConstraint annotation within @Table annotation.

```java
@Entity
@Table(
    name = "USERS",
    uniqueConstraints = @UniqueConstraint(name = "UniqueUsername", columnNames = "USERNAME")
)
public class User {

    @Override
    public boolean equals(Object other) {
        if (this == other) return true;
        if (other == null) return false;
        if (!(other instanceof User)) return false;
        User that = (User) other;
        return this.getUsername().equals(that.getUsername());
    }

    @Override
    public int hashCode() {
        return getUsername().hashCode();
    }

    // Other class members and methods go here
}
```

A side note is that we are using getter methods of "other" reference via getter methods. Cuz that other ref might be a Hibernate proxy, which we cant directly access the instance variable.

Now we can compare entities in detached state properly 
