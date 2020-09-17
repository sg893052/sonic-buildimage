# Feature Name

## LLDP OC YANG and KLISH CLI Config support

# High Level Design Document

## Rev 0.1

[TOC]

# List of Tables
[Table 1: Abbreviations](#Table 1: Abbreviations)

# Revision

| Rev  | Date      | Author     | Change Description |
| ---- | --------- | ---------- | ------------------ |
| 0.1  | 5/18/2020 | Praveen HM | Initial version    |
|      |           |            |                    |
|      |           |            |                    |


# About this Manual
This document provides OpenConfig YANG and KLISH Config CLI implementation for LLDP feature in SONiC.
# Scope
This document covers the "configuration" commands supported for LLDP based on OpenConfig YANG. 

# Definition/Abbreviation
## Table 1: Abbreviations
| **Term** | **Meaning**                    |
| -------- | ------------------------------ |
| LLDP | Link Layer Discovery Protocol |
|                |       |


# 1 Requirement Overview
## 1.1 Functional Requirements

## 1.2 Configuration and Management Requirements

Support OpenConfig based Config KLISH CLIs, REST/RESTCONF and gNMI UI for LLDP protocol

## 1.3 Scalability Requirements

- Not applicable

## 1.4 Warm Boot Requirements

- LLDP will relearn the neighbors post warm reboot 



# 2 Functionality

# 3 Design

## 3.1 Overview

LLDP being a third party code can be configured via lldpcli or lldpctl. All LLDP IS-CLI/REST requests are updated to the Config DB. LLDPMgr listens to these updates and eventually programs LLDP via lldpcli commands.

## 3.2 DB Changes
This section describes the changes made to different DBs for supporting LLDP config CLIs.
### 3.2.1 CONFIG DB
A new Global LLDP table is added with the following fields

**LLDP**

    key                          = LLDP|GLOBAL            ; 
    enabled                      = "true" | "false"       ;
    hello_time                   = 1*3DIGIT               ; 
    multiplier                   = 1*2DIGIT               ;
    mode                         = "receive" | "transmit" ;
    supp_mgmt_address_tlv        = "true" | "false"       ;
    supp_system_capabilities_tlv = "true" | "false"       ;
    system_name                  = 0*255VCHAR             ;
    system_description           = 0*255VCHAR             ;
    
    Example:
    127.0.0.1:6379[4]> hgetall LLDP|GLOBAL
     1) "enabled"
     2) "true"
     3) "hello_time"
     4) "10"
     5) "multiplier"
     6) "3"
     7) "mode"
     8) "receive"
     9) "supp_system_capabilities_tlv"
    10) "true"
    11) "supp_mgmt_address_tlv"
    12) "true"
    13) "system_name"
    14) "Broadcom"
    15) "system_description"
    16) "Broadcom Sonic"
    127.0.0.1:6379[4]>
    

A new LLDP_PORT table is defined to hold per interface configuration

**LLDP_PORT**

    key       = LLDP_PORT|ifname        ; 
    enabled   = "true" | "false"        ; 
    mode      = "receive" | "transmit"  ;
                                                     
    Example:                                               
    127.0.0.1:6379[4]> hgetall LLDP_PORT|Ethernet4
    1) "enabled"
    2) "false"
    3) "mode"
    4) "transmit"



## 3.3 CLI
### 3.3.1 Data Models
LLDP is already defined and supported in Openconfig YANG model.

https://github.com/openconfig/public/blob/master/release/models/lldp/openconfig-lldp.yang

Supported YANG objects and attributes are as per below tree:

```
module: openconfig-lldp
  +--rw lldp
     +--rw config
     |  +--rw enabled?                      boolean
     |  +--rw hello-timer?                  uint64
     |  +--rw suppress-tlv-advertisement*   identityref
     |  +--rw system-name?                  string
     |  +--rw system-description?           string
     |  +--rw oc-lldp-ext:multiplier?       uint8
     |  +--rw oc-lldp-ext:mode?             lldp-ext-mode-type
     +--rw interfaces
        +--rw interface* [name]
           +--rw name         -> ../config/name
           +--rw config
           |  +--rw name?               oc-if:base-interface-ref
           |  +--rw enabled?            boolean
           |  +--rw oc-lldp-ext:mode?   lldp-ext-mode-type
```



### 3.3.2 Configuration Commands

#### 3.3.2.1 Global Commands

This command can be used to enable LLDP globally. LLDP is enabled by default.

**[no] lldp enable**

Use no command to disable LLDP globally

Eg:

```
sonic(config)# lldp enable
```

Configuring other Global parameters:

**System Name**
The global system name for LLDP is useful for differentiating between devices. By default, the host name is used. By specifying a descriptive system name using this command, user may find it easier to distinguish the device with LLDP. 

**[no] lldp system-name <system_name>**

Use no command to use default hostname

```
sonic(config)# lldp system-name Leaf1
```

**System description**
The default system description can be overriden by configuring system description using this command.

**[no] lldp system-description <system_description>**

Use no command to use default system description

```
sonic(config)# lldp system-description "Broadcom Sonic"
```

**LLDP frame Receiption and Transmission**
By default both transmit and receive of LLDP frames is enabled. This command can be used to configure either in receive only or transmit only mode.

**[no] lldp <receive | transmit>**

Use no command to set to default

```
sonic(config)# lldp receive

sonic(config)# lldp transmit
```

**Hello-Time**
Default transmit frequency of LLDP frames is 30 seconds. This command can be used to configure hello timer from a range of 5 to 254 seconds.

**[no] lldp timer <hello_time>**

Use no command to set hello timer to default

```
sonic(config)# lldp timer 10
```

**Multiplier**
Multiplier value is used to determine the timeout interval (i.e. hello-time x multiplier value) after which LLDP neighbor entry is deleted. By default, four consecutive LLDP hello packets can be missed before removing the neighbor information. This command can be used to set multiplier value from a range of 1 to 10.

**[no] lldp multiplier <multiplier>**

Use no command to set multiplier to default

```
sonic(config)# lldp multiplier 5
```

**Optional LLDP TLVs**
By default, management address and system capabilities TLV are advertised in LLDP frames. This command can be used to enable sending of these TLVs to the Peer.

**[no] lldp tlv-select < system-capabilities | management-address >**

Use no command to disable sending of TLVs in LLDP frames to the Peer 

```
sonic(config)# lldp tlv-select system-capabilities

sonic(config)# no lldp tlv-select management-address
```



#### 3.3.2.1 Interface Level Commands

This command can be used to enable LLDP for a particular interface. LLDP is enabled by default for all the interfaces.

**[no] lldp enable**

Use no command to disable LLDP for that interface

Eg:

```
sonic(config)# interface Ethernet 0
sonic(conf-if-Ethernet0)# lldp enable
```

**LLDP frame Receiption and Transmission**
By default both transmit and receive of LLDP frames is enabled for all interfaces. This command can be used to configure either in receive only or transmit only mode for a particular interface. If Global Mode is set, then Interface level mode doesnt take effect.

**[no] lldp <receive | transmit>**

Use no command to set mode to default

```
sonic(config)# interface Ethernet 0
sonic(conf-if-Ethernet0)# lldp receive
```



### 3.3.3 REST API Support

Below REST URIs are supported

```
<REST-SERVER:PORT>/restconf/data/openconfig-lldp:lldp/config/enabled
<REST-SERVER:PORT>/restconf/data/openconfig-lldp:lldp/config/hello-timer
<REST-SERVER:PORT>/restconf/data/openconfig-lldp:lldp/config/suppress-tlv-advertisement
<REST-SERVER:PORT>/restconf/data/openconfig-lldp:lldp/config/system-name
<REST-SERVER:PORT>/restconf/data/openconfig-lldp:lldp/config/system-description
<REST-SERVER:PORT>/restconf/data/openconfig-lldp:lldp/config/openconfig-lldp-ext:multiplier
<REST-SERVER:PORT>/restconf/data/openconfig-lldp:lldp/config/openconfig-lldp-ext:mode
<REST-SERVER:PORT>/restconf/data/openconfig-lldp:lldp/interfaces/interface={name}/config/enabled
<REST-SERVER:PORT>/restconf/data/openconfig-lldp:lldp/interfaces/interface={name}/config/openconfig-lldp-ext:mode
```

# 4 Flow Diagrams

# 5 Serviceability and Debug

# 6 Warm Boot Support
# 7 Scalability
# 8 Limitations
LLDP code supports many other features like MED and DOT3 and all these are configurable via lldpcli.
However these features are not supported via IS-CLI/REST.

# 9 Unit Test

1) Verify all Global Commands via Klish
2) Verify Config DB and LLDP backend is updated
3) Verify all Interface Level Commands via Klish
4) Verify show lldp neighbor command is uneffected
5) Verify all LLDP configs are retained after Config Save and reboot
6) Verify all REST URIs
