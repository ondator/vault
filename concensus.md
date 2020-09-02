# Consensus

Main idea is to achieve linearizability in distributed systems. To achieve such guarantees on some point some nodes will provide their decisions and algorithm should pick one of this decisions. Such "picking" named quorum and such algorithm should comply laws:
+ **single decision** -- all nodes should get same final decisions
+ **integrity** -- no one node can't get more than one final decision
+ **validity** -- if node get final decision, than this decision was suggested by some node
+ **completeness** -- every available node finally select one of decisions

The two most popular kinds of such algorithms are [Paxos-like](#paxos) and [Total Order Broadcast](#total-order-broadcast)

> Practically no one never implement any Total Order Broadcast or Paxos algorithm, because its realization are very complicated. If you need consensus system (as for example Distribute Locking Service) it will be much wisely to use etcd or ZooKeeper

## Total Order Broadcast

In short, the `Total Order Broadcast` (or atomic broadcast)
ensures that messages sent to a set of processes are in turn delivered by all those processes in the
same total order. To achieve such guarantees concrete implementation should provide
+ **reliable delivery** -- all messages should be delivered (if recipient is down sender should retry until success)
+ **totally ordered delivery** -- all messages should be delivered in same order as sent

`Total Order Broadcast` is a kind of many different algorithms that can be grouped in 5 types: `communication history`, `privilege-based`, `moving sequencer`, `fixed sequencer` and `destinations agreement`. All of them has their proc and cons, so I provide here one of the most popular algorithm of fixed sequencer type called Zab(ZooKeeper Atomic Broadcast).

### ZooKeeper Atomic Broadcast

Zab has 4 phases:

0. **Leader election** -- in this stage, which also names fast leader election, nodes elect prospective leader.
1. **Discovery** -- in this stage nodes quorum change prospective leader from stage 0 to leader with max `zxid`(ZooKeeper [logical timestamp](lamport-clock.md))
2. **Synchronization** -- it's possibly that some followers has not replicate some data before previous leader died. In this stage followers receives missing transactions and commits them locally
3. **Broadcast** -- prospective leader become elected and become accept write requests 
>Technically after stage 0 leader could be a random node, but actually Zookeeper tries to find node with max `zxid` in order to reduce stage 1 and thus improve performance

#### Leader election
At the phase 0 in order to simplicity our nodes will vote for candidate with larger transaction number from their local wal

```scala
//todo
```

#### Discovery
Now all our followers will check that our prospective leader REALLY has largest `zxid`. If not, than unmasker node became a new prospective leader

```scala
//todo
```

#### Synchronization
Here our leader should send uncommitted transactions to followers

```scala
//todo
```


#### Broadcast

Here our new leader became elected and start receive write payload

```scala
//todo
```

> Here is just a demo of ZAB. Pls don't use it in your production code

## Paxos

Paxos is an eldest consensus algorithm, it was designed by Leslie Lamport at 1990