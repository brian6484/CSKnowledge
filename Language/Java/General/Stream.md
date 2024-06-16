## Stream
There are so many methods in this Stream API but I will be updating gradually

## peek() vs map()
So map is used when you are **transforming** the object. But peek() is used for just performing *side-effects()*. For example

```java
public DeliveryOrderResponse sortByOrderId() {
    this.orderResponseList.sort(OrderResponse.getIdComparator());
    return this;
}

public DeliveryOrderResponse setTotalAmountToNoneIfZero() {
    if (this.totalAmount == 0) {
        this.totalAmount = null;
    }
    return this;
}
```

For these kind of cases you would use peek. But map() is like
```java
.map(deliveryOrderResponse -> {
                DeliveryOrderResponse newResponse = new DeliveryOrderResponse(
                        new DeliveryResponse(
                                deliveryOrderResponse.getDeliveryId(),
                                deliveryOrderResponse.getDeliveryTime(),
                                deliveryOrderResponse.getDeliveryStatus()
                        )
                );
                newResponse.setOrderResponseList(new ArrayList<>(deliveryOrderResponse.getOrderResponseList()));
                newResponse.sortByOrderId();
                newResponse.setTotalAmountToNone();
                return newResponse;
            })
```
