# B-Tree
Another approach to data access is to have [wal](wal-hash.md) for fault-tolerance only and maintain separate structure to store our data. This structure called `B-Tree` and defined below:

> Let `h >= 0` be an integer, `k` a natural number. A directed
tree `T` is in the class `t(k,h)` of B-trees if `T` is either empty `(h=0)`
or has the following properties:
> + Each path from the root to any leaf has the same length `h`,
also called the height of `T`, i.e., `h` = number of nodes in path.
> + Each node except the root and the leaves has at least `k + 1`
sons. The root is a leaf or has at least two sons.
> + Each node has at most `2k + 1` sons. 

In practice however two last laws are modified a little: every node is a leaf or has at least two sons and each node has at most `f` sons where `f` named `branching factor` and has magnitude of hundred.

Node in our B-Tree based index is map of key and pointers to other nodes or pointers to data if node is leaf

Thus basic insert algorithm is:
+ find a node to insert
+ if node size less than threshold insert new value
+ if node size more than threshold insert new value, split node into two and update parent node

```scala
// todo
```