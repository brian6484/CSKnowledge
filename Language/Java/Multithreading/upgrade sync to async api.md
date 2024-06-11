## Example
Assuming you are making a Danawa clone site (price comparison site). Each major shops like Coupang, Gmarket, etc will provide a getPrice API 
of their items and we are gonna call its API for each of the item. But if it is a syncrhonous API, there will be a time delay of getting each
item from each shop and the time delay will be exponential. But first lets see the sync API

### sync api
This delay() is used to simulate certain time delay.
```java
public double getPrice(String product) {
    return calculatePrice(product);
}

private double calculatePrice(String product) {
    delay();
    return random.nextDouble() * product.charAt(0) + product.charAt(1);
}
```

### async api
so to reduce this time delay and allow consumers of our danawa product to do some other stuff while waiting, we should make this a async
api. This getPriceAsync method can return **immediately** without waiting for computation to finish cuz of `Future`, which is a 
*handle* for a result that is not available yet.

![Screenshot 2024-01-01 234837](https://github.com/brian6484/CSKnowledge/assets/56388433/029807b1-525d-4da6-9b85-869e42f07034)

We first create an instance of CF and rmb unlike Future, we dont need to place explicit computation code in our CF instance right?
This instance will eventually contain a result when it becomes available. We then fork a separate thread to calculate price **asynchronously**
and return the `Future` instance without waiting for the computation to complete.

When price is finally available, you can **complete()** the CF by using complete() to set the result.

A client of our API can consume as such:

![Screenshot 2024-01-01 235908](https://github.com/brian6484/CSKnowledge/assets/56388433/6fd7bdd9-ac3b-4a89-a3be-f7792be7b53b)

Client can ask price of an item and since this shop returns an async API, the invocation **almost immediately** returns a `Future`, 
which the client can retrieve its result later. Whilst waiting, client can do other stuff without being blocked and when there are no
other stuff to do, the client can invoke *get()* on this `Future` object.

This leads to 2 possible actions
1) if the computation is still in process, it blocks the thread
2) but if it is done, client can simply unwrap this result value contained in the `Future` object.

In this case, it returns
```
Invocation returned after 43 msecs
Price is 123.26
Price returned after 1045 msecs
```

It is possible to prevent the client from being blocked at all.
