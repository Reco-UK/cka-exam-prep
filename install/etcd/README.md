# HA ETCD

ETCD is a distributed reliable key value store which is Simple, fast and secure.

It can be run in isolation of be set up as a peer within a cluster of ETCD data stores.

When running multiple instances of ETCD, they all maintain an identical copy of the data store and read
access is available on each of the peers.

When it comes to writing only one instance of ETCD has the ability to commit to its data store, which is known as the leader.

All write requests are passed to the leader regardless of the peer receiving the request. The request will not be considered committed until it has been written to the leaders store and replicated to all other peers and consensus is achieved 
between the peers. (A write is considered complete when a majority of the peers have the change written to their data stores)

The ECTD cluster uses leader elect to promote one of the peers as the leader, this process uses the RAFT protocol to do so.

When the leader is being nominated, all peers receive a request from the RAFT protocol algorithm and the first peer to complete the timer will request leadership and the other peers will vote
on that peer becoming the leader.

The leader will then continue to be the leader notify the other peers of its status, if the notification are not received by the other peers, then it is deemed to be offline and a new election process will occur.

A new leader can only be elected when a quorum is achieved on which peer should be the next leader.

The quorum is peers/2 + 1, with .5 numbers being rounded down. This why you should always have at least three peers when working multiple ETCD stores.

```(3 \ 2 = 1.5 + 1 = 2.5 round = 2.0)``` with the difference being the fault tolerance, so in this instance the tolerance is 1.

So the above examples shows that when working with 3 peers the quorum can be achieved if one peer goes down.

Running 2 is the same as running a single ETCD store as quorum would never be achieved when leader elect process is initialised.

**When selecting the number of masters (and therefore ETCD data stores) it is suggested to use an odd number as when working with an even number of nodes, there is a 
higher chance of the cluster failing if the network was partitioned and nodes when segmented.**