# QoS Maps

# High Level Design Document
#### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [References](#references)
  * [Definition/Abbreviation](#definitionabbreviation)
  * [1 Feature Overview](#1-feature-overview)
     * [1.1 Requirements](#11-requirements)
        * [1.1.1 Functional Requirements](#111-functional-requirements)
        * [1.1.2 Configuration and Management Requirements](#112-configuration-and-management-requirements)
     * [1.2 Design Overview](#12-design-overview)
        * [1.2.1 Config_DB Schema](#121-config_db-schema)
           * [1.2.1.1 TC_TO_DSCP_MAP](#1211-tc_to_dscp_map)
           * [1.2.1.2 TC_TO_DOT1P_MAP](#1212-tc_to_dot1p_map)
           * [1.2.1.3 PORT_QOS_MAP](#1213-port_qos_map)
        * [1.2.2 SAI Overview](#122-sai-overview)
  * [2 Functionality](#2-functionality)
     * [2.1 Target Deployment Use Cases](#21-target-deployment-use-cases)
     * [2.2 Functional Description](#22-functional-description)
  * [3 User Interface](#3-user-interface)
     * [3.1 Data Models](#31-data-models)
        * [3.1.1 OpenConfig Support](#311-openconfig-support)
     * [3.2 KLISH CLI](#32-klish-cli)
        * [3.2.1 Configuration Commands](#321-configuration-commands)
        * [3.2.2 Show Commands](#322-show-commands)
        * [3.2.3 Debug Commands](#323-debug-commands)
     * [3.3 REST API Support](#33-rest-api-support)
  * [4 Flow Diagrams](#4-flow-diagrams)
  * [5 Error Handling](#5-error-handling)
  * [6 Serviceability and Debug](#6-serviceability-and-debug)
  * [7 Warm Boot Support](#7-warm-boot-support)
  * [8 Scalability](#8-scalability)
  * [9 Unit Test](#9-unit-test)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 5/14/2020  | Pankaj, Ashok | Initial version  |


# About this Manual
This document provides information about Egress QoS Map support.
It also provides information about Qos-Map support on PortChannel and L3-VLAN
interface.

# Scope
This document describes the following QoS Maps features:
- TC-to-DSCP Map Creation
- TC-to-DSCP Map binding on Ethernet/PortChannel/L3-VLAN interface
- TC-to-DOT1P Map Creation
- TC-to-DOT1P Map binding on Ethernet/PortChannel interface
- DOT1P-to-TC Map binding on PortChannel interface
- DSCP-to-TC Map binding on PortChannel/L3-VLAN interface

# References
- DSCP-to-TC and DOT1P-to-TC QoSMap CLIs are described [here](https://github.com/project-arlo/SONiC/blob/626b62f1950227aa5714d7078a6137ce06105856/doc/SONiC_QoS_Mgmt_HLD-1.1.md)

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|:-------------------------|:------------------------------------|
| QoS                      | Quality of Service       
| TC                       | Traffic Class
| DSCP                     | Differentiated services code point; The 6-bit value in the IP packet header
| DOT1P                    | Packet Code Point (PCP); The 3-bit value in VLAN header 

# 1 Feature Overview
SONiC supports internal TC determination based on DSCP-to-TC and DOT1P-to-TC map only on an Ethernet interface,
it does not support the same on a PortChannel and L3-VLAN interface.
It also does not support DSCP/PCP remarking based on Egress QoSMaps (TC-to-DSCP and TC-to-DOT1P).

As part of this feature the following capabilities will be added:
- Ability to remark the PCP in the packet based on TC-to-DOT1P Map
- Ability to remark the DSCP in packet based on TC-to-DSCP Map
- Ability to assign a TC to the packet received on a PortChannel/L3-VLAN interface based on DSCP-to-TC Map
- Ability to assign a TC to the packet received on a PortChannel interface based on DOT1P-to-TC Map


## 1.1 Requirements

### 1.1.1 Functional Requirements

- Provide support to create TC-to-DSCP Map
- Provide support to create TC-to-DOT1P Map
- Provide support to associate TC-to-DSCP Map with Ethernet/PortChannel/L3-VLAN interface.
- Provide support to associate TC-to-DOT1P Map with Ethernet/PortChannel interface
- Provide support to associate DSCP-to-TC Map with PortChannel and L3-VLAN interface.
- Provide support to associate DOT1P-to-TC Map with PortChannel.
- Provide support to classify a packet received on an L3-VLAN interface to TC based on associated DSCP-to-TC Map
- Provide support to classify a packet received on a PortChannel interface to TC
  based on associated DSCP-to-TC/DOT1P-to-TC Map
- Provide support to remark DSCP in outgoing packet based on associated TC-to-DSCP Map on an interface.
- Provide support to remark PCP in outgoing packet based on associated TC-to-PCP Map on an interface.

### 1.1.2 Configuration and Management Requirements
- CLI configuration and show commands
- REST API support
- gNMI Support

Please check User Interface Section for more details.

## 1.2 Design Overview

### 1.2.1 Config_DB Schema

#### 1.2.1.1 TC_TO_DSCP_MAP
A new table is added to store TC-to-DSCP Maps.
- Producer: User Interface (Rest/CLI etc)
- Consumer: Qos Orchagent

```
; TC to DSCP map
key        = "TC_TO_DSCP_MAP|"name
;field    value
tc_value   = 1*DIGIT
dscp_value = 1*DIGIT ; DSCP range 0-63

Example:
{
"TC_TO_DSCP_MAP": {
    "AZURE": {
        "0": "6",
        "1": "13",
        "2": "22",
        "3": "30",
        "4": "39",
        "5": "46",
        "6": "55",
        "7": "60"
    }
  }
}

```
#### 1.2.1.2 TC_TO_DOT1P_MAP
A new table is added to store TC-to-DOT1P Maps.
- Producer: User Interface (Rest/CLI etc)
- Consumer: Qos Orchagent

```
; TC to DOT1P Map
key        = "TC_TO_DOT1P_MAP|"name
;field    value
tc_value   = 1*DIGIT
dot1p_value = 1DIGIT ; dot1p range 0-7

Example:
{
"TC_TO_DOT1P_MAP": {
    "AZURE": {
        "0": "0",
        "1": "0",
        "2": "3",
        "3": "3",
        "4": "3",
        "5": "6",
        "6": "6",
        "7": "7"
    }
  }
}

```
#### 1.2.1.3 PORT_QOS_MAP
Existing PORT_QOS_MAP table will have two new fields to indicate the TC-to-DSCP and TC-to-DOT1P
map binding on the interface.
- Producer: User Interface (Rest/CLI etc)
- Consumer: Qos Orchagent

```
; PORT_QOS_MAP table. Defines port qos mapping
key = PORT_QOS_MAP:ifname

;field  = value

tc_to_dscp_map = ref_hash_key_reference; reference to tc_to_dscp map name key
tc_to_dot1p_map = ref_hash_key_reference; reference to tc_to_dot1p map name key  

Example:
{
"PORT_QOS_MAP": {
    "Ethernet50,PortChannel10": {
        "dscp_to_tc_map": "[DSCP_TO_TC_MAP|AZURE]", 
        "dot1p_to_tc_map": "[DOT1P_TO_TC_MAP|AZURE]"
        "tc_to_dscp_map": "[TC_TO_DSCP_MAP|AZURE]", 
        "tc_to_dot1p_map": "[TC_TO_DOT1P_MAP|AZURE]"
    },
    "Vlan10": {
        "tc_to_dscp_map": "[TC_TO_DSCP_MAP|AZURE]"
    }
  }
}
```

### 1.2.2 SAI Overview
In current SAI spec there is no support to apply a QoS map on L3-VLAN interface;
To support QoS Map support on L3-VLAN interface it is required to enhance the SAI API; 
Following new CREATE_AND_SET SAI Attributes are proposed to be added as part of router interface API:

Note: Color shall not be supported as part of proposed implementation.

```
To Enable DSCP -> TC MAP on router interface  
- SAI_ROUTER_INTERFACE_ATTR_QOS_DSCP_TO_TC_MAP  
```
```
To Enable DSCP -> COLOR MAP on router interface
- SAI_ROUTER_INTERFACE_ATTR_QOS_DSCP_TO_COLOR_MAP  
```
```
To Enable TC AND COLOR -> DSCP MAP  
- SAI_ROUTER_INTERFACE_ATTR_QOS_TC_AND_COLOR_TO_DSCP_MAP
```

# 2 Functionality
## 2.1 Target Deployment Use Cases

Features explained in the document will be useful when user wants to do DSCP/PCP remarking or assign a TC using QosMap;
QosMaps on L3-VLAN interface will be helpful when QoSMaps need to be
applied at a VLAN interface level and a port level granularity does not suffice.

## 2.2 Functional Description

Ingress QoS Maps provide user the capability to assign different internal Traffic Class based on DSCP/PCP values in packet.
Egress(Remark) Maps provide user the capability to remark the PCP/DSCP fields in outgoing packet so that downstream node can provide proper quality of service.

# 3 User Interface
## 3.1 Data Models

### 3.1.1 OpenConfig Support  
OpenConfig QoS YANG model does not have support for QoS Map. 
The OpenConfig YANG models are extended to support QoSMap feature.

- TC to DSCP map  
    No Open Config YANG available.  
    Augment Open Config YANG model with new definitions
    
- TC to DOT1P map  
    No Open Config YANG available.  
    Augment Open Config YANG model with new definitions

Note: TC is referred as forwarding-group/fwd-group

````
module: openconfig-qos
  +--rw qos
     +--rw config
     +--ro state
     |  +--rw interface* [interface-id]
     |     +--rw interface-id                      -> ../config/interface-id
     |     +--rw oc-qos-maps-ext:interface-maps
     |        +--rw oc-qos-maps-ext:config
     |        |  +--rw oc-qos-maps-ext:forwarding-group-to-dscp?             -> ../../../../../forwarding-group-dscp-maps/forwarding-group-dscp-map/config/name
     |        |  +--rw oc-qos-maps-ext:forwarding-group-to-dot1p?            -> ../../../../../forwarding-group-dot1p-maps/forwarding-group-dot1p-map/config/name
     |        +--ro oc-qos-maps-ext:state
     |           +--ro oc-qos-maps-ext:forwarding-group-to-dscp?             -> ../../../../../forwarding-group-dscp-maps/forwarding-group-dscp-map/config/name
     |           +--ro oc-qos-maps-ext:forwarding-group-to-dot1p?            -> ../../../../../forwarding-group-dot1p-maps/forwarding-group-dot1p-map/config/name
     |
     +--rw oc-qos-maps-ext:forwarding-group-dscp-maps
     |  +--rw oc-qos-maps-ext:forwarding-group-dscp-map* [name]
     |     +--rw oc-qos-maps-ext:name                                 -> ../config/name
     |     +--rw oc-qos-maps-ext:config
     |     |  +--rw oc-qos-maps-ext:name?   string
     |     +--ro oc-qos-maps-ext:state
     |     |  +--ro oc-qos-maps-ext:name?   string
     |     +--rw oc-qos-maps-ext:forwarding-group-dscp-map-entries
     |        +--rw oc-qos-maps-ext:forwarding-group-dscp-map-entry* [fwd-group]
     |           +--rw oc-qos-maps-ext:fwd-group    -> ../config/fwd-group
     |           +--rw oc-qos-maps-ext:config
     |           |  +--rw oc-qos-maps-ext:fwd-group    -> ../../../../../../oc-qos:forwarding-groups/forwarding-group/config/name
     |           |  +--rw oc-qos-maps-ext:dscp?        uint8
     |           +--ro oc-qos-maps-ext:state
     |              +--ro oc-qos-maps-ext:fwd-group    -> ../../../../../../oc-qos:forwarding-groups/forwarding-group/config/name
     |              +--ro oc-qos-maps-ext:dscp?        uint8
     +--rw oc-qos-maps-ext:forwarding-group-dot1p-maps
     |  +--rw oc-qos-maps-ext:forwarding-group-dot1p-map* [name]
     |     +--rw oc-qos-maps-ext:name                                  -> ../config/name
     |     +--rw oc-qos-maps-ext:config
     |     |  +--rw oc-qos-maps-ext:name?   string
     |     +--ro oc-qos-maps-ext:state
     |     |  +--ro oc-qos-maps-ext:name?   string
     |     +--rw oc-qos-maps-ext:forwarding-group-dot1p-map-entries
     |        +--rw oc-qos-maps-ext:forwarding-group-dot1p-map-entry* [fwd-group]
     |           +--rw oc-qos-maps-ext:fwd-group    -> ../config/fwd-group
     |           +--rw oc-qos-maps-ext:config
     |           |  +--rw oc-qos-maps-ext:fwd-group    -> ../../../../../../oc-qos:forwarding-groups/forwarding-group/config/name
     |           |  +--rw oc-qos-maps-ext:dot1p?       uint8
     |           +--ro oc-qos-maps-ext:state
     |              +--ro oc-qos-maps-ext:fwd-group    -> ../../../../../../oc-qos:forwarding-groups/forwarding-group/config/name
     |              +--ro oc-qos-maps-ext:dot1p?       uint8

````
 
## 3.2 KLISH CLI

### 3.2.1 Configuration Commands

- Config TC to DSCP map  
````
  sonic(config)# qos-map tc-dscp-map <mapname>
  sonic(conf-tc-dscp-map-mapname)# traffic-class <0..7> dscp <0..63>

  sonic(config)# no tc-dscp-map <mapname>
````

- Config TC to DOT1P map  
````
  sonic(config)# qos-map tc-dot1p-map <mapname>
  sonic(conf-tc-dot1p-map-mapname)# traffic-class <0..7> dot1p <0..7>

  sonic(config)# no tc-dot1p-map <mapname>
````

- Config interface QoS
````
  sonic(config)# qos interface <ifname>
  sonic(conf-qos-ifname)# tc-to-dscp-map <mapname>
  sonic(conf-qos-ifname)# tc-to-dot1p-map <mapname>
  sonic(conf-qos-ifname)# dscp-to-tc-map <mapname>
  sonic(conf-qos-ifname)# dot1p-to-tc-map <mapname>
     
  sonic(conf-qos-ifname)# no tc-to-dscp-map <mapname>
  sonic(conf-qos-ifname)# no tc-to-dot1p-map <mapname>
  sonic(conf-qos-ifname)# no dscp-to-tc-map <mapname>
  sonic(conf-qos-ifname)# no dot1p-to-tc-map <mapname>
````

### 3.2.2 Show Commands

- Show interface QoS configuration
````
  >show qos interface <ifname>  	
  Sample Output:
      Traffic-Class-to-DSCP map: tc-to-dscp-map_1
      Traffic-Class-to-DOT1P map: tc-to-dot1p-map_1
````

- Show TC to DSCP map
````
  >show qos-map tc-dscp-map <mapname>   
  Sample Output:
  TC-DSCP-MAP: tc-to-dscp-map1
  ----------------------------
      TC DSCP
  ----------------------------
      0    8
      1    14
      2    21
  ----------------------------  
````

- Show TC to DOT1P map
````
  >show qos-map tc-dot1p-map <mapname>
  Sample Output:
  TC-DOT1P-MAP: tc-to-dot1p-map1
  ----------------------------
      TC DOT1P
  ----------------------------
      0    0
      2    3
      3    5
  ----------------------------  
````

- Show running config
````
  Work in Progress
````


### 3.2.3 Debug Commands
````
  Work in Progress
````

## 3.3 REST API Support
Supported.

# 4 Flow Diagrams
N/A

# 5 Error Handling
N/A

# 6 Serviceability and Debug
User can use show commands; 
Debug commands to provide Orchagent state will be developed and updated.

# 7 Warm Boot Support
QoS Maps will function during SONiC warm-boot;
System and SWSS docker warm restarts will be supported.
Data-plane traffic will not be interrupted during the warm-boot process.

# 8 Scalability
There is no software limit on number of Maps;
Different limits can be imposed by different platforms and ASICs.
As part of this feature 64 Maps in the system will be validated.

# 9 Unit Test

**Qos map KLISH CLIs**
1. Verify tc-to-dscp map configurations
2. Verify tc-to-dot1p-map-configurations
3. Verify binding tc-to-dscp map on Ethernet interface
4. Verify binding tc-to-dscp map on Portchannel interface
5. Verify binding tc-to-dscp map on L3-VLAN interface
6. Verify binding dscp-to-tc map on PortChannel interface
7. Verify binding dscp-to-tc map on L3-VLAN interface
8. Verify binding dot1p-to-tc map on PortChannel interface

**TC->DSCP Functionality**
1. Verify default TC->DSCP behavior on an interface without user-defined map assigned.
2. Create new TC->DSCP map. Bind on Ethernet interface; Verify the traffic behavior is as per the TC-DSCP map bound.
3. Create new TC->DSCP map. Bind on PortChannel interface; Verify the traffic behavior is as per the TC-DSCP map bound.
4. Create new TC->DSCP map. Bind on L3-VLAN interface; Verify the traffic behavior is as per the TC-DSCP map bound.
5. Update mapping in the bound map and verify the traffic behavior with updated TC->DSCP values.
6. Delete TC->DSCP map binding from interface and verify the traffic behavior

**DSCP->TC Functionality**
1. Verify default DSCP->TC behavior on L3-VLAN interface without user-defined map assigned.
2. Create new DSCP->TC map. Bind on L3-VLAN interface; Verify the traffic behavior is as per the DSCP-TC map bound.
3. Delete DSCP->TC map binding applied on L3-VLAN interface and verify the traffic behavior.
4. Verify default DSCP->TC behavior on PortChannel interface without user-defined map assigned.
5. Create new DSCP->TC map. Bind on PortChannel interface; Verify the traffic behavior is as per the DSCP-TC map bound.
6. Delete DSCP->TC map binding applied on PortChannel interface and verify the traffic behavior.

**DOT1P->TC Functionality**
1. Verify default DOT1P->TC behavior on PortChannel interface without user-defined map assigned.
2. Create new DOT1P->TC map. Bind on PortChannel interface; Verify the traffic behavior is as per the DOT1P-TC map bound.
3. Delete DOT1P->TC map binding applied on PortChannel interface and verify the traffic behavior.

**TC->DOT1P Functionality**
1. Verify default TC->DOT1P behavior on an interface without user-defined map assigned.
2. Create new TC->DOT1P map. Bind on Ethernet interface; Verify the traffic behavior is as per the TC-DOT1P map bound.
3. Create new TC->DOT1P map. Bind on PortChannel interface; Verify the traffic behavior is as per the TC-DOT1P map bound.
4. Update mapping in the bound map and verify the traffic behavior with updated TC->DOT1P values.
5. Unbind the TC->DOT1P map binding from interface and verify the traffic behavior

**Egress maps DPB**
1. Verify DPB functionality by binding TC->DSCP map on Ethernet interface
2. Verify DPB functionality by binding TC->DOT1p map on Ethernet interface

**Warm reboot and config reload**
1. Verify warm-reboot with TC-DSCP map configuration, verify traffic behaviour after the warm-reboot.
2. Verify warm-reboot with TC-DOT1P map configuration, verify traffic behaviour after the warm-reboot.
3. Verify operations like update/delete/unbind/bind with QoS Maps after Warm-Boot.
4. Verify config-reload with TC-DSCP map configuration, verify traffic behaviour after the config reload.
5. Verify config-reload with TC-DOT1P map configuration, verify traffic behaviour after the config-reload.
6. Verify operations like update/delete/unbind/bind with QoS Maps after config reload.

**Negative cases**
1. Error should be displayed when user attempts to delete map applied on interface
2. Verify with out-of-range TC->DSCP value configuration
3. Verify with out-of-range TC->DOT1P value configuration
