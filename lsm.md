# Log-structured merge tree

Ok, now we [have](wal-hash.md) a simple key-value database based on append-only log and in-memory hash-based indexes. But what if we want to store in our database as much data so our keys will get out off memory? Seems like we should find some way to lookup keys on disk without in-memory indexes.

First of all lets introduce some constrants for our segments:
+ our segments should be sorted by key
+ in our nonactive segment every key should be present at most ones

> Such structure cold `SS-table` (Sorted string table)

Now we can compact our segments by mergesort. And can use spatial index to find our records

```scala
//todo
```

The real problem here is to keep segments sorted. In order to solve it we will keep a tree structure in memory(called `MemTable`) and when it will exceed some treshold dump it to disk.

So when we read data from our database we should seek key sequentially in MemTable and then all SS-Tables from newest one to oldest. Here we have another problem: if we try to find some data which doesn't exisistsin our database it will scan all segments. To aviod such unnessesary scans we can create [Bloom filter](bloom-filter.md) for our keys 

> original LSM paper can be found at https://www.cs.umb.edu/~poneil/lsmtree.pdf
