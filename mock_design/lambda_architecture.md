<div align='center'>
    <h1> Design a Big Data Lambda Architecture for Batch and Real-Time Analytics </h1>
</div>

# Lambda Architecture

- Batch Layer (persistent data storage & processing):

  - Processes large datasets in batches.
    - In AWS, [Amazon Kinesis Data Firehose](https://github.com/camponogaraviera/aws/blob/dev/services/aws_etl.md#amazon-kinesis) can batch, compress, transform, and encrypt input raw data streams before loading them into a data lake such as AWS S3.
    - In Apache, Apache Spark can proccess the data by executing scripts (Java, R, Scala, Python, and SQL) and distributing tasks across a Hadoop cluster to handle the data in a parallelized manner.
  - Stores historical and accurate data in a distributed file system (e.g., S3 or HDFS).
  - Generates precomputed results as batch views using [AWS Glue](https://aws.amazon.com/glue/) for the serving layer.

- Speed Layer (real-time processing):

  - As opposed to the batch layer, which provides historical data, the speed layer provides low-latency, real-time views.
  - Processes streaming data, i.e., it ingests and processes data as it arrives using [Amazon Redshift streaming ingestion](https://aws.amazon.com/redshift/redshift-streaming-ingestion/).
  - Generates real-time views that complement batch views from the batch layer.

- Serving Layer (query engine):

  - Comprises an Amazon Redshift Serverless endpoint and a consumption layer including [AWS API Gateway](https://aws.amazon.com/api-gateway/), [AWS SageMaker](https://aws.amazon.com/pm/sagemaker/), and [AWS QuickSight](https://aws.amazon.com/quicksight/?amazon-quicksight-whats-new.sort-by=item.additionalFields.postDateTime&amazon-quicksight-whats-new.sort-order=desc).
  - This layer combines batch views (historical, complete data) from the batch layer and real-time views (latest, low-latency data) from the speed layer to serve predictions or aggregated outputs. [Amazon Redshift Serverless](https://aws.amazon.com/redshift/redshift-serverless/) can be used for querying structured and semistructured data without loading it into Amazon Redshift tables, as well as scaling analytics without managing the data warehouse infrastructure.
  - Real-time data analytics can be computed with [Amazon Redshift Cluster](https://docs.aws.amazon.com/redshift/).

# References

[1] https://aws.amazon.com/blogs/big-data/build-a-big-data-lambda-architecture-for-batch-and-real-time-analytics-using-amazon-redshift/
