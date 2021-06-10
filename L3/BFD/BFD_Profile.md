# Feature Name
*Profile Support for BFD configuration*

# High Level Design Document
# Table of Contents
- [1 Feature Overview](#1-Feature-Overview)
    - [1.1 Requirements](#12-Requirements)
- [2 Functionality](#2-Functionality)
- [3 Design](#3-Design)
    - [3.1 Overview](#31-Overview)
	    - [3.1.1 BFD Profile ](#311-BFD-Profile)
		    - [3.1.1.1 BFD Profile Configuration](#3111-BFD-Profile-configuration)
		    - [3.1.1.2 BFD Profile with static peer](#3112-BFD-Profile-with-static-peer)
		    - [3.1.1.3 BFD Profile with BGP](#3113-BFD-Profile-configuration)
		    - [3.1.1.4 BFD Profile with OSPF](#3114-BFD-Profile-configuration)
		    - [3.1.1.5 BFD Profile with PIM](#3115-BFD-Profile-configuration)
	  - [3.1.2 BFD Passive mode](312-BFD-Passive-mode)
	  - [3.1.2 Minimum TTL for multi-hop peer](312-Minimum-TTL-for-multi-hop-peer)
    - [3.2 DB Changes](#32-DB-Changes)
        - [3.2.1 CONFIG DB](#321-CONFIG-DB)
	        - [3.2.1.1 BFD_PROFILE](3211-BFD-PROFILE)
	        - [3.2.1.2 BFD_PEER_SINGLE_HOP](3212-BFD_PEER_SINGLE_HOP)
	        - [3.2.1.3 BFD_PEER_MULTI_HOP](3213-BFD_PEER_MULTI_HOP)
	        - [3.2.1.4 BGP_PEER_GROUP](3214-BGP_PEER_GROUP)
	        - [3.2.1.5 BGP_NEIGHBOR](3215-BGP_NEIGHBOR)
	        - [3.2.1.6 OSPFV2_INTERFACE](3216-OSPFV2_INTERFACE)
	        - [3.2.1.6 PIM_INTERFACE](3216-PIM_INTERFACE)
    - [3.3 User Interface](#36-User-Interface)
        - [3.3.1 Data Models](#331-Data-Models)
        - [3.3.2 CLI](#332-CLI)
	        - [3.3.2.1 Show Commands](#3321-Show-Commands)
		        - [3.3.2.1.1 Show BFD Profile](#33211-Show-Bfd-Profile)
		        - [3.3.2.1.2 Show BFD Peer](#33211-Show-Bfd-Peer)

# List of Tables
[Table 1: Abbreviations](#table-1-Abbreviations)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 05/17/2021  |   Sumit Agarwal    | Initial version                   |

# About this Manual
This document provides comprehensive functional and design information about the *Profile configuration support in BFD* feature implementation in SONiC.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| BFD                      | Bidirectional Forwarding Detection  |
| BGP                      | Border Gateway Protocol             |
| OSPF                     | Open Shortest Path First            |
| PIM                      | Protocol-Independent Multicast      |

# 1 Feature Overview
## 1.1 Requirements
1. Support BFD profile configuration.
2. Support BFD profile configuration for static peers.
3. Support BFD profile configuration for BGP.
4. Support BFD profile configuration for OSPF.
5. Support BFD profile configuration for PIM.

# 2 Functionality
BFD profile is a template which can be applied to multiple BFD peers, making it easier to change default configuration without configuring each of the peer separately. Profile also enables changing BFD timers of dynamic session without configuring static BFD peers.

# 3 Design
## 3.1 Overview
### 3.1.1 BFD Profile
#### 3.1.1.1 BFD Profile configuration
BFD profile is a template wherein all BFD params can be configured and then the profile can be applied to BFD peers. 
Below CLI's are added to configure BFD profile and its params.
```
sonic(config)# bfd
sonic(conf-bfd)# profile test
sonic(conf-bfd-profile)# 
  detect-multiplier  Configure peer detection multiplier
  echo-interval      Configure peer echo interval
  echo-mode          Configure echo mode
  minimum-ttl        Configure minimum expected TTL for incoming multihop BFD packets
  passive-mode       Configure the BFD peer as passive
  receive-interval   Configure peer receive interval
  transmit-interval  Configure peer transmit interval
```
#### 3.1.1.2 BFD Profile with static peer
BFD profile can be associated with a BFD peer using below CLI, BFD params configured in static BFD peer takes precedence over profile.
```
sonic(config)# bfd
sonic(conf-bfd)# peer 1.1.1.1 interface Ethernet32  
sonic(conf-bfd-peer)# profile test
```
#### 3.1.1.3 BFD Profile with BGP
BFD profile can be associated with a  BGP neighbor using below CLI
```
sonic(config)# router bgp 1
sonic(config-router-bgp)# neighbor 1.1.1.1               
sonic(config-router-bgp-neighbor)# bfd profile test
```
BFD profile can be associated with a BGP peer-group using below CLI
```
sonic(config)# router bgp 1
sonic(config-router-bgp)# peer-group test 
sonic(config-router-bgp-pg)# bfd profile test
```
#### 3.1.1.4 BFD Profile with OSPF
BFD profile can be associated with OSPF by enabling it on the interface using below CLI
```
sonic(config)# interface Ethernet 32
sonic(conf-if-Ethernet32)# ip ospf bfd profile test
```
#### 3.1.1.5 BFD Profile with PIM
BFD profile can be associated with PIM by enabling it on the interface using below CLI
```
sonic(config)# interface Ethernet 32
sonic(conf-if-Ethernet32)# ip pim bfd profile test
```
#### 3.1.1.6 Profile configuration scenarios
**Scenario 1:**
If the BFD profile associated with BFD peer, BGP neighbor/peer-group, OSPF and PIM is not configured.
Profile will take effect after it is configured.

**Scenario 2:**
BFD profile is associated with static BFD peer and BFD params are configured in the static peer as well.
Params configured in static peer takes precedence over BFD profile.

**Scenario 3:**
BGP, OSPF and PIM shares a BFD session, BFD profile associated with BGP, OSPF and PIM is different.
The latest configured profile either in BGP, OSPF or PIM takes effect.

**Scenario 4:**
BFD profile configuration is changed dynamically. 
All the configuration parameters will take effect immediately, BFD timers will be renegotiated using the polling method. 

**Scenario 5:**
BFD profile associated with BGP, OSPF, PIM or BFD peer is deleted.
The associated BFD session reverts to default values. The profile configuration should be deleted from BGP, OSPF, PIM or BFD peer as well and reconfigured to take effect.

### 3.1.2 BFD Passive mode:
A CLI is added to support passive mode, when passive mode is configured BFD local peer will not initiate session  creation request but will respond to session creation request received from the peer device. By default passive mode is disabled.

Passive mode can be enabled in both BFD peer and profile as below:
```
sonic(config)# bfd
sonic(conf-bfd)# peer 1.1.1.1 interface Ethernet32
sonic(conf-bfd-peer)# passive-mode 
```
```
sonic(config)# bfd
sonic(conf-bfd)# profile test
sonic(conf-bfd-profile)# passive-mode 
```

### 3.1.3 Minimum TTL for multi-hop peer:
Minimum TTL can be configured for multi-hop BFD peer, if the TTL of the received BFD packet is less than the configured TTL, the packet is discarded. Default TTL value for multi-hop peer is 254.

Minimum TTL can be configured in BFD peer and profile as below:

```
sonic(config)# bfd
sonic(conf-bfd)# peer 1.1.1.1 local-address 1.1.1.2 multihop
sonic(conf-bfd-peer)# minimum-ttl 253 
```
```
sonic(config)# bfd
sonic(conf-bfd)# profile test
sonic(conf-bfd-profile)# minimum-ttl 253  
```
## 3.2 DB Changes

### 3.2.1 CONFIG DB
#### 3.2.1.1 BFD_PROFILE
A new table BFD_PROFILE with below elements is added.
```
Producer: Configuration
Consumer: bgpcfgd 
Description: New table to store BFD profile configurations.

New BFD profile Table with below key and fields is added.

key = BFD_PROFILE|profile-name;
 1) detection-multiplier
 2) minimum-ttl
 3) enabled
 4) desired-minimum-tx-interval
 5) required-minimum-receive
 6) desired-minimum-echo-receive
 7) echo-active
 8) passive-mode
```
#### 3.2.1.2 BFD_PEER_SINGLE_HOP
```
A new element is added to BFD_PEER_SINGLE_HOP table
1. passive-mode
2. profile-name
```
#### 3.2.1.3 BFD_PEER_MULTI_HOP
```
A new element is added to BFD_PEER_MULTI_HOP table
1. passive-mode
2. profile-name
3. minimum-ttl
```
#### 3.2.1.4 BGP_PEER_GROUP
```
A new element is added to BGP_PEER_GROUP table
1. bfd_profile
```
#### 3.2.1.5 BGP_NEIGHBOR
```
A new element is added to BGP_NEIGHBOR table
1. bfd_profile
```
#### 3.2.1.6 OSPFV2_INTERFACE
```
A new element is added to OSPFV2_INTERFACE table
1. bfd-profile
```

#### 3.2.1.6 PIM_INTERFACE
```
A new element is added to PIM_INTERFACE table
1. bfd-profile
```
## 3.3 User Interface

### 3.3.1 Data Models
Below are the yang models modified to support BFD profile, only the newly added elements is listed down here.

openconfig-bfd-ext.yang
```
  +--rw bfd
+    +--rw oc-bfd-ext:bfd-profile
+    |  +--rw oc-bfd-ext:profile* [profile-name]
+    |     +--rw oc-bfd-ext:profile-name    -> ../config/profile-name
+    |     +--rw oc-bfd-ext:config
+    |     |  +--rw oc-bfd-ext:profile-name?                   string
+    |     |  +--rw oc-bfd-ext:enabled?                        boolean
+    |     |  +--rw oc-bfd-ext:desired-minimum-tx-interval?    uint32
+    |     |  +--rw oc-bfd-ext:required-minimum-receive?       uint32
+    |     |  +--rw oc-bfd-ext:detection-multiplier?           uint8
+    |     |  +--rw oc-bfd-ext:passive-mode?                   boolean
+    |     |  +--rw oc-bfd-ext:minimum-ttl?                    uint8
+    |     |  +--rw oc-bfd-ext:desired-minimum-echo-receive?   uint32
+    |     |  +--rw oc-bfd-ext:echo-active?                    boolean
+    |     +--ro oc-bfd-ext:state
+    |        +--ro oc-bfd-ext:profile-name?                   string
+    |        +--ro oc-bfd-ext:enabled?                        boolean
+    |        +--ro oc-bfd-ext:desired-minimum-tx-interval?    uint32
+    |        +--ro oc-bfd-ext:required-minimum-receive?       uint32
+    |        +--ro oc-bfd-ext:detection-multiplier?           uint8
+    |        +--ro oc-bfd-ext:passive-mode?                   boolean
+    |        +--ro oc-bfd-ext:minimum-ttl?                    uint8
+    |        +--ro oc-bfd-ext:desired-minimum-echo-receive?   uint32
+    |        +--ro oc-bfd-ext:echo-active?                    boolean
```
```
+    +--rw oc-bfd-ext:bfd-shop-sessions
+    |  +--rw oc-bfd-ext:single-hop* [remote-address interface vrf local-address]
+    |     +--rw oc-bfd-ext:config
+    |     |  +--rw oc-bfd-ext:passive-mode?                   boolean
+    |     |  +--rw oc-bfd-ext:profile-name?                   string
+    |     +--ro oc-bfd-ext:state
+    |        +--ro oc-bfd-ext:passive-mode?                  boolean
+    |        +--ro oc-bfd-ext:profile-name?                  string
```
```
+    +--rw oc-bfd-ext:bfd-mhop-sessions
+    |  +--rw oc-bfd-ext:multi-hop* [remote-address interface vrf local-address]
+    |     +--rw oc-bfd-ext:config
+    |     |  +--rw oc-bfd-ext:passive-mode?                  boolean
+    |     |  +--rw oc-bfd-ext:minimum-ttl?                   uint8
+    |     |  +--rw oc-bfd-ext:profile-name?                  string
+    |     +--ro oc-bfd-ext:state
+    |        +--ro oc-bfd-ext:passive-mode?                  boolean
+    |        +--ro oc-bfd-ext:minimum-ttl?                   uint8
+    |        +--ro oc-bfd-ext:profile-name?                  string

```
openconfig-bgp-ext.yang
```
        |     |  +--rw neighbors
        |     |  |     +--rw oc-bfd:enable-bfd
        |     |  |     |  +--rw oc-bfd:config
+       |     |  |     |  |  +--rw oc-bgp-ext:bfd-profile string
+        |     |  |     |  +--ro oc-bfd:state                             
+       |     |  |     |     +--ro oc-bgp-ext:bfd-profile string
``` 
openconfig-ospfv2-ext.yang
```
        +--rw subinterfaces
        |  +--rw subinterface* [index]
        |     +--rw index           -> ../config/index
        |     +--rw oc-ip:ipv4
+       |     |  +--rw oc-ospfv2-ext:ospfv2
+       |     |  |  +--rw oc-ospfv2-ext:if-addresses* [address]
+       |     |  |     +--rw oc-ospfv2-ext:config
+       |     |  |     |  +--rw oc-ospfv2-ext:bfd-profile string     
+       |     |  |     +--ro oc-ospfv2-ext:state
+       |     |  |     |  +--ro oc-ospfv2-ext:bfd-profile string
```
openconfig-pim-ext.yang
```
        |     +--rw pim
        |     |  +--rw interfaces
        |     |     +--rw interface* [interface-id]
        |     |        +--rw config
+       |     |        |  +--rw oc-pim-ext:bfd-enabled   boolean
+       |     |        |  +--rw oc-pim-ext:bfd-profile   string
        |     |        +--ro state
+       |     |        |  +--ro oc-pim-ext:bfd-enabled     boolean
+       |     |        |  +--ro oc-pim-ext:bfd-profile     string
```
### 3.3.2 CLI
#### 3.3.2.1 Show Commands
##### 3.3.2.1.1 Show BFD Profile
A new command is added to display BFD profiles
```
The below command displays all the configured BFD profiles:

sonic# show bfd profile 
BFD Profile:
    Profile-name: fast
        Enabled: True
        Echo-mode: Enabled
        Passive-mode: Enabled
        Minimum-Ttl: 254
        Detect-multiplier: 4
        Receive interval: 123ms
        Transmission interval: 123ms
        Echo transmission interval: 1234ms
```

```
The below command displays the specified BFD profile:

sonic# show bfd profile fast
BFD Profile:
    Profile-name: fast
        Enabled: True
        Echo-mode: Enabled
        Passive-mode: Enabled
        Minimum-Ttl: 254
        Detect-multiplier: 4
        Receive interval: 123ms
        Transmission interval: 123ms
        Echo transmission interval: 1234ms
```
##### 3.3.2.1.2 Show BFD Peer
In multi-hop bfd peer two new elements Active/Passive mode and Minimum TTL has been added.
```
    peer 1.1.1.2 multihop local-address 1.1.1.1 vrf default
        ID: 82748345
        Remote ID: 0
        Active mode
        Minimum TTL: 123
        Status: down
        Downtime: 0 day(s), 0 hour(s), 0 min(s), 19 sec(s)
        Diagnostics: ok
        Remote diagnostics: ok
        Peer Type: configured
        Local timers:
            Detect-multiplier: 3
            Receive interval: 300ms
            Transmission interval: 300ms
            Echo transmission interval: 60ms
        Remote timers:
            Detect-multiplier: 3
            Receive interval: 1000ms
            Transmission interval: 1000ms
            Echo transmission interval: 0ms
```
In single-hop bfd peer a new elements Active/Passive mode has been added.
```
    peer 1.1.1.2 vrf default interface Ethernet32
        ID: 2286155092
        Remote ID: 0
        Passive mode
        Status: down
        Downtime: 0 day(s), 0 hour(s), 1 min(s), 6 sec(s)
        Diagnostics: ok
        Remote diagnostics: ok
        Peer Type: configured
        Local timers:
            Detect-multiplier: 3
            Receive interval: 300ms
            Transmission interval: 300ms
            Echo transmission interval: 0ms
        Remote timers:
            Detect-multiplier: 3
            Receive interval: 1000ms
            Transmission interval: 1000ms
            Echo transmission interval: 0ms
```
