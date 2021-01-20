# IP ECMP Load-Share Hash Configuration

# High Level Design Document
###### Rev 0.2

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
| Rev |    Date    |       Author(s)    | Description                |
|:---:|:----------:|:-------------------|:---------------------------|
| 0.1 | 10/14/2020 | Sathishkumar S, Suman Kumar, Utpal Kant Pintoo | Initial version |
| 0.2 | 01/08/2021 | Utpal Kant Pintoo  | Symmetric Hash |

# About this Manual
This document provides OpenConfig Yang and KLISH Config CLI support for IP ECMP load-share parameters in SONiC.

# Scope
This document describes the high level description of IP ECMP load-share hash configurations in SONiC.

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term**    | **Meaning**                             |
|-------------|-----------------------------------------|
| CLI         | Command Line Interface                  |
| IP          | Internet Protocol                       |
| ECMP        | Equal Cost Multi-Path                   |

# 1 Requirement Overview

## 1.1 Functional Requirements

1. User should be able to modify the following parameters for IP ECMP load-share hashing
  - Hash Seed
  - IPv4 packet-header fields
  - IPv6 packet-header fields
2. User should be able to enable/disable symmetric hashing for IP ECMP
3. User should be able to fetch current configured parameters for IP ECMP load-share hashing

## 1.2 Configuration and Management Requirements

  - Open Config based KLISH CLIs to modify/fetch IP ECMP load-share hashing parameters
  - REST URL APIs support to modify/fetch IP ECMP load-share hashing parameters

## 1.3 Scalability Requirements

  - Not applicable

## 1.4 Warm Boot Requirements

  - This configuration should persist across warmboot

# 2 Functionality

## 2.1 Target Deployment Use Cases

 ECMP Polarization: Traffic, which gets load-shared on a device, does not get load-shared further if identical load-share parameters are used in successive devices. In Multi-tier topology, ECMP hash polarization is common. SONiC addresses this by changing hash "seed" based on switch role.

 TCP Optimizer: This is a common service as part of service-chaining. The expectation here is that a bidirectional flow always go through the same TCP optimizer. To ensure this, symmetric hashing can be used so that same nexthop is chosen for bidirectional flows.

## 2.2 Functional Description

Equal Cost Multi-Path (ECMP) is used in Layer-3 routing to forward traffic to a destination using multiple available paths. Increasing the number of paths, increases the available bandwidth to reach the destination. However, all available paths should be closely load-shared so that no one path is over/under-utilized. It is also required to maintain same path for a given traffic "flow" to avoid any packet re-ordering issue. ASICs achieve this by using an hash algorithm to generate a hash value from packet header and/or static hash configurations. This, generated hash value, is then used to load-share flows across different available paths.

Symmetric hashing allows bidirectional flows with a given Src-IP and Dst-IP and/or given Src-Port and Dst-Port to follow the same path even when they are interchanged. This can be controlled, separately, for IPv4 and IPv6 flows on the device.

This feature allows user to modify the IP ECMP load-share hashing parameters to affect traffic load-sharing across multiple available paths. All the configurations are per-device.

It is important to note here that this feature is not intended to affect port-channel load distribution behaviour.

# 3 Design
## 3.1 Overview

Below is a high-level hierarchy of IP ECMP load-share hash config OC YANG extended model.

    module: openconfig-loadshare-mode-ext
      +--rw loadshare
         +--rw config
         +--ro state
         +--rw seed-attrs
         |  +--rw config
         |  |  +--rw hash?             string
         |  |  +--rw ecmp-hash-seed?   uint32
         |  +--ro state
         |     +--ro hash?             string
         |     +--ro ecmp-hash-seed?   uint32
         +--rw ipv4-attrs
         |  +--rw config
         |  |  +--rw ipv4?               string
         |  |  +--rw ipv4-l4-src-port?   boolean
         |  |  +--rw ipv4-l4-dst-port?   boolean
         |  |  +--rw ipv4-src-ip?        boolean
         |  |  +--rw ipv4-dst-ip?        boolean
         |  |  +--rw ipv4-ip-proto?      boolean
         |  |  +--rw ipv4-symmetric?     boolean
         |  +--ro state
         |     +--ro ipv4?               string
         |     +--ro ipv4-l4-src-port?   boolean
         |     +--ro ipv4-l4-dst-port?   boolean
         |     +--ro ipv4-src-ip?        boolean
         |     +--ro ipv4-dst-ip?        boolean
         |     +--ro ipv4-ip-proto?      boolean
         |     +--ro ipv4-symmetric?     boolean
         +--rw ipv6-attrs
            +--rw config
            |  +--rw ipv6?               string
            |  +--rw ipv6-l4-src-port?   boolean
            |  +--rw ipv6-l4-dst-port?   boolean
            |  +--rw ipv6-src-ip?        boolean
            |  +--rw ipv6-dst-ip?        boolean
            |  +--rw ipv6-next-hdr?      boolean
            |  +--rw ipv6-symmetric?     boolean
            +--ro state
               +--ro ipv6?               string
               +--ro ipv6-l4-src-port?   boolean
               +--ro ipv6-l4-dst-port?   boolean
               +--ro ipv6-src-ip?        boolean
               +--ro ipv6-dst-ip?        boolean
               +--ro ipv6-next-hdr?      boolean
               +--ro ipv6-symmetric?     boolean

With above OCYang model support, user can use the REST URL APIs corresponding to the above OCYang model and retrieve IP ECMP load-share hash related configuration and operational/state data.

KLISH CLIs will be provided for IP ECMP load-share hash config and show commands. Please refer to CLI section for more details.

## 3.2 DB Changes

### 3.2.1 CONFIG DB

IP ECMP load-share configuration data will be as per SONiC YANG model.

    127.0.0.1:6379[4]> hgetall "ECMP_LOADSHARE_TABLE_IPV4|ipv4"
    1)  "ipv4_l4_src_port"
    2)  "true"
    3)  "ipv4_l4_dst_port"
    4)  "true"
    5)  "ipv4_src_ip"
    6)  "true"
    7)  "ipv4_dst_ip"
    8)  "true"
    9)  "ipv4_protocol"
    10) "true"
    11) "ipv4_symmetric"
    12) "false"

    127.0.0.1:6379[4]> hgetall "ECMP_LOADSHARE_TABLE_IPV6|ipv6"
    1)  "ipv6_l4_src_port"
    2)  "true"
    3)  "ipv6_l4_dst_port"
    4)  "true"
    5)  "ipv6_src_ip"
    6)  "true"
    7)  "ipv6_dst_ip"
    8)  "true"
    9)  "ipv6_next_hdr"
    10) "true"
    11) "ipv6_symmetric"
    12) "false"

    127.0.0.1:6379[4]> hgetall "ECMP_LOADSHARE_TABLE_SEED|hash"
    1)  "ecmp_hash_seed"
    2)  "100"

### 3.2.2 APP DB

Existing "ecmp_hash_seed" field is being used for seed parameter. For other parameters, switch table includes following new fields:
  - ecmp_hash_fields_ipv4
  - ecmp_hash_fields_ipv6
  - ecmp_hash_mode_ipv4
  - ecmp_hash_mode_ipv6

Sample output:

    127.0.0.1:63792> HGETALL "SWITCH_TABLE:switch"
    1)  "ecmp_hash_fields_ipv4"
    2)  "ipv4_src_ip,ipv4_dst_ip,ipv4_protocol,ipv4_l4_src_port,ipv4_l4_dst_port"
    3)  "ecmp_hash_fields_ipv6"
    4)  "ipv6_src_ip,ipv6_dst_ip,ipv6_next_hdr,ipv6_l4_src_port,ipv6_l4_dst_port"
    5)  "ecmp_hash_seed"
    6)  "100"
    7)  "ecmp_hash_mode_ipv4"
    8)  "ipv4_symmetric"
    9)  "ecmp_hash_mode_ipv6"
    10) "ipv6_symmetric"
    127.0.0.1:63792>

### 3.2.3 STATE DB

### 3.2.4 ASIC DB

### 3.2.5 COUNTER DB

### 3.2.6 Configuration save and restart

## 3.3 Switch State Service Design

### 3.3.1 Switchmgrd

A new manager module - "switchmgr" has been introduced. It subscribes to the new IPv4/v6 ECMP load-share tables in the CONFIG DB. It processes CONFIG DB messages and updates the APP DB switch-table with all the valid packet-fields and symmetric hash config for IP ecmp.

### 3.3.2 Orchestration Agent

"Switchorch" component in OA subscribes to the switch table in APP DB and processes the new fields in switch table. The list of packet-fields for ecmp load-share is maintained as an attribute of hash objects - one each for IPv4 and IPv6. On receiving ecmp load-share packet-fields update, old hash object, if any, is deleted and the existing packet-fields are cleared in ASIC. Then a new hash object with supplied packet-fields is created and ASIC is programmed with the same. For symmetric hash, appropriate switch attribute (ipv4/v6) is called with user config (enable/disable).

## 3.4 SyncD

## 3.5 SAI

Existing SAI attributes being used:

    SAI_HASH_ATTR_NATIVE_HASH_FIELD_LIST
    SAI_SWITCH_ATTR_ECMP_HASH_IPV4
    SAI_SWITCH_ATTR_ECMP_HASH_IPV6
    SAI_SWITCH_ATTR_ECMP_DEFAULT_HASH_SEED

New SAI attributes (experimental):

    SAI_SWITCH_ATTR_ECMP_SYMMETRIC_HASH_IPV4
    SAI_SWITCH_ATTR_ECMP_SYMMETRIC_HASH_IPV6

## 3.6 CLI

### 3.6.1 Data Models

Please refer section 3.2

### 3.6.2 Configuration Commands

      [no] ip load-share hash ipv4 {ipv4-src-ip | ipv4-dst-ip | ipv4-ip-proto | ipv4-l4-src-port | ipv4-l4-dst-port | symmetric}
      [no] ip load-share hash ipv6 {ipv6-src-ip | ipv6-dst-ip | ipv6-next-hdr | ipv6-l4-src-port | ipv6-l4-dst-port | symmetric}
      [no] ip load-share hash seed <seed-value>

Example:

      ip load-share hash seed 12

      ip load-share hash ipv4 ipv4-src-ip
      ip load-share hash ipv4 ipv4-dst-ip
      ip load-share hash ipv4 symmetric

      ip load-share hash ipv6 ipv6-l4-src-port
      ip load-share hash ipv6 ipv6-l4-dst-port
      ip load-share hash ipv6 symmetric

### 3.6.3 Show Commands

      show ip load-share

Sample Output:

      IP Hash Mode: Default
      IPv6 Hash Mode: Symmetric
      Packet Header Fields:
      IP: ipv4-src-ip ipv4-dst-ip ipv4-ip-proto ipv4-l4-src-port ipv4-l4-dst-port
      IPv6: ipv6-src-ip ipv6-dst-ip ipv6-next-hdr ipv6-l4-src-port ipv6-l4-dst-port
      Hash seed: 10

### 3.6.4 Debug Commands

Not applicable

### 3.6.5 REST API Support

REST URI for IP ECMP Load-share Hash configuration:

    PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv4-attrs/config/ipv4-src-ip" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadshare-mode-ext:ipv4-src-ip\":true}"

    DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv4-attrs/config/ipv4-src-ip" -H "accept: */*"

    PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv4-attrs/config/ipv4-dst-ip" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadshare-mode-ext:ipv4-dst-ip\":true}"

    DELETE "<REST-SERVER:PORT>//restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv4-attrs/config/ipv4-dst-ip" -H "accept: */*"

    PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv4-attrs/config/ipv4-ip-proto" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadshare-mode-ext:ipv4-ip-proto\":true}"

    DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv4-attrs/config/ipv4-ip-proto" -H "accept: */*"

    PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv4-attrs/config/ipv4-l4-src-port" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadshare-mode-ext:ipv4-l4-src-port\":true}"

    DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv4-attrs/config/ipv4-l4-src-port" -H "accept: */*"

    PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv4-attrs/config/ipv4-l4-dst-port" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadshare-mode-ext:ipv4-l4-dst-port\":true}"

    DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv4-attrs/config/ipv4-l4-dst-port" -H "accept: */*"

    PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv4-attrs/config/ipv4-symmetric" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadshare-mode-ext:ipv4-symmetric\":true}"

    DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv4-attrs/config/ipv4-symmetric" -H "accept: */*"

    PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv6-attrs/config/ipv6-l4-src-port" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadshare-mode-ext:ipv6-l4-src-port\":true}"

    DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv6-attrs/config/ipv6-l4-src-port" -H "accept: */*"

    PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv6-attrs/config/ipv6-l4-dst-port" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadshare-mode-ext:ipv6-l4-dst-port\":true}"

    DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv6-attrs/config/ipv6-l4-dst-port" -H "accept: */*"

    PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv6-attrs/config/ipv6-src-ip" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadshare-mode-ext:ipv6-src-ip\":true}"

    DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv6-attrs/config/ipv6-src-ip" -H "accept: */*"

    PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv6-attrs/config/ipv6-dst-ip" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadshare-mode-ext:ipv6-dst-ip\":true}"

    DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv6-attrs/config/ipv6-dst-ip" -H "accept: */*"

    PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv6-attrs/config/ipv6-next-hdr" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadshare-mode-ext:ipv6-next-hdr\":true}"

    DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv6-attrs/config/ipv6-next-hdr" -H "accept: */*"

    PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv6-attrs/config/ipv6-symmetric" -H "accept: */*" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-loadshare-mode-ext:ipv6-symmetric\":true}"

    DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv6-attrs/config/ipv6-symmetric" -H "accept: */*"

REST URIs for IP ECMP Load-share Hash show:

    GET "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/seed-attrs" -H "accept: application/yang-data+json"

    GET "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv4-attrs" -H "accept: application/yang-data+json"

    GET "<REST-SERVER:PORT>/restconf/data/openconfig-loadshare-mode-ext:loadshare/ipv6-attrs" -H "accept: application/yang-data+json"

### 3.6.6 Operational Commands

### 3.6.7 Limitations
  1. Symmetric hashing cannot be enabled unless following packet-fields are enabled for ECMP hash:
      - (Src IP, Dst IP) and/or
      - (L4 SrcPort, L4 DstPort)


  2. If user has enabled symmetric hashing, user cannot enable/disable any of these packet-fields - Src-Ip, Dst-IP, L4-Src-Port and L4-Dst-Port, in ECMP hash configuration.

# 4 Flow Diagrams

# 5 Serviceability and Debug

# 6 Warm Boot Support

# 7 Dynamic Port Breakout

# 8 Scalability

# 9 Unit Test

## 9.1 IP ECMP Load-share Testcases

    1. Verify ECMP load-share for "default" config

    2. Verify ECMP load-share for user-configured IPv4/v6 fields
       a. Select only src-ip and dst-ip, send traffic by changing either of them and verify traffic is load-shared
       b. Select only src-ip and dst-ip, do not change src/dst-ip in traffic but change l4-src/dst-port and verify traffic is not load-shared

    3. Verify ECMP polarization for L3 underlay traffic(IPv4/v6) *server-->leaf1-->spine1/2-->leaf3/4-->server*
       a. Configure different hash seed on different tiers in a clos topology and confirm that traffic is load-shared in all tiers
       b. Select different packet-fields in different tiers in a clos topology and confirm that traffic is load-shared in all tiers

    4. Verify ECMP polarization for L3 Vxlan traffic(ipv4/v6) *server-->Leaf1-->spine1/2-->leaf3/4-->server, Vxlan tunnel between leaves - 1 & 3 and 1 & 4*
       a. Configure different hash seed in different tiers in a clos topology and confirm that traffic is load-shared in all tiers
       b. Select different packet-fields in different tiers in a clos topology and confirm that traffic is load-shared in all tiers

    5. Verify IP ECMP load-share config is retained after
       a. Device Warmboot
       b. Device Reload

## 9.2 Symmetric Hash Testcases

    1. Verify the bidirectional IPv4 flows follow the same path with default ECMP hash pkt-fields and symmetric hashing enabled
    2. Verify the bidirectional IPv4 flows follow the same path with SIP/DIP pkt-fields and symmetric hashing enabled
    3. Verify the bidirectional IPv4 flows follow the same path with L4-SrcP/DstP pkt-fields and symmetric hashing enabled
    4. Verify the bidirectional IPv4 flows follow the same path with SIP/DIP/L4-SrcP/DstP pkt-fields and symmetric hashing enabled
    5. Verify the bidirectional IPv4 flows follow the same path with default ECMP hash pkt-fields and symmetric hashing enabled
    6. Verify the bidirectional IPv4 flows follow the same path with SIP/DIP pkt-fields and symmetric hashing enabled
    7. Verify the bidirectional IPv4 flows follow the same path with L4-SrcP/DstP pkt-fields and symmetric hashing enabled
    8. Verify the bidirectional IPv4 flows follow the same path with SIP/DIP/L4-SrcP/DstP pkt-fields and symmetric hashing enabled
    9. Verify ECMP symmetric hash config is retained after
       a. Device Warmboot
       b. Device Reload
