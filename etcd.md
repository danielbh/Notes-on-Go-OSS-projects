https://github.com/etcd-io/etcd
http://play.etcd.io/home

This is the database that stores kubernetes state. 

### Raft

[What is Raft?](https://raft.github.io/)

[raft visualization](https://github.com/ongardie/raftscope)

[AWESOME raft visualization and walkthrough](http://thesecretlivesofdata.com/raft/)

Consensus Problem: When you have a client that sends a value to a server and there are multiple copies of that value. You need to come to a consensus on what all those values are. Each copy is called a node.

Node states:

Follower- All nodes start here.

Candidate- If followers don't hear from a leader they become a candidate. Then the candidate then requests votes from other nodes. Nodes reply with their votes. A candidate becomes a leader if it gets the votes from the majority of the nodes.

Leader- All changes to a system go through the leader.

Log Replication
- A client sends a value to the server "5"
- Each change is added as an entry in the node's log.
- This log entry is currently uncommitted so it won't update the node's value.
- To commit the entry the node first replicates it to the follower nodes...
- then the leader waits until a majority of nodes have written the entry.
- The entry is now committed on the leader node and the node state is "5".
- The leader then notifies the followers that the entry is committed.
- The cluster has now come to consensus about the system state.

Leader Election- When a candidate becomes a leader after getting votes from the majority of nodes.
In Raft there are two timeout settings which control elections.

election timeout- the amount of time a follower waits until becoming a candidate. The election timeout is randomized to be between 150ms and 300ms.
