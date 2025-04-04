<div align='center'>
  <h1>Data Lakes vs Data Warehouses</h1>
</div>

# Table of Contents

- Data Lakes
- Data Warehouses

# Data Lakes

- Data Types: Data lakes store all types of raw data: structured, unstructured, semi-structured, relational, and non-relational data. This flexibility allows data lakes to ingest data from a wide range of sources.

- Processing: Data lakes allow data to be stored as-is, with no strict requirement for pre-processing. This means data can be retained in its raw format and processed at the time of analysis as needed.

- Data Lakes use a `schema-on-read` approach, meaning that the data schema is defined only when the data is read or queried. 

# Data Warehouses

- Data Types and Processing: Data warehouses generally require data to be pre-processed, cleaned, and structured before being stored. This means data must often be transformed into a structured format suitable for analytics, making data warehouses less flexible in the types of data they store.

- Use Cases: They are optimized for analytics, operational reporting, and business intelligence (BI) use cases, where structured and reliable data is essential for generating insights.

- Data Warehouses use a `schema-on-write` approach, meaning that data must conform to a defined schema before it is written into the warehouse.
