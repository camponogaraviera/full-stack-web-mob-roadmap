<div align='center'>
  <h1>ACID and Base Properties</h1>
</div>

# Table of Contents

- ACID
  - Atomicity
  - Consistency
  - Isolation
  - Durability
  - Can NoSQL Databases be ACID-compliant?
- BASE
  
# ACID

ACID is a database transaction model mostly used by Relational databases. 
ACID stands for Atomicity, Consistency, Isolation, and Durability. 
These form a set of properties that ensure database transactions are processed reliably.

Note: a transaction is a sequence of database operations performed as a single unit of work. ACID transactions are typically used within a single database to ensure data integrity.

## Atomicity

Ensures the transaction is either executed completely (succeeds) or not at all (fails).

## Consistency

The transaction undergoes a rollback (is reverted) unless all database rules are applied. In other words, data inserted into the table must conform to the database schema.

## Isolation

Transactions operate independently without interference from concurrent transactions. This requires a database row to be locked when a transaction begins, and be unlocked for next transactions only when the previous transaction is finished.

## Durability

After a transaction is committed, it persists even if the system experiences a crash immediately afterward. Unlike memory cache, in the event of a server failure, the data remains (persistent storage).

## Can NoSQL Databases be ACID-compliant?

Some NoSQL databases have implemented features to ensure partial ACID compliance. One example is [mongoDB](https://www.mongodb.com/basics/acid-transactions), which has support for [multi-document ACID transactions](https://www.mongodb.com/blog/post/mongodb-multi-document-acid-transactions-general-availability). Another example is [DynamoDB](https://github.com/camponogaraviera/full-stack-roadmap/blob/dev/system_design_and_infrastructure/database/06_technologies/DynamoDB.md), with the newly added support for [transactions](https://aws.amazon.com/blogs/aws/new-amazon-dynamodb-transactions/) that has enabled ACID properties to be enforced by the application.

Without Transactions, DynamoDB is BASE-compliant.

# BASE

BASE is a database transaction model mostly used by NoSQL databases. The BASE model is not as strict as the ACID model when it comes to consistency. While ACID properties prioritizes consistency, BASE properties aim for higher availability and partition tolerance in distributed systems.

- Basically Available: focuses on ensuring the system remains operational and can serve requests, even during partial failures or network partitions.

- Soft-state: acknowledges that data stored in the system may not always be immediately consistent across all nodes due to replication delays or partitions.

- Eventually Consistent: means that while immediate consistency isn't guaranteed, given enough time without new updates, the data will eventually reach a consistent state across all nodes.
