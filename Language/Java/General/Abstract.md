## Abstract class
Class that cannot be instantiated and is meant to be inherited by other classes.
It can have
- abstract methods
- concrete already-implemented methods
- subclass must implement those abstract methods unless they themselves are abstract classes

### Example
```java
public abstract class OIDCVerifier {
    // Abstract method to check support for a specific OAuthType
    protected abstract boolean support(OAuthType oAuthType);

    // Method to extract Apple email from an idToken (concrete method)
    protected final String getAppleEmail(String idToken) {
        // Some implementation to extract email from idToken...
    }

    // Abstract method to verify an idToken (subclasses need to provide specific logic)
    protected final OIDCInfo verifyIdToken(String idToken) {
        // Some implementation to verify idToken...
    }

    // Abstract methods to fetch OIDC public keys and validate audience
    protected abstract OIDCPublicKeys getOIDCPublicKeys(String iss);
    protected abstract boolean audienceValid(String aud);
    protected abstract OAuthType getOAuthType();
}

```

## Abstract method
It is method without implementation. It is used by abstract class or interface that is intended to be implemented when 
another class extends it (class) or implements it (interface).

## Abstract method in abstract class
```java
public abstract class Animal {
    public abstract void makeSound();  // Abstract method with no implementation

    public void eat() {  // Concrete method with implementation
        System.out.println("Eating...");
    }
}
```

The makeSound method is abstract cuz it has no implementation. So subclasses that extend that abstract class MUST 
implement the abstract method or it needs to be declared abstract itself.

```java
public class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Bark");  // Implementing the abstract method
    }
}
```

## Abstract method in abstract interface
Since java 8, interfaces can have abstract methods and static methods 
```java
public interface Vehicle {
    void drive();  // Abstract method

    default void honk() {  // Default method with implementation
        System.out.println("Honk!");
    }
}
```
honk method is a default method that classes which implemented this interface can either
1) override it
2) use it as it is
