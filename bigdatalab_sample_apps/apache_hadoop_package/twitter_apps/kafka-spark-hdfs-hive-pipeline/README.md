# Kafka-Spark-Hdfs-Hive Pipeline
This is a simple pipeline application with kafka producer to get raw tweet data(json) from twitter. Then Spark Streaming will be used to extract required schema from the tweet data and dump the schema-oriented data to hdfs (/user/hive/twitter path location in hdfs). After this, one will create hive external table pointing to /user/hive/twitter path in hdfs. Then one can do hive query on tweet data.

## Getting Started

These instructions will get you running this application on **BigDataLab** environment for learning and development purposes.

### Prerequisites

Setup **BigDataLab** environment and have IP address of package host and hdfs host accessible from your local machine.  


### Installing

A step by step series to install the pipeline:

* SSH Login into Kafka container with user as 'root' and password as 'root@123':

```bash
ssh root@<package_host_ip> -p 7777
```

* Clone the **BigDataLabs_Sample_Apps** repository :

```bash
git clone https://github.com/DCEngines/bigdatalab.io.git
```

* Go to the Directory bigdatalab.io/bigdatalab_sample_apps/apache_hadoop_package/twitter_apps/kafka-spark-hdfs-hive-pipeline:

```bash
cd bigdatalab.io/bigdatalab_sample_apps/apache_hadoop_package/twitter_apps/kafka-spark-hdfs-hive-pipeline
```

* Install required to run kafka twitter producer :

```bash
pip install -r requirements.txt
```

* Add your twitter app credentials  in twitter_app_credentials.txt.

* Open the below url on browser for Zeppelin Web UI :

```
<package_host_ip>:8080
```

* Go to Zeppelin interpreter setting and find spark interpreter and edit the spark interpreter to add the following property :

```
spark.hadoop.df.defaultFS with value hdfs://<hdfs_host_ip>:9000
```

* Also add below dependency in spark interpreter artifact :

```
org.apache.spark:spark-sql-kafka-0-10_2.11:2.1.0
``` 

* Save the spark interpreter(It will also restart the interpreter and download the dependency) 

* Go to The **Twitter-Spark-Stream** notebook in Notebook drop down. And set HDFS_URL with:

```scala
val HDFS_URL = "hdfs://<hdfs_host_ip>:9000"
```

Above steps will setup and configure the pipeline.

## Running the App

* At directory bigdatalab.io/bigdatalab_sample_apps/apache_hadoop_package/twitter_apps/kafka-spark-hdfs-hive-pipeline in Kafka Container, run below command:

```bash
python twitter_producer.py
```

* At **Twitter-Spark_Stream** notebook in Zeppelin, start(run) first para. It will run for 30 sec.

* After above para stops, stop the Kafka twitter_producer.py also(using ctrl-C or ^C) in Kafka Container.

* Go To The **Twitter-HiveQL** notebook in Notebook drop down, start(run) first para. This is create twitterdb database and tweets table inside hive.

Now you can run any hive query on tweets using **Twitter-HiveQl** notebook. There are few query already present in notebook.

