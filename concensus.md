# Concensus

Main idea is to achieve linearizability in distributed systems. To achieve such guarantees on some point some nodes will provide their descisions and algorithm should pick one of this descisions. Such "picking" named quorum and such algorithm should comply laws:
+ **single descision** -- all nodes should get same final descisions
+ **integrity** -- no one node can't get more than one final descision
+ **validity** -- if node get final descision, than this descision was suggested by some node
+ **completeness** -- every available node finaly select one of descisions

Practically no one develop such algorithm by themself because you can achieve such behaviour by implementing one of `Total Order Broadcast` algorithm

> Practically no one never implement any Total Order Broadcast algorithm too, because its realisation are very complicated. If you need Concensus system (as for example Distribute Locking Service) it will be much wisely to use etcd or ZooKeeper

## Total Order Broadcast

In short, the `Total Order Broadcast`
ensures that messages sent to a set of processes are in turn delivered by all those processes in the
same total order. To achieve such guarantees conrete implementation should provide
+ **reliable delivery** -- all mesages should be delivered (if recipient is down sender should retry until success)
+ **totaly ordered delivery** -- all messages should be delivered in same order as sent

The most important implementations of `Total Order Broadcast` are `communication history`, `privilege-based`, `moving sequencer`, `fixed sequencer` and `destinations agreement`. All of them has their proc and cons, so I provide 


