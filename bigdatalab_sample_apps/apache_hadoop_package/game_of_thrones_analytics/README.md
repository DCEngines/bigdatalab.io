# Game of Thrones Analytics with Cassandra, Spark and R

The example is picked up from [HERE](https://www.supergloo.com/fieldnotes/apache-spark-cassandra/). We have the data about the battles of Game of Thrones. We won't do any predictions but we would find out the kings that were attacked the most and the most aggressive kings. 

## Getting Started

These instructions will get you running this application on **BigDataLab** environment for learning and development purposes.

### Prerequisites

Setup **BigDataLab** environment and have IP address of package host and hdfs host accessible from your local machine.


### Installing

using the following steps to install the example.

* SSH Login to package host using user as 'root' and password as 'root@123':

```bash
ssh root@<package_host_ip>
```

* Go into Cassandra container:
```bash
ssh root@172.26.201.23 -p 7789 
```


* Install *Curl*  cmd script:

```bash
apt-get -y update && apt-get -y install curl
```

* Download the data on Cassandra server. Since the image is standard Cassandra image you will have to download with curl command:

```bash
curl -O https://raw.githubusercontent.com/chrisalbon/war_of_the_five_kings_dataset/master/5kings_battles_v1.csv
```

* Run a Perl script to update the end-of-line encodings from Mac to Unix on the CSV file:

```bash
perl -pi -e 's/\r/\n/g' 5kings_battles_v1.csv
``` 
Cassandra must be running in your cluster. Connect to cassandra create a KeySpace and a table. Populate the table with the downloaded battle data.

* Go to CQL Shell:

```bash
cqlsh
```

* Create Keyspace **gameofthrones** on cassandra:

```sql
CREATE KEYSPACE gameofthrones WITH replication = {'class': 'SimpleStrategy', 'replication_factor' : 1};
```

* Create Table in Keyspace "gameofthrones" as "battles" on cassandra:

```sql
CREATE TABLE gameofthrones.battles (
name TEXT,
year INT,
battle_number INT,
attacker_king TEXT,
defender_king TEXT,
attacker_1 TEXT,
attacker_2 TEXT,
attacker_3 TEXT,
attacker_4 TEXT,
defender_1 TEXT,
defender_2 TEXT,
defender_3 TEXT,
defender_4 TEXT,
attacker_outcome TEXT,
battle_type TEXT,
major_death TEXT,
major_capture TEXT,
attacker_size TEXT,
defender_size TEXT,
attacker_commander TEXT,
defender_commander TEXT,
summer TEXT,
location TEXT,
region TEXT,
note TEXT,
PRIMARY KEY(battle_number)
);
```

* Populate the table "battles" with downloaded csv data:

```sql
 COPY gameofthrones.battles (
name,
year,
battle_number,
attacker_king,
defender_king,
attacker_1,
attacker_2,
attacker_3,
attacker_4,
defender_1,
defender_2,
defender_3,
defender_4,
attacker_outcome,
battle_type,
major_death,
major_capture,
attacker_size,
defender_size,
attacker_commander,
defender_commander,
summer,
location,
region,
note)
FROM '5kings_battles_v1.csv'  // update this location as necessary
WITH HEADER = true;
```

Data is now populated in the Cassandra table. Now let's start the analysis. For that, we will use a **spark-shell**.

* SSH Login into Spark Container with user as 'root' and password as 'root@123':

```bash
ssh root@<package_host_ip> -p 7770
```

* Go to **spark** directory:

```bash
cd /spark
```

* Connect Spark master to cassandra:

```bash
bin/spark-shell --master spark://10.0.7.2:7077 --packages datastax:spark-cassandra-connector:2.0.5-s_2.11 --conf spark.cassandra.connection.host=10.0.7.5
```

* And on the spark-shell(```scala>```), create a Dataframe on the Cassandra table and peek into it.

```scala
scala> import org.apache.spark.sql.functions._
scala> import com.datastax.spark.connector._
scala> import com.datastax.spark.connector.rdd._
scala> import org.apache.spark.sql.cassandra._

scala> val df = spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "battles", "keyspace" -> "gameofthrones")).load()
scala> df.show 
```

### Running the App

* Now let's do some analysis. Find out which king has carried out most number of attacks.

```scala
scala> val countsByAttack = df.groupBy("attacker_king").count().sort(desc("count"))    
scala> countsByAttack.show()
```

* Register the battles DataFrame as a temp table. Now find out which king was attacked most number of times using SQL.

```scala
scala> df.createOrReplaceTempView("battlesTable")
scala> spark.sql("select defender_king, count(*) battles from battlesTable group by defender_king order by battles desc ").show
```

* Let's try out some analysis with Spark R. Start a Spark R shell with Cassandra connector.

```bash
./bin/sparkR --master spark://10.0.7.2:7077 --packages datastax:spark-cassandra-connector:2.0.5-s_2.11 --conf spark.cassandra.connection.host=10.0.7.5
```

* Create a DataFrame that points to the Cassandra's table. And create a temporary table out of it. 

```splus
> battles <-read.df( source = "org.apache.spark.sql.cassandra", keyspace = "gameofthrones", table = "battles")
> createOrReplaceTempView(battles, "battlesTable")
```

* Time for analysis - find out using SQL which region has seen the most blood.

```splus
> destDF <- sql("select region, count(*) battles from battlesTable group by region order by battles desc")
> head(destDF)
```

### Note : -

Monitor the queries on the Spark UI : - *http://<package_host_ip>:4040/SQL*

