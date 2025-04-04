<div align='center'>
  <h1>Data Normalization vs Denormalization</h1>
</div>

# Table of Contents

- About
- Data Normalization
- Data Denormalization 

# About

By design, horizontally distributed NoSQL databases, such as DynamoDB and MongoDB, do not support traditional SQL-based JOIN operations across shards. As a circumvention, a JOIN clause can be faked by doing a second key-value lookup (not recommended as it goes against the design philosophy of NoSQL). Another workaround is data denormalization. 

# Data Normalization

Normalization is the traditional way of splitting entities across different tables in a relational database. For example, storing customers and restaurants into different tables in a relational database.

- Pros: data in a single table is not redundant (requires less storage). Enables faster writes, and updates happen in just one table (e.g., updating a customer's phone number).
- Cons: slower reads (more lookups or roundtrips between different tables), and indexing is less efficient due to JOINs.

# Data Denormalization 

Denormalization is an optimization technique that deliberately introduces redundancy into a database to reduce the need for join operations. It can be applied to both SQL and NoSQL databases. In horizontally distributed NoSQL databases, its design philosophy prioritizes a single table design enabling queries to be performed in a single table avoiding JOIN operations.

- Pros: faster reads (fewer lookups) since the information for a given request is in a single row. Prioritizes a single-table design model minimizing the number of tables as compared to normalized data.
- Cons: since it has redundant data (requires more storage) it can cause slower writes and duplicated updates. The database is complex, and updates involve iterating through every row. It can lead to data inconsistency.
