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
| iBGP     | Interior Boarder Gateway Protocol              |


# 1 Feature Overview

## 1.1 Requirements

1. Support REST URIs for Nexthop tracking configurations

2. Support REST URIs for  Nexthop tracking operational state data.

3. Support KLISH CLIs for Nexthop tracking configurations

4. Support KLISH CLIs for Nexthop tracking operational state data.



# 2 Functionality



Next hop tracking (NHT) is an optimization feature that reduces the processing time involved in the BGP bestpath algorithm by monitoring changes to the routing table.

FRR introduced [Next hop Tracking](http://docs.frrouting.org/projects/dev-guide/en/latest/next-hop-tracking.html) functionality. This functionality requires resolving BGP neighbors before setting BGP connection. This required explicit config named ebgp-multihop. 

Sometimes BGP neighbors are not directly connected and sessions are iBGP. In this case current BGP configuration prevents FRR to establish BGP connections. Reason would be waiting for NHT to resolve the routes. To fix this, it is required either to add static routes for each non-directly connected iBGPneighbor, or enable command `ip nht resolve-via-default`

Nexthop resolution in rib (zebra) by default does not consider default route (0.0.0.0/0) for resolving the next-hops. In FRR, this is controlled by 'ip nht resolve-via-default' knob, and is turned off by default. If the user wishes to use default route for resolving the nexthops (including bgp peers), this configuration can be enabled.

Turning on 'resolve-via-default' by default may come as a surprise after upgrade, as the routes which were unresolved due to unavailability of preferred prefixes for reachability of next-hop will automatically become resolved and installed. Therefore this configuration is not enabled by default.

More information about FRR NHT feature can be found at links provided in Referenc section.

This HLD defines SONiC  KLISH CLI and REST URI to support Nexthop Tracking feature. 

This feature will add new Open Config extended URIs to support NHT config and state information.

New KLISH Config CLIs will be added to enable  nexthop resolution by default as part of this feature.


# 3 Design

## 3.1 Overview

### 3.1.1 NHT configuration

#### 3.1.1.1 Configure NHT resolution via default route

Nexthop resoltion using default route can be configured using below KLISH CLI command under global config mode. 

Command Syntax


```
ip [vrf <vrf-name>] nht resolve-via-default

ipv6 [vrf <vrf-name>] nht resolve-via-default
```



Examples  


```
sonic(confg)# ip ?
  . . .
  nht                  Nexthop Tracking and resolution
  vrf                  VRF instance configuration
sonic(config)# ip nht ?
  resolve-via-default  Resolve via default route
sonic(config)#

sonic(config)# ip vrf Vrf-blue nht ?
  resolve-via-default  Resolve via default route
sonic(config)#
 
sonic(confg)# ipv6 ?
  . . .
  nht                  Nexthop Tracking and resolution
  vrf                  VRF instance configuration
sonic(config)# ipv6 nht ?
  resolve-via-default  Resolve via default route
sonic(config)#

sonic(config)# ipv6 vrf Vrf-blue nht ?
  resolve-via-default  Resolve via default route
sonic(config)#
```

### 3.1.2 NHT operational States

#### 3.1.2.1 NHT state information retreival

No new show CLI commands will be added.

Configuration information will be displayed as part of show running-configuration.


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
        +--rw openconfig-nexthop-tracking-ex:nexthop-tracking
        |  +--rw openconfig-nexthop-tracking-ex:address-family* [family]
        |     +--rw openconfig-nexthop-tracking-ex:family    -> ../config/family
        |     +--rw openconfig-nexthop-tracking-ex:config
        |     |  +--rw openconfig-nexthop-tracking-ex:family?                oc-inet:ip-version
        |     |  +--rw openconfig-nexthop-tracking-ex:resolve-via-default?   boolean
        |     +--ro openconfig-nexthop-tracking-ex:state
        |     |  +--ro openconfig-nexthop-tracking-ex:family?                oc-inet:ip-version
        |     |  +--ro openconfig-nexthop-tracking-ex:resolve-via-default?   boolean
        
```



REST URI will be as below

```
/openconfig-netinst:network-instances/openconfig-netinst:network-instance/openconfig-nexthop-tracking-ex:nexthop-tracking/openconfig-nexthop-tracking-ex:address-family/openconfig-nexthop-tracking-ex:config/openconfig-nexthop-tracking-ex:resolve-via-default

/openconfig-netinst:network-instances/openconfig-netinst:network-instance/openconfig-nexthop-tracking-ex:nexthop-tracking/openconfig-nexthop-tracking-ex:address-family/openconfig-nexthop-tracking-ex:state/openconfig-nexthop-tracking-ex:resolve-via-default

/openconfig-netinst:network-instances/openconfig-netinst:network-instance/openconfig-nexthop-tracking-ex:nexthop-tracking/openconfig-nexthop-tracking-ex:address-family/openconfig-nexthop-tracking-ex:routes/openconfig-nexthop-tracking-ex:route/openconfig-nexthop-tracking-ex:config

/openconfig-netinst:network-instances/openconfig-netinst:network-instance/openconfig-nexthop-tracking-ex:nexthop-tracking/openconfig-nexthop-tracking-ex:address-family/openconfig-nexthop-tracking-ex:routes/openconfig-nexthop-tracking-ex:route/openconfig-nexthop-tracking-ex:state


```



CURL config command examples

```
curl  --insecure -X PUT "https://<server>:<port>/restconf/data/openconfig-network-instance:network-instances/network-instance=default/openconfig-nexthop-tracking-ext:nexthop-tracking" -H "accept: */*" -H  "Content-Type: application/yang-data+json" -d "{\"openconfig-nexthop-tracking-ext:nexthop-tracking\":{\"openconfig-nexthop-tracking-ext:address-family\":[{\"openconfig-nexthop-tracking-ext:config\":{\"openconfig-nexthop-tracking-ext:family\" : \"IPV4\",\"openconfig-nexthop-tracking-ext:resolve-via-default\" : true}, \"openconfig-nexthop-tracking-ext:family\" : \"IPV4\"}]}}"

curl  --insecure -X PATCH "https://<server>:<port>/restconf/data/openconfig-network-instance:network-instances/network-instance=default/openconfig-nexthop-tracking-ext:nexthop-tracking/openconfig-nexthop-tracking-ext:family=IPV4" -H "accept: */*" -H  "Content-Type: application/yang-data+json" -d "{\"openconfig-nexthop-tracking-ext:nexthop-tracking\":{\"openconfig-nexthop-tracking-ext:address-family\":[{\"openconfig-nexthop-tracking-ext:config\":{\"openconfig-nexthop-tracking-ext:family\" : \"IPV4\",\"openconfig-nexthop-tracking-ext:resolve-via-default\" : true}, \"openconfig-nexthop-tracking-ext:family\" : \"IPV4\"}]}}"

curl --insecure -X GET "https://<server>:<port>/restconf/data/openconfig-network-instance:network-instances/network-instance=default/openconfig-nexthop-tracking-ext:nexthop-tracking" -H "accept: application/yang-data+json"

curl --insecure -X DELETE "https://<server>:<port>/restconf/data/openconfig-network-instance:network-instances/network-instance=default/openconfig-nexthop-tracking-ext:nexthop-tracking/address-family=IPV4" -H "accept: */*"

curl --insecure -X DELETE "https://<server>:<port>/restconf/data/openconfig-network-instance:network-instances/network-instance=default/openconfig-nexthop-tracking-ext:nexthop-tracking" -H "accept: */*"


```




### 3.3.2 CLI

#### 3.3.2.1 Show Commands

##### 3.3.2.1.1 Show NHT state information

Nexthop resoltion using default route can be configured using below KLISH CLI command under global config mode. 

Command Syntax


```
ip [vrf <vrf-name>] nht resolve-via-default

ipv6 [vrf <vrf-name>] nht resolve-via-default

```



## 3.4 Upgrade Downgrade considerations

There is no impact on upgrading the image to latest version.

On downgrading to previous release version NHT configurations will be lost.


## 3.5 Scale Considerations

Not applicable.


## 3.6 UT Cases

1. Configure NHT resolve via default route for ipv4 and verify running configuration for configured value.

2. Configure NHT resolve via default route for ipv6 and verify running configuration for configured value.

3. Configure NHT resolve via default route for ipv4 for a user VRF and verify running configuration for configured value.

4. Configure NHT resolve via default route for ipv6 within a user VRF verify running configuration  for configured value.

5. Configure NHT resolve via default route for both IPV4 and IPV6 on both default and user VRF. Save configuration. Restart the systema and verify that the configuration is intack over restart.



## 3.6 References

FRR NHT feature http://docs.frrouting.org/projects/dev-guide/en/latest/next-hop-tracking.html

SONiC UT additions https://github.com/Azure/sonic-buildimage/pull/5600


