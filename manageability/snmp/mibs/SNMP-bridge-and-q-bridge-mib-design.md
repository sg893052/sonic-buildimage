# The SNMP BRIDGE-MIB and Q-BRIDGE-MIB design
# High Level Design Document
### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About this Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definitions/Abbreviation](#definitionsabbreviation)
 
  * [1 Overview](#1-overview)
    * [1.1 System Chart](#11-system-chart)
    * [1.2 Modules description](#12-modules-description)
      * [1.2.1 SNMP agent](#121-snmp-agent)
      * [1.2.2 Redis DB](#122-Redis-DB)

  * [2 Requirements](#2-requirements)
    * [2.1 The BRIDGE-MIB requirements](#21-the-BRIDGE-MIB-Scalar-requirements)
    * [2.2 The Q-BRIDGE-MIB Scalar requirements](#22-the-Q-BRIDGE-MIB-Scalar-requirements)
    * [2.3 The Q-BRIDGE-MIB Table requirements](#23-the-Q-BRIDGE-MIB-Table-requirements)

  * [3 Modules Design](#3-modules-design)
    * [3.1 Modules that need to be updated](#31-modules-that-need-to-be-updated)
      * [3.1.1 SNMP agent](#311-snmp-agent)

# List of Tables
* [Table 1: Revision](#revision)
* [Table 2: Abbreviations](#definitionsabbreviation)

###### Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 |             | Suresh Babu R, Mohammed Faraaz C       | Initial version                   |

# About this Manual
This document provides general information about the design of some of the objects of  BRIDGE-MIB and Q-BRIDGE-MIB implementations in SONiC.
# Scope
This document describes the high level design of the design of MIB for BRIDGE-MIB and Q-BRIDGE-MIB.
# Definitions/Abbreviation
###### Table 2: Abbreviations
| Definitions/Abbreviation | Description                                |
|--------------------------|--------------------------------------------|
| SNMP                     | Simple Network Management Protocol         |
| API                      | Application Programmable Interface         |

# 1 Overview
## 1.1 System Chart
Following diagram describes a top level overview of the SONiC SNMP MIB architecture:
![Design](https://drive.google.com/open?id=19pLP7IQTHD6De4nSY3wrUzGA8ion-8Oy)
## 1.2 Modules description
### 1.2.1 SNMP agent
Stores the information from Various DBs in the local cache and handles the SNMP requests.
### 1.2.2 REDIS DB
Redis DB runs inside the container "database". Redis DB works with the data in format of key-value tuples, needs no predefined schema and holds various SNMP counters under various logical DBs such as Config_db, Asic_db, etc.

# 2 Requirements
The following OIDs should be supported in the SNMP agent:
## 2.1 The BRIDGE-MIB  requirements
| OID                                      | SNMP counter           | Description                              | DB | Key | Value |
|------------------------------------------|------------------------|----------------------------------------------|----------|----------|---------|
| 1.3.6.1.2.1.17.1.1 | dot1dBaseBridgeAddress | The MAC address used by this bridge when it must be referred to in a unique fashion. It is recommended that this be the numerically smallest MAC address of all ports that belong to this bridge. However, it is only required to be unique. When concatenated with dot1dStpPriority, a unique BridgeIdentifier is formed,which is used in the Spanning Tree Protocol. |  |  | |
| 1.3.6.1.2.1.17.1.2 | dot1dBaseNumPorts | The number of ports controlled by this bridging entity. |  |  | |
| 1.3.6.1.2.1.17.1.3 | dot1dBaseType | Indicates what type of bridging this bridge canperform.  If a bridge is actually performing a certain type of bridging, this will be indicated by entries in the port table for the given type. |  |  | |
| 1.3.6.1.2.1.17.1.4.1.1 | dot1dBasePort | The port number of the port for which this entry contains bridge management information | ASIC_DB |  | |
| 1.3.6.1.2.1.17.1.4.1.2 | dot1dBasePortIfIndex | The value of the instance of the ifIndex object,defined in IF-MIB, for the interface corresponding to this port. |Agent index map  |  | |
| 1.3.6.1.2.1.17.1.4.1.4 | dot1dBasePortDelayExceededDiscards | The number of frames discarded by this port due to excessive transit delay through the bridge.  It is incremented by both transparent and source route bridges. |  |  | 0|
| 1.3.6.1.2.1.17.1.4.1.5 | dot1dBasePortMtuExceededDiscards | The number of frames discarded by this port due to an excessive size.  It is incremented by both transparent and source route bridges. |  |  | 0|
| 1.3.6.1.2.1.17.4.2 | dot1dTpAgingTime | The timeout period in seconds for aging out | Config_Db | switch\|switch |  |
## 2.2 The Q-BRIDGE-MIB Scalar requirements
| OID                                      | SNMP counter           | Description                              | DB | Key | Value |
|------------------------------------------|------------------------|---------------------------------------------|----------|----------|---------|
| 1.3.6.1.2.1.17.7.1.1.1 | dot1qVlanVersionNumber | The version number of IEEE 802.1Q that this device supports. |  |  |  |
| 1.3.6.1.2.1.17.7.1.1.2 | dot1qMaxVlanId | The maximum IEEE 802.1Q VLAN-ID that this device out |  |  | 4094(Fixed) |
| 1.3.6.1.2.1.17.7.1.1.3 | dot1qMaxSupportedVlans | The maximum number of IEEE 802.1Q VLANs that this device supports. |  |  | 4094(Fixed) |
| 1.3.6.1.2.1.17.7.1.1.4 | dot1qNumVlans | The current number of IEEE 802.1Q VLANs that are configured in this device. | Config_Db | VLAN\|Vlan\<vlanNumber\> | |
| 1.3.6.1.2.1.17.7.1.4.1 | dot1qVlanNumDeletes | The number of times a VLAN entry has been deleted from the dot1qVlanCurrentTable (for any reason).  If an entry is deleted, then inserted, and then deleted, this counter will be incremented by 2.. |  |  | 0|
## 2.3 The Q-BRIDGE-MIB Table requirements
| OID                                      | SNMP counter           | Description                              | DB | Key | Value |
|------------------------------------------|------------------------|----------------------------------------------|----------|----------|---------|
| 1.3.6.1.2.1.17.7.1.2.1.1.2 | dot1qFdbDynamicCount | The current number of dynamic entries in this Filtering Database. | Asic _Db | ASIC_STATE:SAI_OBJECT_TYPE_FDB_ENTRY:\<FDB_ENTRY\> | |
| 1.3.6.1.2.1.17.7.1.2.2.1.1| dot1qTpFdbAddress | A unicast MAC address for which the device has forwarding and/or filtering information. | Asic _Db | ASIC_STATE:SAI_OBJECT_TYPE_FDB_ENTRY:\<FDB_ENTRY\> | |
| 1.3.6.1.2.1.17.7.1.2.2.1.2| dot1qTpFdbPort | Either the value '0', or the port number of the port on which a frame having a source address equal to the value of the corresponding instance of dot1qTpFdbAddress has been seen. A value of '0' indicates that the port number has not been learned but that the device does have some forwarding/filtering information about this address (e.g., in the dot1qStaticUnicastTable). | Asic _Db | ASIC_STATE:SAI_OBJECT_TYPE_FDB_ENTRY:\<FDB_ENTRY\> | |
| 1.3.6.1.2.1.17.7.1.2.2.1.3 | dot1qTpFdbStatus |  | Asic _Db | ASIC_STATE:SAI_OBJECT_TYPE_FDB_ENTRY:\<FDB_ENTRY\> | |
| 1.3.6.1.2.1.17.7.1.4.2.1.2 | dot1qVlanIndex | The VLAN-ID or other identifier referring to this VLAN. | Config_Db|VLAN_MEMBER  | |
| 1.3.6.1.2.1.17.7.1.4.2.1.4 | dot1qVlanCurrentEgressPorts | The set of ports that are transmitting traffic for this VLAN as either tagged or untagged frames. | Config_Db | VLAN_MEMBER | |
| 1.3.6.1.2.1.17.7.1.4.2.1.5 | dot1qVlanCurrentUntaggedPorts | The set of ports that are transmitting traffic for this VLAN as untagged frames. | Config_Db  | VLAN_MEMBER | |
| 1.3.6.1.2.1.17.7.1.4.3.1.1 | dot1qVlanStaticName | An administratively assigned string, which may be used to identify the VLAN. |Config_Db  | VLAN_MEMBER | |
| 1.3.6.1.2.1.17.7.1.4.3.1.2 | dot1qVlanStaticEgressPorts | The set of ports that are permanently assigned to the egress list for this VLAN by management.  Changes to a bit in this object affect the per-port, per-VLAN Registrar control for Registration Fixed for the relevant GVRP state machine on each port.  A port may not be added in this set if it is already a member of the set of ports in dot1qVlanForbiddenEgressPorts.  The  default value of this object is a string of zeros of appropriate length, indicating not fixed. | Config_Db |VLAN_MEMBER  | |
| 1.3.6.1.2.1.17.7.1.4.3.1.4 | dot1qVlanStaticUntaggedPorts | The set of ports that should transmit egress packets for this VLAN as untagged.  The default value of this object for the default VLAN (dot1qVlanIndex = 1) is a string of appropriate length including all ports.  There is no specified default for other VLANs.  If a device agent cannot support the set of ports being set, then it will reject the set operation with an error.  For example, a manager might attempt to set more than one VLAN to be untagged on egress where the device does not support this IEEE 802.1Q option. | Config_Db |VLAN_MEMBER  | |
| 1.3.6.1.2.1.17.7.1.4.3.1.5 | dot1qVlanStaticRowStatus | This object indicates the status of this entry. | Config_Db | VLAN_MEMBER | |
| 1.3.6.1.2.1.17.7.1.4.5.1.1 | dot1qPvid | The PVID, the VLAN-ID assigned to untagged frames or Priority-Tagged frames received on this port. The value of this object MUST be retained across reinitializations of the management system. | Config_Db |VLAN_MEMBER  | |
# 3 Modules Design
## 3.1 Modules that need to be updated
### 3.1.1 SNMP agent
SNMP agent should be modified to support objects for Bridge and Q-Bridge by reading values from Redis DB as described in [Requirements](#2-requirements).