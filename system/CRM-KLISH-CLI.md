# CRM OC Yang and KLISH CLI Config Support
# High Level Design Document
#### Rev 0.1

# Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About this Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definitionabbreviation)
  * [1 Overview](#1-overview)
  * [2 Functionality](#2-functionality)
  * [3 Design](#3-design)
  * [4 Flow Diagrams](#4-flow-diagrams)
  * [5 Serviceability and Debug](#5-serviceability-and-debug)
  * [6 Warm Boot Support](#6-warm-boot-support)
  * [7 Scalability](#7-scalability)
  * [8 Unit Test](#8-unit-test)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations) <br>

# Revision
| Rev  |   Date   |   Author   | Change Description |
| :--: | :------: | :--------: | :----------------: |
| 0.1  | 6/3/2020 | David Sips |  Initial version   |
|      |          |            |                    |
|      |          |            |                    |

# About this Manual
This document provides OpenConfig Yang and KLISH Config CLI implementation for CRM feature in SONiC. CRM monitors utilization of ASIC resources by polling SAI attributes.

# Scope
This document covers the "configuration" commands supported for CRM based on OpenConfig YANG.

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term** | ***Meaning***                                                |
| -------- | ------------------------------------------------------------ |
| CRM      | Critical Resource Monitoring (https://github.com/Azure/SONiC/wiki/Critical-Resource-Monitoring-High-Level-Design) |
| KLISH    | Kommand Line Interface Shell (http://libcode.org/projects/klish/) |


# 1 Overview
## 1.1 Functional Requirements
## 1.2 Configuration and Management Requirements
Support OpenConfig based Config KLISH CLIs to CRM protocol

## 1.3 Scalability Requirements

Not Applicable

## 1.4 Warm Boot Requirements

Not Applicable

# 2 Functionality
## 2.1 Target Deployment Use Case

Buzznik+

# 3 Design
## 3.1 Overview

CRM. 

## 3.2 Database

No  configuration entries are added or modified for any SONiC database for the OC Yang and KLISH CLI support. For reference, this section describes the format of Config DB entries already present in SONiC for CRM.

### 3.2.1 CONFIG DB
```
; Defines schema for CRM configuration attributes
key                                  = CRM                             ; CRM configuration
; field                              = value
polling_interval                     = UINT32                        ; polling interval
acl_counter_threshold_type           = "percentage" / "used" / "free"  ; Threshold type for 'acl counter'
acl_entry_threshold_type             = "percentage" / "used" / "free"  ; Threshold type for 'acl entry'
acl_group_threshold_type             = "percentage" / "used" / "free"  ; Threshold type for 'acl group'
acl_table_threshold_type             = "percentage" / "used" / "free"  ; Threshold type for 'acl table'
dnat_entry_threshold_type            = "percentage" / "used" / "free"  ; Threshold type for 'Destination NAT'
fdb_entry_threshold_type             = "percentage" / "used" / "free"  ; Threshold type for 'fdb entry'
ipmc_entry_threshold_type            = "percentage" / "used" / "free"  ; Threshold type for 'ipmc entry'
ipv4_neighbor_threshold_type         = "percentage" / "used" / "free"  ; Threshold type for 'ipv4 neighbor'
ipv4_nexthop_threshold_type          = "percentage" / "used" / "free"  ; Threshold type for 'ipv4 next-hop'
ipv4_route_threshold_type            = "percentage" / "used" / "free"  ; Threshold type for 'ipv4 route'
ipv6_neighbor_threshold_type         = "percentage" / "used" / "free"  ; Threshold type for 'ipv6 neighbor'
ipv6_nexthop__threshold_type         = "percentage" / "used" / "free"  ; Threshold type for 'ipv6 next-hop'
ipv6_route_threshold_type            = "percentage" / "used" / "free"  ; Threshold type for 'ipv6 route'
nexthop_group_member_threshold_type  = "percentage" / "used" / "free"  ; Threshold type for 'next-hop group member'
nexthop_group_object_threshold_type  = "percentage" / "used" / "free"  ; Threshold type for 'next-hop group object'
snat_entry_threshold_type            = "percentage" / "used" / "free"  ; Threshold type for 'Source NAT'
acl_counter_low_threshold            = UINT32                        ; Low threshold for 'acl counter'
acl_entry_low_threshold              = UINT32                        ; Low threshold for 'acl entry'
acl_group_low_threshold              = UINT32                        ; Low threshold for 'acl group'
acl_table_low_threshold              = UINT32                        ; Low threshold for 'acl table'
dnat_entry_low_threshold             = UINT32                        ; Low threshold for 'Destination NAT' 
fdb_entry_low_threshold              = UINT32                        ; Low threshold for 'fdb entry'
ipmc_entry_low_threshold             = UINT32                        ; Low threshold for 'ipmc entry'
ipv4_neighbor_low_threshold          = UINT32                        ; Low threshold for 'ipv4 neighbor'
ipv4_nexthop_low_threshold           = UINT32                        ; Low threshold for 'ipv4 next-hop'
ipv4_route_low_threshold             = UINT32                        ; Low threshold for 'ipv4 route'
ipv6_neighbor_low_threshold          = UINT32                        ; Low threshold for 'ipv6 neighbor'
ipv6_nexthop_low_threshold           = UINT32                        ; Low threshold for 'ipv6 next-hop'
ipv6_route_low_threshold             = UINT32                        ; Low threshold for 'ipv6 route'
nexthop_group_member_low_threshold   = UINT32                        ; Low threshold for 'next-hop group member'
nexthop_group_object_low_threshold   = UINT32                        ; Low threshold for 'next-hop group object'
snat_entry_low_threshold             = UINT32                        ; Low threshold for 'Source NAT' 
acl_counter_high_threshold           = UINT32                        ; High threshold for 'acl counter'
acl_entry_high_threshold             = UINT32                        ; High threshold for 'acl entry'
acl_group_high_threshold             = UINT32                        ; High threshold for 'acl group'
acl_table_high_threshold             = UINT32                        ; High threshold for 'acl table'
dnat_entry_high_threshold            = UINT32                        ; High threshold for 'Destination NAT'
fdb_entry_high_threshold             = UINT32                        ; High threshold for 'fdb entry'
ipmc_entry_high_threshold            = UINT32                        ; High threshold for 'ipmc entry'
ipv4_neighbor_high_threshold         = UINT32                        ; High threshold for 'ipv4 neighbor'
ipv4_nexthop_high_threshold          = UINT32                        ; High threshold for 'ipv4 next-hop'
ipv4_route_high_threshold            = UINT32                        ; High threshold for 'ipv4 route'
ipv6_neighbor_high_threshold         = UINT32                        ; High threshold for 'ipv6 neighbor'
ipv6_nexthop_high_threshold          = UINT32                        ; High threshold for 'ipv6 next-hop'
ipv6_route_high_threshold            = UINT32                        ; High threshold for 'ipv6 route'
nexthop_group_member_high_threshold  = UINT32                        ; High threshold for 'next-hop group member'
nexthop_group_object_high_threshold  = UINT32                        ; High threshold for 'next-hop group object'
snat_entry_high_threshold            = UINT32                        ; High threshold for 'Source NAT'
```

### 3.3 Data Model

### 3.3.1 openconfig-crm.yang

CRM currently has no OpenConfig data model defined. Consequently, an OC compatible model will need to be written. The logical location would be an extension to openconfig-system.yang. The YANG tree will look like so:

```
module: openconfig-system
  +--rw system
     +--rw oc-sys-ext:crm
        +--rw oc-sys-ext:config
        |  +--rw oc-sys-ext:polling-interval?   uint32
        |  +--rw oc-sys-ext:thresholds
        |     +--rw oc-sys-ext:ipv4
        |     |  +--rw oc-sys-ext:neighbor
        |     |  |  +--rw oc-sys-ext:type?   threshold
        |     |  |  +--rw oc-sys-ext:high?   uint32
        |     |  |  +--rw oc-sys-ext:low?    uint32
        |     |  +--rw oc-sys-ext:nexthop
        |     |  |  +--rw oc-sys-ext:type?   threshold
        |     |  |  +--rw oc-sys-ext:high?   uint32
        |     |  |  +--rw oc-sys-ext:low?    uint32
        |     |  +--rw oc-sys-ext:route
        |     |     +--rw oc-sys-ext:type?   threshold
        |     |     +--rw oc-sys-ext:high?   uint32
        |     |     +--rw oc-sys-ext:low?    uint32
        |     +--rw oc-sys-ext:ipv6
        |     |  +--rw oc-sys-ext:neighbor
        |     |  |  +--rw oc-sys-ext:type?   threshold
        |     |  |  +--rw oc-sys-ext:high?   uint32
        |     |  |  +--rw oc-sys-ext:low?    uint32
        |     |  +--rw oc-sys-ext:nexthop
        |     |  |  +--rw oc-sys-ext:type?   threshold
        |     |  |  +--rw oc-sys-ext:high?   uint32
        |     |  |  +--rw oc-sys-ext:low?    uint32
        |     |  +--rw oc-sys-ext:route
        |     |     +--rw oc-sys-ext:type?   threshold
        |     |     +--rw oc-sys-ext:high?   uint32
        |     |     +--rw oc-sys-ext:low?    uint32
        |     +--rw oc-sys-ext:dnat
        |     |  +--rw oc-sys-ext:type?   threshold
        |     |  +--rw oc-sys-ext:high?   uint32
        |     |  +--rw oc-sys-ext:low?    uint32
        |     +--rw oc-sys-ext:snat
        |     |  +--rw oc-sys-ext:type?   threshold
        |     |  +--rw oc-sys-ext:high?   uint32
        |     |  +--rw oc-sys-ext:low?    uint32
        |     +--rw oc-sys-ext:fdb
        |     |  +--rw oc-sys-ext:type?   threshold
        |     |  +--rw oc-sys-ext:high?   uint32
        |     |  +--rw oc-sys-ext:low?    uint32
        |     +--rw oc-sys-ext:ipmc
        |     |  +--rw oc-sys-ext:type?   threshold
        |     |  +--rw oc-sys-ext:high?   uint32
        |     |  +--rw oc-sys-ext:low?    uint32
        |     +--rw oc-sys-ext:nexthop
        |     |  +--rw oc-sys-ext:group
        |     |     +--rw oc-sys-ext:member
        |     |     |  +--rw oc-sys-ext:type?   threshold
        |     |     |  +--rw oc-sys-ext:high?   uint32
        |     |     |  +--rw oc-sys-ext:low?    uint32
        |     |     +--rw oc-sys-ext:object
        |     |        +--rw oc-sys-ext:type?   threshold
        |     |        +--rw oc-sys-ext:high?   uint32
        |     |        +--rw oc-sys-ext:low?    uint32
        |     +--rw oc-sys-ext:acl
        |        +--rw oc-sys-ext:table
        |        |  +--rw oc-sys-ext:type?   threshold
        |        |  +--rw oc-sys-ext:high?   uint32
        |        |  +--rw oc-sys-ext:low?    uint32
        |        +--rw oc-sys-ext:group
        |           +--rw oc-sys-ext:type?      threshold
        |           +--rw oc-sys-ext:high?      uint32
        |           +--rw oc-sys-ext:low?       uint32
        |           +--rw oc-sys-ext:counter
        |           |  +--rw oc-sys-ext:type?   threshold
        |           |  +--rw oc-sys-ext:high?   uint32
        |           |  +--rw oc-sys-ext:low?    uint32
        |           +--rw oc-sys-ext:entry
        |              +--rw oc-sys-ext:type?   threshold
        |              +--rw oc-sys-ext:high?   uint32
        |              +--rw oc-sys-ext:low?    uint32
        +--ro oc-sys-ext:state
           +--ro oc-sys-ext:polling-interval?      uint32
           +--ro oc-sys-ext:thresholds
           |  +--ro oc-sys-ext:ipv4
           |  |  +--ro oc-sys-ext:neighbor
           |  |  |  +--ro oc-sys-ext:type?   threshold
           |  |  |  +--ro oc-sys-ext:high?   uint32
           |  |  |  +--ro oc-sys-ext:low?    uint32
           |  |  +--ro oc-sys-ext:nexthop
           |  |  |  +--ro oc-sys-ext:type?   threshold
           |  |  |  +--ro oc-sys-ext:high?   uint32
           |  |  |  +--ro oc-sys-ext:low?    uint32
           |  |  +--ro oc-sys-ext:route
           |  |     +--ro oc-sys-ext:type?   threshold
           |  |     +--ro oc-sys-ext:high?   uint32
           |  |     +--ro oc-sys-ext:low?    uint32
           |  +--ro oc-sys-ext:ipv6
           |  |  +--ro oc-sys-ext:neighbor
           |  |  |  +--ro oc-sys-ext:type?   threshold
           |  |  |  +--ro oc-sys-ext:high?   uint32
           |  |  |  +--ro oc-sys-ext:low?    uint32
           |  |  +--ro oc-sys-ext:nexthop
           |  |  |  +--ro oc-sys-ext:type?   threshold
           |  |  |  +--ro oc-sys-ext:high?   uint32
           |  |  |  +--ro oc-sys-ext:low?    uint32
           |  |  +--ro oc-sys-ext:route
           |  |     +--ro oc-sys-ext:type?   threshold
           |  |     +--ro oc-sys-ext:high?   uint32
           |  |     +--ro oc-sys-ext:low?    uint32
           |  +--ro oc-sys-ext:dnat
           |  |  +--ro oc-sys-ext:type?   threshold
           |  |  +--ro oc-sys-ext:high?   uint32
           |  |  +--ro oc-sys-ext:low?    uint32
           |  +--ro oc-sys-ext:snat
           |  |  +--ro oc-sys-ext:type?   threshold
           |  |  +--ro oc-sys-ext:high?   uint32
           |  |  +--ro oc-sys-ext:low?    uint32
           |  +--ro oc-sys-ext:fdb
           |  |  +--ro oc-sys-ext:type?   threshold
           |  |  +--ro oc-sys-ext:high?   uint32
           |  |  +--ro oc-sys-ext:low?    uint32
           |  +--ro oc-sys-ext:ipmc
           |  |  +--ro oc-sys-ext:type?   threshold
           |  |  +--ro oc-sys-ext:high?   uint32
           |  |  +--ro oc-sys-ext:low?    uint32
           |  +--ro oc-sys-ext:nexthop
           |  |  +--ro oc-sys-ext:group
           |  |     +--ro oc-sys-ext:member
           |  |     |  +--ro oc-sys-ext:type?   threshold
           |  |     |  +--ro oc-sys-ext:high?   uint32
           |  |     |  +--ro oc-sys-ext:low?    uint32
           |  |     +--ro oc-sys-ext:object
           |  |        +--ro oc-sys-ext:type?   threshold
           |  |        +--ro oc-sys-ext:high?   uint32
           |  |        +--ro oc-sys-ext:low?    uint32
           |  +--ro oc-sys-ext:acl
           |     +--ro oc-sys-ext:table
           |     |  +--ro oc-sys-ext:type?   threshold
           |     |  +--ro oc-sys-ext:high?   uint32
           |     |  +--ro oc-sys-ext:low?    uint32
           |     +--ro oc-sys-ext:group
           |        +--ro oc-sys-ext:type?      threshold
           |        +--ro oc-sys-ext:high?      uint32
           |        +--ro oc-sys-ext:low?       uint32
           |        +--ro oc-sys-ext:counter
           |        |  +--ro oc-sys-ext:type?   threshold
           |        |  +--ro oc-sys-ext:high?   uint32
           |        |  +--ro oc-sys-ext:low?    uint32
           |        +--ro oc-sys-ext:entry
           |           +--ro oc-sys-ext:type?   threshold
           |           +--ro oc-sys-ext:high?   uint32
           |           +--ro oc-sys-ext:low?    uint32
           +--ro oc-sys-ext:resources
           |  +--ro oc-sys-ext:dnat-entries-used?                 uint32
           |  +--ro oc-sys-ext:dnat-entries-available?            uint32
           |  +--ro oc-sys-ext:snat-entries-used?                 uint32
           |  +--ro oc-sys-ext:snat-entries-available?            uint32
           |  +--ro oc-sys-ext:fdb-entries-used?                  uint32
           |  +--ro oc-sys-ext:fdb-entries-available?             uint32
           |  +--ro oc-sys-ext:ipmc-entries-used?                 uint32
           |  +--ro oc-sys-ext:ipmc-entries-available?            uint32
           |  +--ro oc-sys-ext:ipv4-neighbors-used?               uint32
           |  +--ro oc-sys-ext:ipv4-neighbors-available?          uint32
           |  +--ro oc-sys-ext:ipv4-nexthops-used?                uint32
           |  +--ro oc-sys-ext:ipv4-nexthops-available?           uint32
           |  +--ro oc-sys-ext:ipv4-routes-used?                  uint32
           |  +--ro oc-sys-ext:ipv4-routes-available?             uint32
           |  +--ro oc-sys-ext:ipv6-neighbors-used?               uint32
           |  +--ro oc-sys-ext:ipv6-neighbors-available?          uint32
           |  +--ro oc-sys-ext:ipv6-nexthops-used?                uint32
           |  +--ro oc-sys-ext:ipv6-nexthops-available?           uint32
           |  +--ro oc-sys-ext:ipv6-routes-used?                  uint32
           |  +--ro oc-sys-ext:ipv6-routes-available?             uint32
           |  +--ro oc-sys-ext:nexthop-groups-used?               uint32
           |  +--ro oc-sys-ext:nexthop-groups-available?          uint32
           |  +--ro oc-sys-ext:nexthop-group-members-used?        uint32
           |  +--ro oc-sys-ext:nexthop-group-members-available?   uint32
           +--ro oc-sys-ext:acl-resources
           |  +--ro oc-sys-ext:ingress
           |  |  +--ro oc-sys-ext:switch
           |  |  |  +--ro oc-sys-ext:groups-used?        uint32
           |  |  |  +--ro oc-sys-ext:groups-available?   uint32
           |  |  |  +--ro oc-sys-ext:tables-used?        uint32
           |  |  |  +--ro oc-sys-ext:tables-available?   uint32
           |  |  +--ro oc-sys-ext:vlan
           |  |  |  +--ro oc-sys-ext:groups-used?        uint32
           |  |  |  +--ro oc-sys-ext:groups-available?   uint32
           |  |  |  +--ro oc-sys-ext:tables-used?        uint32
           |  |  |  +--ro oc-sys-ext:tables-available?   uint32
           |  |  +--ro oc-sys-ext:port
           |  |  |  +--ro oc-sys-ext:groups-used?        uint32
           |  |  |  +--ro oc-sys-ext:groups-available?   uint32
           |  |  |  +--ro oc-sys-ext:tables-used?        uint32
           |  |  |  +--ro oc-sys-ext:tables-available?   uint32
           |  |  +--ro oc-sys-ext:rif
           |  |  |  +--ro oc-sys-ext:groups-used?        uint32
           |  |  |  +--ro oc-sys-ext:groups-available?   uint32
           |  |  |  +--ro oc-sys-ext:tables-used?        uint32
           |  |  |  +--ro oc-sys-ext:tables-available?   uint32
           |  |  +--ro oc-sys-ext:lag
           |  |     +--ro oc-sys-ext:groups-used?        uint32
           |  |     +--ro oc-sys-ext:groups-available?   uint32
           |  |     +--ro oc-sys-ext:tables-used?        uint32
           |  |     +--ro oc-sys-ext:tables-available?   uint32
           |  +--ro oc-sys-ext:egress
           |     +--ro oc-sys-ext:switch
           |     |  +--ro oc-sys-ext:groups-used?        uint32
           |     |  +--ro oc-sys-ext:groups-available?   uint32
           |     |  +--ro oc-sys-ext:tables-used?        uint32
           |     |  +--ro oc-sys-ext:tables-available?   uint32
           |     +--ro oc-sys-ext:vlan
           |     |  +--ro oc-sys-ext:groups-used?        uint32
           |     |  +--ro oc-sys-ext:groups-available?   uint32
           |     |  +--ro oc-sys-ext:tables-used?        uint32
           |     |  +--ro oc-sys-ext:tables-available?   uint32
           |     +--ro oc-sys-ext:port
           |     |  +--ro oc-sys-ext:groups-used?        uint32
           |     |  +--ro oc-sys-ext:groups-available?   uint32
           |     |  +--ro oc-sys-ext:tables-used?        uint32
           |     |  +--ro oc-sys-ext:tables-available?   uint32
           |     +--ro oc-sys-ext:rif
           |     |  +--ro oc-sys-ext:groups-used?        uint32
           |     |  +--ro oc-sys-ext:groups-available?   uint32
           |     |  +--ro oc-sys-ext:tables-used?        uint32
           |     |  +--ro oc-sys-ext:tables-available?   uint32
           |     +--ro oc-sys-ext:lag
           |        +--ro oc-sys-ext:groups-used?        uint32
           |        +--ro oc-sys-ext:groups-available?   uint32
           |        +--ro oc-sys-ext:tables-used?        uint32
           |        +--ro oc-sys-ext:tables-available?   uint32
           +--ro oc-sys-ext:acl-table-resources
              +--ro oc-sys-ext:entries* []
                 +--ro oc-sys-ext:index?               uint64
                 +--ro oc-sys-ext:entries-used?        uint32
                 +--ro oc-sys-ext:entries-available?   uint32
```



### 3.3.2 Configuration Commands

The below CLI configuration mode commands are supported. CRM is not configured by default. The administrator may use below commands to configure critical resource monitoring.



**Polling Interval** The polling interval for CRM. This is the interval the monitoring process will periodically poll SAI counters to determine resource usage.

**[no]  crm  polling  interval  < time-interval >**

Use no form of the command to reset the default value to 300s.

    sonic-cli(config)# crm polling interval 120
    sonic-cli(config)# no crm polling interval



**Threshold type** for FDB entries, IP Multicast entries, Network Address Translation resources may be configured. The **all** qualifier for **< resource >** allows all attributes to be set (or cleared) in bulk.

**[no]  crm  thresholds  < resource >  type  < percentage |used | free > **

**resource** is one of the following:

```
  all      CRM configuration for all resources
  dnat     CRM configuration for Destination NAT
  fdb      CRM configuration for FDB resource
  ipmc     CRM configuration for IPMC resource
  snat     CRM configuration for Source NAT resource
```

Use no form of the command to reset the thresholds to the default of percentage.

    sonic-cli(config)# crm threshold all type percentage
    sonic-cli(config)# crm threshold ipmc type used
    sonic-cli(config)# no crm threshold fdb type
    sonic-cli(config)# no crm threshold all type



**Threshold high and low values** for FDB entries, IP Multicast entries, Network Address Translation resources may be configured. The **all** qualifier for **< resource >** allows all attributes to be set (or cleared) in bulk. Resource is one of the following:

**[no]  crm  thresholds  < resource >  < high | low >  < value > **

```
  all      CRM configuration for all resources
  dnat     CRM configuration for Destination NAT
  fdb      CRM configuration for FDB resource
  ipmc     CRM configuration for IPMC resource
  snat     CRM configuration for Source NAT resource
```

Use no form of the command to reset the thresholds to remove the value.

    sonic-cli(config)# crm threshold all high 90
    sonic-cli(config)# crm threshold ipmc low 65
    sonic-cli(config)# no crm threshold fdb low
    sonic-cli(config)# no crm threshold all high



Threshold type and high and low values for **ACL Group Counters or Entries** may be configured.

**[no]  crm  thresholds  acl  group  < counter | entry >  type  < percentage | used | free >**

**[no]  crm  thresholds  acl  group  < counter | entry >  < high | low >  < value >**

Use no form of the command to reset the thresholds to remove the value or to revert the type to default percentage..

    sonic-cli(config)# crm threshold acl group counter type used
    sonic-cli(config)# crm threshold acl group counter high 42
    sonic-cli(config)# crm threshold acl group entry low 60
    sonic-cli(config)# no crm threshold acl group counter type
    sonic-cli(config)# no crm threshold acl group entry low



Threshold type and high and low values for **ACL Groups** may be configured.

**[no]  crm  thresholds  acl  group  type  < percentage | used | free >**

**[no]  crm  thresholds  acl  group  < high | low >  < value >**

Use no form of the command to reset the thresholds to remove the value or to revert the type to default percentage..

    sonic-cli(config)# crm threshold acl group type free
    sonic-cli(config)# crm threshold acl group high 24
    sonic-cli(config)# no crm threshold acl group type
    sonic-cli(config)# no crm threshold acl group low



Threshold type and high and low values for **ACL Tables** may be configured.

**[no]  crm  thresholds  acl  table  type  < percentage | used | free >**

**[no]  crm  thresholds  acl  table< high | low >  < value >**

Use no form of the command to reset the thresholds to remove the value or to revert the type to default percentage..

    sonic-cli(config)# crm threshold acl table type free
    sonic-cli(config)# crm threshold acl table low 128
    sonic-cli(config)# no crm threshold acl table type
    sonic-cli(config)# no crm threshold acl table high



Threshold type and high and low values for **IPv4 and IPv6 Entries** may be configured.

**[no]  crm  thresholds  < ipv4 | ipv6 >  < neighbor | nexthop | route >  type  < percentage | used | free >**

**[no]  crm  thresholds  < ipv4 | ipv6 >  < neighbor | nexthop | route >  < high | low >  < value >**

Use no form of the command to reset the thresholds to remove the value or to revert the type to default percentage..

    sonic-cli(config)# crm threshold ipv4 neighbor type used
    sonic-cli(config)# crm threshold ipv6 nexthop high 42
    sonic-cli(config)# crm threshold ipv4 route low 60
    sonic-cli(config)# no crm threshold ipv6 nexthop type
    sonic-cli(config)# no crm threshold ipv4 neighbor low



Threshold type and high and low values for **Nexthop Group Members or Objects** may be configured.

**[no]  crm  thresholds  nexthop group  < member | object >  type  < percentage | used | free >**

**[no]  crm  thresholds  nexthop group  < member | object >  < high | low >  < value >**

Use no form of the command to reset the thresholds to remove the value or to revert the type to default percentage..

    sonic-cli(config)# crm thresholds nexthop group member type percentage
    sonic-cli(config)# crm thresholds nexthop group member high 50
    sonic-cli(config)# crm thresholds nexthop group object low 60
    sonic-cli(config)# no crm thresholds nexthop group object type
    sonic-cli(config)# no crm thresholds nexthop group member high



### 3.3.3 Show Commands

The below CLI show commands are supported.



Show **summary** information for CRM. 

**show  crm  < summary >**

**summary** is an optional parameter

```
sonic-cli(config)# show crm summary

CRM Summary
-----------
Polling Interval: 300 second(s)

sonic-cli(config)# show crm

CRM Summary
-----------
Polling Interval: 300 second(s)

```



Show **resource** information for the system as related to CRM. 

**show  crm  resources < dnat | fdb | ipmc | ipv4 | ipv6 | nexthop >**

Specification of a resource type (dnat, fdb, etc.) is an optional. If omitted, all resources are shown.

```
sonic-cli(config)# show crm resources nexthop

Resource Name           Used Count    Available Count
--------------------  ------------  -----------------
nexthop_group                    0                256
nexthop_group_member             0              32768

sonic-cli(config)# show crm resources ipmc

Resource Name           Used Count    Available Count
--------------------  ------------  -----------------
ipmc_entry                       0              24576

sonic-cli(config)# show crm resources ipv4

Resource Name           Used Count    Available Count
--------------------  ------------  -----------------
ipv4_neighbor                    0              24576
ipv4_nexthop                     0              65534
ipv4_route                       1              32751

```



Show **ACL resource** information for the system as related to CRM.

**show  crm  resources acl < group | table  >**

Specification of a ACl resource type (group or table) is an optional. If omitted, all resources are shown (group, then table).

    sonic-cli(config)# show crm resources acl group
    
    Stage    Bind Point    Resource Name      Used Count    Available Count
    -------  ------------  ---------------  ------------  -----------------
    INGRESS  PORT          acl_group                   0                256
    INGRESS  PORT          acl_table                   0                  3
    INGRESS  LAG           acl_group                   0                256
    INGRESS  LAG           acl_table                   0                  3
    INGRESS  VLAN          acl_group                   0                256
    INGRESS  VLAN          acl_table                   0                  9
    INGRESS  RIF           acl_group                   0                256
    INGRESS  RIF           acl_table                   0                  9
    INGRESS  SWITCH        acl_group                   0                256
    INGRESS  SWITCH        acl_table                   0                  9
    EGRESS   PORT          acl_group                   0                256
    EGRESS   PORT          acl_table                   0                  2
    EGRESS   LAG           acl_group                   0                256
    EGRESS   LAG           acl_table                   0                  2
    EGRESS   VLAN          acl_group                   0                256
    EGRESS   VLAN          acl_table                   0                  2
    EGRESS   RIF           acl_group                   0                256
    EGRESS   RIF           acl_table                   0                  2
    EGRESS   SWITCH        acl_group                   0                256
    EGRESS   SWITCH        acl_table                   0                  2
    
    sonic-cli(config)# show crm resources acl table
    
    Table ID    Resource Name    Used Count    Available Count
    ----------  ---------------  ------------  -----------------




Show **resource** information for the system as related to CRM. 

**show  crm  resources < all >**

```
sonic-cli(config)# show crm resources

Resource Name           Used Count    Available Count
--------------------  ------------  -----------------
ipv4_route                       1              32751
ipv6_route                       2              10238
ipv4_nexthop                     0              65534
ipv6_nexthop                     0              65534
ipv4_neighbor                    0              24576
ipv6_neighbor                    0              12288
nexthop_group_member             0              32768
nexthop_group                    0                256
fdb_entry                        0              65535
ipmc_entry                       0              24576
snat_entry                       0               1024
dnat_entry                       0               1024

Stage    Bind Point    Resource Name      Used Count    Available Count
-------  ------------  ---------------  ------------  -----------------
INGRESS  PORT          acl_group                   0                256
INGRESS  PORT          acl_table                   0                  3
INGRESS  LAG           acl_group                   0                256
INGRESS  LAG           acl_table                   0                  3
INGRESS  VLAN          acl_group                   0                256
INGRESS  VLAN          acl_table                   0                  9
INGRESS  RIF           acl_group                   0                256
INGRESS  RIF           acl_table                   0                  9
INGRESS  SWITCH        acl_group                   0                256
INGRESS  SWITCH        acl_table                   0                  9
EGRESS   PORT          acl_group                   0                256
EGRESS   PORT          acl_table                   0                  2
EGRESS   LAG           acl_group                   0                256
EGRESS   LAG           acl_table                   0                  2
EGRESS   VLAN          acl_group                   0                256
EGRESS   VLAN          acl_table                   0                  2
EGRESS   RIF           acl_group                   0                256
EGRESS   RIF           acl_table                   0                  2
EGRESS   SWITCH        acl_group                   0                256
EGRESS   SWITCH        acl_table                   0                  2

Table ID    Resource Name    Used Count    Available Count
----------  ---------------  ------------  -----------------


```



Show **threshold** information for CRM. 

**show  crm  thresholds**

```
sonic-cli(config)# show crm resources thresholds

Resource Name         Threshold Type      Low Threshold    High Threshold
--------------------  ----------------  ---------------  ----------------
acl_counter           free                           16                32
acl_entry             percentage                     82                95
acl_group             percentage                     60                75
acl_table             used                         1776              1492
dnat_entry            free                          128               256
fdb_entry             percentage                     85                95
ipmc_entry            percentage                     80                90
ipv4_nexthop          used                        40960             49152
ipv4_route            free                         1024              4096
ipv6_neighbor         percentage                     75                87
ipv6_neighbor         percentage                     75                87
ipv6_nexthop          used                        40960             49152
ipv6_route            free                         1024              4096
nexthop_group         percentage                     80                90
nexthop_group_member  percentage                     80                90
snat_entry            used                          512               768
```



## 3.3 REST API Support
Below REST URIs are supported

```
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/oc-sys-ext:polling-interval
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/acl/group/counter/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/acl/group/counter/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/acl/group/counter/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/acl/group/entry/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/acl/group/entry/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/acl/group/entry/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/acl/group/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/acl/group/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/acl/group/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/acl/table/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/acl/table/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/acl/table/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/dnat/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/dnat/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/dnat/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/fdb/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/fdb/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/fdb/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipmc/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipmc/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipmc/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv4/neighbor/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv4/neighbor/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv4/neighbor/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv4/nexthop/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv4/nexthop/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv4/nexthop/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv4/route/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv4/route/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv4/route/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv6/neighbor/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv6/neighbor/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv6/neighbor/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv6/nexthop/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv6/nexthop/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv6/nexthop/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv6/route/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv6/route/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/ipv6/route/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/nexthop/group/member/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/nexthop/group/member/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/nexthop/group/member/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/nexthop/group/object/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/nexthop/group/object/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/nexthop/group/object/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/snat/high
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/snat/low
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:config/thresholds/snat/type
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/lag/groups-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/lag/groups-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/lag/tables-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/lag/tables-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/port/groups-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/port/groups-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/port/tables-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/port/tables-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/rif/groups-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/rif/groups-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/rif/tables-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/rif/tables-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/switch/groups-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/switch/groups-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/switch/tables-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/switch/tables-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/vlan/groups-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/vlan/groups-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/vlan/tables-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/egress/vlan/tables-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/lag/groups-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/lag/groups-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/lag/tables-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/lag/tables-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/port/groups-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/port/groups-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/port/tables-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/port/tables-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/rif/groups-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/rif/groups-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/rif/tables-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/rif/tables-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/switch/groups-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/switch/groups-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/switch/tables-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/switch/tables-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/vlan/groups-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/vlan/groups-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/vlan/tables-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-resources/ingress/vlan/tables-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-table-resources/entries{index}/tables-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/acl-table-resources/entries{index}/tables-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/dnat-entries-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/dnat-entries-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/fdb-entries-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/fdb-entries-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/ipmc-entries-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/ipmc-entries-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/ipv4-neighbors-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/ipv4-neighbors-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/ipv4-nexthops-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/ipv4-nexthops-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/ipv4-routes-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/ipv4-routes-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/ipv6-neighbors-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/ipv6-neighbors-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/ipv6-nexthops-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/ipv6-nexthops-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/ipv6-routes-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/ipv6-routes-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/nexthop-group-members-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/nexthop-group-members-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/nexthop-groups-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/nexthop-groups-used
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/snat-entries-available
/restconf/data/openconfig-system:system/oc-sys-ext:crm/oc-sys-ext:state/resources/snat-entries-used

```

# 4 Flow Diagrams

None

# 5 Serviceability and Debug

None

# 6 Warm Boot Support
None

# 7 Scalability
None

# 8 Unit Test
1. Verify all Global Commands via KLISH
2. Verify Config DB is updated
3. Verify Show Commands
4. Verify all REST URIs

