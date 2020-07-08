# Counters Rate Utilization and Load-interval Support

# High Level Design Document
#### Rev 0.2

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definition-abbreviation)
  * [1 Feature Overview](#1-feature-overview)
    * [1.1 Requirements](#11-requirements)
      * [1.1.1 Functional Requirements](#111-functional-requirements)
      * [1.1.2 Configuration and Management Requirements](#112-configuration-and-management-requirements)
      * [1.1.3 Scalability Requirements](#113-scalability-requirements)
      * [1.1.4 Warm Boot Requirements](#114-warm-boot-requirements)
    * [1.2 Design Overview](#12-design-overview)
      * [1.2.1 Basic Approach](#121-basic-approach)
      * [1.2.2 Container](#122-container)
      * [1.2.3 SAI Overview](#123-sai-overview)
  * [2 Functionality](#2-functionality)
    * [2.1 Functional Description](#21-functional-description)
  * [3 Design](#3-design)
    * [3.1 Overview](#31-overview)
      * [3.1.1 Orchagent](#311-orchagent)
      * [3.1.2 SYNCD](#312-syncd)
      * [3.1.3 Mgmt-framework](#313-mgmt-framework)
    * [3.2 DB Changes](#32-db-changes)
      * [3.2.1 CONFIG DB](#321-config-db)
      * [3.2.2 APP DB](#322-app-db)
      * [3.2.3 COUNTER DB](#323-counter-db)
    * [3.3 SAI](#33-sai)
    * [3.4 CLI](#34-cli)
      * [3.4.1 Data Models](#341-data-models)
      * [3.4.2 Show Commands](#342-show-commands)
      * [3.4.3 REST APIs](#343-rest-apis)
  * [4 Error Handling](#5-error-handling)
  * [5 Serviceability and Debug](#6-serviceability-and-debug)
    * [5.1 Configuration Commands](#51-configuration-commands)
  * [6 Warm Boot Support](#7-warm-boot-support)
  * [7 Scalability](#8-scalability)
  * [8 Unit Test](#9-unit-test)
    * [8.1 CLI Test Cases](#91-cli-test-cases)
    * [8.2 Rest API Test Cases](#92-rest-api-test-cases)
    * [8.3 Functional Test Cases](#93-functional-test-cases)
    * [8.4 Scaling Test Cases](#94-scaling-test-cases)
    * [8.5 Warm Boot Test Cases](#95-warm-boot-test-cases)
    * [8.6 Negative Test Cases](#96-negative-test-cases)
  * [9 Future support](#9-future-support)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:---------------------:|-----------------------------------|
| 0.1 | 06/20/2020  | Rupesh Kumar   | Initial version                   |

# About this Manual
This document provides details of interface/queue counters rate/utilization/load-interval functionality in SONiC

# Scope

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term**                 | **Meaning**                                  |
|--------------------------|----------------------------------------------|
| BPS                      | Bytes Per Second                             |
| bps                      | bits per second                              |
| PPS                      | Packets Per Second                           |
| NBI                      | North Bound interfaces (Klish/REST/GNMMI     |

# 1 Feature Overview
## 1.1 Requirements
### 1.1.1 Functional Requirements

1. Support for show port/port-channel utilization
2. Support for show port/port-channel BPS/PPS/bps
3. Support for show queue counters PPS/BPS.
4. Dynamic load interval for rate calculations per port/port-channel.
5. Dynamic load interval for rate calculations for all queue counters.

### 1.1.2 Configuration and Management Requirements
1. Existing show interface counters CLI to be enhanced to display interface utilization/PPS/MBPS/Mbps
2. Existing show queue counters to be enhanced to show PPS and BPS.
3. Add config command to set load interval
4. Supports all NBI (REST/GNMI/OpenConfig/Klish)

### 1.1.3 Scalability Requirements

### 1.1.4 Warm Boot Requirements

Warm boot functionality is not supported for this feature.
Following are the limitations which prevent the warm-boot support for this feature.
```
1. Cached counters are not preserved in DB 
2. PPS/BPS/bps/rate calculations need the time elapsed during warm-boot.
```

## 1.2 Design Overview
### 1.2.1 Basic Approach

Rate calculations are done in backend and NBI will pick the data from counters DB.
Interface load interval and global queue load interval are supported. 
The load interval is fixed for this release and the current load interval can be fetched from APP_DB.

### 1.2.2 SAI Overview
No SAI changes.

# 2 Functionality

## 2.1 Functional Description

SYNCD flex counter thread fetches the interface and queue counters from SAI. The counters do not include rate/BPS/bps/utilization data. Enhancing this functionality to generate rate/utilization data from the fetched SAI counters and save in COUNTERS DB.

Load interval functionality allows to configure per interface rate calculations and global queue rate calculations. 

# 3 Design

## 3.1 Overview


### 3.1.1 Orchagent

Ports orchagent will be programming load interval from SWITCH_TABLE to SYNCD.
Queue counters are fetched every 10 seconds, so the load interval can only be multiples of 10.

### 3.1.2 SYNCD

SYNCD flex counter thread fetches the interface and queue counters from SAI and updates the COUNTERS DB. 
When load interval is set, then thread caches the counters and after every load interval,  will generate the  interface utilization counters and update the new fields to same COUNTERS DB.


```
// stats has current counters read from SAI
// cache has stats from last load interval
// load_interval is per port/queue load interval
// poll_interval is interval at which stats are polled from SAI.

// Logic to set load_divisor is called when polling interval or load_interval changes
if (load_interval > poll_interval)
    load_divisor = load_interval / poll_interval

if (load_interval < poll_interval)
    # log message and set to same as poll_interval.
    load_interval = poll_interval
    load_divisor = 1

// This logic is called every load interval.
// counter overflow will be handled.

# Interface IN counters
current = stats[SAI_PORT_STAT_IF_IN_UCAST_PKTS] + stats[SAI_PORT_STAT_IF_IN_NON_UCAST_PKTS];
prev = cache[SAI_PORT_STAT_IF_IN_PKTS]
PPS = (current - prev) / load_divisor)
BPS = ((stats[SAI_PORT_STAT_IF_IN_OCTETS] - cache[SAI_PORT_STAT_IF_IN_OCTETS]) / load_divisor)
bps = BPS << 3
utilization = (bps/port-speed)

# Interface OUT counters
current = stats[SAI_PORT_STAT_IF_OUT_UCAST_PKTS] + stats[SAI_PORT_STAT_IF_OUT_NON_UCAST_PKTS];
prev = cache[SAI_PORT_STAT_IF_OUT_PKTS]
PPS = (current - prev) / load_divisor)
BPS = ((stats[SAI_PORT_STAT_IF_IN_OCTETS] - cache[SAI_PORT_STAT_IF_IN_OCTETS]) / load_divisor)
bps = BPS << 3
utilization = (bps/port-speed)

Queue Counters

current = stats[SAI_QUEUE_STAT_PACKETS]
prev = cache[SAI_QUEUE_STAT_PACKETS]
PPS = (current - prev) / load_divisor)
current = stats[SAI_QUEUE_STAT_BYTES]
prev = cache[SAI_QUEUE_STAT_BYTES]
BPS = (current - prev) / load_divisor)
bps = BPS << 3

```

### 3.1.3 Mgmt-framework
To be updated by DELL
The transformer code fetches the new counters and shares it to the client.

## 3.2 DB Changes

### 3.2.1 CONFIG DB

#### SWITCH_TABLE
```
Global Switch level config to be stored in this table.

    port_load_interval               = 3DIGIT  ; Value in seconds. 10-600 supported. Default is 10 seconds
    queue_load_interval         = 3DIGIT  ; Value in seconds. 10-600 supported. Default is 10 seconds

"SWITCH|switch"
 "port_load_interval" : "10" ; Load interval for interface rate/utilization.
 "queue_load_interval" : "10" ; Load interval for Queue rate/utilization
```

#### PORT_TABLE
```
    load_interval               = 3DIGIT ; Value in seconds. 10-600 supported. Default is 10 seconds

"PORT|ifName"
 "port_load_interval" : "10" ; Load interval for interface rate/utilization.
```

#### PORTCHANNEL_TABLE
```
    port_load_interval               = 3DIGIT ; Value in seconds. 10-600 supported. Default is 10 seconds

"PORTCHANNEL|portchannelName"
 "load_interval" : "10" ; Load interval for portchannel rate/utilization.
```

### 3.2.2 APP DB

#### SWITCH_TABLE
```
Global Switch level config set by portsOrch.

    port_load_interval               = 3DIGIT  ; Value in seconds. 10-600 supported. Default is 10 seconds
    queue_load_interval         = 3DIGIT  ; Value in seconds. 10-600 supported. Default is 10 seconds


"SWITCH_TABLE|switch"
 "port_load_interval" : "10" ; Load interval for interface rate/utilization.
 "queue_load_interval" : "10" ; Load interval for Queue rate/utilization
```

#### PORT_TABLE
```
Interface level load interval set by portsOrch.

    port_load_interval               = 3DIGIT ; Value in seconds. 10-600 supported. Default is 10 seconds


"PORT_TABLE|ifName"
 "port_load_interval" : "10" ; Load interval for interface rate/utilization.

```
#### LAG_TABLE
```
Portchannel level load interval.

    port_load_interval               = 3DIGIT ; Value in seconds. 10-600 supported. Default is 10 seconds


"LAG_TABLE:PortChannel10"
 "port_load_interval" : "10" ;  Load interval for portchannel rate/utilization.

```

### 3.2.3 COUNTER DB

Following new fields are added to existing COUNTERS Table for both port/portchannel and queue Counters.

```
COUNTERS_DB:
{
    "COUNTERS:oid:<port/portchannel OID>": {
      "PORT_STAT_IF_IN_PKTS_PER_SECOND": "0",
      "PORT_STAT_IF_OUT_PKTS_PER_SECOND": "0",
      "PORT_STAT_IF_IN_OCTETS_PER_SECOND": "0",
      "PORT_STAT_IF_OUT_OCTETS_PER_SECOND": "0",
      "PORT_STAT_IF_IN_BITS_PER_SECOND": "0",
      "PORT_STAT_IF_OUT_BITS_PER_SECOND": "0",
      "PORT_STAT_IF_IN_UTILIZATION": "0",
      "PORT_STAT_IF_OUT_UTILIZATION": "0",
    },
},

"COUNTERS:oid:<queueOID>": {
    "QUEUE_STAT_PACKETS_PER_SECOND": "0",
    "QUEUE_STAT_BYTES_PER_SECOND": "0",
    "QUEUE_STAT_BITS_PER_SECOND": "0",
  }

```

## 3.3 SAI
No changes needed in SAI for this feature.

## 3.4 CLI
### 3.4.1 Data Models

Existing openconfig interface yang and sonic yang to be extended to support this feature.
DELL team to add more details here.

### 3.4.2 Show Commands

#### Interface Counters

##### show interface Ethernet <>

```
sonic# show interface Ethernet 64
Ethernet64 is up, line protocol is up
Hardware is Eth
Mode of IPV4 address assignment: not-set
Mode of IPV6 address assignment: not-set
Interface IPv6 oper status: Disabled
IP MTU 9100 bytes
LineSpeed 25GB, Auto-negotiation off
Last clearing of "show interface" counters: 1970-01-01 00:00:00
10 seconds input rate 84640 bits/sec, 10236 Bytes/sec, 52 packets/sec
10 seconds output rate 176760 bits/sec, 22432 Bytes/sec, 45 packets/sec
```

##### show interface counters
```
       IFACE    STATE    RX_OK    RX_MBPS  RX_MbPS    RX_UTIL    RX_ERR    RX_DRP    RX_OVR    TX_OK    TX_MBPS    TX_MbPS    TX_UTIL    TX_ERR    TX_DRP    TX_OVR
------------  -------  -------  --------  --------  ---------  --------  --------  --------  -------    --------  --------  ---------  --------  --------  --------
  Ethernet48        U   87,219  0.00 MB/s  0.00 Mb/s  0.00%         0         3         0   86,871      0.00 MB/s  0.00 Mb/s  0.00%         0         0         0
  Ethernet49        U    5,649  0.00 MB/s  0.00 Mb/s  0.00%         0     5,649         0    5,645      0.00 MB/s  0.00 Mb/s  0.00%         0         0         0  

```

#####  show queue counters
```
---------------------------------------------------------------------------------------------------
Port        TxQ  Counter/pkts   Counter/bytes  Rate/PPS       Rate/BPS       Drop/pkts   Drop/bytes
---------------------------------------------------------------------------------------------------
Ethernet0   UC0  0              0              0/s            0/s            0           0
Ethernet0   UC1  0              0              0/s            0/s            0           0
Ethernet0   UC2  0              0              0/s            0/s            0           0
Ethernet0   UC3  0              0              0/s            0/s            0           0
...

Queue counter load interval: 10 sec
```

##### show queue counters interface CPU
```
---------------------------------------------------------------------------------------------------
TxQ  Counter/pkts   Counter/bytes  Rate/PPS       Rate/BPS       Drop/pkts   Drop/bytes
---------------------------------------------------------------------------------------------------
MC0  0              0              0/s            0/s            0           0          
MC1  0              0              0/s            0/s            0           0
MC2  0              0              0/s            0/s            0           0
MC3  0              0              0/s            0/s            0           0
MC4  0              0              0/s            0/s            0           0

Queue counter load interval: 10 sec
```
### 3.4.3 REST APIs

To be filled by DELL team

# 4 Error Handling

# 5 Serviceability and Debug

Below config commands are added in CLICK to help during debugging.
This release supports only fixed load interval.
These config commands help during debugging if the user wishes to change the load interval.

### 5.1 Configuration Commands

#### config interface load_interval Ethernet0 <load-interval-in-seconds>

Set rate calculation interval for the interface(port/portchannel). Supported values are 10 seconds to 600 seconds.


  config interface load_interval Ethernet0 30

#### config queue load_interval <load-interval-in-seconds>

Set rate calculation interval for the queue. Supported values are 10 seconds to 600 seconds.

  config queue load_interval 30

# 6 Warm Boot Support
Not applicable for this feature.

# 7 Scalability

# 8 Unit Test 
** SOME sections below to be filled by DELL Team **
## 8.1 CLI Test Cases
   
## 8.2 Rest API Test Cases

## 8.3 Functional Test Cases
1. Have different rates of traffic on port and verify that counters DB has proper rate/PPS/BPS/bps
2. Have traffic on multiple ports on ingress/egress directions and verify rates on all ports.
3. Verify that rate calculation is done as per configured load interval.
4. Verify polling interval changes and rate calculation is proper.
5. Verify rate calculations are done with default settings, when no load-interval is configured.
6. Verify cached interface counters get cleared during DPB.
7. Verify cached queue counters gets cleared during DPB.
8. Verify Stats cache elements are freed during DPB for both interface and queue counters.
9. Verify for different port speeds.


## 8.4 Scaling Test Cases

## 8.5 Negative Test Cases


# 9 Future support

