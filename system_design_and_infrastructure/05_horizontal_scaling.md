<div align='center'>
  <h1> Sharding and Horizontal Scaling </h1>
</div>

# Table of Contents

- Sharding
- Horizontal Scaling
  - Load Balancer
  - Routing Algorithms
    - Round robin
    - Weighted round-robin
  - Consistent Hashing
  - Database Replication
  - Multi-master Replication
  - Bidirectional and Circular Replication

# Sharding (database level)

Sharding is a form of horizontal scaling at the database level, where data can be split across multiple shards. It is used to balance the database workload enabling the database to handle larger datasets and increased traffic.

Sharding involves dividing a table's rows across multiple shards (a.k.a partitions), where each shard is stored on a separate server or node. A shard key (e.g., UserID) is used to determine which shard a particular row of data belongs to.

Each shard has the same schema but contains a subset of the data. The data in each shard is unique and does not overlap with data in other shards, which helps distribute the load across multiple servers. 

By distributing the data in this way, sharding improves query performance, scalability, and fault tolerance, which are key benefits of horizontal scaling.

While sharding is difficult to implement with SQL databases, it is possible in modern MySQL using Vitess, a database clustering system originally developed at YouTube.

# Horizontal Scaling (application level)

Horizontal scaling at the application level (scaling out) is the practice of increasing the number of servers, i.e., computer instances to distribute the load of incoming requests across many servers and handle more traffic or data. This practice increases the number of machines (server nodes), rather than the machine's read and write capacity units.

At the application level means deploying the application to multiple servers (instances) and using a load balancer to distribute incoming requests (traffic) across these servers evenly. 

Having stateless servers greatly simplifies the process of horizontal scaling because any server can handle any request without having to store session-specific data or state. In contrast, stateful servers require more complex scaling solutions to manage session consistency across instances.

- Pros: a distributed system, which relies on multiple machines, increases fault tolerance, and reduces latency by allowing multi-region deployments in regions closer to the user's location.

- Cons: are more expensive than vertical scaling in the short run. Requires a distributed database.

## Load Balancer

A load balancer redirects/offloads/distributes incoming traffic (requests) across a fleet of servers to avoid a stack overflow. Private IP addresses are then assigned to the load balancer for connecting clients to available servers that will handle requests. Clients only have access to the load balancer IP which is public, not to the private ones.

## Routing Algorithms

- `Round robin load balancing algorithm`: presumes that all servers have the same RCU and WCU read and write capacity units. Requests are then routed to servers in order: the 1st request gets routed to server 1, the 2nd request to server 2, and so on.

- `Weighted round robin load balancing algorithm`: allows traffic to be distributed across servers according to their provisioned Read Capacity Unity (RCU) and Write Capacity Unity (WCU). Servers with more read and write capacity can handle more requests.

- `Least Connections`: 

- `Weighted Least Connections`:

- `IP Hash`:

- `Least Response Time`:

- `Random`:

- `Least Bandwidth`:

## Consistent Hashing

In distributed systems, data needs to be distributed among shards. Typically, this is achieved using a hashing function that hashes the sharding keys a.k.a partition keys, such as UserId or IPs, used to retrieve and modify data. However, shards can become overloaded with time (shard exhaustion), and in addition, adding (scaling up) and removing (scaling down) shards causes uneven data distribution. To circumvent this issue, consistent hashing is employed for data resharding (redistribution).

## Database Replication

In this approach, a master/slave relationship is used to ensure data Availability (see CAP theorem).

- Master: is the original database used to handle write operations.

- Slave: is a copy of the master database used to handle read operations.

When a master goes down, a slave database is ready to take its place.

## Multi-master Replication

## Bidirectional and Circular Replication
