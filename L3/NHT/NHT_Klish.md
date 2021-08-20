# Feature Name

Nexthop Tracking REST URI and KLISH CLI Support

# High Level Design Document

# Table of Contents

- [1 Feature Overview](#1-Feature-Overview)
  - [1.1 Requirements](#12-Requirements)
- [2 Functionality](#2-Functionality)
- [3 Design](#3-Design)
  - [3.1 Overview](#31-Overview)
    - [3.1.1 NHT Configuration ](#311-NHT-Configuration)
    - [3.1.2 NHT Operational State](312-NHT-operational-state)
  - [3.2 DB Changes](#32-DB-Changes)
    - [3.2.1 CONFIG DB](#321-CONFIG-DB)
      - [3.2.1.1 NHT VRF Table](3211-NHT-VRF-Table)
  - [3.3 User Interface](#36-User-Interface)
    - [3.3.1 Data Models](#331-Data-Models)
    - [3.3.2 CLI](#332-CLI)
      - [3.3.2.1 Show Commands](#3321-Show-Commands)
        - [3.3.2.1.1 Show NHT State information](#33211-Show-NHT-state-information)
  - [3.4 Upgrade Downgrade Considerations](#34-Upgrade-Downgrade-Considerations)
  - [3.5 Scale Considerations](#35-Scale-Considerations)
  - [3.6 UT Cases](#36-UT-Cases)
  - [3.7 References](#37-References)

# List of Tables

[Table 1: Abbreviations](#table-1-Abbreviations)

# Revision

| Rev  |    Date    |     Author     | Change Description |
| :--: | :--------: | :------------: | ------------------ |
| 0.1  | 08/19/2021 | Naveen Suvarna | Initial version    |
|      |            |                |                    |

# About this Manual

This document provides comprehensive functional information about the REST and KLISH CLI implementaion for IP Layer-3 Nexthop Tracking feature.

# Definition/Abbreviation

### Table 1: Abbreviations

| **Term** | **Meaning**                                    |
| -------- | ---------------------------------------------- |
| NHT      | Nexthop Tracking                               |
| IPv4     | Internet Protocol Version 4, IP address family |
| IPv6     | Internet Protocol Version 6, IP address family |
| CLI      | Command Line Interface                         |
| VRF      | Virtual Routing and Forwarding instance        |

# 1 Feature Overview

## 1.1 Requirements

1. Support REST URIs for Nexthop tracking configurations 

2. Support REST URIs for  Nexthop tracking operational state data.

3. Support KLISH CLI for Nexthop tracking configurations 

4. Support REST URIs for  Nexthop tracking operational state data.

   

# 2 Functionality

Nexthop Tracking (NHT) feature allows users to configure and track IP route Next hop resolution. IP route nexthop resolutions can be controlled by user configured parameters. NHT operational state details about how the Nexthops are resolved for any IP route. 

This feature supports REST URIs and KLISH CLIs for configureing NHT route resolution rules and retrieving operational deatils on IP route next hop resolution.

# 3 Design

## 3.1 Overview

### 3.1.1 NHT configuration

#### 3.1.1.1 Configure NHT resolution via default route

Nexthop resoltion using feault rote can be configured using below KLISH CLI command under VRF config mode.  seperate commands will be provided for IPV4 and IPV6 address family types. both ipv4 and ipv6 configurations will be disabled by default.

A new VRF mode will be added to configure the NHT parameters. These configurations are allowed on both deault and user VRF instances.

```
sonic(config)# vrf <vrf-name>
sonic(conf-vrf)# ip nht ?
  resolve-via-default  Resolve via default route
sonic(conf-vrf)# ip nht resolve-via-default 
sonic(conf-vrf)#
  
sonic(config)# vrf <vrf-name>
sonic(conf-vrf)# ipv6 nht ?
  resolve-via-default  Resolve via default route
sonic(conf-vrf)# ipv6 nht resolve-via-default 
sonic(conf-vrf)#

```

### 3.1.2 NHT operational States

#### 3.1.2.1 NHT state information retreival

Show Klish CLI commands will be provided to retrieve Nexthop tracking configurations as below syntax.

```
show ip nht [ <IPv4-Address> | vrf <vrf-name> [ <IPv4-Address> ] ]

show ipv6 nht [ <IPv6-Address> | vrf <vrf-name> [ <IPv6-Address> ] ]

sonic# show ip nht ?
  <cr>
  A.B.C.D    IPv4 Address
  vrf        Specify the VRF
sonic# show ip nht vrf Vrf-blue ?
  <cr>
  A.B.C.D   IPv4 Address
sonic# 

sonic# show ipv6 nht ?
  <cr>
  X:X::X:X  IPv6 Address
  vrf        Specify the VRF
sonic# show ip nht vrf Vrf-blue ?
  <cr>
  X:X::X:X  IPv6 Address
sonic# 

```

Same show command will display all the Routes with their nexthop reoltion information. Command doesnot support all VRF option.

 Show command CLI output will be as below [ TBD: more detailed and coorected CLI show output]

```
sonic# show ip nht 
VRF Name: default
 IPv4 nexthop resolve by default: enabled
 Routes
  3.3.3.3
   Resolved via kernel
   via 11.0.0.6, swp1
   Client list: bgp(fd 12)
  11.0.0.10
   resolved via connected
   is directly connected, swp2
   Client list: bgp(fd 12)
  11.0.0.18
   resolved via connected
   is directly connected, swp4
   Client list: bgp(fd 12)
  11.11.11.11
   resolved via kernel
   via 10.0.1.2, eth0
   Client list: bgp(fd 12) 
sonic# 


```



## 3.2 DB Changes

### 3.2.1 CONFIG DB

#### 3.2.1.1 NHT VRF table

A new table NHT_VRF_AF  will be introduced with VRF-Name and Address-Family as key. Address family values can be IPV4 and IPV6.

```
Producer: Configuration
Consumer: bgpcfgd
Description: New table to store NHT configuration.

New NHT_VRF_AF Table with below key and fields is added.

key = NHT_VRF_AF|vrf_name|family;
 1) resolve-via-default
 2) family

```

## 3.3 User Interface

### 3.3.1 Data Models

Nexthop tracking config and state informations are modeled under network instance heirarchy. Below are the yang models added to support NHT configuration and state information.

openconfig-nexthop-tracking-ext.yang

```
module: openconfig-network-instance
  +--rw network-instances
     +--rw network-instance* [name]
        +--rw name 
        +- . . . <snip>
        +--rw oc-nexthop-tracking-ext:nexthop-tracking
        |  +--rw oc-nexthop-tracking-ext:address-family* [family]
        |     +--rw oc-nexthop-tracking-ext:family    -> ../config/family
        |     +--rw oc-nexthop-tracking-ext:config
        |     |  +--rw oc-nexthop-tracking-ext:family?                oc-inet:ip-version
        |     |  +--rw oc-nexthop-tracking-ext:resolve-via-default?   boolean
        |     +--ro oc-nexthop-tracking-ext:state
        |     |  +--ro oc-nexthop-tracking-ext:family?                oc-inet:ip-version
        |     |  +--ro oc-nexthop-tracking-ext:resolve-via-default?   boolean
        |     +--ro oc-nexthop-tracking-ext:routes
        |        +--ro oc-nexthop-tracking-ext:route* [prefix]
        |           +--ro oc-nexthop-tracking-ext:prefix      -> ../state/prefix
        |           +--ro oc-nexthop-tracking-ext:state
        |           |  +--ro oc-nexthop-tracking-ext:prefix?             oc-inet:ip-prefix
        |           |  +--ro oc-nexthop-tracking-ext:connected?          boolean
        |           |  +--ro oc-nexthop-tracking-ext:status?             string
        |           |  +--ro oc-nexthop-tracking-ext:psuedowires?        string
        |           |  +--ro oc-nexthop-tracking-ext:update-sent?        boolean
        |           |  +--ro oc-nexthop-tracking-ext:last-update-time?   uint32
        |           +--ro oc-nexthop-tracking-ext:nexthops
        |           |  +--ro oc-nexthop-tracking-ext:nexthop* [type gateway out-interface]
        |           |     +--ro oc-nexthop-tracking-ext:type             -> ../state/type
        |           |     +--ro oc-nexthop-tracking-ext:gateway          -> ../state/gateway
        |           |     +--ro oc-nexthop-tracking-ext:out-interface    -> ../state/out-interface
        |           |     +--ro oc-nexthop-tracking-ext:state
        |           |        +--ro oc-nexthop-tracking-ext:type?            identityref
        |           |        +--ro oc-nexthop-tracking-ext:gateway?         oc-inet:ip-address
        |           |        +--ro oc-nexthop-tracking-ext:out-interface?   string
        |           +--ro oc-nexthop-tracking-ext:clients
        |              +--ro oc-nexthop-tracking-ext:client* [protocol]
        |                 +--ro oc-nexthop-tracking-ext:protocol    -> ../state/protocol
        |                 +--ro oc-nexthop-tracking-ext:state
        |                    +--ro oc-nexthop-tracking-ext:protocol?    identityref
        |                    +--ro oc-nexthop-tracking-ext:filtered?    boolean
        |                    +--ro oc-nexthop-tracking-ext:socket-fd?   uint32
```



REST URI will be as below

```
/oc-netinst:network-instances/oc-netinst:network-instance/oc-nexthop-tracking-ext:nexthop-tracking/oc-nexthop-tracking-ext:address-family/oc-nexthop-tracking-ext:config/oc-nexthop-tracking-ext:resolve-via-default

/oc-netinst:network-instances/oc-netinst:network-instance/oc-nexthop-tracking-ext:nexthop-tracking/oc-nexthop-tracking-ext:address-family/oc-nexthop-tracking-ext:state/oc-nexthop-tracking-ext:resolve-via-default

/oc-netinst:network-instances/oc-netinst:network-instance/oc-nexthop-tracking-ext:nexthop-tracking/oc-nexthop-tracking-ext:address-family/oc-nexthop-tracking-ext:routes/oc-nexthop-tracking-ext:route/oc-nexthop-tracking-ext:config

/oc-netinst:network-instances/oc-netinst:network-instance/oc-nexthop-tracking-ext:nexthop-tracking/oc-nexthop-tracking-ext:address-family/oc-nexthop-tracking-ext:routes/oc-nexthop-tracking-ext:route/oc-nexthop-tracking-ext:state



```



CURL config command examples 

```
curl  --insecure -X PUT "https://lvnvde3550.lvn.broadcom.net:9869/restconf/data/openconfig-network-instance:network-instances/network-instance=default/openconfig-nexthop-tracking-ext:nexthop-tracking" -H "accept: */*" -H  "Content-Type: application/yang-data+json" -d "{\"openconfig-nexthop-tracking-ext:nexthop-tracking\":{\"openconfig-nexthop-tracking-ext:address-family\":[{\"openconfig-nexthop-tracking-ext:config\":{\"openconfig-nexthop-tracking-ext:family\" : \"IPV4\",\"openconfig-nexthop-tracking-ext:resolve-via-default\" : true}, \"openconfig-nexthop-tracking-ext:family\" : \"IPV4\"}]}}"



```

 



### 3.3.2 CLI

#### 3.3.2.1 Show Commands

##### 3.3.2.1.1 Show NHT state information

A new command is added to display NHT information as detailed in section 3.1.2 

```
show ip nht [ <IPv4-Address> | vrf <vrf-name> [ <IPv4-Address> ] ]

show ipv6 nht [ <IPv6-Address> | vrf <vrf-name> [ <IPv6-Address> ] ]

sonic# show ip nht ?
  <cr>
  A.B.C.D    IPv4 Address
  vrf        Specify the VRF
sonic# show ip nht vrf Vrf-blue ?
  <cr>
  A.B.C.D   IPv4 Address
sonic# 

sonic# show ipv6 nht ?
  <cr>
  X:X::X:X  IPv6 Address
  vrf        Specify the VRF
sonic# show ip nht vrf Vrf-blue ?
  <cr>
  X:X::X:X  IPv6 Address
sonic# 
```



## 3.4 Upgrade Downgrade considerations

There is no impact on upgrading the image to latest version.

On downgrading to previous release version NHT configurations will be lost.  

## 3.5 Scale Considerations

Not applicable.



## 3.6 UT Cases

1. Configure NHT resolve via default route for ipv4 and verify running configuration and the show command output for configured value.

2. Configure NHT resolve via default route for ipv6 and verify running configuration and the show command output for configured value.

3. Configure NHT resolve via default route for ipv4 within a user VRF and verify running configuration and the show command output for configured value.

4. Configure NHT resolve via default route for ipv6 within a user VRF verify running configuration and the show command output for configured value.

5. Configure NHT resolve via default route for both IPV4 and IPV6 on both default and user VRF. Save configuration. Restart the systema and verify that the configuration is intack over restart.

6. Configure OSPF and BGP on two nodes with user and default VRFs resulting in multiple routes. Verify NHT route listing in CLI and REST GET commands.

   

## 3.6 References

