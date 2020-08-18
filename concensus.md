# Concensus

Main idea is to achieve linearizability in distributed systems. To achieve such garanties on some point some nodes will provide their descisions and algorithm should pick one of this descisions. Such "picking" named qourum and such algorithm should comply laws:
+ **single descision** -- all nodes should get same final descisions
+ **integrity** -- no one node can't get more than one final descision
+ **validity** -- if node get final descision, than this descision was suggested by some node
+ **completeness** -- every available node finaly select one of descisions