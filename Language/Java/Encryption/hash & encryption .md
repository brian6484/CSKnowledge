## Diff
Hash like SHA256 is a **one-way encryption** whilst encryption is **two-way encryption**. 

## Hash
It changes a plain text into a **fixed length** of encrypted text, called *hash*. For example. SHA256 generates 256 bit
(32 byte) hash.

```java
public class HashExample {

    public static byte[] generateSHA256Hash(String input) throws NoSuchAlgorithmException {
        MessageDigest digest = MessageDigest.getInstance("SHA-256");
        return digest.digest(input.getBytes(StandardCharsets.UTF_8));
    }

    public static void main(String[] args) {
        try {
            String text = "Hello, World!";
            byte[] hash = generateSHA256Hash(text);  // Generate SHA-256 hash
            System.out.println("SHA-256 Hash: " + Arrays.toString(hash));
        } catch (NoSuchAlgorithmException e) {
            System.err.println("Error: " + e.getMessage());
        }
    }
}
```

### Salt
Security with hashing is not entirely perfect cuz you can technically keep trying until you "guess" the original text.
So we wanna add a random value (**salt**) and combine it with our original data.
