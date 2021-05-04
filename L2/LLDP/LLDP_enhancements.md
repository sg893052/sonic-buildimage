

# Feature Name

LLDP enhancements

# High Level Design Document

#### Rev 0.1

# Table of Contents

  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definitionabbreviation)
  * [Requirements Overview](#requirement-overview)
    * [Functional Requirements](#functional-requirements)
    * [Configuration and Management Requirements](#configuration-and-management-requirements)
    * [Scalability Requirements](#scalability-requirements)
    * [Warm Boot Requirements](#warm-boot-requirements)
  * [Functionality](#-functionality)
    * [Functional Description](#functional-description)
  * [Design](#design)
    * [Overview](#overview)
    * [DB Changes](#db-changes)
      * [CONFIG DB](#config-db)
      * [APP DB](#app-db)
    * [Switch State Service Design](#switch-state-service-design)
      * [Orchestration Agent](#orchestration-agent)
      * [STP Container](#stp-container)
    * [SAI](#sai)
    * [CLI](#cli)
      * [Configuration Commands](#configuration-commands)
      * [Show Commands](#show-commands)
      * [Debug Commands](#debug-commands)
      * [Clear Commands](#clear-commands)
  * [Flow Diagrams](#flow-diagrams)
  * [Serviceability and Debug](#serviceability-and-debug)
  * [Warm Boot Support](#warm-boot-support)
  * [Scalability](#scalability)
  * [Industry standard CLI](#industry-standard-cli)
  * [Unit Test](#unit-test)

# List of Tables

[Table 1: Abbreviations](#table-1-abbreviations)

# Revision

| Rev  |    Date    |      Author      | Change Description |
| :--: | :--------: | :--------------: | ------------------ |
| 0.1  | 05/03/2021 | Sandeep, Praveen | Initial version    |
|      |            |                  |                    |
|      |            |                  |                    |
|      |            |                  |                    |


# About this Manual

This document provides general information about the LLDP MED and other enhancements in SONiC.

# Scope

This document describes the high level design of LLDP feature. 

# Definition/Abbreviation

### Table 1: Abbreviations

| **Term** | **Meaning**                       |
| -------- | --------------------------------- |
| CoS      | Class of service                  |
| DSCP     | Differentiated Service Code point |
| MED      | Media endpoint discovery          |
| PoE      | Power over Ethernet               |
|          |                                   |
|          |                                   |
|          |                                   |
|          |                                   |
|          |                                   |


# 1 Requirement Overview

## 1.1 Functional Requirements


 1. Support LLDP MED voice vlan and dscp marking value for phones to use 

 2. Support LLDP MED Power management functionality

 3. Support configuring the management IP address to be advertised by LLDP

    


## 1.2 Configuration and Management Requirements

This feature will support CLI and REST based configurations.

 1. Support CLI configurations as mentioned in section 3.6.2

    

      


## 1.3 Scalability Requirements

None

## 1.4 Warm Boot Requirements

None


# 2 Functionality

## 2.1 Functional Description

### 2.1.1 LLDP-MED

LLDP-MED supports below TLVs:

Network policy profile -

Allows the device to advertise the voice VLAN information to endpoint devices like VoIP phones. Along with voice VLAN, tagging mode, dot1p CoS  and DSCP values can be sent to the endpoint device.

Power management TLV -

Allows the network device and endpoint device to exchange the power information like how the device is powered on, power priority, power required by the device.

### 2.1.2 LLDP Power negotiation

This supports inline power negotitation using LLDP as per IEEE 802.3at and 802.3bt standard.

### 2.1.3 LLDP Management TLV IP address

By default, LLDP will advertise the management IP address configured in the system over management interface. In case user wants to override this and advertise a specific IP address on an interface then the same can be acheived by using the configuration commands specified in Section 3.6.2.2.1

# 3 Design

## 3.1 Overview

![LLDP](images/LLDP.png)

## 3.2 DB Changes

This section describes the changes made to different DBs for supporting this feature.

### 3.2.1 CONFIG DB

Following config DB schemas are defined for supporting this feature.

### NETWORK_POLICY_PROFILE_TABLE

    ;Stores Network policy configuration for a profile number
    ;Status: work in progress
    key                    = NETWORK_POLICY_PROFILE|number	   ; Network policy profile number
    app_type               = "type"			                       ; app_type can be voice or voice-signalling
    vlan                   = "id"			                         ; vlan id range 1 to 4094
    cos                    = 1*DIGIT				                   ; cos value range 0 to 7
    tagged                 = "tagged/untagged"                 ; vlan is tagged or untagged
    dscp                   = 2*DIGIT                           ; dscp value range 0 to 63


### LLDP_PORT_TABLE

    ;Stores LLDP configuration for the port
    ;Status: work in progress
    key                           = LLDP_PORT|ifname			      ; LLDP port configuration
    enabled                       = "true/false"			          ; LLDP enabled or disabled on port
    mode                          = "receive/transmit"			    ; LLDP mode transmit or receive
    mgmt_ipv4                     = "ipv4 addr"				          ; IPv4 address to be advertised as management IP
    mgmt_ipv6                     = "ipv6 addr"                 ; IPv6 address to be advertised as management IP
    supp_med_network_policy_tlv   = "true/false"                ; Suppress LLDP-MED network policy TLV advertise
    supp_med_power_mgmt_tlv       = "true/false"                ; Suppress LLDP-MED power management TLV advertise
    supp_dot3_power_mgmt_tlv      = "true/false"                ; Suppress LLDP power negotiation TLV (802.3at/bt)
    network_policy                = 1*3DIGIT                    ; Network policy profile number


### 3.2.2 STATE DB

Below tables are updated by LLDP 

### LLDP_MED_POWER_MGMT_TLV_TABLE

```
;Stores LLDP-MED power management TLV received by LLDP neighbor
;Status: work in progress
key                    = LLDP_MED_POWER_MGMT_TLV:ifname				; LLDP-MED power management TLV received on port
power_type             = "type"			  ; Power type pse or pd
power_source           = "source"     ; Power source unknown or primary or backup
power_priority         = "priority"   ; Power priority low, high, critical or unknown
power_level            = "level"      ; Power value in milliwatts
```

### LLDP_8023AT_POWER_MGMT_TLV_TABLE

```
;Stores LLDP 802.3at power management TLV received by LLDP neighbor
;Status: work in progress
key                    = LLDP_8023AT_POWER_MGMT_TLV:ifname		; LLDP-MED power management TLV received on port
power_type             = "type"			  ; Power type pse or pd
power_supported        = "true/false" ; whether Power is supported or not
power_enabled          = "true/false" ; whether Power is enabled or not
pair_control           = "true/false" ; whether pair selection can be controlled or not
power_pairs            = "pairs"      ; power pairs in use signal or spare
class                  = 1DIGIT       ; class 0 to 4
power_type             = 1DIGIT       ; 802.3at type 1 or type 2
power_source           = "source"     ; Power source unknown or primary or backup
power_priority         = "priority"   ; Power priority low, high, critical or unknown
requested_power        = "level"      ; Requested power value in milliwatts
allocated_power        = "level"      ; Allocated power value in milliwatts
```

### LLDP_8023BT_POWER_MGMT_TLV_TABLE

```
;Stores LLDP 802.3bt power management TLV received by LLDP neighbor
;Status: work in progress
key                    = LLDP_8023BT_POWER_MGMT_TLV:ifname		; LLDP-MED power management TLV received on port
power_type             = "type"			  ; Power type pse or pd
power_supported        = "true/false" ; whether Power is supported or not
power_enabled          = "true/false" ; whether Power is enabled or not
pair_control           = "true/false" ; whether pair selection can be controlled or not
power_pairs            = "pairs"      ; power pairs in use signal or spare
class                  = 1DIGIT       ; class 0 to 4
power_type             = 1DIGIT       ; 802.3at type 1 or type 2
power_source           = "source"     ; Power source unknown or primary or backup
power_priority         = "priority"   ; Power priority low, high, critical or unknown
pd_4pid                = "true/false" ; PD supports 4 pair power
requested_power        = "level"      ; Requested power value in milliwatts
allocated_power        = "level"      ; Allocated power value in milliwatts
requested_power_mode_a = "level"      ; Requested mode A power value in milliwatts
requested_power_mode_b = "level"      ; Requested mode B power value in milliwatts
allocated_power_mode_a = "level"      ; Allocated mode A power value in milliwatts
allocated_power_mode_b = "level"      ; Allocated mode B power value in milliwatts
power_status_pd        = 1DIGIT       ; 0 to 3, 3 - 4pair dual sig, 2 - 2pair dual sig, 1 - single sig
power_status_class_mode_a    = 1DIGIT       ; 0 to 7
power_status_class_mode_b    = 1DIGIT       ; 0 to 7
power_status_power_class_ext = 1*2DIGIT     ; 0 to 15
system_setup_pt_ext    = 1DIGIT       ; 0 to 7
system_setup_pd_load   = "true/false"  ; Power demand is electrically isolated or not
auto_class_req         = "true/false" ; PD requests autoclass measurement
power_down_req         = "true/false" ; PD requested power down
power_down_time        = 1*6DIGIT     ; PD requested power down time range 0 to 262143
```



Below tables are updated by PoE module.

### POE_MED_POWER_MGMT_TLV_TABLE

```
;Stores LLDP-MED power management TLV received by LLDP neighbor
;Status: work in progress
key                    = LLDP_MED_POWER_MGMT_TLV:ifname				; LLDP-MED power management TLV received on port
power_type             = "type"			  ; Power type pse or pd
power_source           = "source"     ; Power source unknown or primary or backup
power_priority         = "priority"   ; Power priority low, high, critical or unknown
power_level            = "level"      ; Power value in milliwatts
```

### POE_8023AT_POWER_MGMT_TLV_TABLE

```
;Stores LLDP 802.3at power management TLV received by LLDP neighbor
;Status: work in progress
key                    = LLDP_8023AT_POWER_MGMT_TLV:ifname		; LLDP-MED power management TLV received on port
power_type             = "type"			  ; Power type pse or pd
power_supported        = "true/false" ; whether Power is supported or not
power_enabled          = "true/false" ; whether Power is enabled or not
pair_control           = "true/false" ; whether pair selection can be controlled or not
power_pairs            = "pairs"      ; power pairs in use signal or spare
class                  = 1DIGIT       ; class 0 to 4
power_type             = 1DIGIT       ; 802.3at type 1 or type 2
power_source           = "source"     ; Power source unknown or primary or backup
power_priority         = "priority"   ; Power priority low, high, critical or unknown
requested_power        = "level"      ; Requested power value in milliwatts
allocated_power        = "level"      ; Allocated power value in milliwatts
```

### POE_8023BT_POWER_MGMT_TLV_TABLE

```
;Stores LLDP 802.3bt power management TLV received by LLDP neighbor
;Status: work in progress
key                    = LLDP_8023BT_POWER_MGMT_TLV:ifname		; LLDP-MED power management TLV received on port
power_type             = "type"			  ; Power type pse or pd
power_supported        = "true/false" ; whether Power is supported or not
power_enabled          = "true/false" ; whether Power is enabled or not
pair_control           = "true/false" ; whether pair selection can be controlled or not
power_pairs            = "pairs"      ; power pairs in use signal or spare
class                  = 1DIGIT       ; class 0 to 4
power_type             = 1DIGIT       ; 1 to 4, 802.3at type 1 or type 2, 802.3bt type 3 or type 4
power_source           = "source"     ; Power source unknown or primary or backup
power_priority         = "priority"   ; Power priority low, high, critical or unknown
pd_4pid                = "true/false" ; PD supports 4 pair power (Not required since we are PSE ??)
requested_power        = "level"      ; Requested power value in milliwatts
allocated_power        = "level"      ; Allocated power value in milliwatts
requested_power_mode_a = "level"      ; Requested mode A power value in milliwatts
requested_power_mode_b = "level"      ; Requested mode B power value in milliwatts
allocated_power_mode_a = "level"      ; Allocated mode A power value in milliwatts
allocated_power_mode_b = "level"      ; Allocated mode B power value in milliwatts
power_status_pse       = 1DIGIT       ; 0 to 3, 3 - 4pair dual sig, 2 - 4pair single sig, 1 - 2pair power
pse_power_pair_ext     = 1DIGIT       ; 0 to 3, 3 - both alt, 2 - alt B, 1 - alt A
power_status_class_mode_a    = 1DIGIT       ; 0 to 7
power_status_class_mode_b    = 1DIGIT       ; 0 to 7
power_status_power_class_ext = 1*2DIGIT     ; 0 to 15
system_setup_pt_ext    = 1DIGIT       ; 0 to 7 system power type extension
pse_max_power          = "level"      ; Max available PSE power value in milliwatts
pse_auto_class_support = "true/false" ; PSE supports autoclass or not
pse_auto_class_completed = "true/false" ; PSE autoclass measurement completed

```

## 3.4 LLDP Container



## 3.5 SAI

None

## 3.6 CLI

### 3.6.1 Data Models

### 3.6.2 Configuration Commands

### 3.6.2.1 Global level 

### 3.6.2.1.1 Network policy profile

Create a network policy profile. The profile number can range from 1 to 128.

**[no] network-policy profile number**

Configure the network policy profile parameters using below command -

**[no] {voice | voice-signaling} vlan [** vlan-id [**{cos** value **| dscp** value **}** | **untagged** ] **| [dot1p {cos** value **| dscp** value **}] ]**

**voice** - to select application type voice

**voice-signalling** - to select application type voice

**vlan** vlan-id - specify the voice vlan 

**cos** value - specify L2 priority class of service value for the vlan

**dscp** value - specify the differentiated services code point value

**dot1p** - this option allows to use 802.1 priority tagging with VLAN 0

**untagged** - specify voice traffic to be untagged

Example -

Configuring voice vlan with tagging, cos and dscp values

```
sonic(config)# network-policy profile 1
sonic(conf-network-policy)# voice vlan 100 cos 4 dscp 20
```
Configuring voice vlan with priority tagging (vlan 0), cos and dscp values

```
sonic(config)# network-policy profile 2
sonic(conf-network-policy)# voice vlan dot1p cos 3 dscp 10
```

Configuring voice vlan as untagged 

```
sonic(config)# network-policy profile 3
sonic(conf-network-policy)# voice vlan 100 untagged
```
 ### 3.6.2.2 Interface level 

 ### 3.6.2.2.1 LLDP management ip address

This command can be used to configure an IPv4 or IPv6 management address that will be used to advertise by LLDP on an interface.

**[no] lldp tlv-set { management-address {ipv4|ipv6} ip-address}**

```
sonic(config)# interface Ethernet 0
sonic(conf-if-Ethernet0)# lldp tlv-set management-address ipv4 10.1.1.1
```


### 3.6.2.2.2 LLDP-MED network policy

This command can be used to configure the network policy profile for an interface.

**[no] network-policy <number>**

Example -

Configuring a network policy profile and applying it to an interface

```
sonic(config)# network-policy profile 1
sonic(conf-network-policy)# voice vlan 100 cos 4 dscp 20
sonic(config)# interface Ethernet 0
sonic(conf-if-Ethernet0)# network-policy 1
```

### 3.6.2.2.3 LLDP-MED TLV advertise selection

This command can be used to select whether to advertise the LLDP-MED TLVs or not. By default the LLDP-MED TLVs are advertised. 

**[no] lldp med-tlv-select [network-policy | power-management]** 

Example -

```
sonic(config)# interface Ethernet 0
sonic(conf-if-Ethernet0)# no lldp med-tlv-select network-policy
```

```
sonic(config)# interface Ethernet 0
sonic(conf-if-Ethernet0)# no lldp med-tlv-select power-management
```

### 3.6.2.2.4 LLDP 802.3 power management TLV advertise selection

This command can be used to select whether to advertise the LLDP 802.3at or bt power management TLVs or not. By default this TLV is advertised.

**[no] lldp tlv-select power-management**

Example -

```
sonic(config)# interface Ethernet 0
sonic(conf-if-Ethernet0)# no lldp tlv-select power-management
```



### 3.6.3 Show Commands




### 3.6.4 Debug Commands



### 3.6.5 Clear Commands



### 3.6.6 REST API Support

REST APIs is supported in this release

### 

# 4 Flow Diagrams



# 5 Serviceability and Debug



# 6 Warm Boot Support

Warm boot is not supported

# 7 Scalability




# 8 Unit Test



