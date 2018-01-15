# Sentiment Analysis of Input from Flume
Apache Flume is a service for collecting log data. You can capture events in Flume and store them in HDFS for analysis. In this example, we will configure Flume to write incoming messages to data files stored in HDFS and then apply the sentiment analaysis on these messages.

###### Note : - This example is pickup from [HERE](https://www.cloudera.com/documentation/other/tutorial/CDH5/topics/ht_flume_to_hdfs.html).
## Prerequisites
Get **BigDataLab Cloudera Package** and make sure following services are running on Cloudera cluster.
* HDFS service
* Hue service 
* Flume service

## Getting Started

### Writing from Flume to HDFS
You can configure Flume to write incoming messages to data files stored in HDFS for later processing.
To configure Flume to write to HDFS.
* Open Hue. Click File Browser.
* Create the ***/user/cloudera/flume/events*** directory.
  * In the /user directory, click NEW->Directory.
  * Create a directory named cloudera.
  * In the cloudera directory, create a directory named flume.
  * In the flume directory, create a directory named events.
  * Check the box to left of the events directory, then click the Permissions setting.
  * Enable Write access for Group and Other users.
  * Click Submit.
* Change the Flume configuration.
    * Open the Cloudera Manager in your web browser.
    * In the list of services, click Flume.
    * Click the Configuration tab.
    * Scroll or search for the Configration File item.
    * Append the following lines to the Configuration File settings. Please change ***HDFS-NAMENODE-IP*** to the hdfs namenode container's ip address.
    ```
        tier1.sinks.sink1.type=HDFS
        tier1.sinks.sink1.fileType=DataStream
        tier1.sinks.sink1.channel=channel1
        tier1.sinks.sink1.hdfs.path=hdfs://<HDFS-NAMENODE-IP>:8020/user/cloudera/flume/events
    ```
    * At the top of the setting list, click Save Changes.
* On the far right, choose Actions->Restart to restart Flume.
* When the restart is complete, click Close.
* Click the Home tab. If necessary, start the Yarn service.
* Enter to the container where the Flume Agent is running.
* In a terminal window, launch Telnet with the command ```telnet localhost 9999```.
* At the prompt, enter ```Hello HDFS!```
* In the Hue File Browser, open the /user/cloudera/flume/events directory.
* There will be a file named FlumeData with a serial number as the file extension. Click the file name link to view the data sent by Flume to HDFS.

### Sentiment Analysis of Input from Flume
#### Java 
Please check java command is available or not by running below command:
```sh
sh> java -version
```

If above shows Java version, then skip the below command to export the java:
```sh
sh> export PATH=$PATH:/usr/java/jdk1.7.0_67-cloudera/bin
```

Please check again after running above command, whether java is available or not.

#### Run Sentiment Analysis 
Now that Flume is sending data to HDFS, you can apply the Sentiment Analysis example to comments to you enter.
The source for this example is provided in ***bigdatalab.io/bigdatalab_sample_apps/cloudera_hadoop_package/sentiment_analysis***.
To test sentiment analysis with Flume input:
* In Flume agent container, clone above source and got to ***flumeToHDFS*** directory.
* Launch Telnet with the command ```telnet localhost 9999```.
* Enter the following lines, hitting Enter after each line. (Telnet returns the response OK to each line).
```
I enjoy using CDH. I think CDH is wonderful.
I like the power and flexibility of CDH.
I dislike brussels sprouts. I hate mustard greens.
Flume is a great product. I have several use cases in mind for which it is well suited.
```
* Run ```make run_flume``` to start the Sentiment Analysis example via the makefile. The application returns results from all counters, ending with custom counters and report.
###### Note : - If getting `Permission denied` error while running `make` command, then please grant write permission to /user/cloudera directory in HDFS.
