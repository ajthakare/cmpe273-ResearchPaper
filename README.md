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

![Anatomy](./Reference_Material/anatomy.png)

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

The Viewstamped Replication protocol ensures that at least a quorum of replicas acknowledges the request before executing it. Following steps are followed for client request handling and data replication - 

1. Client send a message request with a client-id, request number and operation to be performed.
2. Primary check if request number is greater than previous one. If request number is greater, primary increment the op-number, append the operation in op-log and subsequently update the client table. Then it sends a prepare message to all the replicas.
3. Replica checks for the view-num. If message view-num is not equal to their view-num, then a new primary is elected and according to who is behind, state transition takes place. If view-num is equal, the op-num is incremented, the operation is appended in op-log and client table is updated. Now replica send prepare-ok message back to primary.
4. When primary receive minimum f+1 prepare-ok message, it commits the operation, increment the commit-num and send a reply response to client.
5. When new client or same client with a new request send message to primary, the whole process takes place again, but this time commit-num is incremented in all the replicas. Hence operation is also committed in the replicas.
6. When system is experiencing downtime, primary sends a commit message to all the replicas. The commit message contains the view-num and commit-num. 

The prepare message and commit message act as heartbeat for primary. If replica doesn't receive these message for so long, they get to know that primary is dead and they do a primary election. This process of primary election is called view-change.

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

### Contributions by Team Members

##### Ajinkya Thakare

- Explored and explained introduction of Viewstamped Replication protocol
- Explained fault-tolerance handling in the protocol and it's limitations in real world scenarios
- Researched the uses of protocol in distributed applications
- Played an active role in Q&A session after presentation

##### Akshay Jaiswal

- Researched the purpose and workings of the view change protocol in Viewstamped replication.
- Understood and explained view change protocol with a simple example.
- During the presentation explained the following points -
1. Need of view change.
2. Why view change happens.
3. How a new leader is elected
4. How the view change happens.
5. Result of view change.
- Actively participated in Q&A session during presentation

##### Sarthak Sinhgal

- Researched the structure of replica node in an ensemble/cluster
- During the presentation, discussed the anatomy of replica and explained the following components -
1. Op-Log
2. Op-num
3. Commit-Num
4. View-num
5. Primary field
6. Status
7. Config field
8. Client-table
- Explained the characteristics of the operations and approval process from the nodes in ensemble
- Played an active role in Q&A session after presentation

##### Shashank Singh

- Understood and explained the normal functioning of Viewstamped Replication protocol
- Explained the process of client's request handling by primary and how primary forwards the request to and accepts replies back from replicas.
- Explained heartbeat detection in the system.
- Actively participated in the Q&A session during presentation

### Reference Materials

- Viewstamped Replication - Orignal (1988) [link here](./Reference_Material/viewstamped-replication.pdf)
- Viewstamped Replication - Revisited (2012) [link here](./Reference_Material/viewstamped-replication-revisited.pdf)
- Viewstamped Replication Explained [link here](./Reference_Material/viewstamped-replication-explained.pdf)

### Reference Links

- http://blog.brunobonacci.com/2018/07/15/viewstamped-replication-explained/
- https://medium.freecodecamp.org/viewstamped-replication-revisited-a-summary-144ac94bd16f
- https://blog.acolyer.org/2015/03/06/viewstamped-replication-revisited/
