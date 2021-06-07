# Telemetry and Monitoring

## Highlevel Design Document

### Rev 0.1

# Table of Contents

- [Telemetry and Monitoring](#telemetry-and-monitoring)
  - [Highlevel Design Document](#highlevel-design-document)
    - [Rev 0.1](#rev-01)
- [Table of Contents](#table-of-contents)
  - [List of Tables](#list-of-tables)
  - [Revision](#revision)
  - [About This Manual](#about-this-manual)
  - [Scope](#scope)
  - [Definition/Abbreviation](#definitionabbreviation)
    - [Table 1: Abbreviations](#table-1-abbreviations)
- [1 Feature Overview](#1-feature-overview)
  - [1.1 Requirements](#11-requirements)
    - [1.1.1 Functional Requirements](#111-functional-requirements)
    - [1.1.2 Configuration and Management Requirements](#112-configuration-and-management-requirements)
    - [1.1.3 Scalability Requirements](#113-scalability-requirements)
  - [1.2 Design Overview](#12-design-overview)
    - [1.2.1 Basic Approach](#121-basic-approach)
    - [1.2.2 Container](#122-container)
    - [1.2.3 SAI Overview](#123-sai-overview)
- [2 Functionality](#2-functionality)
  - [2.1 Target Deployment Use Cases](#21-target-deployment-use-cases)
  - [2.2 Functional Description](#22-functional-description)
- [3 Design](#3-design)
  - [3.1 Overview](#31-overview)
  - [3.2 DB Changes](#32-db-changes)
    - [3.2.1 CONFIG DB](#321-config-db)
    - [3.2.2 APP DB](#322-app-db)
    - [3.2.3 STATE DB](#323-state-db)
    - [3.2.4 ASIC DB](#324-asic-db)
    - [3.2.5 COUNTER DB](#325-counter-db)
  - [3.3 Daemons](#33-daemons)
  - [3.4 Switch State Service Design](#34-switch-state-service-design)
    - [3.4.1 Orchestration Agent](#341-orchestration-agent)
    - [3.4.2 Other Process](#342-other-process)
  - [3.5 SyncD](#35-syncd)
  - [3.6 SAI](#36-sai)
  - [3.7 CLI](#37-cli)
    - [3.7.1 Data Models](#371-data-models)
    - [3.7.2 Configuration Commands](#372-configuration-commands)
      - [3.7.2.1 Setting up switch-wide TAM attributes](#3721-setting-up-switch-wide-tam-attributes)
      - [3.7.2.2 Setting up Collectors](#3722-setting-up-collectors)
      - [3.7.2.2 Setting up Flow Groups](#3722-setting-up-flow-groups)
      - [3.7.2.2 Setting up Samplers](#3722-setting-up-samplers)
    - [3.7.3 Show Commands](#373-show-commands)
      - [3.7.3.1 Listing the Switch-wide TAM attributes](#3731-listing-the-switch-wide-tam-attributes)
      - [3.7.3.2 Listing the Collectors](#3732-listing-the-collectors)
      - [3.7.3.3 Listing the Samplers](#3733-listing-the-samplers)
      - [3.7.3.4 Listing the Flow groups](#3734-listing-the-flow-groups)
      - [3.7.3.3 Listing the TAM features](#3733-listing-the-tam-features)
    - [3.7.5 Debug Commands](#375-debug-commands)
    - [3.7.6 REST API Support](#376-rest-api-support)
- [4 Flow Diagrams](#4-flow-diagrams)
  - [4.1 Config call flow](#41-config-call-flow)
- [5 Error Handling](#5-error-handling)
  - [CLI](#cli)
- [6 Serviceability and Debug](#6-serviceability-and-debug)
- [7 Warm Boot Support](#7-warm-boot-support)
- [8 Scalability](#8-scalability)
- [9 Unit Test](#9-unit-test)
  - [CLI](#cli)

## List of Tables

[Table 1: Abbreviations](#table-1-abbreviations)

## Revision

| Rev |     Date    |       Author       | Change Description                |
|---|-----------|------------------|-----------------------------------|
| 0.1 | 08/18/2020  | Bandaru Viswanath  | New draft for SONiC TAM Infrastructure            |

## About This Manual

This document provides general information about the TAM infrastructure implementation in SONiC.

## Scope

 SAI community defined a Telemetry and Monitoring specification that defines a unified interface - [TAM](https://github.com/opencomputeproject/SAI/tree/master/doc/TAM) - for provisioning and monitoring various telemetry and monitoring technologies / application.

This document describes the high level design of TAM infrastructure in SONiC. The design is intended to help implement TAM applications that use and share the common TAM infrastructure.

## Definition/Abbreviation

### Table 1: Abbreviations

| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| SAI                      | Switch Abstraction Interface        |
| TAM                      | Telemetry and Monitoring            |

# 1 Feature Overview

For an introduction to TAM, reader is encouraged to consult the SAI specification referred earlier. In general, TAM deals with dataplane telemetry applications that are supported by the silicon. The SONiC NOS is responsible for provisioning the dataplane application behavior, using the appropriate SAI API.

There are many common aspects among the TAM features, as defined in the TAM specification. For example, almost all of them will require a controller to be setup, which is the intended receiver for the telemetry information. Likewise, when the applications may allow a per-flow (or a group of flows) configuration. The TAM infrastructure implements these common aspects so that applications can share the design and implementation without having to re-implement from scratch. More details on provisioning are avaiable in subsequent sections.

## 1.1 Requirements

### 1.1.1 Functional Requirements

1.0 TAM infrastructure must allow configuration of common dataplane specific switch-wide attributes such as switch-identifier to be used in data-export, enterprise-id to be used in IPFIX export records etc.

2.0 TAM infrastructure must allow configuring different types of collectors. It must support - IPv4 or IPv6,  TCP or UDP for the collectors. The collectors shall be named to be referenced by individual features.

3.0 TAM infrastucture must allow a common specification of a flow (or flow-group) definition that can be named and refererenced by individual features.

4.0 TAM infrastucture must allow a common specification of a sampling rate definition on a given interface that can be named and refererenced by individual features.

5.0 TAM infrastucture must support activating and deactivating individual features.

### 1.1.2 Configuration and Management Requirements

The TAM infrastructure supports the new management framework and KLISH CLI for configuration.

### 1.1.3 Scalability Requirements

In general, TAM applications are scaled based on availability of resources in hardware such as ACLs. Application specific scalability constraints may be imposed by corresponding applications.

## 1.2 Design Overview

### 1.2.1 Basic Approach

The TAM infrastructure is newly developed.

### 1.2.2 Container

A container called 'tam' is introducted as a holder for all TAM applications, including the infrastructure.

### 1.2.3 SAI Overview

The SAI TAM spec specifies the TAM APIs to be used to configure the TAM functionality. Please refer to SAI-Proposal-TAM2.0-v2.0.docx in https://github.com/opencomputeproject/SAI/tree/master/doc/TAM for more details.

# 2 Functionality

## 2.1 Target Deployment Use Cases

TAM infrastructure is not an independent feature.

## 2.2 Functional Description

TAM infrastructure is not an independent feature.

# 3 Design

## 3.1 Overview

## 3.2 DB Changes

### 3.2.1 CONFIG DB

TAM\_SWITCH\_TABLE

This table holds the current user configuration for the switch-wide attributes. 


    ;Defines TAM Global configuration in CONFIG_DB

    key                = global         ; Only one instance and 
                                        ; has a fixed key ”global".
    switch-id          = 1 * 5DIGIT    ; Switch Id to be used in reports
    enterprise-id      = 1 * 5DIGIT    ; IPFIX

    Example:
    > keys *TAM_SWITCH*
    1) "TAM_SWITCH_TABLE|global"

    > HGETALL "TAM_SWITCH_TABLE|global"
    1)"switch-id”
    2)54325
    3)"enterprise-id"
    4)22334


TAM\_COLLECTORS\_TABLE

This table holds the current user configuration for the Collectors. 


    ;Defines TAM Collector configuration in CONFIG_DB

    key = name 			            ; name is collector name and should be unique. 
    ipaddress = ipv4_prefix 		; Collector IP address
    port = port_num 		        ; Port Number
    ipaddress-type = “ipv4”/”ipv6” 	; IP type
    protocol = ”TCP"/”UDP”          ; Transport protocol to be used to reach collector
                                    ; UDP is default

    Example: 
    > keys *TAM_COLLECTORS* 
    1) "TAM_COLLECTOR_TABLE|collector1" 

    > HGETALL "TAM_COLLECTORS_TABLE|collector1" 
    1) "ipaddress"
    2) "10.20.2.1"
    3) "port"
    4) "6050"
    5) "ipaddress-type"
    6) "ipv4"
    7) "protocol"
    8) "UDP"

TAM\_FEATURES\_TABLE

This table holds the current user configuration for each of the TAM features. 


    ;Defines TAM Features configuration in CONFIG_DB

    key = name                      ; Feature name and should be among 
                                    ; IFA, DROPMONITOR, TAILSTAMPING
    status = ACTIVE" / INACTIVE"  ; Activate or Turnoff the feature

    Example: 
    > keys *TAM_FEATURES* 
    1)"TAM_FEATURES_TABLE|IFA" 
    2)"TAM_FEATURES_TABLE|DROPMONITOR" 
    3)"TAM_FEATURES_TABLE|TAILSTAMPING" 

    > hgetall "TAM_FEATURES_TABLE|ifa”
    1) "status"
    2) "ACTIVE"

TAM\_SAMPLINGRATE\_TABLE

This table holds sampling rates used across the TAM features.


    ;Defines TAM Sample-Rate configuration in CONFIG_DB

    key = name                  ; Sampler name and should be unique

    sampling-rate = 1 * 5DIGIT  ; Sampling rate. One packet in every ‘rate’ packets 
                                ; will be sampled

    Example: 
    > keys *TAM_SAMPLINGRATE_TABLE* 
    1)"TAM_SAMPLINGRATE_TABLE|aggressive" 
    2)"TAM_SAMPLINGRATE_TABLE|lazy" 

    > hgetall "TAM_SAMPLINGRATE_TABLE|aggressive”
    1) "sampling-rate"
    2) 1000


TAM\_FLOWGROUP\_TABLE
This table holds various flow-group references that are setup to be used with the TAM features.

    ;Defines TAM Flow Group configuration in CONFIG_DB

    key             = name         ; Flowgroup name and should be unique
    id              = 1 * 5DIGIT   ; Unique identification for a flow-group
    table-name      = 1*255VCHAR   ; ACL Table reference          
    
    Example: 
    > keys *TAM_FLOWGROUP_TABLE* 
    1)"TAM_FLOWGROUP_TABLE|websrv1" 
    2)"TAM_FLOWGROUP_TABLE|storagecluster2" 

    > hgetall "TAM_FLOWGROUP_TABLE|storagecluster2"
    1) "id"
    2) 1000
    3) "table-name"
    4) "TAM"


### 3.2.2 APPL DB

TAM\_APPL\_SWITCH\_TABLE

This table holds the current operational switch-wide attributes for the TAM features.

    ;Operational TAM Global Status in STATE_DB

    key                   = global         ; Only one instance and 
                                          ; has a fixed key ”global"
    op-switch-id          = 1 * 5DIGIT    ; Currently used switch-id
    op-enterprise-id      = 1 * 5DIGIT    ; Currently used enterprise-id

    Example:
    > keys *TAM_APPL_SWITCH*
    1) "TAM_APPL_SWITCH_TABLE|global"

    > HGETALL "TAM_APPL_SWITCH_TABLE|global"
    1)”op-switch-id”
    2)54325
    3)”op-enterprise-id"
    4)22334

### 3.2.3 STATE DB

TAM\_STATE\_FEATURES\_TABLE

This table holds the current operational status for the TAM features.


    ;Reflects TAM Features status in STATE_DB
    key = name                      ; Feature name and should be among 
                                    ; IFA, DROPMONITOR, TAILSTAMPING
    op-status = ACTIVE" / INACTIVE" / "UNSUPPORTED" / "INSUFFICIENT_RESOURCES" 
                                    ; current status of the feature

    Example: 
    > keys *TAM_STATE_FEATURES* 
    1)"TAM_STATE_FEATURES_TABLE|IFA" 
    2)"TAM_STATE_FEATURES_TABLE|DROPMONITOR" 
    3)"TAM_STATE_FEATURES_TABLE|TAILSTAMPING" 

    > hgetall "TAM_FEATURES_TABLE|IFA”
    1) "op-status"
    2) "UNSUPPORTED"


### 3.2.4 ASIC DB

N/A

### 3.2.5 COUNTER DB

N/A

## 3.3 Daemons

A new daemon called `tammgrd` is introduced, which is responsible for managing the common attributes for all the TAM features. This daemon listens for changes on the `TAM_SWITCH_TABLE` in the CONFIG_DB and `TAM_STATE_FEATURES_TABLE` in the STATE_DB and effects the changes in global attributes only when all features are inactive. The `TAM_APPL_SWITCH_TABLE` in APPL_DB reflects the currently operational attributes.

This daemon is also responsible for setting the initial/default values for the global attributes in the absense of any user configuration. The default values are derived from the system mac-address.

## 3.4 Switch State Service Design

### 3.4.1 Orchestration Agent

Orchestration Agent determines the support for the individual TAM features based on the underlysing SAI capabilities. The support status is reflected in the SWITCH_TABLE as part of the APPL_DB. Individual TAM features use this status to determine whether the configuration should be procecced or not. Likewise, the Management infrastructure uses this to determine whether to allow the user configuration or not.

### 3.4.2 Other Process

N/A

## 3.5 SyncD

N/A

## 3.6 SAI

The SAI TAM API spec defines all TAM APIs supported in SAI. Please refer to SAI-Proposal-TAM2.0-v2.0.docx in https://github.com/opencomputeproject/SAI/tree/master/doc/TAM for more details.

## 3.7 CLI

### 3.7.1 Data Models

The user facing data model is based on OpenConfig compatible TAM yang model. The backend data model (SONiC YANG) will use the formats in CONFIG_DB, APPL_DB and STATE_DB. See above sections.

### 3.7.2 Configuration Commands

#### 3.7.2.1 Setting up switch-wide TAM attributes

Two switch-wide attributes are supported by the TAM infrastructure. Any changes to the switch-wide attributes are effected only when none of TAM features are active.

| **Attribute**                 | **Description**                         |
|--------------------------|-------------------------------------|
| `switch-id`               | A 32-bit idenitifier that uniquely identifies the switch, and is used in the telemetry reports. When not configured, the last 16-bits from the system mac address are used.        |
| `enterprise-id`            | A 32-bit identifier that is used in the IPFIX telemetry reports. When not configured, the last 16-bits from the system mac address are used.            |

The command syntax for setting up the switch-wide TAM attributes are as follows:

```
sonic (config-tam)# [no] switch-id <number>
sonic (config-tam)# [no] enterprise-id <number>
```

#### 3.7.2.2 Setting up Collectors

A collector is typically a machine reachable from the switch, where the telemetry reports are sent. The collectors are application specific, in the sense that the collectors must understand the data format of the telemetry reports they receive. A collector may be capable of processing reports from one or more applications.

TAM infrastructure allows us to create a collector by specifying the reachability information and associating a reference to this collector. This reference, a name, can be specified as a collector by individual feature configurations.

A collector, while being associated with any TAM application, can't be removed. The `no` command fails citing existing association.

The following collector attribtes are supported.

| **Attribute**                 | **Description**                         |
|--------------------------|-------------------------------------|
| `name`               | A string that uniquely identifies the collector, and will be referrenced from other configurations        |
| `ip-address`               | The IP address of the collector        |
| `protocol`            | Specifies the transport protocol to be used for reaching the collector. Valid values are `"UDP"` and `"TCP"`. When not specified, `"UDP"` is used as default.           |
| `port`               | Port number on which the Collector is listening for the reports.        |

The command syntax for setting up the collectors are as follows:

```
sonic (config-tam)# collector <name> ip <ip-address> port <port-number> [protocol { UDP | TCP }] 

sonic (config-tam)# no collector <name> 
```

#### 3.7.2.2 Setting up Flow Groups

A flow-group is a packet match criterion that defines a set of flows that are of interest. For example, `all packets destined for a the webserver at 10.10.1.1` is an example flow group. Multiple flows can match a given flow-group criterion. Typically, a flow-group is defined with a combination of L2 and L3 header fields, with some fields specified as wild-cards.

TAM infrastructure allows us to create a flow-group by specifying the tuple information and associating a reference to this flow-group from TAM applications. A flow-group can be removed with the `no` form of the command. Flow Groups that are actively referenced in other applications can't be removed.

The following flow-group attribtes are supported. Except the `name` attribute, all the other attributes are optional. When not specified, they default to the equivalent of a wildcard in the packet matching.


| **Attribute**                 | **Description**                         |
|--------------------------|-------------------------------------|
| `name`               | A string that uniquely identifies the flow-group, and will be referrenced from other configurations        |
| `src-mac`            | Source MAC address of the packets belonging to the flow-group |
| `dst-mac`               | Destination MAC address of the packets belonging to the flow-group   |
| `ethertype`            | Ethertype of the packets belonging to the flow-group          |
| `src-ip`               | Source IP address of the packets belonging to the flow-group       |
| `dst-ip`               | Destination IP address of the packets belonging to the flow-group       |
| `l4-src-port`               | Source Port (L4) of the packets belonging to the flow-group       |
| `l4-dst-port`               | Destination Port (L4) of the packets belonging to the flow-group       |
| `protocol`            | Protocol field of the ip-header of the packets belonging to the flow-group  |
| `priority`            | Priority of the flow-group, among the other flow-groups that are created. Range is 1 - 1024, Default value is 100  |


The command syntax for setting up the flow-groups is as follows:

```
sonic (config-tam)# flow-group <name> [src-mac <src_mac>] [dst-mac <dst_mac>] [ethertype <ethertype>] [src-ip <src_ip>] [dst-ip <dst_ip>] [src-l4-port <src_l4_port>] [dst-l4-port <dst_l4_port>] [protocol <protocol>] [priority <priority_value>]

sonic (config-tam)# no flow-group <name> 
```

#### 3.7.2.3 Attaching a Flow Group to an interface

Created flow-groups can be attached to an interface. This is accomplished via the interface mode as shown below. The flow-group can be attached to more than one interface.

```
sonic (config) # interface <Interface Name>
sonic (config-if-EthernetXY)# [no] flow-group <Flow-Group-Name>
```

#### 3.7.2.4 Setting up Samplers

TAM infrastructure supports setting up a sampling configuration and refer to this sampler configuration from the application which support sampling for the traffic.

A sampler, while being associated with any TAM application, can't be removed. The `no` command fails citing existing association.

The following sampling attribtes are supported.

| **Attribute**                 | **Description**                         |
|--------------------------|-------------------------------------|
| `name`               | A string that uniquely identifies the sampler, and will be referrenced from other configurations        |
| `sample-rate`               | Sampling rate, one in every `sample-rate` packets will be sampled      |

The command syntax for setting up the samplers are as follows:

```
sonic (config-tam)# sampler <name>  rate <sample-rate> 

sonic (config-tam)# no sampler <name> 

```

### 3.7.3 Show Commands

#### 3.7.3.1 Listing the Switch-wide TAM attributes

The following command lists all the switch-wide TAM attributes currently in use.

```
sonic # show tam switch
```
An example invocation is as below

```
sonic # show tam switch

TAM Device information
----------------------
Switch ID      : 23456
Enterprise ID  : 1234
```

#### 3.7.3.2 Listing the Collectors

The following command lists the details for all collectors or for a specific collector.

```
sonic # show tam collectors [<collector-name>]
```
Sample usage shown below.

```
sonic # show tam collectors

Name               IP Address       Port    Protocol 
----------------   --------------   ------  --------  
IFA_Col_i19        192.168.78.121   7071    UDP      
MOD_Col_m16        192.168.78.123   7076    UDP       

sonic-cli# show tam collectors IFA_Col_i19
 
Name          : IFA_Col_i19
IP Address    : 192.168.78.121
Port          : 7071
Protocol      : UDP
```

#### 3.7.3.3 Listing the Samplers

The following command lists the details for all samplers or for a specific sampler.

```
sonic # show tam samplers [<sampler-name>]
```
Sample usage shown below.

```
sonic # show tam samplers

Name         Sample Rate
-----------  -----------
sflow_low    1
aggresive    2000

sonic-cli# show tam samplers aggresive
 
Name          : aggresive
Sample Rate   : 2000
```

#### 3.7.3.4 Listing the Flow groups

The following command lists the details for all flow-groups or for a specific flow-group. Note that only explicitly configured tuples are displayed.

```
sonic # show tam flow-groups [<name>]
```
Sample usage shown below.

```
sonic # show tam flow-groups

Flow Group Name    : udp_port_239
   Id              : 4025
   Priority        : 100
   SRC IP          : 10.72.195.23
   DST L4 Port     : 239
   Ingress Intf    : Ethernet20
Packet Count       : 10584
 
Flow Group Name    : udp_port_241
   Id              : 4022
   Priority        : 99
   SRC Port        : 1906
   DST L4 Port     : 241
Packet Count       : 8654367

sonic # show tam flow-groups udp_port_239

Flow Group Name    : udp_port_239
   Id              : 4025
   Priority        : 100
   SRC IP          : 10.72.195.23
   DST L4 Port     : 239
Packet Count       : 10584
```

#### 3.7.3.3 Listing the TAM features

The following command lists the current status for all TAM features or for a specific feature.

```
sonic # show tam features [ { ifa | drop-monitor | tail-stamping }]
```
Sample usage shown below.

```
sonic # show tam features

Name         Status
-----------  ---------
ifa          Active
drop-monitor Active

sonic-cli# show tam features ifa
 
Name          : ifa
Status        : Active
```

### 3.7.5 Debug Commands
N/A

### 3.7.6 REST API Support

#### Yang-based REST API based on the openconfig-tam module defintions

##### Obtaining the status of all TAM feautures on the switch

* Method : GET
* URI :  /restconf/data/openconfig-tam:tam/features-state
* Response format
```json
{
  "openconfig-tam:features-state": {
    "feature": [
      {
        "feature-ref": "string",
        "state": {
          "feature-ref": "string",
          "op-status": "ACTIVE"
        }
      }
    ]
  }
}
```

##### Obtaining a status of a specific TAM feature

* Method : GET
* URI :  /restconf/data/openconfig-tam:tam/features-state/feature={feature-ref}
* Response format
```json
{
  "openconfig-tam:feature": [
    {
      "feature-ref": "string",
      "state": {
        "feature-ref": "string",
        "op-status": "ACTIVE"
      }
    }
  ]
}
```

##### Activating/De-activating a specific TAM feature

* Method : PATCH
* URI : /restconf/data/openconfig-tam:tam/features/feature={feature-ref}
* Data format
```json
{
  "openconfig-tam:feature": [
    {
      "feature-ref": "string",
      "config": {
        "feature-ref": "string",
        "status": "ACTIVE"
      }
    }
  ]
}
```

##### Obtaining TAM switch-wide attributes

* Method : GET
* URI : /restconf/data/openconfig-tam:tam/switch
* Response format
```json
{
  "openconfig-tam:switch": {
    "config": {
      "switch-id": 0,
      "enterprise-id": 0
    },
    "state": {
      "switch-id": 0,
      "enterprise-id": 0,
      "op-switch-id": 0,
      "op-enterprise-id": 0
    }
  }
}
```

##### Setting-up TAM switch-wide attribute : switch-id

* Method : PATCH
* URI :  /restconf/data/openconfig-tam:tam/switch/config/switch-id
* Data format
```json
{
  "openconfig-tam:switch-id": 0
}
```
##### Setting-up TAM switch-wide attribute : enterprise-id

* Method : PATCH
* URI : /restconf/data/openconfig-tam:tam/switch/config/enterprise-id
* Data format
```json
{
  "openconfig-tam:enterprise-id": 0
}
```

##### Resetting the TAM switch-wide attributes to defaults

* Method : DELETE
* URI : /restconf/data/openconfig-tam:tam/switch/config/switch-id 
* URI : /restconf/data/openconfig-tam:tam/switch/config/enterprise-id

##### Obtaining all of the collectors

* Method : GET
* URI : /restconf/data/openconfig-tam:tam/collectors
* Response format
```json
{
  "openconfig-tam:collectors": {
    "collector": [
      {
        "name": "string",
        "config": {
          "name": "string",
          "ip": "string",
          "port": 0,
          "protocol": "UDP",
          "encapsulation": "NONE"
        },
        "state": {
          "name": "string",
          "ip": "string",
          "port": 0,
          "protocol": "UDP",
          "encapsulation": "NONE"
        }
      }
    ]
  }
}
```

##### Obtaining a specific collector

* Method : GET
* URI : /restconf/data/openconfig-tam:tam/collectors/collector={name}
* Response format
```json
{
  "openconfig-tam:collector": [
    {
      "name": "string",
      "config": {
        "name": "string",
        "ip": "string",
        "port": 0,
        "protocol": "UDP",
        "encapsulation": "NONE"
      },
      "state": {
        "name": "string",
        "ip": "string",
        "port": 0,
        "protocol": "UDP",
        "encapsulation": "NONE"
      }
    }
  ]
}
```
##### Creating a collector

* Method : PATCH
* URI : /restconf/data/openconfig-tam:tam/collectors
* Data format
```json
{
  "openconfig-tam:collectors": {
    "collector": [
      {
        "name": "string",
        "config": {
          "name": "string",
          "ip": "string",
          "port": 0,
          "protocol": "UDP",
          "encapsulation": "NONE"
        }
      }
    ]
 }
}
```
##### Deleting a collector

* Method : DELETE
* URI : /restconf/data/openconfig-tam:tam/collectors/collector={name}

##### Obtaining all of the Samplers

* Method : GET
* URI : /restconf/data/openconfig-tam:tam/samplers
* Response format
```json
{
  "openconfig-tam:samplers": {
    "sampler": [
      {
        "name": "string",
        "config": {
          "name": "string",
          "sampling-rate": 0
        },
        "state": {
          "name": "string",
          "sampling-rate": 0
        }
      }
    ]
  }
}
```

##### Obtaining a specific sampler

* Method : GET
* URI :  /restconf/data/openconfig-tam:tam/samplers/sampler={name}
* Response format
```json
{
 "openconfig-tam:sampler": [
    {
      "name": "string",
      "config": {
        "name": "string",
        "sampling-rate": 0
      },
      "state": {
        "name": "string",
        "sampling-rate": 0
      }
    }
 ]
}
```
##### Creating a Sampler

* Method : PATCH
* URI :  /restconf/data/openconfig-tam:tam/samplers
* Data format
```json
{
 "openconfig-tam:samplers": {
    "sampler": [
      {
        "name": "string",
        "config": {
          "name": "string",
          "sampling-rate": 0
        }
      }
    ]
 }
}
```
##### Deleting a Sampler

* Method : DELETE
* URI :  /restconf/data/openconfig-tam:tam/samplers/sampler={name}


##### Obtaining all of the flow-groups

* Method : GET
* URI :  /restconf/data/openconfig-tam:tam/flowgroups
* Response format
```json
{
  "openconfig-tam:flowgroups": {
    "flowgroup": [
      {
        "name": "string",
        "config": {
          "name": "string",
          "id": 0,
          "priority": 0,
          "ip-version": "UNKNOWN"
        },
        "state": {
          "name": "string",
          "id": 0,
          "priority": 0,
          "ip-version": "UNKNOWN"
        },
        "l2": {
          "config": {
            "source-mac": "string",
            "source-mac-mask": "string",
            "destination-mac": "string",
            "destination-mac-mask": "string",
            "ethertype": "string"
          },
          "state": {
            "source-mac": "string",
            "source-mac-mask": "string",
            "destination-mac": "string",
            "destination-mac-mask": "string",
            "ethertype": "string"
          }
        },
        "ipv4": {
          "config": {
            "source-address": "string",
            "destination-address": "string",
            "dscp": 0,
            "protocol": "string",
            "hop-limit": 0
          },
          "state": {
            "source-address": "string",
            "destination-address": "string",
            "dscp": 0,
            "protocol": "string",
            "hop-limit": 0
          }
        },
        "ipv6": {
          "config": {
            "source-address": "string",
            "source-flow-label": 0,
            "destination-address": "string",
            "destination-flow-label": 0,
            "dscp": 0,
            "protocol": "string",
            "hop-limit": 0
          },
          "state": {
            "source-address": "string",
            "source-flow-label": 0,
            "destination-address": "string",
            "destination-flow-label": 0,
            "dscp": 0,
            "protocol": "string",
            "hop-limit": 0
          }
        },
        "transport": {
          "config": {
            "source-port": "string",
            "destination-port": "string",
            "tcp-flags": [
              "string"
            ]
          },
          "state": {
            "source-port": "string",
            "destination-port": "string",
            "tcp-flags": [
              "string"
            ]
          }
        }
      }
    ]
  }
}
```
##### Creating a flow-group

* Method : PATCH
* URI : /restconf/data/openconfig-tam:tam/flowgroups
* Data format
```json
{
  "openconfig-tam:flowgroups": {
    "flowgroup": [
      {
        "name": "string",
        "config": {
          "name": "string",
          "id": 0,
          "priority": 0,
          "ip-version": "UNKNOWN"
        },
        "l2": {
          "config": {
            "source-mac": "string",
            "source-mac-mask": "string",
            "destination-mac": "string",
            "destination-mac-mask": "string",
            "ethertype": "string"
          }
        },
        "ipv4": {
          "config": {
            "source-address": "string",
            "destination-address": "string",
            "dscp": 0,
            "protocol": "string",
            "hop-limit": 0
          }
        },
       "ipv6": {
          "config": {
            "source-address": "string",
            "source-flow-label": 0,
            "destination-address": "string",
            "destination-flow-label": 0,
            "dscp": 0,
            "protocol": "string",
            "hop-limit": 0
          }
        },
        "transport": {
          "config": {
            "source-port": "string",
            "destination-port": "string",
            "tcp-flags": [
              "string"
            ]
          }
        }
      }
    ]
  }
}
```
##### Deleting a flow-group

* Method : DELETE
* URI :  /restconf/data/openconfig-tam:tam/flowgroups/flowgroup={name}

 # 4 Flow Diagrams

## 4.1 Config call flow

All the configuration is stored in the CONFIG_DB by the management framework.

# 5 Error Handling

## CLI

* CLI configuration sanity will be enforced by the CLI handler & CVL and any invalid configuration is rejected. An error is displayed to the user notifying the reason for rejection of the configuration.

# 6 Serviceability and Debug

N/A

# 7 Warm Boot Support

No special handling is done for the warm boot case. The TAM configuration is restored from the Config DB and TAM functionality will continue to work as it is through a warm boot.

# 8 Scalability

N/A

# 9 Unit Test

## CLI

The CLI testcases are included as part of individual feature testcases.

# Broadcom Internal Information : To be removed before publishing externally.

## Revision History

* This document has been derived from the earlier (Arlo+/Buzznik) TAM feature HLDs. The common TAM functionality has been brought into a single place in this HLD.

## Key notes

* The INSUFFICIENT_RESOURCES status for a TAM feature is introduced primarily to cover the need where firmware based telemetry features can't be activated because there are no free embedded-cores. This status remains unused in Buzznik+ timeframe since the number of cores on any slicion are equal/greater than the applications, but will need to be addressed in subsequent releases.

## Specific Limitations

