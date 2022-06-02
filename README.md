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

![image](https://user-images.githubusercontent.com/36766101/171516317-7c12c639-087f-4247-842d-8b605efe1c5e.png)

![image](https://user-images.githubusercontent.com/36766101/171408182-593ab002-3e12-47d2-bf41-8e4d10801f62.png)
![image](https://user-images.githubusercontent.com/36766101/171408271-ac961088-ef63-4e77-b9c5-be667ffd9fd4.png)
![image](https://user-images.githubusercontent.com/36766101/171411155-64935646-7aec-4ae2-9ef2-0d0506591684.png)



The entire contents of the three tables will be exported from RDS to Kafka by the source connector, then exported from Kafka to S3 by the sink connector.



Kafka and Apache ZooKeeper run on Amazon MSK. Update the bootstrap.servers property to reflect your comma-delimited list of Amazon MSK Kafka Bootstrap Brokers. To get the list of the Bootstrap Brokers for your Amazon MSK cluster, use the AWS Management Console or the following AWS CLI commands:


# get the msk cluster's arn
aws kafka list-clusters --query 'ClusterInfoList[*].ClusterArn'
# use msk arn to get the brokers
aws kafka get-bootstrap-brokers --cluster-arn your-msk-cluster-arn
# alternately, if you only have one cluster, then
aws kafka get-bootstrap-brokers --cluster-arn $(aws kafka list-clusters | jq -r '.ClusterInfoList[0].ClusterArn')


# Using Kafka Connect to Capture Data from a Relational Database
Kafka Connect can be used for a variety of databases, given it has a multitude of built-in connectors. In this hands-on lab, we set up Kafka Connect in a way allowing us to continuously stream data from a SQLite database directly into Kafka. This lab demonstrates how easy it is to use Kafka Connect to get data into Kafka for further analysis.

# Confirm Access to Amazon MSK from Kafka Connect
To confirm you have access to Kafka running on Amazon MSK, from the Kafka Connect container running on Amazon EKS, try listing the exiting Kafka topics:

bin/kafka-topics.sh --list \
  --bootstrap-server $BBROKERS \
  --command-config config/client-iam.properties
  
# You can also try listing the existing Kafka consumer groups:

bin/kafka-consumer-groups.sh --list \
  --bootstrap-server $BBROKERS \
  --command-config config/client-iam.properties
  
  
  
The ability of the Kafka client to interact with Amazon MSK, AWS SSM Parameter Store, and AWS Secrets Manager is based on two IAM policies created by Terraform, EKSKafkaClientMSKPolicy and EKSScramSecretManagerPolicy. These two policies are associated with a new IAM role, which in turn, is associated with the Kubernetes Service Account, kafka-client-msk-sasl-scram-serviceaccount. This service account is associated with the Kafka client pod as part of the Kubernetes Deployment resource in the Helm chart.

# Use the Kafka client to create Kafka topics and Apache Kafka ACLs
export KAFKA_CONTAINER=$(  kubectl get pods -n kafka -l app=kafka-client-msk |     awk 'FNR == 2 {print $1}')
    
kubectl exec -it $KAFKA_CONTAINER -n kafka -- bash


export ZOOKPR=$(  aws ssm get-parameter --name /msk/scram/zookeeper     --query 'Parameter.Value' --output text)
export BBROKERS=$(  aws ssm get-parameter --name /msk/scram/brokers     --query 'Parameter.Value' --output text)


![image](https://user-images.githubusercontent.com/36766101/171518249-c16c8dd1-ddb8-473f-bafe-cf5b07b5bd1c.png)
![image](https://user-images.githubusercontent.com/36766101/171518859-0d677f77-71a5-4a93-8b8d-e21b5a6da7e8.png)


![image](https://user-images.githubusercontent.com/36766101/171525721-a771257c-6b20-4721-9b56-1ffc43b5c0d7.png)
![image](https://user-images.githubusercontent.com/36766101/171527305-00200794-04de-42d7-a310-bf20739f75c8.png)
![image](https://user-images.githubusercontent.com/36766101/171527682-dc9c63cf-acee-46a5-a87b-f32dc855b6ed.png)


