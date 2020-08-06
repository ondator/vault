# LSM vs B-Tree

Here is comparision of B-Tree-based and LSM-based storage systems

+ Insert in LSM are very optimal because you need to insert into MemTable opposite to B-Tree where every insert is 2 IO operations (insert into B-Tree and insert into wal). On the other hand during read operations you should scan MemTable and few segments when in B-Tree you can quickly traverse a tree. So LSMs are for write-heavy workloads and BTrees are for read-heavy workloads
+ 