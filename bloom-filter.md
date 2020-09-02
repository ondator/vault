# Bloom filter

`Bloom filter` is a probabilistic data structure which can decide does an element contain in a large set. However there is a probability(proportional to set size) of false-positive decision(there are no element in set, but BF response with true)

Bloom filter frequently used in storage systems in order to increase performance. For example in [LSM-based DBMSs](lsm.md) it can be used to avoid scan all SSTables for key missing in DB.

## Implementation

Bloom filter(*BF*) is just a bit array size *m*. Also you should define a set of *k* morphisms(*k<m*) *h_1...h_k* which are simply set a *BF[k]* to 1

```scala
//todo
```

When you check *BF* for an element *e* you should just check all it's *h_i* for 1. If all of them are set to 1 then either *e* presence in BF or all *BF[i]* were set occasionally 

```scala
//todo
```