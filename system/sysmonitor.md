

# System Resource Monitoring

System Resource Monitoring Enhancements.

# High Level Design Document

#### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definition-abbreviation)
  * [Feature Overview](#FeatureOverview)
  * [Requirements](#Requirements)
  * [Design](#Design)
  * [Unit Test](#UnitTest)


# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 04/12/2019  |   Kalimuthu        | Initial version                   |

# About this Manual

This document provides general information about the System Resource Monitoring Enhancements feature implementation in SONiC.

# Scope

This document describes the high level design of System Resource Monitoring Enhancements feature. 

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| ODM                      | Original Design Manufacturer        |
| RSS                      | Resident Set Size

# 1 Feature Overview

As SONiC runs on different ODM platform hardware configurations, it becomes necessary to have a monitoring framework that is native to SONiC to monitor the usage of system resources and software service state and generate  the syslog alert when it reaches a certain threshold . However, it should be noted that the scope of this framework is limited to monitoring and generating the syslog alert. The user can take the corrective measures in the context of the SONiC OS. 



## 1.1 Requirements
  
### 1.1.1 Functional Requirements

 - The monitoring framework should monitor both hardware and software resources in the  SONiC system
 - The hardware resource monitoring should include CPU, physical memory
   and disk usage. 
 - The software resource monitoring should include  process, docker and
   core systemd services in the system
 - It should monitor and report the resource usage  through system syslog message
 - The system monitoring framework should run as a separate service and it should get launched automatically during bootup.  System monitoring service should be  started by default. 
 - It should support three levels of threshold limit for each resource type
 - It should generate syslog message with log level as INFO, WARNING, ALERT and CRITICAL for respective threshold levels.
 - The resource threshold limit should be derived from the respective resource type and its system configuration.
 - Resource monitoring interval should be derived from the system platform configuration
 - Only one syslog message should be generated when the resource usage crosses the threshold limit.
 - When a resource usage stays at same level for long time, it should generate the syslog memssage for every predefined interval.
 - The framework should also support the custom action handlers so that applications can perform actions specific to the  resource limit and the resource type.
 - The framework should check resource usage at every predefined sampling interval.

Resource Monitoring
 - CPU Monitoring
	 - Generate the syslog alert with process stats when its usage crosses the threshold limit.
	 - CPU threshold shall be  predefined as  70%, 80%, 90% of high CPU utilization.
 - Memory Monitoring
	 - Generate the syslog alert with system memory info and per process memory stats when system memory usage crosses the threshold limit.
	 - Memory threshold shall be predefined as 70%, 80%, 90% of system memory
	 - Per process memory monitoring
		 - Generate the syslog alert with process memory stats when per process memory crosses the threshold limit.
		 - Memory threshold shall be predefined as 30%, 40%,  above 50% of system memory.

 - Disk Partition Monitoring
	 - Generate the syslog alert with partition stats when its usages crosses the threshold limit.
	 - Disk threshold shall be predefined as 70%, 80%, 90% of partition size
	 - 
 - Service Monitoring
	 - It should monitor the core systemd services and generated a syslog for system ready state.
	 - Generate  'system ready' syslog message when all the core services are up and port initialization is done.
	 - Generate 'system not ready' syslog message along with state of services when one of the services goes down. 
 

### 1.1.2 Configuration and Management Requirements

 - Threshold levels are derived from the system configurations which includes
     - Running config
	 - Number of CPU cores
	 - Hardware Memory size
	 - Disk size and number of partitions
	 - Platform specific data

### 1.1.3 Scalability Requirements
   NA
### 1.1.4 Warm Boot Requirements
  NA
# 2 Design
## 2.1 Overview

Sysmonitor framework monitors various software and hardware resources in the SONiC system.  There would be three levels of threshold limit defined for each of the resource type. It checks resource usage with its predefined threshold limit and generates the syslog alert message along with resource stats information.  
Only one syslog message should be generated when the resource usage crosses the threshold limit. When a resource usage stays at same level for a long time, it should generate the syslog memssage for every predefined interval.  The predefined interval would be ten times of sampling interval.



![](images/Sysmonitor.png)



The framework supports both software and hardware resource types.  It includes the three major hardware resource types as CPU, Memory and Disk partitions and the software resource types as systemd core services.  

## 2.4 Syslog Levels 

The syslog alert message and related statistics are forwarded to syslog messaging system. The following three levels of syslog shall be supported 

	Level0  - INFO(NORMAL)
	Level1  - WARN
	Level2  - ALERT
	Level3  - CRITICAL
 
## 2.3 Threshold Limit

 The resource limits are automatically derived from the system configurations(system memory, cores, disk, running config, etc) .  By default, the threshold limits are defined as three levels and each of the level maps to corresponding syslog levels.
  
	  Level0 - 00-70%  - INFO(NORMAL)
	  Level1 - 70-80%  - WARN
	  Level2 - 80-90%  - ALERT
	  Level3 - 90-100% - CRITICAL
 
 There are thresholds  that are specific to the particular resource would be defined under the resource type. 

## 2.4 Sampling Interval

   By default, the sampling interval is set to 3 minutes which indicates that every 3 minutes resource usage is being monitored and checked against the threshold. The sampling interval is fixed by default and it gets adjusted based on the system resource configuration (System memory, cpu core, disk, running configs, etc).
      
#### System Service Monitoring:
   
   In SONiC, it is essential know the current state the system (e.g. whether the system is ready to handle all the config commands or not).  If one of the core services are down, there should be way to identify the system state that it is not ready to take the config commands.  The sysmonitor framework monitors the system core services and port initialization state and generates the system ready message. If one of the system core service goes down,  it alerts with the 'system not ready' message because  the service is not ready to handle the  config commands.  The system ready state message is sent to both syslog as well as console session so that user would know the live state on the console.
  
  The system core services includes  'swss',  'bgp',  'teamd',  'pmon',  'syncd' and  'database'. Other service can be added to list as required.
It also monitors the dockers in the system . If any of the docker goes up or down, it checks the state of system and report with syslog accordingly.

- #### Example
		   - Dec 10 08:35:48.817550 System is ready
  
#### Memory Monitoring

Memory is a critical resource in the system. It is essential to monitor the memory usage  at the system wide as well as per process level and report the usage across the system. This helps to identify memory distribution across system, the spike in the memory allocation and also if there is any memory leaks in the process. 

The framework  monitors the memory usage at system level as well as per process level. Threshold is defined for both per process level and system level.  

#### System Memory Usage: 

  Memory usage of overall system is being monitored with predefined threshold.  When the usage crosses the threshold limit, syslog message is being generated.  Syslog message is generated with following information.

 - Overall system memory usage information
 - Memory usage information of all running processes 
	 - Process name, 
	 - Process Id, 
	 - Used memory size.

##### System Memory Threshold Limit 
The resource limits are automatically derived from the system configurations. 

 -  Overall memory usage  thresholds are derived from the system memory
	- INFO     -  00-70%  of system memory (NORMAL)
	- WARN     -  70-80%  of system memory
	- ALERT    -  80-90%  of system memory
	- CRITICAL -  90-100% of system memory
	
Memory usage of resource is dumped on the console with the following format:

- Process name, Process ID, RSS( physical memory)
 
 #### Example :
	- Dec 11 13:06:19.397949 sonic WARN system#state: System memory usage is above 70%, Total: 15.6G, Free: 1.8G, Used: 10.8G, Buffers: 314.8M, Cached: 2.7G
	- Dec 11 13:06:19.477884 sonic INFO system#state: MEM :: Name: orchagent, Pid:6269, Rss:10.5M
	- Dec 11 13:06:19.477951 sonic INFO system#state: MEM :: Name: ospfd, Pid:11029, Rss:10.5M
	- Dec 11 13:06:19.478011 sonic INFO system#state: MEM :: Name: redis-server, Pid:1006, Rss:10.6M
	- Dec 11 13:06:19.478060 sonic INFO system#state: MEM :: Name: zebra, Pid:9625, Rss:11.3M
	   
#### Per Process Memory Usage:

  When  per process memory usage goes beyond threshold limit, should also generate the process memory usage info on the system log message. The message is generated with the following information.
  
 - Memory info of a process 
	 - Process name, 
	 - Process Id
	 - Rss - Used memory size.
	  
##### Per Process Memory Threshold Limit 
  
 - Per process usage Memory threshold is derived from the overall system memory
	 - INFO      - 00-30%  of system memory(NORMAL)
	 - WARN      - 30-40%  of system memory
	 - ALERT     - 40-50%  of system memory
	 - CRITICAL  - 50-100% of system memory 

#### Example
	 - Dec 11 13:03:19.209233 sonic INFO system#state: Per process memory threshold exceeded for process rest_server[3781], threshold 30% of system memory 478.6M, current usage 538.2M
	 - Dec 11 13:03:19.242928 sonic INFO system#state: Per process memory threshold exceeded for process syncd[14083], threshold 30% of system memory 478.6M, current usage 515.3M


#### CPU Monitoring:

CPU usage of all the process in the system is being monitored.  When the usage crosses the threshold, syslog message is being generated.  Syslog message is generated with following information for each process.

- Process info:
	- Process Name
	- Process Id
	- CPU usage Time
   

##### Per Process CPU threshold limits:

-  The CPU threshold limit is considered as duration of sampling interval in which the process high CPU condition detected.

	- INFO     -  00-70%  of CPU utilization (NORMAL)
	- WARN     -  70-80%  of High CPU utilization
	- ALERT    -  80-90%  of High CPU utilization
	- CRITICAL -  90-100% of High CPU utilization

	When it crosses each threshold level, a syslog message is generated. When a high cpu condition comes from higher to lower, a cleared log message is generated.
	- Example
		May  4 17:32:55.596696 sonic WARNING system#monitor: High CPU usage 84.2%(80-90%) of process snmp.out[10952] is cleared, Current CPU usage is 79.4%

	- Note: 
		- CPU usage of a process is measured with average CPU usage of all the CPU cores in the system.

#### Disk Partition Monitoring:

   In a long running network environment,  monitoring the disk partition usage is a crucial process. As a network operating system, disk partitions are mainly used for storing the log files, core dumps,  debug infos, application files, config files and OS images. It is essential to keep track of the disk partition usage. When the usage crosses the threshold, syslog message is being generated.  Syslog message is generated with the following partition information.

 - Partition name 
 - Used space
 - Free space 
 - Total space

 - Overall partion usage thresholds are derived as 
 
	- INFO     -  00-70%  of total partition size (NORMAL)
	- WARN     -  70-80%  of total partition size 
	- ALERT    -  80-90%  of total partition size
	- CRITICAL -  90-100% of total partition size

#### Example:
Nov 27 07:16:50.878011 sonic INFO system#state: DISK usage of '/' is above 8%, Total: 31.4G, Free: 20.1G, Used: 9.7G
Nov 27 07:16:50.878849 sonic INFO system#state: DISK:: {'used': 38285312, 'free': 3890614272,  'mountpoint': '/var/log', 'total': 4160421888}

	
## 2.5 Resource DB

Monitoring framework has a self contained Python database for maintaining the state of each resources.  The duration and number of entries for a resource is automatically tuned based on the system resource configuration.


## 2.6 Tech-Support 

All the resource statistics and usage alerts are forwarded to syslog.  The syslog is automatically monitored by the logrotate framework. During the techsupport data collection, all the syslog data added as part of the tech-support data archive.


# 3 Unit Test

|S.No|  Testcase                                     |  Result |
|---|-----------------------------------------------| ------- |
| 1 | Simulate and verify the overall memory usage when it goes above threshold |   |
| 2 | Verify the per process memory usage and check the syslog alert || 
| 3 | Verify the high CPU condition of a process and check the syslog alert | |
| 4 | Verify the disk partition usage and check the syslog alert |  |
| 5 | Verify the system service status and check for system ready message | |
| 6 | Simulate the system services failure and check for the system not read message ||
| 7 | Simulate and verify the threshold crossing from NORMAL to CRITICAL and | |
|   | coming back to NORMAL for memory and CPU  | |
| 8 | Simulate and verify the threshold crossing from NORMAL to CRITICAL and | |
|   | coming back to NORMAL for disk partition usage          | |




<!--stackedit_data:
eyJoaXN0b3J5IjpbLTc5NzA3ODg4MywtMTM4OTA1NjAxNCwtMT
gyODc4ODYwOCw2NTU5NTM4NjUsLTEyNzE3NDI0ODYsLTM0MDY5
NTUyNiwtODAwNTE4MzQ2LDczNjUxNDY4MSwtNjkxNTMwODEzLD
E0Mjk3NjM0NjksLTEzNTYwNDIxODIsNDAxNDI3MTY0LDEwNTUy
NzQ2ODQsMTM3MDY3Nzg5LDcyNzY0NjM4MCwxMzI4OTkyMzA5LC
0xNzgyMDc5ODcyLDE3MjQzMjM1MSwtMTgzNzc3MTE0NCwxNDY1
MTIxMzg5XX0=
-->
