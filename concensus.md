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

`Total Order Broadcast` is a kind of many different algorithms that can be grouped in 5 types: `communication history`, `privilege-based`, `moving sequencer`, `fixed sequencer` and `destinations agreement`. All of them has their proc and cons, so I provide here few the most popular algorithms called Zab(ZooKeeper Atomic Broadcast), Paxos and Raft.

### ZooKeeper Atomic Broadcast

Zab is algorithm of fixed sequencer type. It has 4 phases:

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

### Paxos

Paxos is an eldest consensus algorithm, it was designed by Leslie Lamport at 1990. The main idea is that there are 3 types of nodes *proposers*, *acceptors*, and *learners*:
1. *proposers* -- propose some value and responsible to commit a value
2. *acceptors* -- votes for proposal
3. *learners* -- are source of truth. They remember all decisions were made

> there are could be multiple proposers, so Paxos is a destination agreement type protocol

Paxos also has 4 phases:
#### Prepare
When proposer send his proposal to acceptors. Every proposal should have a number and every new should be larger(not necessary incrementally) than previous

#### Promise 
When acceptor receive proposal **N** and it doesn't receive before any proposals larger than **N** then it response with Promise, which is in fact a pair of (*N_i*, *V_i*) where *N_i* is a number of already accepted proposal(or *N* if no proposals has been accepted before) and *V_i* is a value from *N_i* proposal(or null if no proposals has been accepted before). Also acceptor should not accept any proposals with № < N

#### Accept
If proposer receive a quorum of acceptors then it send an "accept" message to all acceptors with value V and number N where V is max of all Promices by N_i

#### Accepted 
Acceptor accept new value(if it doesn't violate his promises) and send "accepted" message to all learners.

> Realy the best example of Paxos I found [here]():
> 
> Consider three servers: Server1, Server2, and Server3. They all want to use the Paxos protocol to make all members agree that they are leaders. These servers are the Proposer role and the values that they propose are their names. They need consent from the three members: Acceptor 1-3. Server2 initiates proposal 1 (with 1 as the ProposeID), Server1 initiates proposal 2 and Server3 initiates proposal 3.
>
>First, it is the Prepare phase:
>
>Assume that the message sent from Server1 arrives at Acceptor 1 and Acceptor 2 first, both of which have not received a request. Therefore, they receive this request and return [2, null] to Server1. At the same time, they promise not to receive requests with an ID smaller than 2;
>
>Then the message sent from Server2 arrives at Acceptor 2 and Acceptor3, and Acceptor 3 has not received a request. Therefore, Acceptor 3 returns [1, null] to Proposer 2 and promises not to receive messages with an ID smaller than 1. Because Acceptor 2 has already received the request from Server1 and promised not to receive requests with an ID smaller than 2, Acceptor 2 will refuse the request from Server2.
>
>Finally, the message from Server3 arrives at Acceptor 2 and Acceptor 3, both of which have already received a proposal. However, because this message has an ID that is greater than 2 (the ID of the message that has been received by Acceptor 2) and 1 (the ID of the message that has been received by Acceptor 3), both Acceptor 2 and Acceptor 3 receive this proposal and return [3, null] to Server3.
>
>At this point, because Server2 does not receive more than half of the replies, it obtains a new message with 4 as its ID and sends this message to Acceptor 2 and Acceptor 3. Since 4 is greater than 3 (the maximum ID of the proposals that Acceptor 2 and Acceptor 3 have received), this proposal is received and [4, null] is returned to Server2.
>
>Next, it is the Accept phase:
>
>Because Server3 has received more than half of the replies (2 replies) and the returned value is null, Server3 submits the proposal [3, server3].
>
>Because Server1 has also received more than half of the replies in the Prepare phase and the returned value is null, Server1 submits the proposal [2, server1].
>
>Because Server2 has also received more than half of the replies and the returned value is null, Server2 submits the proposal [4, server2].
>
>When Acceptor 1 and Acceptor 2 receive the proposal [2, server1] from Server1, Acceptor 1 accepts this request and Acceptor 2 refuses this request because it has promised not to accept a proposal with an ID smaller than 4.
>
>Acceptor 2 and Acceptor 3 accept the proposal [4, server2] from Server2 when they receive that proposal.
>
>Acceptor 2 and Acceptor 3 will refuse the proposal [3, server3] from Server3 because they have promised not to accept a proposal with an ID smaller than 4.
>
>At this point, more than half of the Acceptors (Acceptor 2 and Acceptor 3) have accepted the proposal [4, server2]. The Learner perceives that the proposal is passed and starts to learn the proposal. Therefore Server2 becomes the final leader.

As I already said Paxos was an eldest one distributed concensus algorithm, also it has some pitfalls in virtue of it is very limited usage now(Raft and MultiPaxos are much more widely used):
+ **It's very complicated in implementation** -- All fixed sequencer algorithms has more simplistic realization. (Reali it's quite difficult to understanding too. I spent a lot of time to drill down into all this V and N)
+ **It's can loss some data** -- The final result of a proposal is only known to partial Acceptors. This cannot guarantee that each instance for state machine replication has a completely consistent log
+ **It can never converge** -- After finding its proposal refuged on *Promise* phase Proposal usualy generates a new one. When system has many Proposals this can lead to endless loop

### Raft
Raft is new algorithm(2014) inspired by many Paxos disadvantages. I will not provide detail description of Raft because of existense of [briliant visualization](http://thesecretlivesofdata.com/raft/) but I will list here a key aspects:
+ it is a fixed sequencer, so it's a lot simplier
+ proposal numbers are strictly incremental, so it has much less chatty communications
+ it has clearly identified phases, so it's realy simplier to understand
+ Raft is based on random timeouts concepts(actually two sets of timeouts *election* and *heartbeat* timeout), so all operations latency are at least `2*max(heartbeat_timeout)` (because [Little's law](laws.md/#littles-law) )
+ It's very easy to add or remove new nodes
+ In case of net-split Raft can loose some data. Assume there are 5 nodes A,B,C,D,E and A was a leader. Than we have a netsplit and A and B was separated from C,D and E. On the new partition after election timeout C become a leader. Then A receive messages and C receive messages. When netsplit will be healed A become a follower node(because C has higher epoch number) and log from C will be replicated to A and B, but data sended from A to B will be thrown away.