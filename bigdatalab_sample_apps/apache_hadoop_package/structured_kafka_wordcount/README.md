# Structured Spark Streaming Kafka WordCount Example

This example consumes messages from one or more topics in Kafka and does word count. Following is the usage of word count example.

```bash
StructuredKafkaWordCount <bootstrap-servers> <subscribe-type> <topics>
```

## Getting Started

These instructions will get you running this application on **BigDataLab** environment for learning and development purposes.

### Prerequisites

Setup **BigDataLab** environment and have IP address of package host and hdfs host accessible from your local machine.


### Installing the Application

using the following steps to install the example.

* SSH Login into Kafka container with user as 'root' and password as 'root@123':

```bash
ssh root@<package_host_ip> -p 7777
``` 

* Go the Kafka directory:

```bash
cd /kafka
``` 

* Create a Kafka topic, let's say "test":

```bash
bin/kafka-topics.sh --create --zookeeper <kafka-container-name>:2181 --replication-factor 1 --partitions 1 --topic test
```

* Start a console producer. Input from the console is send to the Kafka partition:

```bash
bin/kafka-console-producer.sh --broker-list <kafka-container-name>:9092 --topic test
```
Leave this terminal as it is.

* Start another terminal and SSH Login into Spark container with user as 'root' and password as 'root@123':

```bash
ssh root@<package_host_ip> -p 7770
```

* Go to spark directory:

```bash
cd /spark
```

* Submit the spark example that counts the word published by the console producer. The code for this application can be found [HERE](https://github.com/apache/spark/blob/branch-2.1/examples/src/main/scala/org/apache/spark/examples/sql/streaming/StructuredKafkaWordCount.scala):

```bash
bin/spark-submit  --master spark://10.0.7.11:7077  --jars ./external/kafka-0-10-sql/target/spark-sql-kafka-0-10_2.11-2.1.0.jar  --packages org.apache.spark:spark-streaming-kafka-0-10_2.11:2.1.0  --class org.apache.spark.examples.sql.streaming.StructuredKafkaWordCount   ./examples/target/scala-2.11/jars/spark-examples_2.11-2.1.0.jar   <kafka-container-name>:9092 subscribe test
```

### Running the App

Go back to Kafka container terminal, input some words on the console producer. And one will see the word count for each words in spark container terminal.

