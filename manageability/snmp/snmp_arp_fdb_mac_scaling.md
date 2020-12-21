# SNMP ARP and FDB MAC  Scaling

# High Level Design Document
#### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definitionabbreviation)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev  |    Date    |    Author     | Change Description |
| :--: | :--------: | :-----------: | ------------------ |
| 0.1  | 12/19/2020 | Suresh Babu R | Initial version    |

# About this Manual
This document provides general information about the implementation of handling of snmp requests, netlink notifications and  updating FDB mac entries parallelly in SONiC.
# Scope
This document describes the design of handling snmp requests while updating netlink neighbor notifications and FDB mac entries.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term** | **Meaning**                 |
| -------- | --------------------------- |
| FDB      | Forwarding Data Base        |
| ARP      | Address Resolution Protocol |

# 1 Feature Overview
SONiC uses Net-Snmp based snmp agent which is single threaded. So if user issue snmp requests while snmpd processing netlink neighbor notifications, they get timed out as snmpd is busy in processing netlink notifications. 

If we restarts snmp docker when device has high number( >20k) of neighbour entries, then it will take 4 to 5 minutes to establish communication with sub-agents as snmpd spends time in processing neighbour entries from the kernel.

SONiC python sub-agent is based on aysncio package which is signgle threaded. So if user perform snmp requests while sub-agent updating FDB mac  from ASIC_DB, they might get timed out as sub-agent is busy in updating FDB mac cache.

We should process snmp requests while snmpd is updating netlink notifications and FDB mac cache.



## 1.1 Requirements

### 1.1.1 Functional Requirements

1)Snmp requests should not get timeout while the switch is processing netlink neighbour notifications. 

2)Snmpd should establish communication with sub-agent immediately even when switch has max supported neighbour(ARP) entries.

3)Snmp requests should not timeout while the switch is processing FDB mac entries from ASIC_DB.

4)Python sub-agent should subscribe for DB notifications instead of fetching all entries from DB at regular interval.

### 1.1.2 Configuration and Management Requirements 
No configuration commands 

### 1.1.3 Scalability Requirements
Snmp requests should not timeout when switch is pumped with max supported neighbour(ARP) entries.
### 1.1.4 Cold/Warm Boot Requirements

N/A

# 2 Functionality
See design overview

# 3 Design

## 3.1 Overview
Currently snmpd creates socket for netlink neighbour notifications from kernel and register same with select() system call. Same select() system call also listening for snmp client and sub-agent requests. When there are too many notifications from kernel, then snmpd might not get time to process client and sub-agent requests. 

To overcome this, we will create a separate thread which will be listening for netlink notifications from the kernel and process them in a infinite loop.  Since client and sub-agent requests are handled in the main thread they will not get time out.

In python sub-agent, creating a separate thread for processing FDB mac entries does not help because of GIL( Global Interpreter Lock). So we will create a separate process for reading FDB mac entries from ASIC_DB and processing them and update corresponding cache. This separate process also used for listening DB notification for FDB mac entries from ASIC DB. Also we will use multiprocessing.Queue interface for communication between the main process and this sub-process.



## 3.2 DB Changes

### 3.2.1 CONFIG DB

N/A

### 3.2.3 STATE DB

N/A

### 3.2.4 ASIC DB

N/A

### 3.2.5 COUNTER DB

N/A

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
### 3.3.2 Other Process 
N/A

## 3.4 SyncD
N/A

## 3.5 SAI
N/A

## 3.6 User Interface
N/A

# 4 Flow Diagrams
N/A

# 5 Error Handling
N/A

# 6 Serviceability and Debug
N/A

# 7 Warm Boot Support
N/A

# 8 Scalability
N/A

# 9 Unit Test
1)Pump ARP requests and same time issue snmp requests and verify that snmp requests does not timeout

2)Pump ARP entries, restart snmp docker and issue snmp requests and verify that request does not timeout




# 10 Internal Design Information
Internal BRCM information to be removed before sharing with the community.
