# Bidirectional Link Convenience Method

Chapter 3.2.4

## Bidirectional link requires 2 actions
Let's say we have a relationship of Item to Bid where it is 1:N. Many bids can be placed on a single item.
JPA doesn't manage persistent associations so even if you declare @ManyToOne/@OneToMany and save a bid in a Set collection of bids in Item,
the bid will not be persisted properly.

So if we want to manipulate an association, we have to do it just like how we do with regular Java. We have to consider both sides of relationship and
both sides have to add each other and set association right.

![Screenshot 2023-11-02 153646](https://github.com/brian6484/StudyNote/assets/56388433/5315ee41-3999-49bb-a22f-ce2b1f7d8250)

In Item.class,
```java
public void addBid(Bid bid) {
    if (bid == null) {
        throw new NullPointerException("Can't add a null Bid");
    }
    if (bid.getItem() != null) {
        throw new IllegalStateException("Bid is already assigned to an Item");
    }
    bids.add(bid);
    bid.setItem(this);
}
```

This not only reduces lines of code but ensures that we don't miss out any side of the bidirectional association. (enforcing cardinality of association)
Unlike SQL, which can set this association via a FK, we need a some extra code to ensure data integrity. 
