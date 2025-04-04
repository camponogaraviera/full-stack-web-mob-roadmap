<div align='center'>
  <h1>Relational Databases</h1>
</div>

# Table of Contents

- About
- Components of an RDB
- RDBMS Technologies

# About

A Relational Database (RDB) is a type of database structure that stores structured data in tables with a strict schema using rows and columns. Relational databases prioritize data `Consistency` and `Availability` ([CAP theorem](05_CAP_theorem.md)) in a single node-setup. A `Relational Database Management System (RDBMS)` is the software used to manage and interact with a relational database.

- Pros: suitable for OLAP (**analytical operations**) workloads and to perform **table joins**. Good for static data. Data always maintains the same format. Has support for ACID transactions and is optimized for read-heavy workloads.
- Cons: More expensive to scale horizontally since table joins require multiple round trips to query data. It makes it more difficult to handle too many concurrent connections.

- Keywords:
  - `Table joins (JOIN clause):` are query operations that combine rows from two or more tables based on related columns (fields) between them. This is typically done through foreign keys. For example, a social media application with a MySQL database design would have the following tables: `users table`, `reactions table`, `comments table`, and `photos table`. These tables are then related by foreign keys (userID, reactionID, commentID, photoID). A foreign key in table A is a primary key from table B. It is used to ensure referential integrity, i.e., to ensure that an item or row in table A also exists/matches an item in table B.
  - `Analytical operations:` data aggregation from multiple sources, complex filtering, and statistical analysis.

Note: `Vitess` is a database clustering system for horizontal scaling of MySQL created by YouTube.

# Components of an RDB

- A Schema defines the structure of the database, including the tables and the relationships between them.
  
- A Table is a collection of rows (tuples) and columns (a.k.a attributes).

- Each table has a primary key, which is a unique `identifier` and is indexed by default. Secondary indexes can be added to non-key fields (columns) to improve query performance.

- A collection of rows is known as the `cardinality`.

- Each record has the same number of columns.

- A collection of columns is also known as `Degree`.

- Each column has a domain, i.e., a constraint for input data.

- A foreign key identifies the relationship between two pieces of data.

# RDBMS Technologies 

The following are examples of RDBMS software used to implement/create a relational database model. These softwares use Structured Query Language (SQL) as their primary language for managing and querying the database:

- MySQL.
- SQLite.
- Microsoft SQL Server (MSSQL).
- PostgreSQL.
- Oracle Database.
- MariaDB.
- cockroachDB: has support for horizontal scaling.
- AWS:
  - Amazon RDS.
  - Aurora (OLTP).
  - Redshift (OLAP data warehouse).
