# VLAN AutoState HLD

# High Level Design Document
# Table of Contents
- [1 Feature Overview](#1-feature-overview)
    - [1.1 Target Deployment Use Cases](#11-target-deployment-use-cases)
    - [1.2 Requirements](#12-requirements)
    - [1.3 Design Overview](#13-design-overview)
        - [1.3.1 Basic Approach](#131-basic-approach)
        - [1.3.2 Container](#132-container)
        - [1.3.3 SAI Overview](#133-sai-overview)
- [2 Functionality](#2-functionality)
- [3 Design](#3-design)
    - [3.1 Overview](#31-overview)
    - [3.2 DB Changes](#32-db-changes)
        - [3.2.1 CONFIG DB](#321-config-db)
        - [3.2.2 APP DB](#322-app-db)
        - [3.2.3 STATE DB](#323-state-db)
        - [3.2.4 ASIC DB](#324-asic-db)
        - [3.2.5 COUNTER DB](#325-counter-db)
        - [3.2.6 ERROR DB](#326-error-db)
    - [3.3 Switch State Service Design](#33-switch-state-service-design)
        - [3.3.1 Orchestration Agent](#331-orchestration-agent)
        - [3.3.2 Other Processes](#332-other-processes)
    - [3.4 SyncD](#34-syncd)
    - [3.5 SAI](#35-sai)
    - [3.6 User Interface](#36-user-interface)
        - [3.6.1 Data Models](#361-data-models)
        - [3.6.2 CLI](#362-cli)
        - [3.6.2.1 KLISH Configuration Commands](#3621-klish-configuration-commands)
        - [3.6.2.2 KLISH Show Commands](#3622-klish-show-Commands)
        - [3.6.2.3 Click Configuration Commands](#3623-click-configuration-commands)
        - [3.6.2.4 Click Show Commands](#3624-click-show-Commands)
        - [3.6.3 REST API Support](#363-rest-api-support)
        - [3.6.4 gNMI Support](#364-gnmi-support)
     - [3.7 Warm Boot Support](#37-warm-boot-support)
     - [3.8 Upgrade and Downgrade Considerations](#38-upgrade-and-downgrade-considerations)
     - [3.9 Resource Needs](#39-resource-needs)
- [4 Flow Diagrams](#4-flow-diagrams)
- [5 Error Handling](#5-error-handling)
- [6 Serviceability and Debug](#6-serviceability-and-debug)
- [7 Scalability](#7-scalability)
- [8 Platform](#8-platform)
- [9 Limitations](#9-limitations)
- [10 Unit Test](#10-unit-test)
- [11 Internal Design Information](#11-internal-design-information)
    - [11.1 IS-CLI Compliance](#111-is-cli-compliance)
    - [11.2 Broadcom Packaging](#112-broadcom-sonic-packaging)
    - [11.3 Broadcom Silicon Considerations](#113-broadcom-silicon-considerations)    
    - [11.4 Design Alternatives](#114-design-alternatives)
    - [11.5 Broadcom Release Matrix](#115-broadcom-release-matrix)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 05/02/2021|   Rajesh Sankaran           | Initial version                   |

# About this Manual
This document provides comprehensive functional and design information about the *SVI Autostate* feature implementation in SONiC.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
|                       | |

# 1 Feature Overview

The operstatus of a VLAN is currently controlled by the operational status of its physical port, portchannel members. 
There are deployment scenarios which require that the VLAN be declared operationally up irrespective of the 
operational status of its members. This functionality is described as part of this document. 


## 1.1 Target Deployment Use Cases
AutoState disable is applicable to scenarios where there are no physical ports or LAG as a member but the VLAN operational state 
is still to be kept up for the L3 protocols to operate over this interface.

An example is a border leaf where VXLAN tunnels need to be up without any physical ports being present there. 

## 1.2 Requirements


### 1.2.1 Functional requirements

1. Autostate enable shall be supported per VLAN. 
   When autostate is enabled the VLAN operstatus shall be declared as operationally up when there is atleast 
   1 member of type physical port or LAG which is operationally up, otherwise it shall be declared operationally down. 

2. Autostate disable shall be supported per VLAN. 
   When autostate is disabled, the VLAN operstatus shall be declared operationally up when the setting is made. 

3. It shall be possible to switch between the two modes without having to delete the VLAN. 
   The VLAN operstatus shall be according to the new value of the autostate setting. 

4. The operstatus shall be accessible from one of the Redis DBs. 

5. The operstatus shall also be reflected in the vlan kernel device. 

6. By default the VLAN shall be in Autostate enable mode. 

### 1.2.2 Management requirements

#### 1.2.2.1 KLISH requirements

1. Configuration of Autostate enable/disable per VLAN shall be supported.
2. Configuration ofAutostate enable/disable for a range of VLANs shall be supported.  
3. The above configurations should be displayed as part of a show command.  
4. VLAN operstatus shall be displayed as part of a show command.

#### 1.2.2.2 OCyang requirements

TBD.

#### 1.2.2.3 Click requirements

1. Configuration of Autostate enable/disable per VLAN shall be supported.
2. The above configurations should be displayed as part of a show command.  

## 1.3 Design Overview

### 1.3.1 Basic Approach

1. The VLAN_TABLE in the CONFIG_DB shall support one more parameter for autostate enable/disable.
2. The VlanMgr shall be enhanced to populate the APP_DB with the autostate enable/disable.
3. The orchagent shall be enhanced to process the autostate enable/disable parameter. 
4. The orchagent shall handle the autostate create as well as edit.
5. The orchagent shall maintain a count of the number of members which are operationally up per VLAN. 
   This will be used to determine the operational status of the VLAN when autostate is enabled. 
   The contributors to the the operational status are as follows. 
   - Physical Port members.
   - LAG members.
   - VRF-VNI-VLAN mapping associated with the VLAN. 
6. When the configuration changes from autostate enable to disable the operational status shall be changed
   to operationally up. If the state were to be already operationally up then it shall be a no-op. 
7. When the configuration changes from autostate disable to enable the operational status shall be changed
   to operationally down if the number of operationally up members is zero. If the state were to be already operationally up then it shall be a no-op.  
8. For the kernel device oper status updates, The existing mechanism to inform the VlanMgr about the operational status shall be reused. 

### 1.3.2 Container
1. The swss container shall implement this functionality. 
2. The management framework container will have changes to support the management requirements of this feature.  

### 1.3.3 SAI Overview
N.A

# 2 Functionality

As Described in section 1.2

# 3 Design
## 3.1 Overview
As described in section 1.3

## 3.2 DB Changes

### 3.2.1 CONFIG DB

**VLAN_TABLE**

Producer: Mangement framework

Consumer: VlanMgr

Description: Updated existing table to store autostate configuration.

Schema:

```
;Existing table
;defines VLAN table. Updated to store autostate configuration 
;
;Status: stable

key = VLAN_TABLE:VLAN_NAME ;
autostate = "enable"/"disable" ; autostate configuration of VLAN

```

### 3.2.2 APP DB

**VLAN_TABLE**

Producer: VlanMgr 

Consumer: Orchagent

Description: Updated existing table to store autostate configuration.

Schema:

```
;Existing table
;defines VLAN table. Updated to store autostate configuration 
;
;Status: stable

key = VLAN_TABLE:VLAN_NAME ;
autostate = "enable"/"disable" ; autostate configuration of VLAN

```
### 3.2.3 STATE DB
N.A
### 3.2.4 ASIC DB
N.A
### 3.2.5 COUNTER DB
N.A
### 3.2.6 ERROR DB
N.A

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
As in sec 1.3
### 3.3.2 Other Processes 
N.A
## 3.4 SyncD
N.A

## 3.5 SAI
N.A

## 3.6 User Interface

### 3.6.1 Data Models
TBD

### 3.6.2 CLI

#### 3.6.2.1 KLISH Configuration Commands

```
sonic(conf-if-Vlan100)# [no] autostate
sonic(conf-if-range-vl**)# [no] autostate

```

#### 3.6.2.2 KLISH Show Commands

```
sonic# show Vlan
Q: A - Access (Untagged), T - Tagged
NUM        Status      Q Ports        AutoState
100        Inactive    T  Ethernet0   enable
                       T  Ethernet1
101        Active      T  Ethernet1   disable
200        Inactive                   enable
201        Inactive                   enable
202        Inactive                   enable
203        Active                     disable

sonic# show vlan 101
Q: A - Access (Untagged), T - Tagged
NUM        Status      Q Ports       AutoState
101        Active      T  Ethernet1  disable

```
#### 3.6.2.1 Click Configuration Commands

```
1. config vlan autostate <vid> <enable/disable>
   E.g. config vlan autostate 10 disable

2. config vlan range autostate <vid-start> <vid-end> <enable/disable>
   E.g. config vlan range autostate 10 20 disable
```
#### 3.6.2.2 Click Show Commands

```
1. show vlan id <vlanid> enhanced to add a column to show the autostate.

root@sonic:/home/admin# show vlan id 100
+-----------+--------------+-----------+----------------+-----------------------+-------------+
|   VLAN ID | IP Address   | Ports     | Port Tagging   | DHCP Helper Address   | AutoState   |
+===========+==============+===========+================+=======================+=============+
|       100 |              | Ethernet0 | tagged         |                       | enable      |
|           |              | Ethernet1 | tagged         |                       |             |
+-----------+--------------+-----------+----------------+-----------------------+-------------+

2. show vlan brief enhanced to add a column to show the autostate.

root@sonic:/home/admin# show vlan br
+-----------+--------------+-----------+----------------+-----------------------+-------------+
|   VLAN ID | IP Address   | Ports     | Port Tagging   | DHCP Helper Address   | AutoState   |
+===========+==============+===========+================+=======================+=============+
|       100 |              | Ethernet0 | tagged         |                       | enable      |
|           |              | Ethernet1 | tagged         |                       |             |
+-----------+--------------+-----------+----------------+-----------------------+-------------+
|       101 | 100.1.1.1/24 | Ethernet1 | tagged         |                       | disable     |
+-----------+--------------+-----------+----------------+-----------------------+-------------+
```

#### 3.6.2.3 Exec Commands
N.A

### 3.6.3 REST API Support
TBD

### 3.6.4 gNMI Support
TBD

## 3.7 Warm Boot Support
No impact to warm boot support due to this feature. 

## 3.8 Upgrade and Downgrade Considerations
By default the autostate is enabled. Upgrade from an earlier release will have all the 
configured VLANs in an autostate enabled state. There shall be no change in behavior from the
earlier releases. The user has an option to change the autostate configuration post upgrade.

## 3.9 Resource Needs
N.A

# 4 Flow Diagrams
N.A

# 5 Error Handling
N.A

# 6 Serviceability and Debug
Existing Logging mechanisms and show commands shall help in debuggability.

# 7 Scalability
N.A

# 8 Platform
Applicable on all platforms.

# 9 Limitations
N.A

# 10 Unit Test
TBD

# 11 Internal Design Information

The motivation for this feature is due to an issue faced in the field. Please refer to SONIC-41056 for the description.

## 11.1 IS-CLI Compliance

The following table maps SONIC CLI commands to corresponding IS-CLI commands. The compliance column identifies how the command comply to the IS-CLI syntax:

- **IS-CLI drop-in replace**  *meaning that it follows exactly the format of a pre-existing IS-CLI command.*
- **IS-CLI-like**  *meaning that the exact format of the IS-CLI command could not be followed, but the command is similar to other commands for IS-CLI (e.g. IS-CLI may not offer the exact option, but the command can be positioned is a similar manner as others for the related feature).*
- **SONIC**  *meaning that no IS-CLI-like command could be found, so the command is derived specifically for SONIC.*

|CLI Command|Compliance|IS-CLI Command (if applicable)| Link to the web site identifying the IS-CLI command (if applicable)|
|:---:|:-----------:|:------------------:|-----------------------------------|
| [no] autostate | IS-CLI drop-in replace | [no] autostate | [SVI AutoState](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus7000/sw/interfaces/config/cisco_nexus7000_interfaces_config_guide_8x/config-layer-2-interfaces.html#task_AAFCF1B66FEB4173A9E6658242F1C257) |
| | | | |
| | | | |
| | | | |
| | | | |
| | | | |
| | | | |

**Deviations from IS-CLI:** 
- No support for system level setting of autostate.
- No support autostate exclude command to exclude ports from contributing to the operational state of a VLAN. 

## 11.2 Broadcom SONiC Packaging
All Packages shall support this feature. 

## 11.3 Broadcom Silicon Considerations
Applicable to all broadcom silicon.

## 11.4 Design Alternatives
None.

## 11.5 Broadcom Release Matrix
|Release|Change(s)|
|:-------:|:-------------------------------------------------------------------------|
| 3.3.0 | VLAN AutoState support |
