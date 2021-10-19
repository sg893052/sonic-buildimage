# Interface Down Reason

# High Level Design Document
# Table of Contents
- [1 Feature Overview](#1-Feature-Overview)
    - [1.1 Target Deployment Use Cases](#11-Target-Deployment-Use-Cases)
    - [1.2 Requirements](#12-Requirements)
    - [1.3 Design Overview](#13-Design-Overview)
        - [1.3.1 Basic Approach](#131-Basic-Approach)
        - [1.3.2 Container](#132-Container)
        - [1.3.3 SAI Overview](#133-SAI-Overview)
- [2 Functionality](#2-Functionality)
- [3 Design](#3-Design)
    - [3.1 Overview](#31-Overview)
        - [3.1.1 Service and Docker Management](#311-Service-and-Docker-Management)
        - [3.1.2 Packet Handling](#312-Packet-Handling)
    - [3.2 DB Changes](#32-DB-Changes)
        - [3.2.1 CONFIG DB](#321-CONFIG-DB)
        - [3.2.2 APP DB](#322-APP-DB)
        - [3.2.3 STATE DB](#323-STATE-DB)
        - [3.2.4 ASIC DB](#324-ASIC-DB)
        - [3.2.5 COUNTER DB](#325-COUNTER-DB)
        - [3.2.6 ERROR DB](#326-ERROR-DB)
    - [3.3 Switch State Service Design](#33-Switch-State-Service-Design)
        - [3.3.1 Orchestration Agent](#331-Orchestration-Agent)
        - [3.3.2 Other Processes](#332-Other-Processes)
    - [3.4 SyncD](#34-SyncD)
    - [3.5 SAI](#35-SAI)
    - [3.6 User Interface](#36-User-Interface)
        - [3.6.1 Data Models](#361-Data-Models)
        - [3.6.2 CLI](#362-CLI)
        - [3.6.2.1 Configuration Commands](#3621-Configuration-Commands)
        - [3.6.2.2 Show Commands](#3622-Show-Commands)
        - [3.6.2.3 Exec Commands](#3623-Exec-Commands)
        - [3.6.3 REST API Support](#363-REST-API-Support)
        - [3.6.4 gNMI Support](#364-gNMI-Support)
     - [3.7 Warm Boot Support](#37-Warm-Boot-Support)
     - [3.8 Upgrade and Downgrade Considerations](#38-Upgrade-and-Downgrade-Considerations)
     - [3.9 Resource Needs](#39-Resource-Needs)
- [4 Flow Diagrams](#4-Flow-Diagrams)
- [5 Error Handling](#5-Error-Handling)
- [6 Serviceability and Debug](#6-Serviceability-and-Debug)
- [7 Scalability](#7-Scalability)
- [8 Platform](#8-Platform)
- [9 Limitations](#9-Limitations)
- [10 Unit Test](#10-Unit-Test)
- [11 Internal Design Information](#11-Internal-Design-Information)
    - [11.1 IS-CLI Compliance](#111-IS-CLI-Compliance)
    - [11.2 Broadcom Packaging](#112-Broadcom-SONiC-Packaging)
    - [11.3 Broadcom Silicon Considerations](#113-Broadcom-Silicon-Considerations)    
    - [11.4 Design Alternatives](#114-Design-Alternatives)
    - [11.5 Broadcom Release Matrix](#115-Broadcom-Release-Matrix)

# List of Tables
[Table 1: Abbreviations](#table-1-Abbreviations)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 04/05/2021  | Prasanth K V       | Initial version                   |
| 0.2 | 05/17/2021  | Madhukar K         | Modified portchannel content      |
| 0.3 | 06/22/2021  | Prasanth K V       | Added REST details and DB schema  |
| 0.4 | 06/28/2021  | Madhukar K         | Added portchannel details         |
| 0.5 | 10/18/2021  | Prasanth K V       | Updates to REST and CLI output    |

# About this Manual
This document provides comprehensive functional and design information about the *Interface Down Reason* feature implementation in SONiC.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
|  PMD                     | Physical Medium Dependent           |
|  LACP                    | Link Aggregation Control Protocol   |

# 1 Feature Overview

It is cumbersome to find out the reason for an interface going down by referring the logs and debug commands. In case of a momentarily interface flap, not all relevant information causing this flap would be available at later point of time.
This feature is to help the user in finding the reason for interface down easier.

## 1.1 Target Deployment Use Cases

Many of applications rely on the operational status events for carrying out some time consuming tasks with a lot of database operations. For example, forwarding database flush, MAC learning, Route delete, Route add etc.
So an interface flap affects the system in general and hence it is important to triage such issue faster with less dependency on subject matter expert.


## 1.2 Requirements

1 Overview  
1.0 - Interface Down Reason is about monitoring the events that can cause interface to go down and show this to user in a user readable format.  
1.1 - This has to be supported in both physical interfaces as well as port channel interfaces  
1.2 - The list of reasons to be supported on physical interfaces on multiple hardware platforms:  
- Admin down  
- Remote fault  
- Local fault  
- Link training failed  
- Link training not completed  
- Link training not started  
- Link tuning failed  
- Link tuning not started  
- Link tuning not completed  
- Incompatible transceiver  
- Transceiver not present  
- Port breakout in-progress  
- Port breakout completed  
- High BER  
- PMD-CDR-lock  
- PMD-signal-detected  
- STP error disabled  
- Transceiver error disabled  
- UDLD error disabled  
- Link flap error disabled  
- PHY link up  

1.3 - The list of reasons to be supported on port channel interfaces:  
- Admin down
- Min links criteria not met
- Error disabled
- All the member ports are link down
- LACP convergence failed for member ports

2 Functionality


2.0 Overview  

3 Interfaces  
3.0 - Supported on physical interfaces  
3.1 - Supported on port channel interfaces  

4 Configuration  
4.0 - The feature is enabled by default.  
4.1 - No user configurations are required  

5 User Interfaces  
5.0 - The feature is managed through the SONiC Management Framework, including full support for Klish, REST and gNMI  
5.1 - Base openconfig data model with extensions - https://github.com/openconfig/public/blob/master/release/models/interfaces/openconfig-if-ethernet.yang
6 Serviceability  
6.0  

7 Scaling  

8 Warm Boot/ISSU  
8.0 - The interface down reason is saved across warm-reboot.

9 Platforms  
9.0 - This feature is supported on all SONiC platforms  

10 Feature Interactions/Exclusions  
10.0 - The interface down reason is applicable for physical interfaces and port channel interfaces only.

11 Limitations
11.0 - The events causing the interface to go down depending on silicon may not be supported on all platforms.

## 1.3 Design Overview
### 1.3.1 Basic Approach

### 1.3.2 Container
The changes will be across 3 existing containers - mgmt-framework, swss and syncd. No new containers are introduced for this feature.

### 1.3.3 SAI Overview
SAI specification has to be updated to get the events from SAI to upper layer.

# 2 Functionality

# 3 Design
## 3.1 Overview

### 3.1.1 Service and Docker Management

### 3.1.2 Packet Handling

## 3.2 DB Changes

### 3.2.1 CONFIG DB
### 3.2.2 APP DB
A new field, reason, is been added to PORT_TABLE:  
```
"PORT_TABLE": {
    "Ethernet40": {
	...
	"reason": "OPER_UP",
	...
    }
}
```

For portchannels, a new field, reason, is been added to LAG_TABLE:  
```
"LAG_TABLE": {
    ":PortChannel20": {
        ...
        "reason": "OPER_UP",
        ...
    }
}
```

A new table IF_REASON_EVENT is added for keeping track of the events IF_REASON_EVENT:  
```
"IF_REASON_EVENT:Ethernet64:xcvr-status-up": {
    "timestamp":"2021-06-18.10:08:13.948691",
    "reason":"ERR_DISABLED"
}
"IF_REASON_EVENT:PortChannel20:portchannel-up": {
    "timestamp":"2021-06-20 15:39:45.439412",
    "reason":"OPER_UP"
}
```  
### 3.2.3 STATE DB
### 3.2.4 ASIC DB
### 3.2.5 COUNTER DB
### 3.2.6 ERROR DB

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
*List/describe all the orchagents that are added/changed - sub-section for each.*

### 3.3.2 Other Processes 
*Describe adds/changes to other processes within SwSS (if applicable) - e.g. \*mgrd, \*syncd*

## 3.4 SyncD
*Describe changes to syncd (if applicable).*

## 3.5 SAI
*Describe SAI APIs used by this feature. State whether they are new or existing.*

## 3.6 User Interface
*Please follow the SONiC Management Framework Developer Guide - https://drive.google.com/drive/folders/1J5_VVuwoJBa69UZ2BoXLYW8PZCFIi76K*

### 3.6.1 Data Models
*Include at least the short tree form here (as least for standard extensions/deviations, or proprietary content). The full YANG model can be a reference another file as desired.*

### 3.6.2 CLI
Only existing show command outputs are updated.

#### 3.6.2.1 Configuration Commands
#### 3.6.2.2 Show Commands
#### Physical interface  
- *show interface status*  
A new column, "Reason", is been added in this command output. The high level reasons are  
admin-down  
err-disabled  
phy-link-down  
oper-up  
```
sonic# show interface status
----------------------------------------------------------------------------------
Name      Description     Oper  Reason        AutoNeg  Speed     MTU    Alternate Name
----------------------------------------------------------------------------------
Eth1/1     -         	  down	admin-down    off      100000    9100   Ethernet0
Eth1/2/1   -              down	err-disabled  off      10000     9100   Ethernet4
Eth1/2/2   -              down	phy-link-down off      10000     9100   Ethernet5
Eth1/2/3   -              up	oper-up       off      10000     9100   Ethernet6
```
- *show interface status <reason>*  
There is an option to list the interfaces with the specified reason. The output will have more details like related events with timestamp of occurrence.  
Example:  
```
sonic# show interface status err-disabled
----------------------------------------------------------------------------------
Name   		Event        		Timestamp
----------------------------------------------------------------------------------
Eth1/2/1  	stp-status-up     	2021-04-16 10:23:29
```
- *show interface <interface>*
show interface command to display the down reasons as shown in the below example:
```
sonic# show interface Eth 1/2/2
Eth1/2/2 is up, line protocol is down, reason phy-link-down
Hardware is Eth
Mode of IPV4 address assignment: not-set
Mode of IPV6 address assignment: not-set
Interface IPv6 oper status: Disabled
IP MTU 9100 bytes
LineSpeed 100GB, Auto-negotiation off
Events:
  port-breakout-inprogress at 2021-06-19.06:56:47.199816
  admin-down at 2021-06-19.06:56:47.269480
  initialized at 2021-06-19.06:56:48.730481
  port-breakout-completed at 2021-06-19.06:56:50.871198
  xcvr-status-up at 2021-06-19.06:56:59.084914
Last clearing of "show interface" counters: never
10 seconds input rate 0 packets/sec, 0 bits/sec, 0 Bytes/sec
10 seconds output rate 0 packets/sec, 0 bits/sec, 0 Bytes/sec
Input statistics:
        6 packets, 1710 octets
        6 Multicasts, 0 Broadcasts, 0 Unicasts
        0 error, 6 discarded, 0 Oversize
        0 Packets (128 to 255 Octects)
Output statistics:
        6 packets, 1704 octets
        6 Multicasts, 0 Broadcasts, 0 Unicast
``` 

The list of events:  
 admin-down  
 remote-fault  
 local-fault  
 link-training-failed  
 link-training-not-completed  
 link-training-not-started  
 link-tuning-failed  
 link-tuning-not-started  
 link-tuning-not-completed  
 incompatible-transceiver  
 transceiver-not-present  
 port-breakout-in-progress  
 port-breakout-completed  
 high-ber  
 pmd-cdr-lock  
 pmd-signal-detected    
 stp-status-down  
 stp-status-up  
 xcvr-status-down  
 xcvr-status-up  
 udld-status-down  
 udld-status-up  
 phy-link-up  


#### Port channel interface
- *show interface status*  
Along with the physical interfaces, configured portchannel interfaces are displayed in this command output. The new column, "Reason" displays the high level reason for portchannel down. The reasons are  
Admin-down  
Min-links  
Err-disabled  
All-links-down  
LACP-fail  

```
sonic# show interface status
Name          Description     Oper  Reason          AutoNeg Speed     MTU    Alternate Name
----------------------------------------------------------------------------------
Eth1/1         -              down  admin-down      off     100000    9100   Ethernet0
Eth1/2/1       -              down  err-disabled    off     10000     9100   Ethernet4
Eth1/2/2       -              down  phy-link-down   off     10000     9100   Ethernet5
Eth1/2/3       -              up    oper-up         off     10000     9100   Ethernet6
PortChannel1   -              down  admin-down      -       20000     9100   -
PortChannel3   -              down  min-links       -       30000     9100   -
PortChannel5   -              down  err-disabled    -       30000     9100   -
PortChannel7   -              down  all-links-down  -       40000     9100   -
PortChannel8   -              down  lacp-fail       -       40000     9100   -
PortChannel9   -              up    oper-up         -       10000     9100   -
```

- *show interface PortChannel [id]*  

show interface PortChannel command to display the reason with timestamp:  
```
sonic# show interface PortChannel 1
PortChannel1 is up, line protocol down, reason ERR-DISABLED, mode LACP
delay_restore_status_down at 2021-01-06 07:49:45.737024
stp_status_down at 2021-01-06 11:54:15.137023
Minimum number of links to bring PortChannel up is 1
Mode of IPV4 address assignment: not-set
Mode of IPV6 address assignment: not-set
Fallback: Disabled
Graceful shutdown: Enabled
MTU 9100
LineSpeed 10.0GB
LACP mode ACTIVE interval SLOW priority 65535 address 90:3c:b3:34:25:60
Members in this channel: Ethernet48
LACP Actor port 49  address 90:3c:b3:34:25:60 key 1
LACP Partner port 0  address 00:00:00:00:00:00 key 0
Last clearing of "show interface" counters: never
10 seconds input rate 0 packets/sec, 0 bits/sec, 0 Bytes/sec
10 seconds output rate 0 packets/sec, 0 bits/sec, 0 Bytes/sec
Input statistics:
          18378 packets, 4042580 octets
          18378 Multicasts, 0 Broadcasts, 0 Unicasts
          0 error, 18378 discarded
Output statistics:
          90435 packets, 13274184 octets
          90435 Multicasts, 0 Broadcasts, 0 Unicasts
          0 error, 0 discarded

```
#### 3.6.2.3 Exec Commands

### 3.6.3 REST API Support

GET /restconf/data/openconfig-interfaces:interfaces/interface={name}/openconfig-if-ethernet:ethernet/state/openconfig-interfaces-ext:status/down-reason

Example response data:
{
  "openconfig-interfaces-ext:down-reason": "OPER_UP"
}


GET /restconf/data/openconfig-interfaces:interfaces/interface={name}/openconfig-if-ethernet:ethernet/openconfig-interfaces-ext:reason-events

Example response data:
```
{
  "openconfig-interfaces-ext:reason-events": {
    "reason-event": [
      {
        "reason-event": {
          "reason": "OPER_UP",
          "event": "phy-link-up",
          "timestamp": "2021-06-06 09:29:55.639018"
        }
      }
    ]
  }
}
```

Example response data for link down scenario:
```
{
  "openconfig-interfaces-ext:reason-events": {
    "reason-event": [
      {
        "reason-event": {
          "reason": "PHY_LINK_DOWN",
          "event": "remote-fault",
          "timestamp": "2021-06-07 04:17:13.456626"
        }
      }
    ]
  }
}
```

The values for reason can be OPER_UP, PHY_LINK_DOWN, ERR_DISABLED or ADMIN_DOWN.  
The list of values for event is mentioned in the CLI section.  

For portchannels:

GET /restconf/data/openconfig-interfaces:interfaces/interface={name}/openconfig-if-aggregate:aggregation/state/openconfig-interfaces-ext:reason

Example response data:
```
{
  "openconfig-interfaces-ext:reason": "OPER_UP"
}
```

GET /restconf/data/openconfig-interfaces:interfaces/interface={name}/openconfig-if-aggregate:aggregation/openconfig-interfaces-ext:reason-events

Example response data:
```
{
  "openconfig-interfaces-ext:reason-events": {
    "reason-event": [
      {
        "state": {
          "event": "All-links-down",
          "reason": "ALL_LINKS_DOWN",
          "timestamp": "2021-08-30 05:53:31.432785"
        }
      }
    ]
  }
}
```

Example response data for link down scenario:
```
{
  "openconfig-interfaces-ext:reason-events": {
    "reason-event": [
      {
        "state": {
          "event": "delay_restore_status_down",
          "reason": "ERR_DISABLED",
          "timestamp": "2021-06-09 04:47:33.410626"
        }
      }
    ]
  }
}
```

The values for reason can be OPER_UP, ALL_LINKS_DOWN, ERR_DISABLED, LACP_FAIL, MIN_LINKS_NOT_MET or ADMIN_DOWN.  

### 3.6.4 gNMI Support
*Generally this is covered by the YANG specification. This section should also cover objects where on-change and interval based telemetry subscriptions can be configured.*

## 3.7 Warm Boot Support
*Describe expected behavior and any limitations. Also describe any design artefacts in support of this.*

## 3.8 Upgrade and Downgrade Considerations
*If any - cover things like DB changes/versioning, config migration etc*

## 3.9 Resource Needs
*Describe any significant resource needs for the feature (esp. at scale) - memory, CPU, disk, I/O etc. Only cover for significant needs (designed decision) - not required for small/medium resource usages.*

# 4 Flow Diagrams
*Provide flow diagrams for inter-container and intra-container interactions.*

# 5 Error Handling
*Provide details about incorporating error handling feature into the design and functionality of this feature.*

# 6 Serviceability and Debug
***This section is important and due attention should be given to it**. Topics include:*

- *Commands: Debug commands are those that are not targeted for the end user, but are more for Dev, Support and QA engineers. They are not a replacement for user show commands, and don't necessarily need to comply with all command style rules. Many features will not have these.*
- *Logging: Please state specific, known events that will be logged (and at what severity level)*
- *Counters: Ensure that you add counters/statistics for all interesting events (e.g. packets rx/tx/drop/discard)*
- *Trace: Please make sure you have incorporated the debugging framework feature (or similar) as appropriate. e.g. ensure your code registers with the debugging framework and add your dump routines for any debug info you want to be collected.*

# 7 Scalability
*Describe key scaling factors and considerations.*

# 8 Platform
*Describe any platform support considerations (e.g. supported/not, scaling, deviations etc)*

# 9 Limitations
*More detail on the limitations stated in requirements*

# 10 Unit Test
*List unit test cases added for this feature (one-liners). These should ultimately align to tests (e.g SPytest, Pytest) that can be shared with the Community.*

# 11 Internal Design Information
*Internal BRCM information to be removed before sharing with the community.*

## 11.1 IS-CLI Compliance
*This is here because we don't want to be too externally obvious about a "follow the leader" strategy. However it must be filled in for all Klish commands.*

*The following table maps SONIC CLI commands to corresponding IS-CLI commands. The compliance column identifies how the command comply to the IS-CLI syntax:*

- ***IS-CLI drop-in replace**  – meaning that it follows exactly the format of a pre-existing IS-CLI command.*
- ***IS-CLI-like**  – meaning that the exact format of the IS-CLI command could not be followed, but the command is similar to other commands for IS-CLI (e.g. IS-CLI may not offer the exact option, but the command can be positioned is a similar manner as others for the related feature).*
- ***SONIC** - meaning that no IS-CLI-like command could be found, so the command is derived specifically for SONIC.*

|CLI Command|Compliance|IS-CLI Command (if applicable)| Link to the web site identifying the IS-CLI command (if applicable)|
|:---:|:-----------:|:------------------:|-----------------------------------|
| | | | |
| | | | |
| | | | |
| | | | |
| | | | |
| | | | |
| | | | |

***Deviations from IS-CLI:** If there is a deviation from IS-CLI, Please state the reason(s).*

## 11.2 Broadcom SONiC Packaging
*Cloud base vs. Enterprise etc*

## 11.3 Broadcom Silicon Considerations
*Where this feature is/not supported, silicon-specific scaling factors and behaviors*

## 11.4 Design Alternatives
*Please state any significant design alternatives considered (if any), and why these were not chosen*

## 11.5 Broadcom Release Matrix
*Please state the Broadcom release in which a feature is planned to be introduced. Where a feature spans multiple releases, then please state which enhancements/sub-features go into which Broadcom release*
|Release|Change(s)|
|:-------:|:-------------------------------------------------------------------------|
| | |
