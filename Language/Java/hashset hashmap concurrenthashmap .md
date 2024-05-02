## Intro
Hashset and hashamp belongs to the Collection framework while ConcurrentHashMap belongs to the Concurrent package. This 
concurrent package **extends** the Collection framework to support concurrency and synchronisation.

## HashSet
It implements the Set interface (implement interface II) and does not allow duplicate values. If you want to add objects
to hashset, you **have to implement** equals and hashCode methods.

## HashMap
implements the Map interface and doesnt maintain the order of insertion like hashset. 

### LinkedHashMap
But *LinkedHashMap* maintains order of insertion. 

### TreeMap
Internally stored as Red Black tree, it sorts keys based on alphabetical order.

## Diff between HashSet and HashMap
1) hashmap is in key-value format while hashset can only store Objects
2) implements different interfaces
3) hashmap's hashcode is on its key while hashset's hashcode is on the object and objects' hashcodes are compared with
equals() method to check for its duplicity
4) hashmap is faster cuz it can look up just via the key

## ConcurrentHashMap
It uses a locking mechanism that allows *multiple threads to work on diff segements of the Map* without interfering with
each other. 
