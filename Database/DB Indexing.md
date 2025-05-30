## DB Indexing
So we know db indexes help db searches speed up data retrieval and enforce uniqueness. The most common way it is indexed is using a 
b-tree.

## B-tree
B-tree, unlike binary tree which has 2 nodes per parent node, if fat and short. It can have multiple child nodes and is **self-balancing**
like Java hashmap's red black tree. This means that insertion and removal takes log n time cuz it is balanced.
