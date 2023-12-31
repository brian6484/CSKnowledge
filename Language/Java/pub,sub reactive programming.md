## Reactive programming
It uses pub/sub system like Redis where subscribers subscribe to one or multiple publishers. To make a class pub or sub or both,
you have to implement the interfaces. This reactive programming is usually to tackle cases that Future and CF cannot, which are 
a one-shot (executing code only once) whereas reactive is like a Future object that over time, yields multiple results.

![Screenshot 2023-12-31 220004](https://github.com/brian6484/CSKnowledge/assets/56388433/ad85a6de-cb6c-491d-ba71-7f23e11d24ce)

### Upstream
it is the `source` or publisher of data events. It is from pub to sub.
Kinda the opposite that I imagined where source to subscribers would be called downstream like a dam river.

### Downstream
it is from destination to source (sub to pub)

## Example
for example, a cell in Excel spreadsheet can be both a publisher (publishes its events to subscriber cells) and subscriber
(to react to events from other cells)

```java
private class SimpleCell implements Publisher<Integer>, Subscriber<Integer> {
    private int value = 0;
    private String name;
    private List<Subscriber<? super Integer>> subscribers = new ArrayList<>();

    public SimpleCell(String name) {
        this.name = name;
    }

    @Override
    public void subscribe(Subscriber<? super Integer> subscriber) {
        subscribers.add(subscriber);
    }

    private void notifyAllSubscribers() {
        subscribers.forEach(subscriber -> subscriber.onNext(this.value));
    }

    @Override
    public void onNext(Integer newValue) {
        this.value = newValue;
        System.out.println(this.name + ":" + this.value);
        notifyAllSubscribers();
    }

    // Other methods from the Subscriber interface
    @Override
    public void onSubscribe(Subscription subscription) {
        // Implementation of onSubscribe method
    }

    @Override
    public void onError(Throwable throwable) {
        // Implementation of onError method
    }

    @Override
    public void onComplete() {
        // Implementation of onComplete method
    }

    // Other methods from the Publisher interface
    // These methods are not included in the provided code but should be implemented for completeness
}
```

The onNext method is called by the publisher to update its interval value, print the updated value to the console and notify all subscribers.
One thing to note is that this notifyAllSubscribers updates all subscribers that subscribed to this publisher via onNext method.
So if c1.subscribe(c3) and c1.onNext(10), c3 is subscribed to c1 so when c1's value is updated to 10, c3's value is updated to 10 too.

## Harder example
what if we want a reactive style like c3= c1+c2? We create another class that extend this simplecell class to implement this where
it can store 2 sides of an arithmetic equation. Rmb its inheritiance so we can use its parent (simplecell) class's methods.

This setLeft and setRight updates its value and notifies all subscribers.

```java
public class ArithmeticCell extends SimpleCell {
    private int left;
    private int right;

    public ArithmeticCell(String name) {
        super(name);
    }

    public void setLeft(int left) {
        this.left = left;
        onNext(left + this.right);
    }

    public void setRight(int right) {
        this.right = right;
        onNext(right + this.left);
    }
}
```

so if we do this, since c3's setLeft is subscribed to c1 and c3's setRight is subscribed to c2, upon updates on c1 and c2, c3's value is
also updated.
```java
ArithmeticCell c3 = new ArithmeticCell("C3");
SimpleCell c2 = new SimpleCell("C2");
SimpleCell c1 = new SimpleCell("C1"); 
c1.subscribe(c3::setLeft);
c2.subscribe(c3::setRight);
c1.onNext(10); // Update value of C1 to 10
c2.onNext(20); // update value of C2 to 20
c1.onNext(15); // update value of C1 to 15
```

its gonna print
```
C1:10
C3:10
C2:20
C3:30
C1:15
C3:35
```






