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
![lambda](https://github.com/AmrMomtaz/Health-Monitoring-System/blob/main/Images/lambda.png)
