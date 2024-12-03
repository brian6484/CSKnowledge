## How to get result of asynchronous task
There are mainly 2 ways - await or .then()

## await
```dart
void printUserId() async {
  String? userId = await getUserId(); // Wait for the future to resolve
  print('User ID: $userId');
}

```

## then()
```dart
void printUserId() {
  getUserId().then((userId) {
    print('User ID: $userId');
  });
}

```
