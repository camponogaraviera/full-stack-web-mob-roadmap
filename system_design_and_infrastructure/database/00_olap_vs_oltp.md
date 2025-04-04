<div align='center'>
  <h1>OLAP vs OLTP</h1>
</div>

# About

"Both online analytical processing (OLAP) and online transaction processing (OLTP) are database management systems for storing and processing data in large volumes. They require efficient and reliable IT infrastructure to run smoothly. You can use them both to query existing data or store new data. Both support data-driven decision-making in an organization." —[AWS](https://aws.amazon.com/compare/the-difference-between-olap-and-oltp/#:~:text=OLAP%20and%20OLTP.-,Key%20differences%3A%20OLAP%20vs.%20OLTP,data%20analysis%2C%20and%20identify%20trends.).

# OLAP

Is a database management system used to perform analytical operations: data aggregation from multiple sources, complex filtering, and statistical analysis. Also used to analyze data collected by OLTP. In a retail system, it can be used to generate reports on sales trends, inventory levels, customer demographics, and other key metrics so that businesses can make better decisions.
    
- Uses multidimensional (cubes) or RDBMS databases. Uses star schema (denormalized), snowflake schema (normalized), or other analytical models. 
- Technologies: [Amazon Redshift](https://aws.amazon.com/redshift/).
  
# OLTP

Is a database management system used to process database transactions in real time. OLTP ensures data integrity through ACID compliance. It can be used with either RDBMS or NoSQL database technologies with a normalized or denormalized schema. Use cases: checkouts, searches, manage bookings, social media posts, and retail system.

- Technologies: AWS Aurora (RDBMS), AWS DynamoDB (NoSQL), MongoDB (NoSQL), and Cassandra (NoSQL). 
- In a retail system, OLTP handles tasks such as processing customer orders, updating inventory levels, and managing customer accounts. 
