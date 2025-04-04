<div align='center'>
  <h1>CAP Theorem a.k.a Brewer's Theorem</h1>
</div>

# Table of Contents

- About
  - Availability
  - Consistency
  - Partition-Tolerance
- Tradeoffs
- Database Classification

# About

The CAP theorem states that a `distributed database system` can only satisfy two out of the three following properties: Availability, Consistency, and Partition-Tolerance.

## Availability

Every request (read or write) received by a non-failing node is guaranteed to result in a (non-error) response, but the response might not be up-to-date (temporarily inconsistent). It avoids single points of failure by `replicating the data across multiple nodes`.

## Consistency

Consistency means that data is the same across all nodes. Every read receives the latest write or an error. To achieve this, data is `replicated` and `synchronized` across nodes. In the presence of network partitions, a consistent system may reject some requests to prevent stale or conflicting reads.

- Strong consistency: when the replication is immediate, every read operation returns the most recent write (is up-to-date) or an error.
- Eventual consistency: when the replication is not immediate, the returned data might be stale.

## Partition-Tolerance

The overall system continues to operate even if there are network partitions (communication breakdown/information loss) between nodes.

Note: nodes can be physical machines (such as servers) or virtual instances (such as Docker containers or virtual machines) running on physical machines.

## Why only two of them can be satisfied?

Because even if we assume ideal hardware that never fails, in any real-world distributed system, network partitions (failures, latency, or network splits) are inevitable in a distributed system (due to unpredictable network conditions).

Consider DynamoDB, for instance. Even though database replications are still possible, in the presence of a communication breakdown, the system will sacrifice Availability to maintain Consistency and Partition-Tolerance.

# Tradeoffs

- Consistency and Partition-Tolerance (CP): guarantees that the data is consistent, but sacrifices availability, i.e., some requests will fail in the presence of network partitions.

- Availability and Partition-Tolerance (AP): guarantees that the system remains available (responds to requests) but may serve stale or inconsistent data.

- Consistency and Availability (CA): guarantees that every request receives the most recent write (consistency) and that every request receives a response (availability). However, this is only possible in the absence of network partitions, i.e., in a system that never experiences network failures.

Note1: CA (Consistency + Availability) is not possible in a real-world distributed system. During a network partition, some nodes cannot communicate, which means that to maintain Consistency (C), a node must reject requests since a network partition prevents data synchronization across nodes. But rejecting requests means sacrificing Availability (A).

Note2: in massive systems, `Partition-Tolerance` is not negotiable!

Note3: single-master design prioritizes `Consistency` and `Partition-Tolerance`.

# Database Classification

- MySQL, PostgreSQL, and MariaDB: prioritizes `Consistency` and `Availability` in a `single node-setup`. These databases are commonly used in applications where strong consistency is more important than scalability, such as in financial systems (banks). In a distributed setup, where network partitions are inevitable, they behave as CP systems sacrificing Availability to maintain Consistency.

- Apache HBase and MongoDB: prioritizes `Consistency` and `Partition-Tolerance`. Availability is circumvented by using hot standbys.

- Cassandra, CouchDB, and DynamoDB: prioritizes `Availability` and `Partition-Tolerance`. Unlike master-slave architectures, Cassandra, which has eventual consistency, ensures availability by replicating data across multiple nodes via a peer-to-peer ring structure where all nodes are equal, i.e., any host/node can serve as the primary host (there is no master node). This decentralized architecture ensures there is no single point of failure.
