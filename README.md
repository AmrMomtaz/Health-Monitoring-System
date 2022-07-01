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
![lambda](https://github.com/AmrMomtaz/Health-Monitoring-System/blob/main/images/lambda.png)<br>

* The data is persisted in the batch layer using HDFS commands initiated from the scheduler.
* Then the data is processed regularly using map-reduce to generate batch views creating the serving layer.
* For the speed layer, Spark jobs are initiated for new data generating realtime views.
* We've used <b>DuckDB</b> for both NOSQL databes for the serving layer and speed layer.
* The scheduler handles initiating the mapreduce jobs and expiring the realtime views.

## Micro-services 

The mock microservices system is built in which
services send health messages to a certain client server. The client server should
receive these messages, catalog, and persist them on HDFS.
![microservices](https://github.com/AmrMomtaz/Health-Monitoring-System/blob/main/images/microservices.png)<br>
* The health messages will be in JSON format. Each message contains the service
name, timestamp, CPU utilization percentage, RAM total and free space in GBs,
and Disk total and free space in GBs.<br>
* The services will send messages to the Health Monitor system using the UDP
protocol. Use port 3500 on the Health Monitor side. The message will simply
contain the health message json string.<br>

<b>Please Note:</b> In the next sections we've developed tha mapreduce jobs and spark jobs to work on CSV files.
So you will find a script which converts the JSON file to CSV in mock_microservices directory.
