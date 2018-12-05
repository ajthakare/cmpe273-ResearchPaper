# Viewstamped Replication

### Team Members

- Ajinkya Thakare
- Akshay Jaiswal
- Sarthak Sinhgal
- Shashank Singh

### Summary of Viewstamped Replication

#### Introduction

Viewstamped Replication is a replication protocol for distributed systems and uses log replication and consensus algorithm to guarantee a consistent view for the replicated data. THe protocol achieves fault tolerance by redundancy in time and space. Redundancy in time allows replaying requests which can be dropped,reordered or delayed, which ultimately combats the unreliability of network. Redundancy in space allows adding redundant copies of data in different fault-domain isolated machines, thus making sure the system remains available in the events of hardware failure/crashes. However, the system with Viewstamped Replication protocol can only tolerate non-Byzantine failures. In general, to tolerate f failures, the cluster size should be 2f+1 and the minimum cluster size should be 3.

#### Anatomy

The ensemble or cluster is made of replica nodes. Each replica is composed of the following parts - 

1. **Op-Log:** It consists of a sequence of operations . Operations in Op-Log must be deterministic and idempotent. Each operation in the operation-log has a positional identifier which is a monotonically increasing number.
2. **Op-num:** It represents the lsequence number of the latest operation added to the log.
Note:Operations in the (Op-Log) are appended first, then shared with other replicas and once there is a confirmation that enough replicas have received the operation then it is actually executed.
3. **Commit-Num:** It represents the number of the last operation which was executed in the replica. 
4. **View-num:** It identifies how many times the primary replica has changed in an ensemble. It increases by 1 everythime a new primary replica is assigned in a cluster .
5. **Primary field:** It represnts the primary replica in the cluster.
6. **Status:** The status field shows the current replica operation mode.
7. **Config field:** It contains the list of all the replica nodes in the ensemble with their IP addresses and their unique identifiers.
8. **Client-table:** It keeps track of client’s requests. Clients are identified by a unique id, and each client can only make one request at the time.This ensures that the clients can re-issue the last request without the risk of duplication in the system.

#### Client Request Handling



#### View change protocol:

View change protocol is responsible for leader update. It is used when one of the replicas detect that the current primary is unavailable. If a quorum of nodes agrees then the system will transition to a new primary. Viewstamped replication uses a deterministic function over a fixed property of the replica nodes. For example, Simple sorting is used to determine the order in which replicas will be elected as the leader.

Steps followed for view change:
1. Replica which experienced timeout, will change its status to view change and broadcast a <start-view-change> message to all the replicas.
2. When any replica receives F+1 <star-view-change> messages, it sends a <do-view-change> message to the primary replicas.
3. When new primary receives F+1 <do-view-change>  messages it updates itself with the latest data in the system.
4. The new primary sends <start-view> message to all the replicas.
5. Replicas update themselves with the contents of <start-view> message and change status to normal.
6. If old primary comes back to life, it sends an <get-state> message to all the replicas.
7. Replicas reply with <new-state> message.

This is how the simple Viewstamped Replication protocol handles primary change.

### Reference Materials

- Viewstamped Replication - Orignal (1988) [link here](./Reference_Material/viewstamped-replication.pdf)
- Viewstamped Replication - Revisited (2012) [link here](./Reference_Material/viewstamped-replication-revisited.pdf)
- Viewstamped Replication Explained [link here](./Reference_Material/viewstamped-replication-explained.pdf)

### Reference Links

- http://blog.brunobonacci.com/2018/07/15/viewstamped-replication-explained/
- https://medium.freecodecamp.org/viewstamped-replication-revisited-a-summary-144ac94bd16f
- https://blog.acolyer.org/2015/03/06/viewstamped-replication-revisited/
