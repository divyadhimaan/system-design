# Distributed Consensus with Paxos

### Pre-requisites
- [Consistency Levels](./consistency.md#consistency-levels)
- [Quorum](./consistency.md#quorum)
- [Transaction Isolation Levels](./consistency.md#isolation-levels)
- [CAP Theorem](./glossary.md#cap-theorem-brewers-theorem)
- [ACID Properties](./glossary.md#acid-properties)

## Two Phase Commit

| Phase        | Description                                                                 | Use Case Example                    |
|--------------|-----------------------------------------------------------------------------|-------------------------------------|
| Phase 1: Prepare | Coordinator asks all participants if they can commit (vote yes/no).         | Coordinating DB changes across services |
| Phase 2: Commit / Abort | If all vote yes → send commit; if any vote no → send abort to all.     | Distributed transactions in banking apps |

## Basics

### Consensus
Paxos is designed to **achieve consensus** in a distributed system through **majority agreement** (more than 50% of nodes must agree on a value).


### Distributed Nature
Operates in environments with potential **failures and unreliable message delivery**. Handles node crashes and network issues.


### Use Cases 
Used in systems like **Apache Zookeeper** and **Google Chubby** for tasks such as **distributed locking** and maintaining **order in distributed logs**.

### Importance

Distributed consensus is complex due to **failures and timeouts**. Paxos offers a **fault-tolerant way to reach agreement** even in such scenarios. 

---

## Distributed Consensus in a Multi-Server Setup

This explains the concept of **distributed consensus** in a system with:

- `3 Application Servers`: S1, S2, S3  
- `3 Database Servers`: D1, D2, D3

### 1. Consensus Requirement
- A **write request** is considered *complete and durable* only if **at least two out of the three databases** accept it.
- This majority rule ensures reliability and fault tolerance.

### 2. Broadcasting Write Requests
- When any application server (e.g., S1) receives a write request:
  - It **broadcasts the request to all three databases** (D1, D2, D3).
  - This parallelism improves efficiency and coordination.

### 3. Agreeing on a Log Line
- Before writing, all servers must **agree on the exact log line** (position) where the data will be written.
- This step **prevents accidental overwrites** by ensuring synchronization.

### 4. Log Line Synchronization
- Servers **query the current log line** from all three databases.
- If **2 or more databases agree** on a particular log line, it is selected.
- This ensures all nodes work on a **consistent view** of the log position.

### 5. Locking the Log Line
- To **prevent concurrent writes** to the same log line:
  - A server **locks the selected log line** by becoming its *owner*.
  - This lock mechanism avoids race conditions and maintains consistency.

### 6. Write Operation
- Once a server owns a log line:
  - It **executes the write operation**.
  - The write is **accepted only if the ownership is recognized** by a majority of the databases.

### 7. Two-Phase Protocol

The protocol works in **two distinct phases**:
#### Phase 1: Locking
- Servers **request the log line** and attempt to **lock it** by claiming ownership.

#### Phase 2: Writing
- The server that successfully locks the line performs the write operation.

- Only one server can own and write to a particular log line at a time.
- This prevents inconsistencies and write conflicts.

### 8. Maintaining Consensus
- Both the **log line ownership** and the **write operation** must be acknowledged by a **majority (at least 2/3) of the database servers**.
- This ensures **durability** and **consistency**, even in the presence of:
  - Failures
  - Network partitions
  - Concurrent write requests

---


### Improvements in algorithm

The timestamp-based improvements enhance the original consensus algorithm by:

- Prioritizing **recency of operations**
- Preventing stale writes
- Supporting **retry mechanisms and flexible ownership**
- Maintaining consistency **without tight coupling between lock ownership and writes**