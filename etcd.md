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

election timeout

- the amount of time a follower waits until becoming a candidate. 
- The election timeout is randomized to be between 150ms and 300ms.
- After the election timeout the follower becomes a candidate and starts a new election term...
- votes for itself
- and sends out Request Vote messages to other nodes.
- If the receiving node hasn't voted yet in this term then it votes for the candidate...
- ...and the node resets its election timeout.
- Once a candidate has a majority of votes it becomes leader.
- The leader begins sending out Append Entries messages to its followers.
- These messages are sent in intervals specified by the heartbeat timeout.
- Followers then respond to each Append Entries message.
- This election term will continue until a follower stops receiving heartbeats and becomes a candidate.
- Requiring a majority of votes guarantees that only one leader can be elected per term.
- If two nodes become candidates at the same time then a split vote can occur.

split vote

- Two nodes both start an election for the same term...
- ...and each reaches a single follower node before the other.
- Now each candidate has 2 votes and can receive no more for this term.
- The nodes will wait for a new election and try again.

log replication- Once we have a leader elected we need to replicate all changes to our system to all nodes. This is done by using the same Append Entries message that was used for heartbeats.

- First a client sends a change to the leader.
- The change is appended to the leader's log...
- then the change is sent to the followers on the next heartbeat.
- An entry is committed once a majority of followers acknowledge it.
- and a response is sent to the client.

Raft can even stay consistent in the face of network partitions. Partitions can lead to two leaders, one in each partition. When you add a client and try and update one leader. One leader node cannot replicate to a majority so its log entry stays uncommitted. If a client updates and there is a majority, that value will be updated for each node. When a network partition becomes healed, The node with the lower election term will see this and step down. All nodes will roll back their uncommitted entries and match the new leader's log.


