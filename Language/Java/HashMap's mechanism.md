## HashMap
So whilst I knew that hashmap is a data structure that stores unique keys and its corresponding values. But I did not know the specific inner
mechanism of *how* it worked.

So a hashmap actually uses an *array* (its actually called a table) as its data structure, accessing values by the index. But hashmap uses inputs key (x) into a 
**hashing function f(x)**, calculates the index(y) and store that value in that index y.

![Image](https://github.com/user-attachments/assets/09b4f0a1-882e-46c5-a393-3bbfd18a4fd5)

So the purpose of hashing is to actually finding this index(y). There are some conditions to be followed
1) result of hashing MUST be an integer

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

This is cuz the hash function that receives key as a paramter returns the value (index y) that is result of XORing the original hash code of
key and right-shifted version of the key (16 bits to the right). This is an int. We will explain why we do this additional hashing step
later but it is to reduce hash collisions. 

2) result of hashing MUST NOT exceed the size of the array

## Bucket and its nodes
![Image](https://github.com/user-attachments/assets/72141d0f-bc67-441a-9e73-5a9a811d5324)

Hashmap is basically called a table, which is an array of buckets that is declared as Node<K,V>[] table. 
Each index/slot in this table is called a bucket and each bucket can be empty or store the *first node* of a linked list of Node objects. 
Each node contains a single key-value pair. 

## Possibility of collision
So we can access data directly through a hashing function *as long as u have a key* but there is a disadvantage. Theoretically we can have
infinite keys but there is a limit to the number of indexes that we can make that has to be smaller than the ize of the array. So the collision
between keys can happen where **different keys give the same index**.

So theres 2 ways to handle this issue

### Threshold
We can adjust the bucket size. By default, hashamp increases the bucket size when bucket occupancy reaches 75% and beyond cuz at that level,
the collision probability increases significantly.

In the original creation of hashamp, the default bucket size is 16, with threshold being 0.75. So if data stored in bucket exceeds 12, the bucket
size needs to be adjusted - in which case *the bucket size doubles once the threshold is exceeded*. So bucket size becomes 32.

### Linked List + Red Black Tree
So adjusting bucket size doesnt actually prevent collisions cuz it just mitigates the problem by trying to spread out the nodes more evenly. 
We have to know how to handle one when it happens. 

Linked List (Separate Chaining): 
So each bucket stores reference to *the head of a linked list*. As we are adding the key and value-
1) if bucket is empty, new node of that key and value is store directly onto that index as the first element of a nwe linked list
2) if that bucket is already occupied by an existing Node (meaning a collision has occurred), the HashMap adds the new node to the end of the existing linked list at that bucket.
It essentially "chains" the new node to the previous one.

When we get(key) and try to get value, the hashed key (index y) gives us the bucket index and we go through each node to compare the key-value
pair that we are searching for. So the time complexity is o(n).

Red Black Tree:
The linked list way is okay if its length is small but if many keys collide in the **same bucket**, we need some optimisation. This is where
the RBT comes in. 

If the TREEIFY_THRESHOLD is met (8 elements) AND the MIN_TREEIFY_CAPACITY is met (table size >= 64), where these 2 are critical thresholds to
signal conversion to RBT, the linked list at that specific bucket is transformed into a Red-Black Tree. RBT, being a **self-balancing BST**,
automatically performs operations to maintain as balanced subtree heights as possible. And as we know from Leetcode (wink), we know the
time complexity of balanced binary tree is o(log N) because we can decide to go left or right depending on the value, which signifcantly
reduces search traversal time.
  
