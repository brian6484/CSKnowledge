## State class
It manages the **mutable state** of a StatefulWidget. It updates the widget dynamically.

### **Structure of a StatefulWidget and State Class**

1. **StatefulWidget**:  
   This defines the widget itself, which is immutable. It only holds configuration data or properties.
   
2. **State Class** (`State<T>`):  
   This is where you manage the widget's **mutable state** and handle logic like user input, API calls, etc. The state class is linked to the widget using the `createState()` method.

---

### **Example: Counter App**

#### **1. StatefulWidget (Counter)**
```dart
import 'package:flutter/material.dart';

class Counter extends StatefulWidget {
  @override
  _CounterState createState() => _CounterState();
}
```
- **`Counter`** is a `StatefulWidget`.
- It defines the structure and configuration but doesn’t manage the state.

---

#### **2. State Class (_CounterState)**

```dart
class _CounterState extends State<Counter> {
  int _count = 0; // Mutable state

  // This method rebuilds the widget whenever the state changes.
  void _incrementCounter() {
    setState(() {
      _count++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Counter Example')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text('You have pressed the button $_count times.'),
            ElevatedButton(
              onPressed: _incrementCounter,
              child: Text('Increment Counter'),
            ),
          ],
        ),
      ),
    );
  }
}
```

### **Explanation:**
1. **State Class (_CounterState)**:
   - Manages the mutable state of the `Counter` widget (`_count`).
   - **`setState()`**:  
     Updates the state and triggers the UI to rebuild with the new state.

2. **`build()` Method**:
   - Called every time the state changes.
   - Rebuilds the widget tree to reflect the new state.

---

### **Why Use a State Class?**

1. **Dynamic UI Updates**:  
   The state class allows widgets to change their appearance or behavior dynamically.
   
2. **Separation of Logic and UI**:  
   The widget configuration and state management are kept separate, making the code easier to manage and understand.

3. **Lifecycle Management**:  
   The state class gives access to widget lifecycle methods like:
   - `initState()`: Called when the widget is first created.
   - `dispose()`: Called when the widget is removed from the widget tree.
   - `setState()`: Updates the state and triggers a UI rebuild.

