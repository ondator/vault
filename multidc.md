# Multi DC Architecture

## Kafka
Source https://youtu.be/nQd209e9eCY
### If you have fast network

If occasionally you have a fast cross DC network (stable delays about 30-100ms) best you can do is build Zookeeper cluster with nodes on 3 DC and Kafka on >1 DC.

There are also approach based on Zookeeper hierarchical quorum, but in this case you should provide manual failover

### In normal case

In standart case cross DC cluster is not for you. In this case you should use mirroring. mirroring provide an opportunity to mirror all the data to the second DC.

In order to proper work all topics should be marked with source (DC). BTW mirroring provides data duplication and inconsistent message ordering. Moreover you should provide autofailover in this case on the application side.

## K8s
Source (https://learnk8s.io/bite-sized/connecting-multiple-kubernetes-clusters)

In the case of k8s there is no option to stratch one cluster to many DC. Because of etcd such cluster will not work as far as etcd will never achieve quorum

Community provide some solutions for multi-dc k8s with federated clusters