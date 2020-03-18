# Feature Name
Drop Monitor feature.

# High Level Design Document
#### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definition-abbreviation)
  * [1 Feature Overview](#1-feature-overview)
	  *	[1.1 Requirements](#1_1-requirements)
		  *	[1.1.1 Functional Requirements](#1_1_1-functional-requirements)
		  *	[1.1.2 Configuration and Management Requirements](#1_1_2-configuration-and-management-requirements)
		  *	[1.1.3 Scalability Requirements](#1_1_3-scalability-requirements)
	  * [1.2 Design Overview](#1_2-design-overview)
		  * [1.2.1 Basic Approach](#1_2_1-basic-approach)
		  * [1.2.2 Container](#1_2_2-container)
		  * [1.2.3 SAI Overview](#1_2_3-sai-overview)
  * [2 Functionality](#2-functionality)
	  * [2.1 Target Deployment Use Cases](#2_1-target-deployment-use-cases)
	  * [2.2 Functional Description](#2_2-functional-description)
  * [3 Design](#3-design)
	  * [3.1 Overview](#3_1-overview)
		  * [3.1.1 MODMgr](#3_1_1-modmgr)	
	  * [3.2 DB Changes](#3_2_db-changes)  
		  * [3.2.1 CONFIG DB](#3_2_1-config-db)
		  * [3.2.2 APPL DB](#3_2_2-app-db)
		  * [3.2.3 STATE DB](#3_2_3-state-db)
		  * [3.2.4 ASIC DB](#3_2_4-asic-db)
		  * [3.2.5 COUNTER DB](#3_2_5-counter-db)
	  * [3.3 Switch State Service Design](#3_3-switch-state-service-design)
		  * [3.3.1 Orchestration Agent](#3_3_1-orchestration-agent)
		  * [3.3.2 Other Process](#3_3_2-other-process)
	  * [3.4 Syncd](#3_4-syncd)
	  * [3.5 SAI](#3_5-sai)
	  * [3.6 CLI](#3_6-cli)
		  * [3.6.1 Data Models](#3_6_1-data-models)
		  * [3.6.2 Configuration Commands](#3_6_2-configuration-commands)
		  * [3.6.3 Show Commands](#3_6_3-show-commands)
		  * [3.6.4 Clear Commands](#3_6_4-clear-commands)
		  * [3.6.5 Debug Commands](#3_6_5-debug-commands)
		  * [3.6.6 REST API Support](#3_6_6-rest-api-support)  
  * [4 Flow Diagrams](#4-flow-diagrams)
	  * [4.1 Config Call Flow](#4_1-config-call-flow)
  * [5 Error Handling](#5-error-handling)
  * [6 Serviceability And Debug](#6-serviceability-and-debug)
  * [7 Warm Boot Support](#7-warm-boot-support)
  * [8 Scalability](#8-scalability)
  * [9 Unit Test](#9-unit-test)
  * [10 Internal Design Information](#10-internal-design-information)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |    Date    |     Author      | Change Description |
|:---:|:----------:|:---------------:|--------------------|
| 0.1 | 15/10/2019 | Shirisha Dasari | Initial version    |

# About this Manual
This document provides general information about the drop monitor feature implementation in SONiC.

# Scope
This document describes the high level design of drop monitor feature. 

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term** | **Meaning**                  |
|----------|------------------------------|
| SAI      | Switch abstraction interface |
| TAM      | Telemetry and monitoring     |
| MOD      | Mirror-on-drop               |
| dropMgr  | Drop manager                 |
|          |                              |

# 1 Feature Overview

The drop monitor feature detects and reports specific packet drops on a per-flow basis. The drop reports are sent to a configured external collector in protobuf format.

## 1.1 Requirements
### 1.1.1 Functional Requirements

1.0 Drop monitor allows the user to detect and report specific packet drops on a per flow basis.

1.1 The packet drops are reported to an external collector.  

1.2 In tandem with other TAM features like Threshold, Inband Flow Analyzer etc., drop-monitor can provide valuable information that can be used to design network monitoring applications.

2.0.0 The flow to be monitored for drops is specified via an ACL table and ACL rule.

2.0.0.1 The ACL is bound to the drop monitor feature and defines the match criteria for monitoring drops.

2.0.0.2 A special action, MONITOR_DROPS is used to specify the ACL for drop monitor configuration.
 
2.0.1 A single collector is supported across all drop-monitor flows on the system.

2.0.2 When the first packet of the flow is dropped by the switch, a "Drop-start" report is sent to the collector.

2.0.2.1 Report contains the event type (Drop-start), first 128 bytes of the packet dropped, flow details and the drop reasons for the packet drop. 

2.0.3 "Drop-active" report is sent to the collector when the drop reason for a flow changes.

2.0.3.1 Report contains the event type (Drop-active), flow details and drop reasons for the packet drop. Note that this report is sent only when the reason for the packet drop for this flow changes. 

2.0.3.2 The system_id field of the report carries device-id, a unique switch identifier. This is a configurable parameters. In the absence of a configured device-id, the last 23 bits of system MAC is used as the device-id.

2.0.4 "Drop-stop" report is sent to the collector when the drops cease on the flow. This is determined by the aging interval. If drops are not seen for time t1 > aging interval, a "Drop-stop" report is sent.

2.0.4.1 Report contains the event type (Drop-stop), flow details and the last noted drop reason for packet drops for this flow. 

2.0.5 User can create a flow with a catch-all match criteria to monitor drops on all flows across the switch. 

2.0.6 DropMgr will be a part of the TAM container.

2.0.7 The flow specified (via an ACL) for drop-monitor can be bound to all types of interfaces supported by ACLs in SONiC (i.e physical interfaces, LAG interfaces and VLANs).

2.0.8 Port breakout support is by default since port configuration comes via an ACL.

2.0.9 A sampling rate configuration is supported per flow.

2.0.9.1 Sampling rate determines the number of packets out of 1000 sampled to the drop-monitor application by silicon.

2.0.10 Aging interval configuration is a global configuration for the switch.

2.0.10.1 The drop-monitor application waits for a sampled dropped packet of the flow for aging-interval to determine if drops have stopped on the flow. A "drop-stop" event is sent out to the collector when this happens.

2.0.11 The maximum number of drop monitor flows that can be supported is configurable. This configuration is mandatory for the feature to be functional.

2.0.12 A management IP on the device is mandatory for drop monitor configuration. The IP is used as the SRC IP for reports sent to the collector.

3.0 The drop-monitor feature will be supported on all silicon with appropriate SAI TAM support. The feature will be initially tested out on Broadcom silicon TD3. 

### 1.1.2 Configuration and Management Requirements

The drop monitor feature will support the KLISH CLI framework for configuration commands. REST API and gNMI support will also be available for drop monitor configuration. 

UI support for configuration of drop-monitor flows, aging-interval will be supported. Show command support for the same will also be implemented.

### 1.1.3 Scalability Requirements

The number of drop monitor flows that can be configured is limited by the hardware resources in terms of the ACL resources which in-turn is platform dependent. No restriction is imposed by SONiC on the number of drop monitor flows that can be supported.

## 1.2 Design Overview
### 1.2.1 Basic Approach

The drop monitor feature is a newly developed feature.

### 1.2.2 Container

Drop monitor manager will be a part of the existing TAM container. 

### 1.2.3 SAI Overview

The SAI TAM spec specifies the TAM APIs to be used to configure the drop monitor functionality. Refer section 3.5 for more details on SAI. 

# 2 Functionality
## 2.1 Target Deployment Use Cases

Drop monitoring of specific flows on a switch can be of importance to identify the reasons for packet drops. Appropriate action can be taken to alleviate or stop drops on the flow based upon the information provided by the drop monitor feature on SONiC.

## 2.2 Functional Description

The drop monitor feature detects and reports ingress drops on a per-flow basis to a configured external collector. Dropped packets of the flow are sampled (based upon the sampling rate configuration) to the drop-monitor application.  

Once a flow (matching criteria) is configured for monitoring drops, the drop monitor application reports the following drop events on the flow to the collector in the protobuf format:

  - Drop start: Sent when drops are observed on the flow for the first time. The report contains the event, flow keys, first 128 bytes of the packet and the last observed drop reasons.

  - Drop active: Sent whenever the drop reason on the flow changes. The report contains the event, flow keys, and the drop reasons.

  - Drop stop: Sent when no sampled dropped packets are observed for the aging interval. The report contains event, flow keys and the last observed drop reasons.

The drop reasons supported are silicon specific. The drop reasons supported on Broadcom silicon are available in section 10.

#### Sample CLI and JSON configuration:
     
    ***Example CLI configuration***

    Drop-monitor configuration via KLISH CLI:

    root@sonic:/home/admin# sonic-cli
    sonic# configure terminal 
    sonic(config)# sample s1 rate 10 
    sonic(config)#tam 
    sonic(config-tam)# collector cr1 type ipv4 ip 10.10.10.10 port 9070
    sonic(config-tam)#drop-monitor
    sonic(config-drop-monitor)# feature enable
    sonic(config-drop-monitor)# flow flow1 acl-table A1 acl-rule R1 collector cr1 sample s1 flowgroup-id 1
    sonic(config-drop-monitor)#aging-interval  10

     ACL configuration:

            "ACL_TABLE": {
                "A1": {
                    "policy_desc" : "Drop monitor policy",
                    "stage" : "INGRESS",
                    "type" : "TAM" ,
                    "ports" : "Ethernet20"
                    }
            }
            "ACL_RULE": {
                "A1|R1": {
                    "PRIORITY" : "55",
                    "IP_TYPE" : "ipv4any",
                    "IP_PROTOCOL": "17",
                    "SRC_IP" : "10.10.0.26/32",
                    "DST_IP" : "10.10.1.26/32",
                    "PACKET_ACTION" : "MONITOR_DROPS"
                }
            }

    ***Example JSON configuration***

    {
        "SAMPLE_RATE_TABLE": {
            "s1": {
                "sampling-rate": "10"
            }
        },
        "TAM_COLLECTOR_TABLE": {
            "cr1": {
                "ipaddress": "10.10.10.10",
                "ipaddress-type": "ipv4",
                "port": "9070"
            }
        },
        "TAM_DROP_MONITOR_FEATURE_TABLE": {
            "feature": {
                 "enable": "true"
            }
        },
        "TAM_DROP_MONITOR_FLOW_TABLE": {
            "flow1": {
                "acl-rule-name": "R1",
                "acl-table-name": "A1",
                "collector": "cr1",
                "sample": "s1",
                "flowgroup-id": "1"
            },
        }
        "TAM_DROP_MONITOR_AGING_INTERVAL_TABLE": {
            "aging ": {
                "aging-interval ": "10"
            }
        }
    }

    ***Example REST configuration***

    curl -X POST "https://10.59.143.180/restconf/data/sonic-acl:sonic-acl" -H "accept: application/yang-data+json" -H "authorization: Basic YWRtaW46YnJvYWRjb20=" -H "Content-Type: application/yang-data+json" -d "{ \"sonic-acl:ACL_TABLE\": { \"ACL_TABLE_LIST\": [ { \"aclname\": \"A1\", \"policy_desc\": \"Drop monitor policy\", \"ports\": [ \"Ethernet20\" ], \"stage\": \"INGRESS\", \"type\": \"TAM\" } ] }, \"sonic-acl:ACL_RULE\": { \"ACL_RULE_LIST\": [ { \"IP_PROTOCOL\": 17,\t\t \"IP_TYPE\": \"IPV4ANY\",\t\t \"SRC_IP\": \"10.10.0.26/32\", \"DST_IP\": \"10.10.1.26/32\", \"PACKET_ACTION\": \"MONITOR_DROPS\", \"PRIORITY\": 55, \"aclname\": \"A1\", \"rulename\": \"R1\" } ] }}"


# 3 Design
## 3.1 Overview

![Drop monitor arch](images/dropMonitorArch.png) 

The architecture of the drop monitor feature in SONiC is illustrated in the diagram above.

1. CLI/gNMI/REST interface is used to configure the drop monitor feature functionality. Configuration is saved to CONFIG_DB.
2. DropMonitorMgr picks the configuration from CONFIG_DB.
3. DropMonitorMgr populates the APPL_DB drop monitor tables appropriately.
4. DropMonitorOrch picks the configuration from APPL_DB, interacts with aclOrch for ACL related information.
5. DropMonitorOrch programs the ASIC_DB via SAIREDIS calls.
6. Syncd picks the configuration from ASIC_DB and configures SAI. 
7. When a drop is detected on the flow configured in step 1, a "drop-start" event is sent to the configured collector.
8. When the drop reason on the flow changes, a "drop-active" event is sent to the configured collector.
9. Once drops on the flow have not been seen for configured "aging-interval", a "drop-stop" event is sent to the collector.

## 3.1.1 DropMonitorMgr

The DropMonitorMgr runs in the TAM docker and is used to pass drop monitor configuration arriving from UI to dropMonitorOrch. DropMonitorMgr validates and collates information from CONFIG_DB  tables TAM_COLLECTOR_TABLE, TAM_DROP_MONITOR_FLOW_TABLE, TAM_DROP_MONITOR_AGING_INTERVAL_TABLE and SAMPLE_RATE_TABLE. APPL_DB TAM_DROP_MONITOR_FOW_TABLE is populated with a valid flow configuration once all the required data is available.  

The DropMonitorMgr configures the source IP address to be used in drop reports to the system IP address. 9073 is configured as the source port number to be used in drop reports.

## 3.2 DB Changes
### 3.2.1 CONFIG DB

#### TAM\_DEVICE\_TABLE

    ;Defines TAM device configuration

    key                = device         ; Only one instance and has a fixed key "device".
    deviceid           = 1 * 5DIGIT     ; Uniquely identifies a device on the network to be analyzed

    Example:
    127.0.0.1:6379[4]> keys *TAM_DEVICE*
    1) "TAM_DEVICE_TABLE|device"

    127.0.0.1:6379[4]> HGETALL "TAM_DEVICE_TABLE|device"
    1) "deviceid"
    2) 54325

#### TAM\_COLLECTOR\_TABLE
    
    ;Defines TAM collector configuration.

    key                 = name                        ; collector name and 
                                                        should be unique.
    ipaddress-type      = "ipv4" / "ipv6"             ; Collector IP address type.
    ipaddress           = ipv4_address / ipv6_address ; Collector IP address.
    port                = 1 * 4DIGIT                  ; Collector UDP port number

    Example:
    127.0.0.1:6379[4]> keys *TAM_COLLECTOR*
    1) "TAM_COLLECTOR_TABLE|collector1"

    127.0.0.1:6379[4]> HGETALL "TAM_COLLECTOR_TABLE|collector1"
    1) "ipaddress-type"
    2) "ipv4"
    3) "ipaddress"
    4) 10.10.10.40
    5) "port"
    6) 9070

#### SAMPLE\_RATE\_TABLE
    
    ;Defines sampling configuration.

    key                = sample-name     ; Sample name and should be unique. 
                                       
    sampling-rate      = 1 * 5DIGIT      ; Sampling rate. One packet in every 
                                           ‘rate’ packets will be  
                                           sampled.
  


    Example:
    127.0.0.1:6379[4]> keys *SAMPLE_RATE*
    1) "SAMPLE_RATE_TABLE|sample1"

    127.0.0.1:6379[4]> HGETALL "SAMPLE_RATE_TABLE|sample1"
    1) "sampling-rate"
    2) 100

#### TAM\_DROP\_MONITOR\_FEATURE\_TABLE
    ;Defines TAM drop monitor feature configuration.

    key                 = feature        ; Only one instance with fixed key "feature".
    enable              = "true"/"false" ; Specifies whether DROP MONITOR feature is 
                                         ; enabled or not.

    Example:
    127.0.0.1:6379[4]> keys *DROP_MONITOR_FEATURE*
    1) "TAM_DROP_MONITOR_FEATURE_TABLE|feature"

    127.0.0.1:6379[4]> hgetall TAM_DROP_MONITOR_FEATURE_TABLE|feature  
    1) "enable" 
    2) "true"

#### TAM\_DROP\_MONITOR\_FLOW\_TABLE
    ;Defines a TAM drop monitor flow configuration.

    key                 = name           ; Flow name. Should be unique.
    acl-table-name      = table-name     ; Parameter to map acl table to the
                                           flow.
    acl-rule-name       = rule-name      ; Parameter to map acl rule to the
                                           flow.
    collector-name      = collector-name ; Map collector 
                                           to the flow.
    flowgroup-id        = flowgroup-id   ; Flow group ID. Must be unique per flow.
    sample              = sample-name    ; Map sampling rate configuration to 
                                           flow.
                                           One packet in every ‘sampling-rate’ 
                                           packets will be sampled.
                                           Range is 1 to 10000. 

    Example:
    127.0.0.1:6379[4]> keys *TAM_DROP_MONITOR_FLOW*
    1) "TAM_DROP_MONITOR_FLOW_TABLE|flow1"

    127.0.0.1:6379[4]> HGETALL "TAM_DROP_MONITOR_FLOW_TABLE|flow1"
    1) "acl-table-name"
    2) "table1"
    3) "acl-rule-name"
    4) "rule1"
    5) "collector-name"
    6) "collector1"
    7) "sample"
    8) "sample1"
    9) "flowgroup-id"
    10) "1"

#### TAM\_DROP\_MONITOR\_AGING\_INTERVAL\_TABLE

    ;Defines TAM drop monitor aging interval configuration.

    key                 = aging                 ; key is “aging” and is a 
                                                  fixed key.
    aging-interval      = 1 * 4DIGIT            ; Aging interval in milliseconds. 
                                                  If no packets 
                                                  of the flow have been dropped 
                                                  for an 
                                                  interval = aging-interval, a 
                                                  “drop-stop” report is sent to 
                                                  the collector for the flow.
                                                  

    Example:
    127.0.0.1:6379[4]> keys *TAM_DROP_MONITOR_AGING_INTERVAL*
    1) "TAM_DROP_MONITOR_AGING_INTERVAL_TABLE|aging"

    127.0.0.1:6379[4]> HGETALL "TAM_DROP_MONITOR_AGING_INTERVAL_TABLE|aging"
    1) "aging-interval"
    2) "5"

### 3.2.2 APPL DB

#### TAM\_DROP\_MONITOR\_FEATURE\_TABLE

    ;Defines TAM DROP MONITOR feature configuration

    key                = feature        ; Only one instance and has a fixed key "feature".
    deviceid           = 1 * 5DIGIT     ; Uniquely identifies a device on the network. 

    Example:
    127.0.0.1:6379> keys *TAM_DROP_MONITOR_FEATURE*
    1) "TAM_DROP_MONITOR_FEATURE_TABLE:feature"

    127.0.0.1:6379> HGETALL "TAM_DROP_MONITOR_FEATURE_TABLE:feature"
    1) "deviceid"
    2) 54325

#### TAM\_DROP\_MONITOR\_FLOW\_TABLE

    ;Defines a TAM drop monitor flow configuration.

    key            = name                        ; Flow name. Should be 
                                                   unique
    acl-table-name = table-name                  ; Parameter to map acl table to 
                                                   the flow.
    acl-rule-name  = rule-name                   ; Parameter to map acl rule to 
                                                   the flow.
    dst-ipaddress-type = "ipv4" / "ipv6"         ; Collector IP address type.
    dst-ipaddress  = ipv4_address / ipv6_address ; Collector IP address.
    dst-port       = 1 * 4DIGIT                  ; Collector UDP port number
    src-ipaddress  = ipv4_address / ipv6_address ; Source IP address to be used in the drop 
                                                   event reports sent to collector.
    src-port       = 1 * 4DIGIT                  ; Source port number to be used in the 
                                                   drop event reports sent to the 
                                                   collector.
    sampling-rate  = 1 * 5DIGIT                  ; Sampling rate. 1 out of “rate”
                                                   dropped packets of flow are 
                                                   sampled for processing.
    aging-interval = 1 * 5DIGIT                  ; Optional. Aging interval for 
                                                   drop-                                                  
                                                   monitoring in milliseconds. Determines  
                                                   the interval for which the system 
                                                   waits to declare that a flow is 
                                                   no longer being dropped.
    flowgroup-id   = flowgroup-id                ; Flow group ID. Must be unique per flow.

    Example:
    127.0.0.1:6379> keys *TAM_DROP_MONITOR*
    1) "TAM_DROP_MONITOR_FLOW_TABLE:flow1"

    127.0.0.1:6379> HGETALL "TAM_DROP_MONITOR_FLOW_TABLE:flow1"
    1) "acl-table-name"
    2) "table1"
    3) "acl-rule-name"
    4) "rule1"
    5) "ipaddress-type"
    6) "ipv4"
    7) "dst-ipaddress"
    8) "10.10.10.40"
    9) "dst-port"
    10) "9070"
    11) "src-ipaddress"
    12) "10.10.10.20"
    13) "src-port"
    14) "9070"
    15) "sampling-rate"
    16) "1000"
    17) "aging-interval"
    18) "10"
    17) "flowgroup-id"
    18) "1"

### 3.2.3 STATE DB
N/A

### 3.2.4 ASIC DB
TBD


### 3.2.5 COUNTERS DB
N/A


## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent

A new orchestration agent class, DropMonitorOrch is added to convert the incoming drop monitor configuration from APPL_DB to ASIC_DB configuration. DropMonitorOrch subscribes to the TAM_DROP_MONITOR_FLOW_TABLE and TAM_DROP_MONITOR_AGING_INTERVAL_TABLE of APPL_DB and converts the incoming flow configuration into drop-monitor SAI configuration.

DropMonitorOrch maintains data pertaining to all the currently configured flows and the associated TAM object bindings. TAM object bindings are re-used wherever possible.

Interaction with aclOrch is required to retrieve the SAI object ID associated with the drop-monitor flow ACL rule.  

Drop monitor uses sampling rate configuration for sampling the dropped packets of the flow. The sampling configuration in terms of rate is converted into a SAI sample packet object and provided to the TAM drop-monitor object for configuration.

### 3.3.2 Other Process 

N/A

## 3.4 SyncD

N/A

## 3.5 SAI

The SAI TAM API spec defines all TAM APIs supported in SAI. The following diagram depicts the SAI objects created for drop-monitor configuration and their co-relation.

![TAM object model](images/TAM.png)

## 3.6 CLI
### 3.6.1 Data Models

The yang model for Drop monitor feature CLI commands is provided below:

module sonic-tam-drop-monitor {
    namespace "http://github.com/Azure/sonic-tam-drop-monitor";
    prefix drop-monitor;

    import ietf-yang-types {
            prefix yang;
    }

    import sonic-common {
            prefix scommon;
    }

    import sonic-extension {
            prefix sonic-ext;
    }

    organization
        "SONiC";

    contact
        "SONiC";

    description
        "SONIC TAM DROP MONITOR YANG";

    revision 2019-11-28 {
        description
            "Initial revision.";
    }

    container sonic-tam-drop-monitor {

        container TAM_DROP_MONITOR_FEATURE_TABLE {

            list TAM_DROP_MONITOR_FEATURE_TABLE_LIST {
                key "name";

                leaf name {
                    type enumeration {
                        enum feature;
                    }
                }

                leaf enable {
                    type boolean;
                }
            }
        }
        container TAM_DROP_MONITOR_AGING_INTERVAL_TABLE {

                list TAM_DROP_MONITOR_AGING_INTERVAL_TABLE_LIST {
                        key "name";

                        leaf name {
                                type enumeration {
                                        enum aging;
                                }
                        }

                        leaf aging-interval {
                                type uint16 {
                                        range "1..30" {
                                                error-message "Drop monitor aging interval is out of range";
                                                error-app-tag drop-monitor-aging-invalid;
                                        }
                                }

                                default 3;
                        }
                }
        }

        container SAMPLE_RATE_TABLE {

                list SAMPLE_RATE_TABLE_LIST {
                        key "name";

                        leaf name {
                                type string {
                                        pattern '[a-zA-Z0-9]{1}([-a-zA-Z0-9_]{0,71})';
                                        length 1..72;
                                }
                        }

                        leaf sampling-rate {
                                type uint16 {
                                        range "1..65535"{
                                                error-app-tag "Invalid Drop Monitor Sampling Rate.";
                                        }
                                }
                        }
                }
        }

        container TAM_DROP_MONITOR_FLOW_TABLE {

            list TAM_DROP_MONITOR_FLOW_TABLE_LIST {
                key "name";

                leaf name {
                        type string {
                                pattern '[a-zA-Z0-9]{1}([-a-zA-Z0-9_]{0,71})';
                                length 1..72;
                        }
                }

                leaf acl-table-name {
                        mandatory true;
                        type string;
                }

                leaf acl-rule-name {
                        mandatory true;
                        type string;
                }

                leaf sample {
                        type string {
                                pattern '[a-zA-Z0-9]{1}([-a-zA-Z0-9_]{0,32})';
                                length 1..32;
                        }
                }

                leaf collector-name {
                        type string {
                                pattern '[a-zA-Z0-9]{1}([-a-zA-Z0-9_]{0,32})';
                                length 1..32;
                        }
                }

                leaf flowgroup-id {
                        type uint32 {
                                range "1..655356"{
                                        error-app-tag "Invalid Flow Group Identifier.";
                                }
                        }
                }

                container state {
                        sonic-ext:db-name "COUNTERS_DB";
                        sonic-ext:key-delim ":";

                        config false;
                        description "State data for TAM INT IFA FS FLOW";


                        leaf MATCHED_PACKETS {
                                type yang:counter64;
                                default 0;
                        }

                        leaf MATCHED_OCTETS {
                                type yang:counter64;
                                default 0;
                        }
                }

            }
        }
    }
}


### 3.6.2 Configuration Commands

The configuration commands are split across TAM and drop-monitor modes in CLI. TAM mode hosts common TAM configuration like collector configuration.

1.  [no] collector <name> type <ipv4/ipv6> ip <collector_ip> port <collector_port>

This command is used to configure the external collector IP address and port. Drop reports are sent by the configured external collector in protobuf format by the silicon.

    sonic(config)# tam 
    sonic(config-tam)# collector cr1 type ipv4 ip 10.10.10.10 port 9070

    sonic(config-tam)# no collector cr1
      
2.  [no] device-id <id>

This command is used to configure the device-id of the switch. In the absence of this configuration, the last 23 bits of system MAC are used as the device-id.

    sonic(config)# tam 
    sonic(config-tam)# device-id 54321

    sonic(config-tam)# no device-id
      
#### Drop-monitor mode

A new mode has been introduced within TAM mode for all drop-monitor feature configuration:
 
     sonic(config-tam)# drop-monitor
     sonic(config-drop-monitor)#

1. feature <enable/disable>

This command is used to enable/disable the drop monitor feature functionality.
  
     sonic(config-drop-monitor)# feature enable

     sonic(config-drop-monitor)# feature disable
           
2. [no] flow <flow-name> acl-table <acl-table-name> acl-rule <acl-rule-name> collector <collector-name> sample <sample-name> flowgroup-id <id>

This command is used to configure a drop-monitor flow. The CLI command associates a flow configuration (specified by the ACL table and ACL rule) with a collector and sampling configuration. 

      sonic(config-drop-monitor)#flow f1 acl-table a1 acl-rule r1 collector cr1 sample s1 flowgroup-id 88
      
      sonic(config-drop-monitor)# no flow f1

3. [no] aging-interval <interval>

This command is used to configure an aging-interval for drop-monitor flows configured in the system. The aging interval determines how long the system waits before it decides that drops have ceased on a flow.  

      sonic(config-drop-monitor)#aging-interval <interval>
      
      sonic(config-drop-monitor)#no aging-interval

#### Global mode

1. [no] sample <sample-name> rate <sampling-rate>.

In the global configuration mode, a new CLI has been added for accepting sampling configuration. This CLI is used to create a sample session that co-relates a sampling rate with a session name. The sample session is identified by the name and can be used by multiple features to indicate sampling configuration. Sampling-rate specifies how many packets out of 1000 need to be sampled for processing.

     sonic(config)#sample s1 rate 100 
     
     sonic(config)#no sample s1  

#### Max flows

1. config tam-drop-monitor max-flows <> 

This CLI is used to configure the maximum flows that can be supported for drop monitor feature. A reboot of the device is mandatory post configuration of this command for the configuration to take effect. This configuration is mandatory for feature to be functional. 

     root@leaf1:/home/admin# config tam-drop-monitor max-flows 8192
     Device settings may be modified. Reboot/reload config to apply this change.  continue? [y/N]: 
  
### 3.6.3 Show Commands

1. Show tam collector <all/collector-name>

This command is used to show all the currently configured TAM collectors.


    sonic# show tam collector all
    ------------------------------------------------------------------------------------------------
    NAME           IP TYPE        IP ADDRESS     PORT           
    ------------------------------------------------------------------------------------------------
    cr1            ipv4           10.10.10.2     9070           

2. show tam drop-monitor supported

This command is used to determine if drop-monitor feature functionality is supported.

    sonic# show tam drop-monitor supported 
    Feature Supported      : True


3. show tam drop-monitor flow <all/flow-name>

This command is used to show information about all the configured flows or a specific flow identified by flow-name.

    sonic# show tam drop-monitor flow all
    ------------------------------------------------------------------------------------------------
    FLOW           ACL TABLE      ACL RULE        COLLECTOR       SAMPLE          FLOWGROUP ID   
    ------------------------------------------------------------------------------------------------
    f1             a1             r1              cr1             s1              1              

3. show tam drop-monitor aging-interval

This command is used to show the system-level drop-monitor aging interval configured. 

    sonic#  show tam drop-monitor aging-interval
    Aging interval :  3 seconds

4. show tam drop-monitor statistics <all/flow-name>

This command is used to show per flow statistics for drop-monitor. These statistics indicate the number of packets matching the flow.

    sonic# show tam drop-monitor statistics all
    ------------------------------------------------------------------------------------------------
    FLOW     ACL RULE            ACL TABLE          PACKETS             BYTES
    ------------------------------------------------------------------------------------------------
    F1          R1                  T1                 0                  0

5. show sample <all/sample-name>

This command is used to show per sample configuration. 

    sonic# show sample all
    ------------------------------------------------------------------------------------------------
    SAMPLE NAME    SAMPLE RATE 
    ------------------------------------------------------------------------------------------------
     s1             20                 

6. show tam device-id

This command is used to show the configured device-id.

    sonic# show tam device 
    ------------------------------------------------------------------------------------------------
    TAM Device Information
    ------------------------------------------------------------------------------------------------
    device-id: 2345

### 3.6.4 Clear commands


### 3.6.5 Debug Commands

Internal debug counters tracking internal processing and SAI errors will be maintained at the OrchAgent. These counters will be available via the "show debug dropmonitororch" command as a part of the debug framework.

    root@sonic:/home/admin# show debug dropmonitororch

### 3.6.6 REST API Support
TBD

# 4 Flow Diagrams
## 4.1 Config call flow 

![Config call flow](images/ConfigCallFlow.jpg)

# 5 Error Handling

## CLI

CLI handlers will validate incoming parameters and return an error accordingly. Drop monitor feature support on the platform will be determined to decide if the feature configuration must get accepted/rejected.  

## DropMgr

Any mismatch in the CONFIG_DB and APPL_DB schema for drop-monitor flows will be flagged/logged appropriately.

## DropMonitorOrch

SAI errors will be captured via the error handling framework and handled appropriately. All errors arising out of processing of incoming drop-monitor flow configuration will be captured via syslogs.

# 6 Serviceability and Debug

Logging of errors and invalid parameters will be done at each level allowing a hierarchical view of failures arising out of mis-configuration or system errors. Please refer to section 5 and 3.6.5 for details.

# 7 Warm Boot Support

Warm boot will be supported for drop monitor feature. The drop monitor configuration will remain intact across a warm boot. 

# 8 Scalability

N/A

# 9 Unit Test

## CLI

1. Verify if feature support is validated in CLI handlers while configuring drop monitor flow CLIs.
2. Verify that CLI handlers throw errors for drop monitor config CLIs when the underlying platform does not support drop monitor feature.
3. Verify that the input parameters provided to CLI are validated for ranges and validity.
4. Verify that the successful execution of CLI results in a CONFIG_DB entry for the flow configuration.
5. Verify the schema of TAM\_DROP\_MONITOR\_FLOW\_TABLE in CONFIG_DB when drop monitor flow CLI is executed successfully.
6. Verify that the aging-interval can be configured independently of the drop monitor flow configuration.
7. Verify the schema of TAM\_DROP\_MONITOR\_AGING\_INTERVAL\_TABLE in CONFIG_DB on successful execution of aging-interval config CLI.
8. Verify that no entry is populated in CONFIG_DB when CLI execution fails.
9. Verify sampling configuration CLI and schema in CONFIG_DB.
10. Verify that no command of config CLIs can be executed successfully.
11. Verify that no config commands clear out the CONFIG_DB entries added by config commands appropriately.
12. Verify that show commands show up the configured flows appropriately.
13. Verify that show commands show the currently configured collectors and sampling rates.
14. Verify that show commands show up the currently configured aging interval.
15. Verify that device-id can be configured and retrieved successfully via config commands.

## REST/gNMI

1. Verify if REST and gNMI interfaces are supported.
2. Verify if feature can be configured via the REST interface appropriately.
3. Verify if feature can be configured via gNMI interface.
4. Verify if configuration and status can be retrieved via REST interface.
5. Verify if configuration and status can be retrieved via gNMI interface. 

## DropMgr

1. Verify that feature support is validated at manager before processing CONFIG_DB changes on bootup.
2. Verify that dropmgr does not populate an APPL_DB entry with incomplete configuration in CONFIG_DB.
3. Verify that dropmgr only programs the APPL_DB entry for a flow only when complete configuration of collector, ACL and sample is available in CONFIG_DB.
4. Verify that dropmgr deletes and re-creates an APPL_DB whenever a change is made in the CONFIG_DB drop monitor associated tables.
5. Verify that dropmgr handles deletion of drop monitor configuration in CONFIG_DB.
6. Verify that dropmgr handles changes in aging-interval configuration by updating all currently configured flow entries in APPL_DB.
7. Verify that dropmgr is able to handle multiple flow configurations in CONFIG_DB.
8. Verify that dropmgr logs any errors arising out of processing of CONFIG_DB data.
9. Verify that dropmgr deletes all flows in APPL_DB when a change in device-id is seen in configuration.

## DropMonitorOrch

1. Verify that incoming APPL_DB drop monitor configuration is handled appropriately (create/delete).
2. Verify that multiple notification on APPL_DB can be handled by the orch agent.
3. Verify that a drop monitor flow configuration with complete data can be configured successfully in ASIC_DB.
4. Verify that all required TAM objects can be successfully created on a drop monitor flow config event on APPL_DB.
5. Verify that existing TAM objects for collector, transport, report are re-used as appropriate.
6. Verify that existing TAM objects for Mod and TAM are re-used wherever appropriate.
7. Verify that the TAM object can be bound to the switch/port successfully.
8. Verify that deletion of drop monitor configuration is handled successfully.
9. Verify that deletion of drop monitor flow configuration results in deletion of relevant TAM objects.
10. Verify that a TAM object is not deleted if it is currently in use by another flow configuration.
11. Verify that orch can roll back configuration in case of an error in the sequence of TAM object creation.
12. Verify that orch logs all errors arising out of parameter validation.
13. Verify that orch logs all errors arising out of SAI API failure. 
14. Verify that appropriate debug counters are updated on erroneous conditions.
15. Verify that orch is able to delete a flow configuration successfully.
16. Verify that orch is able to handle changes on an existing flow configuration.

## Functional Tests
1. Verify that drop monitor flow configuration executed successfully results in appropriate ASIC_DB configuration and HW configuration.
2. Verify that once configuration is successful, the configuration can be retrieved via the show commands properly.
3. Verify that with successful configuration, a drop condition can be simulated and traffic matching a flow can be sent.
4. Verify that traffic matching a flow leading to a drop caused by configuration or the flow itself (invalid TTL etc.) results in a "Drop-start" report being received at the collector when the traffic is started.
5. Verify that when the flow is changed to be dropped due to a different reason, a "Drop-active" report is received at the collector.
6. Verify that when the flow is stopped at TG, after the aging interval, a "Drop-stop" report is seen at the collector.
7. Verify that multiple flows being dropped in the system at the same time lead to multiple reports sent to the collector. The reports could be clubbed for multiple flows.
8. Verify that when a flow is re-started after stopping, the sequence of "Drop-start", "Drop-active" and "Drop-stop" reports is seen at the collector.
9. Validate the contents of the "Drop-start" report received at the collector.
10. Validate the contents of the "Drop-active" report received at the collector.
11. Validate the contents of the "Drop-stop" report received at the collector.
12. Validate that no 2 "Drop-start" or "Drop-stop" reports are received sequentially for the same flow.
13. Verify each of the 11 drop reasons marked as to be validated in section 2.2 with appropriate error conditions.

## Negative test cases
1. Verify that orch logs all errors arising out of parameter validation.
2. Verify that orch logs all errors arising out of SAI API failure. 
3. Verify that CLI handlers throw errors for drop monitor config CLIs when the underlying platform does not support drop monitor feature.
4. Verify that multiple start/stop of flows does not lead to out-of-sync drop reports to be sent by the collector.

# 10 Internal Design Information

Broadcom silicon TD3 supports drop-monitoring functionality via a special firmware running on an R5 ARM core within the silicon. SAI configures the SDK and R5 ARM core for monitoring drops on configured flows and report them via protobuf reports to an external collector. The R5 ARM core monitors the drops on a flow, collates information about drop events and sends the protobuf reports to the configured external collector.

Three types of drop events are sent by the R5 core for a flow:

  - Drop start: Sent when drops are observed on the flow for the first time. The report contains the event, flow keys, and the last observed drop reasons.

  - Drop active: Sent whenever the drop reason on the flow changes. The report contains the event, flow keys, and the drop reasons.

  - Drop stop: Sent when no sampled dropped packets are observed for the aging interval. The report contains event, flow keys and the last observed drop reasons.

The drop reasons supported are as below:-


| Drop reasons                  | Drop reasons                | Drop reasons               | Drop reasons     | Drop reasons        | Drop reasons            |
|-------------------------------|-----------------------------|----------------------------|------------------|---------------------|-------------------------|
| L3_SOURCE_MISS                | L3_DEST_MISS                | MCAST_MISS                 | IP_MCAST_MISS    | UNKNOWN_VLAN        | L3_HEADER_MISMATCH      |
| DOS_ATTACK                    | MARTIAN_ADDR                | TUNNEL_ERROR               | PARITY_ERROR     | L3_MTU_FAIL         | HIGIG_HDR_ERROR         |
| MCAST_IDX_ERROR               | CLASS_BASED_MOVE            | L3_ADDR_BIND_FAIL          | MPLS_LABEL_MISS  | MPLS_INVALID_ACTION | MPLS_INVALID_PAYLOAD    |
| TUNNEL_OBJECT_VALIDATION_FAIL | MPLS_SEQUENCE_NUMBER        | L2_NON_UNICAST_MISS        | NIV_PRIO_DROP    | NIV_RPF_FAIL        | UNKNOWN_SUBTENDING_PORT |
| TUNNEL_ADAPT_LOOKUP_MISS      | PACKET_FLOW_SELECT_MISS     | TUNNEL_DECAP_ECN_ERROR     | FAILOVER_DROP    | OTHER_LOOKUP_MISS   | INVALID_TPID            |
| TUNNEL_TTL_ERROR              | MPLS_ILLEGAL_RESERVED_LABEL | L3_HEADER_ERROR            | L2_HEADER_ERROR  | TTL1                | TTL                     |
| FCOE_ZONE_CHECK_FAIL          | IPMC_INTERFACE_MISMATCH     | MPLS_TTL                   | MPLS_UNKNOWN_ACH | OAM_ERROR           | L2_GRE_SIP_MISS         |
| L2_GRE_VPNID_MISS             | BFD_ERROR                   | CONGESTION_CNM_PROXY_ERROR | VXLAN_SIP_MISS   | VXLAN_VPNID_MISS    | NIV_INTERFACE_MISS      |
| NIV_TAG_INVALID               | NIV_TAG_DROP                | NIV_UNTAG_DROP             | TRILL_INVALID    | TRILL_MISS          | TRILL_RPF_FAIL          |
| TRILL_TTL                     | NAT_ERROR                   | TCP_UDP_NAT_MISS           | ICMP_NAT_MISS    | NAT_FRAGMENT        |                         |

The drop reasons qualified on SONiC are listed below:

| **Drop Reasons** | **Description**                  |
|------------------|----------------------------------|
| L3_DEST_MISS     | Missing L3 route/host entry      |
| UNKNOWN_VLAN     | Vlan of packet unknown           |
| MARTIAN_ADDR     | Packet with source IP=0.0.0.0    |
| DOS_ATTACK       | Packet with SIP=DIP              |
| L3_MTU_FAIL      | MTU > configured MTU             |
| L3_ADDR_BIND_FAIL| L3 Packet with incorrect SMAC    |
| INVALID_TPID     | Packet with an invalid TPID      |
| L3_HEADER_ERROR  | Error in L3 header               |
| TTL1             | Packet with TTL=0                |
| TTL              | Packet with TTL=1                |
| PARITY_ERROR     | Parity error                     |

The protobuf format for the reports is provided below:

    /*
     * Copyright (c) 2017 Broadcom. The term "Broadcom" refers
     * to Broadcom Limited and/or its subsidiaries.

     * Licensed under the Apache License, Version 2.0 (the "License");
     * you may not use this file except in compliance with the License.

     * You may obtain a copy of the License at
     * http://www.apache.org/licenses/LICENSE-2.0

     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

    syntax = "proto2";

    message Event {
        required uint64 timestamp               = 1;

        oneof EventType {
            MirrorOnDrop mod_event              = 2;
            FlowLearning flow_event             = 3;
            RouteChangeDetection rcd_event      = 4;
            NetworkLatencyMonitoring nlm_event  = 5;
        }
    }

    message MirrorOnDrop {
        enum MOD_STATE {
            DROP_INVALID = 0;
            DROP_START   = 1;
            DROP_ACTIVE  = 2;
            DROP_STOP    = 3;
        }

        optional MOD_STATE mod_state       = 1;
    }

    message FlowLearning {
        enum FL_STATE {
        	FL_INVALID      = 0;   
	        FL_LEARN        = 1;
	        FL_AGING        = 2;
	        FL_EXPORT       = 3;
	        FL_TABLE_FULL   = 4; /* Atomic event, no other information need to be send */
        }

        optional FL_STATE fl_state    = 1;
    }

    message RouteChangeDetection {
        optional uint32 dummy           = 1;
    }

    message NetworkLatencyMonitoring {
        optional uint32 dummy           = 1;
    }

    message Flow {
        enum DROP_REASON {
	        DROP_INVALID                  = 0;  
         	L3_SOURCE_MISS                = 1;
	        L3_DEST_MISS                  = 2;
	        MCAST_MISS                    = 3;
	        IP_MCAST_MISS                 = 4;
	        UNKNOWN_VLAN                  = 5;
	        L3_HEADER_MISMATCH            = 6;
	        DOS_ATTACK                    = 7;
	        MARTIAN_ADDR                  = 8;
	        TUNNEL_ERROR                  = 9;
	        PARITY_ERROR                  = 10;  
	        L3_MTU_FAIL                   = 11;
	        HIGIG_HDR_ERROR               = 12;
            MCAST_IDX_ERROR               = 13;
            CLASS_BASED_MOVE              = 14;
            L3_ADDR_BIND_FAIL             = 15;
	        MPLS_LABEL_MISS               = 16;
	        MPLS_INVALID_ACTION           = 17;
	        MPLS_INVALID_PAYLOAD          = 18;
	        TUNNEL_OBJECT_VALIDATION_FAIL = 19;
	        MPLS_SEQUENCE_NUMBER          = 20;
	        L2_NON_UNICAST_MISS           = 21;
	        NIV_PRIO_DROP                 = 22;
	        NIV_RPF_FAIL                  = 23;
	        UNKNOWN_SUBTENDING_PORT       = 24;
	        TUNNEL_ADAPT_LOOKUP_MISS      = 25;
	        PACKET_FLOW_SELECT_MISS       = 26;
	        TUNNEL_DECAP_ECN_ERROR        = 27;
	        FAILOVER_DROP                 = 28;
	        OTHER_LOOKUP_MISS             = 29;
	        INVALID_TPID                  = 30;
	        TUNNEL_TTL_ERROR              = 31;
	        MPLS_ILLEGAL_RESERVED_LABEL   = 32;
	        L3_HEADER_ERROR               = 33;
	        L2_HEADER_ERROR               = 34;
	        TTL1                          = 35;
	        TTL                           = 36;
	        FCOE_ZONE_CHECK_FAIL          = 37;
	        IPMC_INTERFACE_MISMATCH       = 38;
	        MPLS_TTL                      = 39;
	        MPLS_UNKNOWN_ACH              = 40;
	        OAM_ERROR                     = 41;
	        L2_GRE_SIP_MISS               = 42;
	        L2_GRE_VPNID_MISS             = 43;
	        BFD_ERROR                     = 44;
	        CONGESTION_CNM_PROXY_ERROR    = 45;
	        VXLAN_SIP_MISS                = 46;
	        VXLAN_VPNID_MISS              = 47;
	        NIV_INTERFACE_MISS            = 48;
	        NIV_TAG_INVALID               = 49;
	        NIV_TAG_DROP                  = 50;
	        NIV_UNTAG_DROP                = 51;
	        TRILL_INVALID                 = 52;
	        TRILL_MISS                    = 53;
	        TRILL_RPF_FAIL                = 54;
	        TRILL_TTL                     = 55;
	        NAT_ERROR                     = 56;
            TCP_UDP_NAT_MISS              = 57;
            ICMP_NAT_MISS                 = 58;
            NAT_FRAGMENT                  = 59;
            NAT_MISS                      = 60;
        }

        optional uint32 proto              = 1;
        optional uint32 sip                = 2;
        optional uint32 dip                = 3;

        optional uint32 l4_sport           = 4;
        optional uint32 l4_dport           = 5;
        optional uint32 vnid               = 6;
        optional uint32 inner_proto        = 7;
        optional uint32 inner_sip          = 8;
        optional uint32 inner_dip          = 9;

        optional uint32 inner_l4_sport     = 10;
        optional uint32 inner_l4_dport     = 11;
        optional bytes  custom_key         = 12;
        optional uint32 group_id           = 13; /* Packet type is derived directly from group ID */
        optional bytes packet              = 14;

        optional uint64 packet_count       = 15;
        optional uint64 byte_count         = 16;

        optional DROP_REASON drop_reason_1 = 17;
        optional DROP_REASON drop_reason_2 = 18;

    }

    message EventPair {
        repeated Event event            = 1;
        repeated Flow flow              = 2;
    }

    message GenEvent {
        required string system_id        = 1;
        optional uint32 component_id     = 2;
        optional uint32 sub_component_id = 3;
        repeated EventPair eventpair     = 4;
        optional string hostname         = 5;
    }
