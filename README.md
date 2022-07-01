# Health Monitoring System

## Problem statement

Modern systems now follow a very well-known architectural pattern known as
microservices. Microservices refer to a group of small servers communicating to
achieve a given task or group of tasks. Each service tends to work independently
and communicate with another service whenever some calculation or information
is needed and cannot be found locally. Since these services work independent from
each other, the system is susceptible to partial failures when some services fail.
Moreover, as services are free to be deployed on different machines and platforms
and may not share the same computing resources, these resources need to be
monitored. For example, storage disks may reach their capacities, CPUs may be
highly utilized, memory may be highly utilized. Therefore, a health monitoring
system is needed to monitor the health of the system services and the resources
they may use. <br>

The goal is to use the lambda architecture for our health
monitor system. See the figure below:<br>
![https://github.com/AmrMomtaz/Health-Monitoring-System/blob/main/images/lambda.png](https://github.com/AmrMomtaz/Health-Monitoring-System/blob/main/images/lambda.png)<br>

* The data is persisted in the batch layer using HDFS commands initiated from the scheduler.
* Then the data is processed regularly using map-reduce to generate batch views creating the serving layer.
* For the speed layer, Spark jobs are initiated for new data generating realtime views.
* The output of mapreduce and spark jobs are <b>PARQUET</b> files.
* We've used <b>DuckDB</b> for both NOSQL databes for the serving layer and speed layer.
* The scheduler handles initiating the mapreduce jobs periodically and expiring the realtime views.

<b>Please note that you should have hadoop installed and you should have $HADOOP_HOME and $HADOOP_CLASSPATH are set correctly.</b>

## Micro-services 

The mock microservices system is built in which
services send health messages to a certain client server. The client server should
receive these messages, catalog, and persist them on HDFS.
![https://github.com/AmrMomtaz/Health-Monitoring-System/blob/main/images/microservices.png](https://github.com/AmrMomtaz/Health-Monitoring-System/blob/main/images/microservices.png)<br>
* The health messages will be in JSON format. Each message contains the service
name, timestamp, CPU utilization percentage, RAM total and free space in GBs,
and Disk total and free space in GBs.<br>
* The services will send messages to the Health Monitor system using the UDP
protocol. Use port 3500 on the Health Monitor side. The message will simply
contain the health message json string.<br>

<b>Please Note:</b> In the next sections we've developed tha mapreduce jobs and spark jobs to work on CSV files.
So you will find a script which converts the JSON file to CSV in mock_microservices directory. Finally the data should have
the following form:<br>
![[data](https://github.com/AmrMomtaz/Health-Monitoring-System/blob/main/images/data.png)](https://github.com/AmrMomtaz/Health-Monitoring-System/blob/main/images/data.png)

## MapReduce

The map reduce jobs will calculate the required statistics:
* The mean CPU utilization for each service.
* The mean Disk utilization for each service.
* The mean RAM utilization for each service.
* The peak of utilization for each resource for each service.
* The count of health messages received for each service.<br>

The map step of any map-reduce job processes each record
individually producing a record or more as a result. The reduce step collects all
records having a common attribute and produces one record summarizing,
reducing, these records.<br>
You can find the MapReduce.jar in the mapreduce directory. Run the mapreduce job using the following command.<br>
```
$HADOOP_HOME/bin/hadoop jar MapReduce.jar HealthMapReduce
```
<b>PLEASE NOTE:</b> You should create a /Input directory in HDFS and put the data in it.
The data should be in .csv files as shown before. You will use the following commands
to create new directory and to move the data to HDFS
```
$HADOOP_HOME/bin/hdfs dfs -mkdir <folder name>
$HADOOP_HOME/bin/hdfs dfs -copyFromLocal <local file path>  <dest(present on hdfs)>
```
If you want to change the source code and create a new mapredeuce.jar file use the following commands:
```
To compile the code:
$HADOOP_HOME/bin/hadoop com.sun.tools.javac.Main HealthMapReduce.java
To create JAR file:
jar cf MapReduce.jar HealthMapReduce*.class
```
(You will find in images photos of HDFS running and the data in the /Input directory)<br><br>

Finally, The mapreduce create 5 PARQUET files which are [year.parquet , mounth.parquet , day.parquet , hour.parquet , minute.parquet] in /Output directory in HDFS.
Everytime mapreduce job is initiated it overwrites them. Then we use DuckDB to query the parquet files. (you will find the query code in the mapreduce directory)
