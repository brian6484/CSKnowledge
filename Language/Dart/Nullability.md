## Issue
I was coding when I saw a question mark(?) right after the type like variable String? name or function return type declaration. What is this 
question mark? It is to allow null values.

In Dart, the `?` is used to indicate **nullability**. When placed after a type, it signifies that the variable or return value can either hold a valid value of that type or `null`.

1. **Nullable Types (`Type?`)**:
   - A variable or return type with a `?` after the type means that the value can be of that type or it can be `null`.
   - For example:
     ```dart
     String? name; // This variable can hold a String or be null.
     ```
     This allows the `name` variable to be `null`, which would not be allowed with a non-nullable type (`String name;`).

2. **Nullable Return Types (`Future<List<T>>?`)**:
   - Similarly, methods that return `Future<T>?` mean that the method can either return a `Future` of type `T` (like `Future<List<ClothingItem>>`), or the method can return `null` (e.g., `Future<List<ClothingItem>>?`).
   - A nullable return type is used when a function can potentially return `null` instead of a valid `Future`. This is often used when there’s a chance that an error or no data is available to create the `Future`.

   Example:
   ```dart
   Future<List<ClothingItem>>? fetchClothingItems(String userId) async {
     if (userId.isEmpty) return null; // Null is returned if no userId is provided.
     // Otherwise, proceed with fetching data.
   }
   ```

3. **Null Safety**:
   - Dart introduced **null safety** in version 2.12, which means that variables and fields are non-nullable by default unless specified otherwise.
   - The `?` allows developers to explicitly define a field or variable as nullable, helping avoid runtime null dereference errors.

   Example of a non-nullable type:
   ```dart
   String name = "John"; // This cannot be null
   ```

   Example of a nullable type:
   ```dart
   String? name = null; // This can be null
   ```

### Summary:
- `Type?` denotes that a value of that type is nullable.
- A nullable return type (`Future<T>?`) means the function can return `null` in addition to a valid value.
- `?` plays a key role in Dart's null safety, enabling safe handling of `null` values and reducing the chance of null dereference errors.

This concept is essential in modern Dart development, especially with Firebase and asynchronous data fetching, where `null` values can sometimes indicate an error or missing data.
