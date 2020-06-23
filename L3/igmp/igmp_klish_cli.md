# IGMP KLISH Support 
# High Level Design Document
#### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definitionabbreviation)
  * [1 Requirement Overview](#1-requirement-overview)
     * [1.1 Functional Requirements](#11-functional-requirements)
     * [1.2 Configuration and Management Requirements](#12-configuration-and-management-requirements)
     * [1.3 Scalability Requirements](#13-scalability-requirements)
     * [1.4 Warm Boot Requirements](#14-warm-boot-requirements)
  * [2 Functionality](#2-functionality)
     * [2.1 Target Deployment Use Cases](#21-target-deployment-use-cases)
     * [2.2 Functional Description](#22-functional-description)
  * [3 Design](#3-design)
     * [3.1 Overview](#31-overview)
     * [3.2 DB Changes](#32-db-changes)
     * [3.3 Switch State Service Design](#33-switch-state-service-design)
     * [3.4 SyncD](#34-syncd)
     * [3.5 SAI](#35-sai)
     * [3.6 CLI](#36-cli)
  * [4 Flow Diagrams](#4-flow-diagrams)
  * [6 Warm Boot Support](#6-warm-boot-support)
  * [7 Dynamic Port Breakout](#7-dynamic-port-breakout)
  * [8 Scalability](#8-scalability)
  * [9 Unit Test](#9-unit-test)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 05-JUN-2020 |  Sathishkumar S    | Initial version                   |

# About this Manual
This document provides OpenConfig Yang and KLISH Config CLI implementation for IGMP feature in SONiC

# Scope
This document describes the high level description of IGMP feature KLISH CLI support in SONiC.

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term**    | **Meaning**                             |
|-------------|-----------------------------------------|
| Bgpcfgd     | BGP docker configuration daemon         |   
| CLI         | Command Line Interface                  |
| FRR         | Free Range Routing software package     |
| IP          | Internet Protocol                       |
| VRF         | Virtual Routing and Forwarding          |
| vtysh       | FRR provided Command Line Interface     |
| IGMP        | Internet Group Management Protocol      |
| PIM         | Protocol Independent Multicast          |




# 1 Requirement Overview

## 1.1 Functional Requirements

  - Support Open Config based KLISH CLIs to customize IGMP


## 1.2 Configuration and Management Requirements

  - Support Open Config based KLISH CLIs to manage IGMP


## 1.3 Scalability Requirements

  - Not applicable



## 1.4 Warm Boot Requirements

  - Not applicable.


# 2 Functionality

## 2.1 Target Deployment Use Cases

In this release we add KLISH based configuration management support for IGMP commands. At present all IGMP related configuration management is done only through 'vtysh' command line interface.


## 2.2 Functional Description

IGMP is an IPv4 protocol that a host uses to request multicast data for a particular group. Using the information obtained through IGMP, the software maintains a list of multicast group or channel memberships on a per-interface basis. The systems that receive these IGMP packets send multicast data that they receive for requested groups or channels out the network segment of the known receivers.

KLISH command line support will be based on Open Config Yang modelling. Open config Yang Model is modified and extended to support all the configuration and operation data show operations. 

All IGMP configuration data will be saved in SONiC Config DB. Config DB configuration will be transformed into 'vtysh' commands and propogated to BGP docker IGMPcommands.

IGMP operational data for show commands and REST requests will be achieved through IGMP vtysh show commands executted via bgpcdfd.

Management Framework provide transformers callbacks will be used to convert REST requests to Config DB. Similarly transformers along with 'bgpcfgd' will be used to convert 'vtysh' given operational data to REST operational data.

# 3 Design
## 3.1 Overview

Below is a high level hierarchy of IGMP OC YANG extended model. IGMP YANG data will be under interface subtree.  This IGMP YANG model primarily contains config and the state data.

        |  +--rw oc-igmp-ext:igmp
        |     |  |  +--rw oc-igmp-ext:interface-id?                 oc-if:interface-id
        |     |  |  +--rw oc-igmp-ext:enabled?                      boolean
        |     |  |  +--rw oc-igmp-ext:version?                      oc-igmp-types:igmp-version
        |     |  |  +--rw oc-igmp-ext:query-interval?               oc-igmp-types:igmp-interval-type
        |     |  |  +--rw oc-igmp-ext:joins
        |     |  |  |  +--rw oc-igmp-ext:join* [mcastgrpaddr srcaddr]
        |     |  |  |     +--rw oc-igmp-ext:mcastgrpaddr    oc-inet:ipv4-address
        |     |  |  |     +--rw oc-igmp-ext:srcaddr         oc-inet:ipv4-address
        |     |  |  |     +--rw oc-igmp-ext:config
        |     |  |  |     |  +--rw oc-igmp-ext:enable?   boolean
        |     |  |  |     +--ro oc-igmp-ext:state
        |     |  |  |        +--ro oc-igmp-ext:enable?   boolean
        |     |  |  +--rw oc-igmp-ext:last-member-query-count?      uint16
        |     |  |  +--rw oc-igmp-ext:last-member-query-interval?   uint32
        |     |  |  +--rw oc-igmp-ext:query-max-response-time?      uint16

Below is a high level hierarchy of IGMP OC YANG extended model to display IGMP operational data. IGMP show YANG data will be under module openconfig-igmp.

        |      +--rw igmp
        |     |  +--rw oc-igmp-ext:statistics
        |     |  |  +--rw oc-igmp-ext:counters
        |     |  |  |  +--rw oc-igmp-ext:queries
        |     |  |  |  |  +--rw oc-igmp-ext:sent
        |     |  |  |  |  |  +--ro oc-igmp-ext:state
        |     |  |  |  |  |     +--ro oc-igmp-ext:v1?   uint32
        |     |  |  |  |  |     +--ro oc-igmp-ext:v2?   uint32
        |     |  |  |  |  |     +--ro oc-igmp-ext:v3?   uint32
        |     |  |  |  |  +--rw oc-igmp-ext:received
        |     |  |  |  |     +--ro oc-igmp-ext:state
        |     |  |  |  |        +--ro oc-igmp-ext:v1?   uint32
        |     |  |  |  |        +--ro oc-igmp-ext:v2?   uint32
        |     |  |  |  |        +--ro oc-igmp-ext:v3?   uint32
        |     |  |  |  +--rw oc-igmp-ext:reports
        |     |  |  |     +--ro oc-igmp-ext:state
        |     |  |  |        +--ro oc-igmp-ext:v1?   uint32
        |     |  |  |        +--ro oc-igmp-ext:v2?   uint32
        |     |  |  |        +--ro oc-igmp-ext:v3?   uint32
        |     |  |  +--rw oc-igmp-ext:mtrace-counters
        |     |  |     +--ro oc-igmp-ext:state
        |     |  |        +--ro oc-igmp-ext:mtrace_request?    uint32
        |     |  |        +--ro oc-igmp-ext:mtrace_response?   uint32
        |     |  |        +--ro oc-igmp-ext:unsupported?       uint32
        |     |  +--rw oc-igmp-ext:sources
        |     |  |  +--rw oc-igmp-ext:igmp-sources
        |     |  |     +--rw oc-igmp-ext:igmp-source* [interface-id src-addr mcastgrp-addr]
        |     |  |        +--rw oc-igmp-ext:interface-id     string
        |     |  |        +--rw oc-igmp-ext:mcastgrp-addr    oc-inet:ip-address
        |     |  |        +--rw oc-igmp-ext:src-addr         oc-inet:ip-address
        |     |  |        +--ro oc-igmp-ext:state
        |     |  |           +--ro oc-igmp-ext:ip-addr?             oc-inet:ipv4-address
        |     |  |           +--ro oc-igmp-ext:uptime?              oc-types:timeticks64
        |     |  |           +--ro oc-igmp-ext:source-forwarding?   string
        |     |  |           +--ro oc-igmp-ext:query-expires?       oc-types:timeticks64
        |     |  +--rw oc-igmp-ext:groups
        |     |  |  +--rw oc-igmp-ext:igmp-groups
        |     |  |     +--rw oc-igmp-ext:igmp-groups* [interface-id mcastgrp-addr]
        |     |  |        +--rw oc-igmp-ext:interface-id     string
        |     |  |        +--rw oc-igmp-ext:mcastgrp-addr    oc-inet:ipv4-address
        |     |  |        +--ro oc-igmp-ext:state
        |     |  |           +--ro oc-igmp-ext:ip-addr?         oc-inet:ipv4-address
        |     |  |           +--ro oc-igmp-ext:uptime?          oc-types:timeticks64
        |     |  |           +--ro oc-igmp-ext:version?         oc-igmp-types:igmp-version
        |     |  |           +--ro oc-igmp-ext:sources-count?   uint16
        |     |  |           +--ro oc-igmp-ext:mode?            string
        |     |  |           +--ro oc-igmp-ext:query-expires?   oc-types:timeticks64
        |     |  +--rw oc-igmp-ext:interfaces
        |     |     +--rw oc-igmp-ext:interface* [interface-id]
        |     |        +--rw oc-igmp-ext:interface-id    -> ../config/interface-id
        |     |        +--rw oc-igmp-ext:config
        |     |        |  +--rw oc-igmp-ext:interface-id?      oc-if:interface-id
        |     |        |  +--rw oc-igmp-ext:enabled?           boolean
        |     |        |  +--rw oc-igmp-ext:version?           oc-igmp-types:igmp-version
        |     |        |  +--rw oc-igmp-ext:query-interval?    oc-igmp-types:igmp-interval-type
        |     |        |  +--rw oc-igmp-ext:filter-prefixes?   string
        |     |        +--ro oc-igmp-ext:state
        |     |           +--ro oc-igmp-ext:query-expires?     oc-types:timeticks64
        |     |           +--ro oc-igmp-ext:interface-id?      oc-if:interface-id
        |     |           +--ro oc-igmp-ext:enabled?           boolean
        |     |           +--ro oc-igmp-ext:version?           oc-igmp-types:igmp-version
        |     |           +--ro oc-igmp-ext:query-interval?    oc-igmp-types:igmp-interval-type
        |     |           +--ro oc-igmp-ext:filter-prefixes?   string
        |     |           +--ro oc-igmp-ext:flags
        |     |           |  +--ro oc-igmp-ext:all-multicast?    string
        |     |           |  +--ro oc-igmp-ext:broadcast?        string
        |     |           |  +--ro oc-igmp-ext:deleted?          string
        |     |           |  +--ro oc-igmp-ext:index?            uint32
        |     |           |  +--ro oc-igmp-ext:multicast?        string
        |     |           |  +--ro oc-igmp-ext:multicast-loop?   string
        |     |           |  +--ro oc-igmp-ext:promiscous?       string
        |     |           +--ro oc-igmp-ext:querier
        |     |           |  +--ro oc-igmp-ext:querier-type?          string
        |     |           |  +--ro oc-igmp-ext:query-startup-count?   uint32
        |     |           |  +--ro oc-igmp-ext:query-timer?           string
        |     |           |  +--ro oc-igmp-ext:query-general-timer?   string
        |     |           +--ro oc-igmp-ext:timers
        |     |           |  +--ro oc-igmp-ext:group-membership-interval?     uint16
        |     |           |  +--ro oc-igmp-ext:last-member-query-count?       uint16
        |     |           |  +--ro oc-igmp-ext:last-member-query-time?        uint32
        |     |           |  +--ro oc-igmp-ext:older-host-present-interval?   uint16
        |     |           |  +--ro oc-igmp-ext:querier-present-interval?      uint16
        |     |           |  +--ro oc-igmp-ext:query-interval?                uint16
        |     |           |  +--ro oc-igmp-ext:query-response-interval?       uint16
        |     |           |  +--ro oc-igmp-ext:robustness-variable?           uint8
        |     |           |  +--ro oc-igmp-ext:startup-query-interval?        uint16
        |     |           +--ro oc-igmp-ext:status?            string
        |     |           +--ro oc-igmp-ext:ip-addr?           oc-inet:ip-address




With above OCYang model support, user can use the REST URL APIs corresponding to the above OCYang model and retrieve IGMP related configuration and operational/state data.

KLISH CLIs will be provided for IGMP configuration and show commands. Please refer to CLI section for more details.

SONiC Yang definitions will be written to support IGMP configuration data.  OC Yang model of IGMP will be mapped to corresponding SONiC Yang data model. 


## 3.2 DB Changes

SONiC Yang definitions for IGMP will be newly defined.  This Yang definition represents IGMP configuration data in SONiC Config DB. IGMP configurations will be stored in config_db.json as per IGMP SONiC YANG defined.

### 3.2.1 CONFIG DB

IGMP Configuration data will be as per SONiC YANG model.

### IGMP_INTERFACE

```
Producer: config manager

Consumer: bgpcfgd

Description: This is a singleton configuration which holds the IGMP multicast group details.

Schema:

;New table
;holds the interface name, multicast group and source address

key = IGMP_INTERFACE|Interface name|Multicast group address|Source address
                        
; field = value
          ; enable = true
```

### IGMP_INTERFACE_QUERY

```
Producer: config manager

Consumer: bgpcfgd

Description: This is a singleton configuration which holds the IGMP query related fields.

Schema:

;New table
;holds the interface name as key

key = IGMP_INTERFACE_QUERY|Interface name
                        
;field = value
enabled = false
version                        = 3                ; IGMP version.
last-member-query-count        = 7                ; IGMP last member query count
last-member-query-interval     = 24               ; IGMP last member query interval
query-interval                 = 12               ; IGMP host query interval
query-max-response-time        = 25               ; IGMP max query response value (seconds)
```
### 3.2.2 APP DB

IGMP will be under FRR BGP docker. Hence there will not be any application data of IGMP in SONiC APP DB.


### 3.2.3 STATE DB
Not applicable

### 3.2.4 ASIC DB
Not applicable

### 3.2.5 COUNTER DB
Not applicable

### 3.2.6 Configuration save and restart

All IGMP configuration done through KLISH CLI or REST API will be saved as running configuration in SONiC config_db.json running config file.  

Upon saving SONiC running config, We will write all the IGMP running configuration into config_db.json file.

Over an System restart or BGP docker restart, all saved configurations will be replayed from config_db.json


## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
Not applicable

### 3.3.2 Other Process
Not applicable

## 3.4 SyncD
Not applicable

## 3.5 SAI
Not applicable


## 3.6 CLI

IGMP commands will be supported in KLISH command line interface. SONiC Supported FRR IGMP commands will be available in KLISH CLI modes with  minor modifications as described in upcoming sections.

For more IGMP command details please refer to FRR package reference manual at http://docs.frrouting.org/en/latest . 

All no form of CLI commands will not accept data value in KLISH unless it is required for extended subcommands. Hence all no form of commands will assume that configured value at that moment will be removed from configuration.

Configuring a parameter, with an already configured value present, will overwrite the existing value of the parameter without warning.

Please note that all KLISH CLI command 'no' forms (unconfig) will not be accepting command parameter values unless those values are used to identify the configuration record uniquely. For example vtysh command 'no ip igmp query-max-response-time 10' will be 'no ip igmp query-max-response-time' in KLISH.

### 3.6.1 Data Models

Please refer section 3.2

### 3.6.2 Configuration Commands


Sub sections below describe KLISH supported CLI commands for IGMP. Command syntax uses below formatting. 
        
	< > : Mandatory
	[ ] : Optional parameters
	( ) : Value in the range
	{ } : one or more parameters
	A.B.C.D : IPv4 dotted format 
	A.B.C.D/M : IPv4 prefix	with mask lenghth
	IFNAME : Interface Name
	KEY : Authentication password string
	NAME : Name (for example route map, prefixlist)


#### 3.6.2.1 IGMP configuration

	        [no] ip igmp version (2-3)
	        [no] ip igmp join [multicast-group-address] [source-address]
	        [no] ip igmp last-member-query-count (1-7)
	        [no] ip igmp last-member-query-interval (1-255)
	        [no] ip igmp query-interval (1-1800)
	        [no] ip igmp query-max-response-time (10-250)


### 3.6.3 Show Commands

Below list of KLISH IGMP show commands will be supported

        	show ip igmp groups
                
                Interface        Address         Group              Mode Timer    Srcs    V    Uptime  
                Vlan301          33.33.33.1      232.1.1.1       ---- 00:00:03    1         2    00:00:02

        	show ip igmp interface [IFNAME]
                               
                Interface : Vlan1301
                State     : up
                Address   : 33.33.33.1
                Uptime    : 00:00:49
                Version   : 2

                Querier
                -------
                Querier     : local
                Start Count : 0
                Query Timer : 00:00:00
                Other Timer : --:--:--

                Timers
                ------
                Group Membership Interval      : 5s
                Last Member Query Count        : 2
                Last Member Query Time         : 2s
                Older Host Present Interval    : 5s
                Other Querier Present Interval : 4s
                Query Interval                 : 2s
                Query Response Interval        : 1s
                Robustness Variable            : 2
                Startup Query Interval         : 1s

                Flags
                -----
                All Multicast   : no
                Broadcast       : yes
                Deleted         : no
                Interface Index : 190
                Multicast       : yes
                Multicast Loop  : 0
                Promiscuous     : no


        	show ip igmp sources
                
                Interface        Address         Group           Source          Timer Fwd Uptime  
                Vlan301          33.33.33.1      232.1.1.1       *               00:02   Y 00:00:03

        	show ip igmp statistics
                
                IGMP RX statistics
                Interface       : global
                V1 query        : 0
                V2 query        : 0
                V3 query        : 36
                V2 leave        : 0
                V1 report       : 0
                V2 report       : 0
                V3 report       : 74
                mtrace response : 0
                mtrace request  : 0
                unsupported     : 0

        	show ip igmp [vrf <vrf-name>] statistics
                
                IGMP RX statistics
                Interface       : global
                V1 query        : 0
                V2 query        : 0
                V3 query        : 20
                V2 leave        : 0
                V1 report       : 0
                V2 report       : 0
                V3 report       : 167
                mtrace response : 0
                mtrace request  : 0
                unsupported     : 0
                
        	show ip igmp [vrf <vrf-name>] sources
                
                Interface        Address         Group           Source          Timer Fwd Uptime  
                Vlan1301         33.33.33.1      232.1.1.1       90.0.0.2        04:15   Y 00:00:05
                Vlan1301         33.33.33.1      232.1.1.1       91.0.0.2        04:15   Y 00:00:05

        	show ip igmp vrf all groups
                
                VRF: Vrf_RED
                Interface        Address         Group           Mode Timer    Srcs V Uptime  
                Vlan1301         33.33.33.1      232.1.1.1       INCL --:--:--    1 3 00:03:10
                Vlan1301         33.33.33.1      232.1.1.2       INCL --:--:--    0 3 00:03:10 
                Vlan1301         33.33.33.1      225.1.1.1       INCL --:--:--    1 3 00:00:01

                VRF: default
                Interface        Address         Group           Mode Timer    Srcs V Uptime  
                Vlan301          33.33.33.1      232.1.1.1       INCL --:--:--    1 3 00:03:42
                Vlan301          33.33.33.1      232.1.1.2       INCL --:--:--    0 3 00:03:42
                Vlan301          33.33.33.1      225.1.1.1       INCL --:--:--    1 3 00:00:20

        	show ip igmp vrf all sources
                
                VRF: Vrf_RED
                Interface        Address         Group           Source          Timer Fwd Uptime  
                Vlan1301         33.33.33.1      232.1.1.1       *               00:03   Y 00:00:04

                VRF: default
                Interface        Address         Group           Source          Timer Fwd Uptime  
                Vlan301          33.33.33.1      232.1.1.1       *               00:04   Y 00:00:05

        	show ip igmp [vrf <vrf-name>] interface [IFNAME]
                
                Interface : Vlan1301
                State     : up
                Address   : 33.33.33.1
                Uptime    : 00:00:49
                Version   : 2

                Querier
                -------
                Querier     : local
                Start Count : 0
                Query Timer : 00:00:00
                Other Timer : --:--:--

                Timers
                ------
                Group Membership Interval      : 5s
                Last Member Query Count        : 2
                Last Member Query Time         : 2s
                Older Host Present Interval    : 5s
                Other Querier Present Interval : 4s
                Query Interval                 : 2s
                Query Response Interval        : 1s
                Robustness Variable            : 2
                Startup Query Interval         : 1s

                Flags
                -----
                All Multicast   : no
                Broadcast       : yes
                Deleted         : no
                Interface Index : 190
                Multicast       : yes
                Multicast Loop  : 0
                Promiscuous     : no              
      
Below list of KLISH IGMP clear commands will be supported


For more details of above list of show commands, please refer to FRR User Manual.



### 3.6.4 Debug Commands

Not applicable 


### 3.6.5 REST API Support


#### 3.6.2.1 IGMP Multicast group join configuration

REST URI for IGMP configuration command will be below

	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface={ifname}/subinterfaces/subinterface={idx}/openconfig-if-ip:ipv4/openconfig-interfaces-ext:igmps/igmp/config" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-interfaces-ext:config\":{\"enable\":true, "mcastgrpaddr":<addr>, "srcaddr":<addr>\"
			
	DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface={ifname}/subinterfaces/subinterface={idx}/openconfig-if-ip:ipv4/openconfig-interfaces-ext:igmps/igmp" -H "accept: */*" -H "Content-Type: application/yang-data+json"


#### 3.6.5.2 IGMP version configuration

	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface={ifname}/subinterfaces/subinterface={idx}/openconfig-if-ip:ipv4/openconfig-interfaces-ext:igmps/igmp/config" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-interfaces-ext:config\":{\"enable\":true, "version":<number>\"
			
	DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface={ifname}/subinterfaces/subinterface={idx}/openconfig-if-ip:ipv4/openconfig-interfaces-ext:igmps/igmp" -H "accept: */*" -H "Content-Type: application/yang-data+json"


#### 3.6.5.5 IGMP last member query count

	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface={ifname}/subinterfaces/subinterface={idx}/openconfig-if-ip:ipv4/openconfig-interfaces-ext:igmps/igmp/config" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-interfaces-ext:config\":{\"enable\":true, "lmquerycount":<count>\"
			
	DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface={ifname}/subinterfaces/subinterface={idx}/openconfig-if-ip:ipv4/openconfig-interfaces-ext:igmps/igmp" -H "accept: */*" -H "Content-Type: application/yang-data+json"

#### 3.6.5.6 IGMP last member query interval

	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface={ifname}/subinterfaces/subinterface={idx}/openconfig-if-ip:ipv4/openconfig-interfaces-ext:igmps/igmp/config" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-interfaces-ext:config\":{\"enable\":true, "lmqueryinterval":<interval>\"
			
	DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface={ifname}/subinterfaces/subinterface={idx}/openconfig-if-ip:ipv4/openconfig-interfaces-ext:igmps/igmp" -H "accept: */*" -H "Content-Type: application/yang-data+json"

#### 3.6.5.7 IGMP query interval

	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface={ifname}/subinterfaces/subinterface={idx}/openconfig-if-ip:ipv4/openconfig-interfaces-ext:igmps/igmp/config" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-interfaces-ext:config\":{\"enable\":true, "queryinterval":<interval>\"
			
	DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface={ifname}/subinterfaces/subinterface={idx}/openconfig-if-ip:ipv4/openconfig-interfaces-ext:igmps/igmp" -H "accept: */*" -H "Content-Type: application/yang-data+json"

#### 3.6.5.8 IGMP query max response time

	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface={ifname}/subinterfaces/subinterface={idx}/openconfig-if-ip:ipv4/openconfig-interfaces-ext:igmps/igmp/config" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-interfaces-ext:config\":{\"enable\":true, "querymaxrestime":<restime>\"
			
	DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface={ifname}/subinterfaces/subinterface={idx}/openconfig-if-ip:ipv4/openconfig-interfaces-ext:igmps/igmp" -H "accept: */*" -H "Content-Type: application/yang-data+json"

#### 3.6.5.9 show ip igmp groups
GET /restconf/data/openconfig-network-instance:network-instances/network-instance={name}/protocols/protocol={identifier},{name1}/igmp/openconfig-igmp-ext:igmp-groups  name= default-vrf, name1= igmp 

#### 3.6.5.10 show ip igmp sources
GET /restconf/data/openconfig-network-instance:network-instances/network-instance={name}/protocols/protocol={identifier},{name1}/igmp/openconfig-igmp-ext:igmp-sources  name= default-vrf, name1= igmp

#### 3.6.5.11 show ip igmp statistics
GET /restconf/data/openconfig-network-instance:network-instances/network-instance={name}/protocols/protocol={identifier},{name1}/igmp/openconfig-igmp-ext:statistics.  name = default-vrf, name1 =igmp

#### 3.6.5.12 show ip igmp interface <>
GET /restconf/data/openconfig-network-instance:network-instances/network-instance={name}/protocols/protocol={identifier},{name1}/igmp/openconfig-igmp-ext:interfaces/interface={interface-id}  name= default-vrf, name1= igmp,interface-id= <interfacename> 

#### 3.6.5.13 show ip igmp vrf all groups
GET /restconf/data/openconfig-network-instance:network-instances/network-instance={name}/protocols/protocol={identifier},{name1}/igmp/openconfig-igmp-ext:igmp-groups  name= all, name1= igmp 

#### 3.6.5.14 show ip igmp vrf all sources
GET /restconf/data/openconfig-network-instance:network-instances/network-instance={name}/protocols/protocol={identifier},{name1}/igmp/openconfig-igmp-ext:igmp-sources  name= all, name1= igmp

#### 3.6.5.15 show ip igmp vrf vrf_RED statistics
GET /restconf/data/openconfig-network-instance:network-instances/network-instance={name}/protocols/protocol={identifier},{name1}/igmp/openconfig-igmp-ext:statistics  name = vrf_RED , name1 = igmp

#### 3.6.5.16 show ip igmp vrf vrf_RED interface <>
GET /restconf/data/openconfig-network-instance:network-instances/network-instance={name}/protocols/protocol={identifier},{name1}/igmp/openconfig-igmp-ext:interfaces/interface={interface-id}  name= vrf_RED, name1= igmp,interface-id= <interfacename> 

#### 3.6.5.17 show ip igmp vrf vrf_RED sources
GET /restconf/data/openconfig-network-instance:network-instances/network-instance={name}/protocols/protocol={identifier},{name1}/igmp/openconfig-igmp-ext:igmp-sources  name= vrf_RED, name1= igmp


### 3.6.6 Operational Commands

Below operational command will be supported from KLISH. 

        clear ip igmp interfaces
        clear ip igmp [vrf <vrf-name>] interfaces


### 3.6.7 Limitations

# 4 Flow Diagrams
Not applicable

# 5 Serviceability and Debug
Not applicable. 


# 6 Warm Boot Support

Upon warm reboot, saved IGMP configurations in config_db.json shall be replayed.


# 7 Dynamic Port Breakout

For IGMP config commands, the interface name is derived from the interface heirarchy of the OCYang model, no specific changes required for
breakout ports. 

# 8 Scalability

Not applicable.

# 9 Unit Test
Unit test cases for this specification are as listed below:

1. Verify configuration add and del "ip igmp version" 
2. Verify configuration add and del "ip igmp join" command
3. Verify configuration add and del "ip igmp last-member-query-count" command
4. Verify configuration add and del "ip igmp last-member-query-interval" command
5. Verify configuration add and del "ip igmp query-interval" command
6. Verify configuration add and del "ip igmp query-max-response-time" command
7. Config reload with IGMP configration enabled
8. System relaod with IGMP configuration enabled
9. Port shut/no shut with IGMP config
10. IGMP Multicast group leave/join
11. IGMP show commands
12. Vlan member add/del with IGMP config
13. Vlan add/del with IGMP config
14. Verify IGMP config on a breakout port
15. Make sure proper error message displayed for invalid IGMP version config
16. Make sure last-member-query-count is given in supported range
17. Make sure last-member-query-interval is configured in supported range and throw appro error for invalid config
18. Make sure appro error message displayed for invalid query-max-response-time config
19. Validate the rest path for join and IGMP query field commands
