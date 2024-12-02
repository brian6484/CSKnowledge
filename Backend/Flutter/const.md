## const keyword
```dart
class HomePage extends StatelessWidget {
  final String userId;

  // Using const constructor
  const HomePage({Key? key, required this.userId}) : super(key: key);
}
```

Adding this const keyword to constructor means that instances are created at compile time (before app runs). This increases efficiency
cuz Flutter engine can **reuse** the same instance, rather than creating one each time.

