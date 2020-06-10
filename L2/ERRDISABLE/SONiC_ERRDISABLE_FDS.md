# Feature Name
ERRDISABLE(Error Disable) Recovery Protocol
# High Level Design Document
#### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definitionabbreviation)
  * [Feature Overview](#feature-overview)
      * [Requirements](#requirements)
      * [Design overview](#design-overview)
  * [Functionality](#-functionality)
      * [Target Deployment Use Cases](#target-deployment-use-cases)
      * [Functional Description](#functional-description)
  * [Design](#design)
      * [Overview](#overview)
      * [DB Changes](#db-changes)
          * [CONFIG DB](#config-db)
          * [APP DB](#app-db)
      * [Switch State Service Design](#switch-state-service-design)
          * [Orchestration Agent](#orchestration-agent)
      * [SAI](#sai)
      * [User Interface](#user-interface)
          * [Data Models](#data-models)
          * [CLI](#cli)
              * [Configuration Commands](#configuration-commands)
              * [Show Commands](#show-commands)
              * [Debug Commands](#debug-commands)
              * [Clear Commands](#clear-commands)
  * [Flow Diagrams](#flow-diagrams)
  * [Error Handling](#error-handling)
  * [Serviceability and Debug](#serviceability-and-debug)
  * [Warm Boot Support](#warm-boot-support)
  * [Scalability](#scalability)
  * [Unit Test](#unit-test)
  * [Internal Design Information](#internal-design-information)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |     Date    |       Author              | Change Description                |
|:---:|:-----------:|:-------------------------:|-----------------------------------|
| 0.1 | 08/06/2020 |     Sandeep Kulambi,                                                       Dhanasekar Rathinavel     | Initial version  |



# About this Manual
This document provides general information about the ERRDISABLE recovery feature implementation in SONiC.
# Scope
This document describes the high level design of ERRDISABLE recovery feature. 

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term** | **Meaning**                     |
| -------- | ------------------------------- |
| PDU      | Protocol Data Unit              |
| REST     | Representational state transfer |
| UDLD     | Uni-directional Link Detection  |



# 1 Feature Overview

ERRDISABLE recovery functionality allows automatic recovery of ports that are shutdown due to specific conditions detected by a feature (Ex - uni-directional failures in case of UDLD). 

## 1.1 Requirements

### 1.1.1 Functional Requirements


  1. Support protocol operation on physical ports and static breakout ports
  2. Support protocol operation on member ports of port-channel interfaces


### 1.1.2 Configuration and Management Requirements
This feature will support CLI and REST based configurations, based on KLISH framework
 1. Support CLI configurations as mentioned in section 3.6.2
 2. Support show commands as mentioned in section 3.6.3
 4. Support REST APIs for config and operational data

     

### 1.1.3 Scalability Requirements

### 1.1.4 Warm Boot Requirements

Warm Boot should be supported.

## 1.2 Design Overview
### 1.2.1 Basic Approach
This is a new feature development.

### 1.2.2 Container
ERRDISABLE recovery functionality should be implemented by respective application in their own containers.

### 1.2.3 SAI Overview
No SAI changes


# 2 Functionality

## 2.1 Target Deployment Use Cases

ERRDISABLE recovery helps in recovering the operational status of the port without manual intervention.


## 2.2 Functional Description

ERRDISABLE recovery functionality allows automatic recovery of ports that are shutdown due to specific conditions detected by a feature (Ex - uni-directional failures in case of UDLD). 
There can be multiple features which can take this action of bringing down the interface, UDLD being one of them. 
When ERRDISABLE recovery is enabled, post shutdown(admin-down) of a port a recovery timer is started as per the configured recovery interval. On expiry of this timer the port is enabled(admin-up) back. This helps in automatic recovery of the port without user intervention.

#### ERRDISABLE Cause

ERRDISABLE recovery supports the following cause,

- udld  :- UDLD has shutdown the port because it detected uni-directional failure.

- bpduguard  :- STP's bpduguard has shutdown the port. 

# 3 Design

## 3.1 Overview

ERRDISABLE recovery functionality should be implemented by each application that claims support.
Application should register with config DB and listen for ERRDISABLE recovery config .
Application should start recovery timer on each port for the following scenarios,
  - When ERRDISABLE is enabled
  - Whenever the protocol puts the port to operationally down state.

## 3.2 DB Changes
This section describes the changes made to different DBs for supporting the ERRDISABLE recovery functionality.
### 3.2.1 CONFIG DB
Following config DB schemas are defined for supporting this feature.
### ERRDISABLE
    ;Stores ERRDISABLE configuration
    ;Status: work in progress
    key                    = ERRDISABLE|RECOVERY  ; Global UDLD table key
    udld                   = "enable/disable"     ; Cause UDLD is enabled or not
    interval               = 2*DIGIT              ; recovery Interval time in secs (30 to 65535, DEF:300)

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
No changes

### 3.3.2 Control packet trap mechanism
No changes

## 3.5 SAI
No SAI changes required

## 3.6 User Interface
### 3.6.1 Data Models
OpenConfig YANG model is not available for ERRDISABLE, we are proposing the following openconfig extension for ERRDISABLE [OC-ERRDISABLE-EXT-YANG](openconfig-errdisable-ext.yang).

ERRDISABLE OpenConfig Extension YANG tree:
```
module: openconfig-errdisable-ext
  +--rw errdisable
     +--rw config
     |  +--rw interval?   uint32
     |  +--rw cause*      identityref
     +--ro state
        +--ro interval?   uint32
        +--ro cause*      identityref

where,
cause := UDLD/BPDUGUARD
```

### 3.6.2 CLI

### 3.6.2.1 Configuration Commands

### 3.6.2.1.1 Global Config

### 3.6.2.1.1.1 ERRDISABLE Recovery interval
This command allows configuring a recovery time.

Use the no form of this command to reset the recovery time to default value.

**errdisable recovery interval <value>**

**no errdisable recovery interval**

**Syntax Description:**

| Keyword  | Description                 |
| -------- | --------------------------- |
| interval <value> | Configure recovery time interval |


**Default**: 300 seconds

**Command Mode**: Global Config

**Example**: errdisable recovery interval 120

### 3.6.2.1.1.2 ERRDISABLE Recovery Cause
This command allows to enable ERRDISABLE recovery for a specific cause.

Use the no form of this command to disable ERRDISABLE recovery for a specific cause.

**errdisable recovery cause {udld}**

**no errdisable recovery cause {udld}**

**Syntax Description:**

| Keyword                       | Description             |
| ----------------------------- | ----------------------- |
| cause {udld/bpduguard}        | Configure ERRDISABLE recovery for UDLD/BPDUGUARD |

**Default**: Disabled

**Command Mode**: Global Config

**Example**: errdisable recovery cause udld

### 3.6.2.2 Show Commands

- show errdisable recovery
``` 
show errdisable recovery

ErrDisable Reason    Timer Status
-----------------    --------------
udld                 Disabled
bpduguard            Disabled

Timeout for Auto-recovery: 300 seconds

```

### 3.6.2.3 Debug Commands

### 3.6.2.4 Exec Commands

### 3.6.3 REST API Support

All REST operations are supported.
Below listed are some of the supported REST URI's.

```
<REST-SERVER:PORT>/restconf/data/openconfig-errdisable-ext:errdisable
<REST-SERVER:PORT>/restconf/data/openconfig-errdisable-ext:errdisable/config
<REST-SERVER:PORT>/restconf/data/openconfig-errdisable-ext:errdisable/config/interval
<REST-SERVER:PORT>/restconf/data/openconfig-errdisable-ext:errdisable/config/udld
<REST-SERVER:PORT>/restconf/data/openconfig-errdisable-ext:errdisable/state/udld
```

# 4 Flow Diagrams

# 5 Error Handling

# 6 Serviceability and Debug

Debug and statistics commands as mentioned in Section 3.6.3 and 3.6.4 will be supported. 

All the logfile and the debug command output will be collected as part of techsupport.

# 7 Warm Boot Support

Application should support warm-boot for ERRDISABLE recovery.
 

# 8 Scalability




# 9 Unit Test

CLI:
1) Verify enabling/disabling of cause
2) Verify configuring various value of interval

Functionality
1) Verify ERRDISABLE recovery kicks in after configured interval. And operationally down ports are attempted to be recovered.

REST
1) Verify all REST commands



# 10 Internal Design Information

### 10.1 IS-CLI Compliance

|                        CLI Command                        |       Compliance       |           IS-CLI Command (if applicable)           | Link to the web site identifying the IS-CLI command (if applicable) |
| :-------------------------------------------------------: | :--------------------: | :------------------------------------------------: | ------------------------------------------------------------ |
| errdisable recovery interval <value\>        |      IS-CLI-like       |       errdisable recovery interval <value\>        |                                                              |
| errdisable recovery cause {feature}          |      IS-CLI-like       |       errdisable recovery cause {feature}         |                                                              |
| show errdisable recovery                     |      IS-CLI-like       |       show errdisable recovery              |                                                              |

### 
