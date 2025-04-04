<div align='center'>
  <h1>MongoDB vs DynamoDB</h1>
</div>

- Choose DynamoDB:
  - For easy integration with everything AWS-related.
  - To start with a cheaper plan. 
  - If it is ok to use HTTPS endpoints to access DynamoDB tables.
  - If items do not exceed 400KB (the limit).
  - If you need a serverless system, i.e., auto-scaling (on-demand capacity mode) for read and write capacity units.

- Choose MongoDB: 
  - To avoid vendor lock-in, i.e., to enable hosting on multiple cloud providers (AWS, Azure, GCP, etc.). It can be integrated into AWS functions.
  - To save money in the long run as the system scales.
  - If it is ok to use TCP socket connections to access a MongoDB database.
  - If items do not exceed 16MB (the limit).
  - If you need auto-scaling (on demand) for read and write capacity units.
  - If you need queries to be done using keys, ranges, JOIN operations, graph traversals, and geospatial queries. MongoDB joins are different from relational database joins.

# Summary

DynamoDB is faster than MongoDB, however, it can be more expensive than MongoDB for larger systems.
