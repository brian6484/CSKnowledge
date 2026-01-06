## DB Indexing
So we know db indexes help db searches speed up data retrieval and enforce uniqueness. The most common way it is indexed is using a 
b-tree.

An index is a data structure (typically B+ tree) that speeds up queries by providing direct pointers to data instead of full table scans. The trade-off is increased storage and slower writes (INSERT/UPDATE/DELETE) since indexes must be maintained. Add indexes on columns frequently used in WHERE clauses, JOINs, or ORDER BY, but avoid over-indexing.

## but why do we use trees
if u hav 1 million records and u wanna find user id = 500k. Instead of scanning line by line, using a tree index that keeps data sorted will make query much faster.

## B-tree
B-tree, unlike binary tree which has 2 nodes per parent node, if fat and short. It can have multiple child nodes and is **self-balancing**
like Java hashmap's red black tree. This means that insertion and removal takes log n time cuz it is balanced.

unlike b+ tree it stores the actual data in all the nodes but b+ tree stores data in just leaf nodes
```
            [50, data50]
          /            \
   [20, data20]    [70, data70]
   /          \    /          \
[10,d10]  [30,d30] [60,d60] [80,d80]
```

## b+ tree
B+ trees store all data in leaf nodes which are linked together, while B-trees store data at all levels. This makes B+ trees better for databases because: leaf links enable fast range scans, internal nodes can hold more keys (better fanout), and performance is consistent. That's why most database indexes use B+ trees.

```
                    [50]                    ← Internal node (just navigation)
                   /    \
              [20]        [70]              ← Internal nodes
             /    \      /    \
         [10,15] [20,30] [50,60] [70,80]   ← Leaf nodes (actual data)
            ↓       ↓       ↓       ↓
         (data)  (data)  (data)  (data)
```

## example
```
SELECT * FROM users WHERE age BETWEEN 20 AND 30;
```

**Step by step:**

**1. Find starting point (age=20):**
```
Start at root [50]
  20 < 50, go left
    Go to [20]
      Find leaf node [20,30]  ← Found it!
```

**2. Follow the links sequentially:**
```
[20,30] ← Start here, has ages 20-30 ✓
   ↓
Read all records with age 20, 21, 22... 30
Done! (because next leaf would be [50,60], which is > 30)
```

**The magic:** Once you find age=20, you don't need to search the tree again. Just follow the chain of leaf nodes (they're linked!) until you pass 30.

## Why Linking Matters

**Without links (B-Tree):**
- Find age=20 → get that record
- Find age=21 → search tree again from root
- Find age=22 → search tree again from root
- Find age=23 → search tree again from root
- ... (very slow!)

**With links (B+ Tree):**
- Find age=20
- Read 21, 22, 23... by just following the → arrows
- Much faster!

## Visual Example with Real Data
```
Leaf nodes (sorted and linked):

[age=18, name=Alice] → [age=20, name=Bob] → [age=25, name=Carol] → [age=30, name=Dave] → [age=35, name=Eve]
                          ↑                                            ↑
                       Start here                                   Stop here
                       
For query "age BETWEEN 20 AND 30":
1. Tree search finds Bob (age=20)
2. Follow link → Carol (age=25) ✓
3. Follow link → Dave (age=30) ✓  
4. Follow link → Eve (age=35) - too high, stop!
```

## wat if the data that im looking for isnt in the leaf node then the internal node wont have the data?
What Gets Indexed is Only values that EXIST in your table get put in the index. **In short: No data in table = No key in tree (internal OR leaf)**
