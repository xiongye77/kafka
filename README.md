# kafka

Amazon RDS for PostgreSQL instance (data source);
Amazon S3 bucket (data sink);
Amazon MSK cluster;
Amazon EKS cluster;
Connectivity between the Amazon RDS instance and Amazon MSK cluster;
Connectivity between the Amazon EKS cluster and Amazon MSK cluster;
Ensure the Amazon MSK Configuration has auto.create.topics.enable=true. This setting is false by default;
IAM Role associated with Kubernetes service account (known as IRSA) that will allow access from EKS to MSK and S3

Kafka Connect is a tool for scalably and reliably streaming data between Apache Kafka and other systems. It makes it simple to quickly define connectors that move large collections of data into and out of Kafka. Kafka Connect can ingest entire databases or collect metrics from all your application servers into Kafka topics, making the data available for stream processing with low latency. 
![image](https://user-images.githubusercontent.com/36766101/166189095-2d6ef7ef-b306-43f6-9216-a677347e8873.png)



Confluent’s Kafka Connect JDBC Source connector imports data from any relational database with a JDBC driver into an Apache Kafka topic. The Kafka Connect JDBC Sink connector exports data from Kafka topics to any relational database with a JDBC driver.

This first Kafka Connect source connector uses Confluent’s Kafka Connect JDBC Source connector (io.confluent.connect.jdbc.JdbcSourceConnector) to export data from RDS with a JDBC driver and import that data into a series of Kafka topics.

Confluent’s Kafka Connect Amazon S3 Sink connector exports data from Apache Kafka topics to S3 objects in either Avro, Parquet, JSON, or Raw Bytes.
This first Kafka Connect sink connector uses Confluent’s Kafka Connect Amazon S3 Sink connector (io.confluent.connect.s3.S3SinkConnector) to export data from Kafka topics to Amazon S3 objects in JSON format.

The entire contents of the three tables will be exported from RDS to Kafka by the source connector, then exported from Kafka to S3 by the sink connector.
