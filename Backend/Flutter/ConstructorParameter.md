## Issue
Observe this constructor parameters, which consist of elements from Dart and Flutter.

```dart
class HomePage extends StatelessWidget {
  final String userId;

  // Using const constructor
  const HomePage({Key? key, required this.userId}) : super(key: key);
}
```

What are those parameters? Let's look at the first one - key. Remember if there is ? at the back of Type, it is nullable.
This key is used by flutter to uniquely identify widget in widget tree.

Second parameter is a must and userId needs to be passed.

Now, what is : super(key: key)? It is calling the constructor of the parent class, which in this case is StatelessWidget.
The super keyword refers to the parent class constructor. The key value is also passed to parent's constructor for flutter to 
handle widget's identity.

## Way to call constructor
HomePage(clothingItems: _clothingItems, userId: '1')

Unlike Java where you pass paramters **positionally**, you need to pass in like a dictionary format for Dart.

