Simple Spark Application
==============

This example has been taken from https://github.com/sryza/simplesparkapp. 

Note: You need to be inside a cloudera container first

A simple Spark application that counts the occurrence of each word in a corpus and then counts the
occurrence of each character in the most popular words.  Includes the same program implemented in
Java and Scala.

    git clone https://github.com/DCEngines/bigdatalab.io.git

To make the jar:
    
    cd /path/to/bigdatalab.io/bigdatalab_sample_apps/cloudera_hadoop_package/simplesparkapp/
    mvn package

it will create a "sparkwordcount-0.0.1-SNAPSHOT.jar" jar file in ./target folder

If maven is not installed in your system:

    wget https://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo
    sed -i s/\$releasever/6/g /etc/yum.repos.d/epel-apache-maven.repo
    yum install -y apache-maven

Insert an input file to hdfs
   
    sudo su hdfs 
    hdfs dfs -put /path/to/bigdatalab.io/bigdatalab_sample_apps/cloudera_hadoop_package/simplesparkapp/data/inputfile.txt /user/spark/

To run from a gateway node in a CDH5 cluster:

    spark-submit --class com.cloudera.sparkwordcount.SparkWordCount --master yarn \
      target/sparkwordcount-0.0.1-SNAPSHOT.jar hdfs://<namenode-ip>:8020/user/spark/inputfile.txt 2

Note: Execute the spark-submit while being hdfs user, else you might get permission error while accessing HDFS.

you can always change input file and threshold(filter out words with fewer than threshold occurrences) which is set to be hdfs://<namenode-ip>:8020/user/spark/inputfile.txt and 2 respectively.
