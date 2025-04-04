<div align='center'>
  <h1> Database Contention, Thundering Herd, & Deadlock</h1>
</div>

# Table of Contents

- [Database Contention](#database-contention)
  - Mitigations
- [Thundering Herd](#thundering-herd)
  - Mitigations
  - Facebook's Thundering Herd Issue
- [Deadlock](#deadlock)

# Database Contention 

Contention refers to a situation in computer systems where multiple processes or threads compete for the same resource simultaneously. In the context of databases, contention typically involves multiple transactions (parallel requests) trying to access the same data or database object, such as tables, rows, or indexes, at the same time. It can lead to decreased performance, deadlocks, and in severe cases, system crashes. 

Examples:

1. Two customers competing for the same flight seat, hotel room, or restaurant reservation slot.

2. A video goes viral and millions of concurrent transactions are sent to the "videos" table to update the `videos.views` counter.

3. Multiple transactions trying to update the same row in a table, leading to deadlocks or lock waits (when one transaction holds a lock on a resource).

## Mitigations 

To mitigate database contention, the following strategies can be implemented: 

1. `Query optimization with Indexing (for relational databases)`: reduces the complexity of queries avoiding unnecessary full table scans during `read-heavy operations`. A [database index](https://en.wikipedia.org/wiki/Database_index) stores the values of one or more columns, that are frequently queried, in a data structure (often a B-Tree or a Hash Table) along with pointers to the original rows, allowing for efficient lookups (search and retrieval).
- Drawback: introduces an overhead in `write-heavy operations` because indexes must be updated whenever data in the indexed column is modified (inserted, updated, or deleted).

2. `Database denormalization`: stores related data in a single table or document avoiding join operations. This reduces the number of read operations required for a single query, thus reducing contention in scenarios with read-heavy workloads (e.g., OLAP or NoSQL).
- Drawback: introduces data redundancy and complex updates that involve iterating through the table's rows.

3. `Locking and serialization`: **locks** are often used to enforce isolation by preventing multiple transactions from accessing or modifying the same data simultaneously. Recalling from ACID transactions, isolation means that transactions operate independently without interference from concurrent transactions. [Serializable isolation](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/transaction-apis.html#transaction-isolation) in database transactions enables concurrent operations (parallel transactions) while ensuring that the result of those transactions is identical to if they were executed sequentially, without concurrency.
- Drawback: locks can lead to contention themselves, especially in write-heavy workloads, causing delays or deadlocks if not managed carefully. However, modern databases employ an optimization technique known as `multiversion concurrency control (MVCC)` that can handle concurrent transactions such as simultaneously reading and writing data while avoiding locking.

# Thundering Herd

The thundering herd is a concurrency problem that occurs ["when a large number of processes or threads waiting for an event are awoken when that event occurs, but only one process is able to handle the event"](https://en.m.wikipedia.org/wiki/Thundering_herd_problem), causing the others to be awakened unnecessarily. It can lead to system inefficiency, as all awakened processes consume CPU time and other resources, even though most will immediately go back to sleep.

Example: imagine 100 server processes all waiting to accept a new client connection request. When a connection arrives, all 100 "wake up" to handle it, but only one actually gets the connection.

## Facebook's Thundering Herd Issue

["When millions of people tune in to a Live broadcast simultaneously, potentially 100s of thousands of video requests will see a cache miss at the Edge Cache servers. This results in excessive queries to the Origin Cache and Live processing servers, which are not designed to handle high concurrent loads. We solved this "thundering herd" problem by creating request queues at the Edge cache servers, allowing one request to go through to the livestream server and return the content to the Edge cache, where it is distributed to the rest of the queue all at once."](https://m.facebook.com/watch/?v=10153675295382200&vanity=Engineering)

## Mitigations 

To mitigate the thundering herd problem, the following strategies can be implemented:

1. `Load balancing`: distribute the load of incoming traffic (requests) across different threads/processes.
2. `Request queuing`: serialize access to shared resources so that only one thread/process handles a resource at a time, preventing simultaneous wake-ups.
3. `Event multiplexing`: system calls such as **select**, **poll**, and **epoll** in Linux use a single thread/process to handle multiple events.
4. `Rate limit`: limiting how often threads/processes wake up can prevent too many of them from waking up all at once.

# Deadlock 

Deadlock is a problem in concurrent programming where two or more processes are waiting for each other to release resources, creating a cycle of dependencies that prevents any of them from proceeding.

# References 

[1] https://learn.microsoft.com/en-us/sql/relational-databases/sql-server-deadlocks-guide?view=sql-server-ver16

[2] https://m.facebook.com/watch/?v=10153675295382200&vanity=Engineering
