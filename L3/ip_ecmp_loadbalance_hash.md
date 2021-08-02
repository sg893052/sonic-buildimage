# IP ECMP Load balance hash configuration support 
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
| 0.1 | 14-OCT-2020 |  Sathishkumar S    | Initial version                   |
|     |             |  Suman Kumar       |                                   |
|     |             |  Utpal Kant        |                                   |

# About this Manual
This document provides OpenConfig Yang and KLISH Config CLI support for IP ECMP loadbalance attributes in SONiC

# Scope
This document describes the high level description of IP ECMP loadbalance attributes configuration in SONiC.

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term**    | **Meaning**                             |
|-------------|-----------------------------------------|
| CLI         | Command Line Interface                  |
| IP          | Internet Protocol                       |
| ECMP        | Equal-cost multi-path routing           |


# 1 Requirement Overview

## 1.1 Functional Requirements

  - Solve ECMP polarization issue: Traffic which was load balanced by previous node cannot load-balance further if the same load-balance parameters are used in both the devices. In Multi-tier topology, ECMP hash polarization is common, SONiC address this by changing hash seed value based on switch role. This HLD aims at providing CLI configurations for Hash seed and also to customize ip v4/v6 packet header fields that can be used for load-balancing.


## 1.2 Configuration and Management Requirements

  - Support Open Config based KLISH CLIs to manage IP ECMP loadbalance parameters


## 1.3 Scalability Requirements

  - Not applicable

## 1.4 Warm Boot Requirements

  - Not applicable.


# 2 Functionality

## 2.1 Target Deployment Use Cases



## 2.2 Functional Description

Equal Cost Multi-Path (ECMP) is used in Layer 3 routing to forward traffic to a destination using mutiple paths. Increasing the number of path, increases the avaliable bandwidth to reach the destination. However all available paths has to be equally load-balanced such that no one path is over/under utilized. It is also required to maintain same path for a given traffic stream (same src/dst ip / l4-src/dsp port) to avoid any packet ordering issue. Most of all ASIC acheives this by hashing packet header fields/data in the received packet.

This HLD allows user to configure different hashing parameter 
- Configure packet header fields used for hashing
- Configure hash seed 

# 3 Design
## 3.1 Overview

Below is a high level hierarchy of IP ECMP load balance hash config OC YANG extended model.

module: sonic-loadbalance-mode
  +--rw sonic-loadbalance-mode
     +--rw ECMP_LOADBALANCE_TABLE_SEED
     |  +--rw ECMP_LOADBALANCE_TABLE_SEED_LIST* [hash]
     |     +--rw hash                enumeration
     |     +--rw ecmp_hash_seed?     int32
     +--rw ECMP_LOADBALANCE_TABLE_IPV4
     |  +--rw ECMP_LOADBALANCE_TABLE_IPV4_LIST* [ipv4]
     |     +--rw ipv4                enumeration
     |     +--rw ipv4-l4-src-port?      boolean
     |     +--rw ipv4-l4-dst-port?      boolean
     |     +--rw ipv4-src-ip?        boolean
     |     +--rw ipv4-dst-ip?        boolean
     |     +--rw ipv4-protocol?      boolean
     +--rw ECMP_LOADBALANCE_TABLE_IPV6
        +--rw ECMP_LOADBALANCE_TABLE_IPV6_LIST* [ipv6]
           +--rw ipv6                enumeration
           +--rw ipv6-l4-src-port?      boolean
           +--rw ipv6-l4-dst-port?      boolean
           +--rw ipv6-src-ip?        boolean
           +--rw ipv6-dst-ip?        boolean
           +--rw ipv6-next-hdr?      boolean

With above OCYang model support, user can use the REST URL APIs corresponding to the above OCYang model and retrieve IP ECMP load balance attributes related configuration and operational/state data.

KLISH CLIs will be provided for IP ECMP loadbalance attributes configuration and show commands. Please refer to CLI section for more details.

## 3.2 DB Changes

### 3.2.1 CONFIG DB

IP ECMP load balance attributes configuration data will be as per SONiC YANG model.

127.0.0.1:6379[4]> hgetall "ECMP_LOADBALANCE_TABLE_IPV4_LIST|IPV4"
1)  "ipv4-l4-src-port"
2)  "true"
3)  "ipv4-l4-dst-port"
4)  "true"
5)  "ipv4-src-ip"
6)  "true"
7)  "ipv4-dst-ip"
8)  "true"
9)  "ipv4-protocol"
10) "true"

127.0.0.1:6379[4]> hgetall "ECMP_LOADBALANCE_TABLE_IPV6_LIST|IPV6"
1)  "ipv6-l4-src-port"
2)  "true"
3)  "ipv6-l4-dst-port"
4)  "true"
5)  "ipv6-src-ip"
6)  "true"
7)  "ipv6-dst-ip"
8)  "true"
9)  "ipv6-next-hdr"
10) "true"

127.0.0.1:6379[4]> hgetall "ECMP_LOADBALANCE_TABLE_SEED_LIST|HASH"
1)  "ecmp-hash-seed"
2)  "100"

### 3.2.2 APP DB

Existing Switch Table has been enhanced to include two new fields.

127.0.0.1:63792> HGETALL "SWITCH_TABLE:switch"
1) "ecmp_hash_fields_ipv4"
2) "ipv4-src-ip,ipv4-dst-ip,ipv4-protocol,ipv4-l4-src-port,ipv4-l4-dst-port"
3) "ecmp_hash_fields_ipv6"
4) "ipv6-src-ip,ipv6-dst-ip,ipv6-next-hdr,ipv6-l4-src-port,ipv6-l4-dst-port"
5) "ecmp_hash_seed"
6) "100"
127.0.0.1:63792>

### 3.2.3 STATE DB
No New table / chagnes in STATE DB.

### 3.2.4 ASIC DB
No New table / chagnes in ASIC DB.

### 3.2.5 COUNTER DB
No New table / chagnes in COUNTER DB.

### 3.2.6 Configuration save and restart



## 3.3 Switch State Service Design
### 3.3.1 Switchmgrd

A new manager module - "switchmgr" has been introduced. It subscribes to the new IPv4/v6 ECMP load-balance tables in the CONFIG DB. It processes CONFIG DB messages and updates the APP DB switch-table with all the valid packet fields for ecmp.

### 3.3.2 Orchestration Agent

"Switchorch" component in OA subscribes to the switch table in APP DB and processes the two new fields in switch table. The list of packet-fields for ecmp load-balancing is maintained as an attribute of hash objects - one each for IPv4 and IPv6. On receiving ecmp load-balance packet-fields update, old hash object, if any, is deleted and the existing packet-fields are cleared in asic. Then a new hash object with supplied packet-fields is created and asic is programmed with the same.

## 3.4 SyncD

## 3.5 SAI
No new attributes have been introduced. Following SAI attributes are being used:
SAI_HASH_ATTR_NATIVE_HASH_FIELD_LIST
SAI_SWITCH_ATTR_ECMP_HASH_IPV4
SAI_SWITCH_ATTR_ECMP_HASH_IPV6
SAI_SWITCH_ATTR_ECMP_DEFAULT_HASH_SEED

## 3.6 CLI

### 3.6.1 Data Models

Please refer section 3.2

### 3.6.2 Configuration Commands


#### 3.6.2.1 Load balance configuration
 
            [no] ip load-share hash ipv4 [ipv4-src-ip] [ipv4-dst-ip] [ipv4-ip-proto] [ipv4-l4-src-port] [ipv4-l4-dst-port]
	    [no] ip load-share hash ipv6 [ipv6-src-ip] [ipv6-dst-ip] [ipv6-next-hdr] [ipv6-l4-src-port] [ipv6-l4-dst-port]
	    [no] ip load-share hash seed <seed-val>


### 3.6.3 Show Commands

            show ip load-balance

            Packet Header Fields:
            ip  : ipv4-src-ip ipv4-dst-ip ipv4-ip-proto ipv4-l4-src-port ipv4-l4-dst-port
            ipv6: ipv6-src-ip ipv6-dst-ip ipv6-next-hdr ipv6-l4-src-port ipv6-l4-dst-port

            Hash seed: 10

### 3.6.4 Debug Commands

Not applicable 


### 3.6.5 REST API Support


REST URI for IP ECMP load balance hash configuration

PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv4-attrs/openconfig-loadbalance-mode-ext:ipv4-attr/config/ipv4-src-ip" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadbalance-mode-ext:ipv4-src-ip\":true}"

DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv4-attrs/openconfig-loadbalance-mode-ext:ipv4-attr/config/ipv4-src-ip" -H "accept: */*"

PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv4-attrs/openconfig-loadbalance-mode-ext:ipv4-attr/config/ipv4-dst-ip" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadbalance-mode-ext:ipv4-dst-ip\":true}"

DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv4-attrs/openconfig-loadbalance-mode-ext:ipv4-attr/config/ipv4-dst-ip" -H "accept: */*"

PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv4-attrs/ipv4-attr/config/ipv4-ip-proto" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadbalance-mode-ext:ipv4-ip-proto\":true}"

DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv4-attrs/openconfig-loadbalance-mode-ext:ipv4-attr/config/ipv4-ip-proto" -H "accept: */*"

PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv4/config/l4-src-port" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadbalance-mode-ext:l4-src-port\":true}"

DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv4/config/l4-src-port" -H "accept: */*"

PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv4/config/l4-dst-port" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadbalance-mode-ext:l4-dst-port\":true}"

DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv4/config/l4-dst-port" -H "accept: */*"

PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv6/config/ipv6-src-ip" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadbalance-mode-ext:ipv6-src-ip\":true}"

DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv6/config/ipv6-src-ip" -H "accept: */*"

PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv6/config/ipv6-dst-ip" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadbalance-mode-ext:ipv6-dst-ip\":true}"

DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv6/config/ipv6-dst-ip" -H "accept: */*"

PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv6/config/ipv6-next-hdr" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadbalance-mode-ext:ipv6-next-hdr\":true}"

DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv6/config/ipv6-next-hdr" -H "accept: */*"

PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv6/config/ipv6-l4-src-port" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadbalance-mode-ext:l4-src-port\":true}"

DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv6/config/ipv6-l4-src-port" -H "accept: */*"

PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv6/config/ipv6-l4-dst-port" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadbalance-mode-ext:ipv6-l4-dst-port\":true}"

DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv6/config/ipv6-l4-dst-port" -H "accept: */*"


REST URI for IP ECMP load balance hash get 

GET "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:seed" -H "accept: application/yang-data+json"

GET "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv4-attrs" -H "accept: application/yang-data+json"

GET "<REST-SERVER:PORT>/restconf/data/openconfig-loadbalance-mode-ext:ipv6" -H "accept: application/yang-data+json" 

### 3.6.6 Operational Commands



### 3.6.7 Limitations

# 4 Flow Diagrams
Not applicable

# 5 Serviceability and Debug
Not applicable. 


# 6 Warm Boot Support


# 7 Dynamic Port Breakout
Not applicable.

# 8 Scalability
Not applicable.

# 9 Unit Test

Unit test cases for this specification are as listed below:

1) Verify ECMP load balance for default config [all ipv4/v6 fields selected]
2) Verify ECMP load balance for user configured ipv4/v6 fields 
   - select only src-ip and dst-ip, send traffic by changing either of them and verify traffic load balances
   - select only src-ip and dst-ip, Dont change src/dst ip in traffic but change l4-src/dst-port and verify traffic is not load balanced
3) Verify ECMP polarization on L3 underday traffic (ipv4/v6) [server-->leaf1-->spine1/2-->leaf3/4-->server]
   - configure different hash seed on different tiers in a clos topology and confirm that traffic is load balances in all tires 
   - select different pkt-fields on different tiers in a clos topology and confirm that traffic is load balances in all tires 
4) Verify ECMP polarization on L3 Vxlan traffic (ipv4/6)[server-->Leaf1-->spine1/2-->leaf3/4-->server], Vxlan tunnel between leaf1 to leaf3/4.
   - configure different hash seed on different tiers in a clos topology and confirm that traffic is load balances in all tires 
   - select different pkt-fields on different tiers in a clos topology and confirm that traffic is load balances in all tires 
5) Verify ECMP load balance config is retained after warm-boot
6) Verify ECMP load balance config after config-save and reload / cold-boot
