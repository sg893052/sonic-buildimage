# Feature Name
IFA - Tail Timestamping feature.

# High Level Design Document
#### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definition_abbreviation)
  * [1 Feature Overview](#1-feature-overview)
      * [1.1 Requirements](#1_1-requirements)
          * [1.1.1 Functional Requirements](#1_1_1-functional-requirements)
          * [1.1.2 Configuration and Management Requirements](#1_1_2-configuration-and-management-requirements)
          * [1.1.3 Scalability Requirements](#1_1_3-scalability-requirements)
      * [1.2 Design Overview](#1_2-design-overview)
          * [1.2.1 Basic Approach](#1_2_1-basic-approach)
          * [1.2.2 Container](#1_2_2-container)
          * [1.2.3 SAI Overview](#1_2_3-sai-overview)
  * [2 Functionality](#2-functionality)
      * [2.1 Target Deployment Use Cases](#2_1-target-deployment-use-cases)
      * [2.2 Functional Description](#2_2-functional-description)
  * [3 Design](#3-design)
      * [3.1 Overview](#3_1-overview)
      * [3.2 DB Changes](#3_2-db-changes)
          * [3.2.1 CONFIG DB](#3_2_1-config-db)
          * [3.2.2 APPL DB](#3_2_2-appl-db)
          * [3.2.3 STATE DB](#3_2_3-state-db)
          * [3.2.4 ASIC DB](#3_2_4-asic-db)
          * [3.2.5 COUNTER DB](#3_2_5-counter-db)
      * [3.3 IFA-TS Manager Daemon](#3_3-ifa_ts-manager-daemon)
      * [3.4 Switch State Service Design](#3_4-switch-state-service-design)
          * [3.4.1 Orchestration Agent](#3_4_1-orchestration-agent)
          * [3.4.2 Other Process](#3_4_2-other-process)
      * [3.5 Syncd](#3_5-syncd)
      * [3.6 SAI](#3_6-sai)
      * [3.7 User Interface](#3_7-user-interface)
          * [3.7.1 Data Models](#3_7_1-data-models)
          * [3.7.2 Configuration Commands](#3_7_2-configuration-commands)
          * [3.7.3 Show Commands](#3_7_3-show-commands)
          * [3.7.4 Debug Commands](#3_7_4-debug-commands)
          * [3.7.5 REST API Support](#3_7_5-rest-api-support)
  * [4 Flow Diagrams](#4-flow-diagrams)
      * [4.1 IFA-TS Config Call Flow](#4_1-config-call-flow)
  * [5 Error Handling](#5-error-handling)
  * [6 Serviceability And Debug](#6-serviceability-and-debug)
  * [7 Warm Boot Support](#7-warm-boot-support)
  * [8 Scalability](#8-scalability)
  * [9 Corequisite](#9-corequisite)
  * [10 Unit Test](#10-unit-test)
  * [11 Broadcom Only](#11-broadcom-only)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 10/29/2019  | Naveen Kumar Aketi | Initial version                   |

# About This Manual
This document provides general information about the IFA - Tail Timestamping (IFA-TS) feature implementation in SONiC.

# Scope
This document describes the high level design of IFA - Tail Timestamping feature.

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| SAI                      | Switch abstraction interface        |
| TAM                      | Telemetry and monitoring            |
| IFA-TS                   | IFA - Tail Timestamping             |
| CRC                      | Cyclic Redundancy Check             |
| MAC                      | Media Access Control                |

# 1 Feature Overview

IFA - Tail Timestamping feature attaches arrival and departure timestamps along with a switch identifier to a frame at one or more switches along a flow path across the network for a given set of flows matching the user provided criterion. Time-stamps can be gathered and analyzed by a designated collector. A network monitoring application can analyze the time-stamps and measure the latency of a flow across the network, trace packets across a path, detect hotspots points and validate arrival sequence.

## 1.1 Requirements
### 1.1.1 Functional Requirements

1.0 IFA - Tail Timestamping feature allows user to enable time stamping on a per flow and per port basis.

1.1 Ingress and Egress timestamps are added at the end of frames.

1.2 Ingress and Egress timestamps are in 1588 Time-of-Day format (UTC time).

1.3 The presence of any tail timestamping headers in the packet must not affect forwarding or processing of the packet in downstream devices, including those that do not support the timestamping feature.

1.4 Tail timestamping packet format is not covered by any standards and proprietary per vendor.

2.0 IFA - Tail Timestamping feature needs configuration as mentioned below.

2.0.1 TAM device identifier to uniquely identify a device in network and to insert in the time stamping header.

2.0.2 ACL configuration to identify a flow that should be tagged with time stamping header.

2.1 IFA - Tail Timestamping feature will be a part of the TAM container along with other TAM features.

3.0 UI commands available to configure IFA-TS configuration.

3.1 UI commands available to show IFA-TS configuration, IFA-TS status and IFA-TS statistics.

3.2 UI commands available to clear IFA-TS configuration.

4.0 The maximum number of IFA-TS flows are platform dependent.

4.1 The maximum packet length supported by IFA-TS is platform dependent.

4.2 Frames received by CPU are ingress time-stamped if the frame matches the user provided IFA-TS flow criterion.

### 1.1.2 Configuration and Management Requirements

IFA - Tail Timestamping feature will use new management framework for CLI. There is no known standard YANG model for Tail Timestamping.

### 1.1.3 Scalability Requirements

IFA - Tail Timestamping feature is scaled based on availability of resources in hardware and software.

## 1.2 Design Overview
### 1.2.1 Basic Approach

The IFA - Tail Timestamping feature is newly developed.

### 1.2.2 Container
No new containers are added for IFA - Tail Timestamping (IFA-TS) feature.

### 1.2.3 SAI Overview

The SAI TAM spec specifies the TAM APIs to be used to configure the IFA-TS functionality. Please refer to saitam.h present in [https://github.com/opencomputeproject/SAI/blob/master/inc/saitam.h](https://github.com/opencomputeproject/SAI/blob/master/inc/saitam.h) for more details.

# 2 Functionality
## 2.1 Target Deployment Use Cases

IFA - Tail Timestamping feature can be used to measure the latency of a flow across the network, trace packets across a path, detect congestion points and validate arrival sequence. IFA - Tail Timestamping feature can be used to proactively monitor the network for performance bottlenecks and assists in planning network capacity and projecting future load.

Note: The mechanism to direct the tail-time-stamped packets to a collector is not part of this feature. User should use appropriate means to direct the tail-timestamped packets to a collector. For example, user can configure a ACL matching the tail-timestamped packets and configure the ACL action as mirror/redirect.

***Sample ACL configuration***
        "ACL_TABLE": {
            "T1": {
                "policy_desc" : "IFA - Tail Timestamping Policy",
                "stage" : "INGRESS",
                "type" : "L3" ,
                "ports" : "Ethernet20"
            }
        }

        "ACL_RULE": {
            "T1|R1": {
                "PRIORITY" : "55",
                "IP_TYPE" : "ipv4any",
                "IP_PROTOCOL": "17",
                "SRC_IP" : "10.10.0.26/32",
                "DST_IP" : "10.10.1.26/32",
                "PACKET_ACTION" : "redirect:Ethernet10"
            }
        }

***IFA and IFA-TS Joint Deployment Topology***

In IFA and IFA-TS joint deployment topology, tail timestamping device will identify INT packets with the help of ACL qualifier TAM_INT_TYPE and inserts timestamping header to all INT packets. Tail timestamping header is added at the end of the packet.

![IFA and IFA-TS Joint Deployment Topology](images/IFA_IFA_TS_Deployment.JPG)

***IFA-TS Deployment Topology***

![IFA-TS Deployment Topology](images/IFA_TS_Deployment.JPG)

***Tail-Timestamping Payload Format***

![IFA-TS Tail-Timestamping Payload Format](images/TS_Tail_Timestamp_Payload_Format.JPG)

## 2.2 Functional Description

IFA - Tail Timestamping feature allows user to enable tail-time-stamping through PACKET\_ACTION field in ACL rule and by specifying ACL table and ACL rule in IFA-TS configuration. PACKET\_ACTION field value INT\_INSERT specifies that flow matching the ACL rule should be tail-time-stamped. For traffic matching the IFA-TS flow, ingress and egress time stamps will be inserted at the end of the frame.

***Sample configuration for tail timestamping packets in a given flow***

    IFA-TS configuration
        (config)# tam 
        (config-tam)# device-id 2345
        (config-tam)# int-ifa-ts
        (config-int-ifa-ts)# feature enable
        (config-int-ifa-ts)# flow F1 acl_table TS acl_rule Rule1

    ACL configuration
        "ACL_TABLE": {
            "TS": {
                "policy_desc" : "IFA - Tail Timestamping Policy",
                "stage" : "INGRESS",
                "type" : "TAM" ,
                "ports" : "Ethernet20"
            }
        }

        "ACL_RULE": {
            "TS|Rule1": {
                "PRIORITY" : "55",
                "IP_TYPE" : "ipv4any",
                "IP_PROTOCOL": "17",
                "SRC_IP" : "10.10.0.26/32",
                "DST_IP" : "10.10.1.26/32",
                "PACKET_ACTION" : "int_insert"
            }
        }

***Sample configuration for tail timestamping all in-band telemetry packets***

    IFA-TS configuration
        (config)# tam 
        (config-tam)# device-id 2345
        (config-tam)# int-ifa-ts
        (config-int-ifa-ts)# feature enable
        (config-int-ifa-ts)# flow F1 acl_table TS acl_rule Rule2

    ACL configuration
        "ACL_TABLE": {
            "TS": {
                "policy_desc" : "IFA - Tail Timestamping Policy - Mach IFA packets",
                "stage" : "INGRESS",
                "type" : "TAM" ,
                "ports" : "Ethernet20"
            }
        }

        "ACL_RULE": {
            "TS|Rule2": {
                "PRIORITY" : "55",
                "IP_PROTOCOL": "17",
                "TAM_INT_TYPE" : "IFA",
                "PACKET_ACTION" : "int_insert"
            }
        }

***Sample JSON configuration***

    {
        "TAM_DEVICE_TABLE": {
            "device": {
                "deviceid": "2345"
            }
        },
        "TAM_INT_IFA_TS_FEATURE_TABLE": {
            "feature": {
                "enable": "true"
            }
        },
        "TAM_INT_IFA_TS_FLOW": {
            "F1": {
                "acl-rule-name": "Rule1",
                "acl-table-name": "TS"
            }
        }
    }

***Sample REST configuration***

    curl -X POST "https://10.59.143.180/restconf/data/sonic-acl:sonic-acl" -H "accept: application/yang-data+json" -H "authorization: Basic YWRtaW46YnJvYWRjb20=" -H "Content-Type: application/yang-data+json" -d "{ \"sonic-acl:ACL_TABLE\": { \"ACL_TABLE_LIST\": [ { \"aclname\": \"TS\", \"policy_desc\": \"IFA - Tail Timestamping Policy - Mach IFA packets\", \"ports\": [ \"Ethernet20\" ], \"stage\": \"INGRESS\", \"type\": \"TAM\" } ] }, \"sonic-acl:ACL_RULE\": { \"ACL_RULE_LIST\": [ { \"IP_PROTOCOL\": 17, \"TAM_INT_TYPE\" : \"IFA\",\t\"PACKET_ACTION\": \"INT_INSERT\", \"PRIORITY\": 55, \"aclname\": \"TS\", \"rulename\": \"Rule2\" } ] }}"

# 3 Design
## 3.1 Overview

***IFA-TS Architecture***

![IFA-TS architecture](images/TS_Arch.JPG)

The above diagram illustrates the architecture of the IFA -Tail Timestamping feature within SONiC.

Below is the call flow sequence specified in above architecture diagram

1 IFA-TS and ACL configuration from CLI is saved to CONFIG DB.

2 IFA-TS Manager reads IFA-TS configuration from CONFIG DB, processes and validates IFA-TS configuration.

3 ACL Manager reads ACL configuration from CONFIG DB, processes and validates ACL configuration.

4 IFA-TS Manager updates valid IFA-TS configuration to APPL DB.

5 ACL Manager updates valid ACL configuration to APPL DB.

6 IFA-TS ORCH reads IFA-TS configuration from APPL DB and creates/deletes TAM INT IFA IFA-TS objects.

7 ACL ORCH reads ACL configuration from APPL DB.

8 ACL ORCH notifies IFA-TS ORCH about ACL rule creation/deletion.

9 IFA-TS ORCH checks for ACL table and ACL rule specified in IFA-TS configuration.

10 IFA-TS ORCH attaches/detaches the TAM INT IFA IFA-TS object to/from ACL ENTRY.

11 ACL ORCH will make SAI API calls to update ASIC DB.

12,13,14 SYNCD reads IFA-TS and ACL information from ASIC DB and configures ASIC accordingly.

A CLI reads IFA-TS ACL counters from COUNTER DB and displays it as output of show commands.

## 3.2 DB Changes
### 3.2.1 CONFIG DB

TAM\_DEVICE\_TABLE

    ;Defines TAM device configuration

    key      = device     ; Only one instance and has a fixed key "device".
    deviceid = 1 * 5DIGIT ; Uniquely identifies a device on the network to be analyzed.

    Example:
    127.0.0.1:6379[4]> keys *TAM_DEVICE*
    1) "TAM_DEVICE_TABLE|device"

    127.0.0.1:6379[4]> HGETALL "TAM_DEVICE_TABLE|device"
    1) "deviceid"
    2) 54325

TAM\_INT\_IFA\_TS\_FEATURE\_TABLE

    ;Defines TAM INT IFA TS feature configuration

    key    = feature          ; Only one instance and has a fixed key "feature".
    enable = "true" / "false" ; Specifies whether TAM INT IFA TS feature is enabled or  not.

    Example:
    127.0.0.1:6379[4]> keys *TAM_INT_IFA_TS_FEATURE*
    1) "TAM_INT_IFA_TS_FEATURE_TABLE|feature"

    127.0.0.1:6379[4]> HGETALL "TAM_INT_IFA_TS_FEATURE_TABLE|feature"
    1) "enable"
    2) "true"

TAM\_INT\_IFA\_TS\_FLOW TABLE

    ;Defines a TAM INT IFA IFA-TS flow configuration

    key            = name       ; name is flow name and should be unique.
    acl-table-name = table-name ; Parameter to map to acl table to the flow.
    acl-rule-name  = rule-name  ; Parameter to map to acl rule to the flow.

    Example:
    127.0.0.1:6379[4]> keys *TAM_INT_IFA_TS_FLOW*
    1) "TAM_INT_IFA_TS_FLOW_TABLE|f1"

    127.0.0.1:6379[4]> HGETALL "TAM_INT_IFA_TS_FLOW_TABLE|f1"
    1) "acl-table-name"
    2) "table1"
    3) "acl-rule-name"
    4) "rule1"

### 3.2.2 APPL DB

TAM\_INT\_IFA\_TS\_FEATURE\_TABLE

    ;Defines TAM INT IFA IFA-TS feature configuration

    key      = device     ; Only one instance and has a fixed key "device".
    deviceid = 1 * 5DIGIT ; Uniquely identifies a device on the network to be analyzed.

    Example:
    127.0.0.1:6379> keys *TAM_INT_IFA_TS_FEATURE*
    1) "TAM_INT_IFA_TS_FEATURE_TABLE:feature"

    127.0.0.1:6379> HGETALL "TAM_INT_IFA_TS_FEATURE_TABLE:feature"
    1) "deviceid"
    2) 54325

TAM\_INT\_IFA\_TS\_FLOW\_TABLE

    ;Defines a TAM INT IFA IFA-TS flow configuration

    key            = name       ; name is flow name and should be unique.
    acl-table-name = table-name ; Parameter to map to acl table to the flow.
    acl-rule-name  = rule-name  ; Parameter to map to acl rule to the flow.

    Example:
    127.0.0.1:6379> KEYS *TAM_INT_IFA_TS_FLOW*
    1) "TAM_INT_IFA_TS_FLOW_TABLE:F1"

    127.0.0.1:6379> HGETALL TAM_INT_IFA_TS_FLOW_TABLE:F1
    1)  "acl-table-name"
    2)  "T1"
    3)  "acl-rule-name"
    4)  "R1"

### 3.2.3 STATE DB
N/A

### 3.2.4 ASIC DB
The ASIC DB is updated by SAI REDIS upon invocation of SAI REDIS APIs by TSOrch.

### 3.2.5 COUNTER DB
N/A

## 3.3 IFA-TS Manager Daemon

TS manager daemon runs as part of TAM docker. IFA-TS manager processes IFA-TS configuration from CONFIG DB, validates for consistency and fullness of IFA-TS configuration and updates valid IFA-TS configuration to APPL DB. 

## 3.4 Switch State Service Design
### 3.4.1 Orchestration Agent

A new orchestration agent class, TSOrch will be added to convert the incoming IFA-TS config to ASIC configuration. TSOrch subscribes to the IFA-TS tables of APPL DB and converts the configuration to the SAI TAM API call sequence described in section 3.6.

TSOrch maintains data pertaining to all the currently configured IFA-TS entities and the associated TAM object bindings. TAM objects are re-used wherever possible.

### 3.4.2 Other Process

Tail timestamping is dependent on ACL configuration. ACLOrch notifies TSOrch about ACL rule creation/deletion  and TSOrch will attach/dettach TAM INT object attribute to ACL if the ACL table and ACL rule are mapped to any TS configuration. 

## 3.5 SyncD
N/A

## 3.6 SAI

The SAI TAM API spec defines all TAM APIs supported in SAI. Please refer to saitam.h present in [https://github.com/opencomputeproject/SAI/blob/master/inc/saitam.h](https://github.com/opencomputeproject/SAI/blob/master/inc/saitam.h) for more details.

Tail timestamping feature is accomplished by attaching a SAI TAM INT object to SAI ACL entry. Below diagram provides details about how the SAI ACL object and SAI TAM objects are stitched.

***SAI objects and their correlation***

![TS TAM objects correlation](images/TS_TAM_correlation.JPG)

## 3.7 User Interface
### 3.7.1 Data Models

The yang model for IFA - Tail Timestamping feature CLI commands is provided below:

    module sonic-tam-int-ifa-ts {
        namespace "http://github.com/Azure/sonic-tam-int-ifa-ts";
        prefix stam-ts;
        import sonic-acl {
            prefix sacl;
        }
        import ietf-yang-types {
                prefix yang;
        }
        import sonic-extension { 
                prefix sonic-ext; 
        }
        organization
            "SONiC";
        contact
            "SONiC";
        description
             "This module defines configuration and operational state
              data for IFA TailStamping Feature."; 
        revision 2019-11-14 {
            description
                "Initial revision.";
        }
        container sonic-tam-int-ifa-ts {
            container TAM_INT_IFA_TS_FEATURE_TABLE {
                list TAM_INT_IFA_TS_FEATURE_TABLE_LIST {
                    key "name";
                    leaf name {
                            type string {
                                    pattern 'feature';
                            }
                    }
                    leaf enable {
                        type boolean;
                        default false;
                    }
                }
            }
            container TAM_INT_IFA_TS_FLOW_TABLE {
                list TAM_INT_IFA_TS_FLOW_TABLE_LIST {
                    key "name";
                    leaf name {
                            type string {
                                    pattern '[a-zA-Z0-9]{1}([-a-zA-Z0-9_]{0,71})'{
                                       error-message "Invalid INT IFA TS Flow name";
                                       error-app-tag int-ifa-ts-invalid-flow-name;
                                    }
                            }
                    }
                    leaf acl-table-name {
                        type string;
                    }
                    leaf acl-rule-name {
                        type string;
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

### 3.7.2 Configuration Commands

(config)# tam
 
(config-tam)# device-id {value}

***Example***

    sonic(config)# tam
    sonic(config-tam)# device-id 2345

(config-tam)# no device-id

Note: When user configured device identifier is cleared, default device identifier will be used. Default device identifier value will be last four bytes of MAC address of the node.

(config-tam)# int-ifa-ts

(config-int-ifa-ts)# feature {enable | disable}

***Example***

    sonic(config-tam)# int-ifa-ts
    sonic(config-int-ifa-ts)# feature enable

(config-int-ifa-ts)# flow {flow-name} acl-table {acl_tbl_name} acl-rule {acl_rule_name}

***Example***

    sonic(config-int-ifa-ts)# flow TS_Flow acl-table T1 acl-rule R1

(config-int-ifa-ts)# no flow { flow-name | all }

### 3.7.3 Show Commands

(sonic)# show tam int-ifa-ts supported

Note: "show tam int-ifa-ts supported" command specifies whether the underlying hardware supports tail timestamping feature or not. Tail timestamping configuration can you configured only when the output of "show tam int-ifa-ts supported" command returns true.  

***Example***

    sonic# show tam int-ifa-ts supported
    Feature Supported      : True

(sonic)# show tam int-ifa-ts status

***Example***

    sonic# show tam int-ifa-ts status
    ---------------------------------------------------------
    TAM INT IFA TS Status
    ---------------------------------------------------------
    Device Identifier    : 2345
    Number of flows      : 1
    Feature Enabled      : True

(sonic)# show tam int-ifa-ts flow { flow-name | all }

***Example***

    sonic# show tam int-ifa-ts flow TS_Flow
    ---------------------------------------------------------
    FLOW           ACL TABLE      ACL RULE
    ---------------------------------------------------------
    TS_Flow        T1             R1

    sonic# show tam int-ifa-ts flow all
    ---------------------------------------------------------
    FLOW           ACL TABLE      ACL RULE
    ---------------------------------------------------------
    TS_Flow        T1             R1

(sonic)# show tam int-ifa-ts statistics flow { flow-name | all }

***Example***

    sonic# show tam int-ifa-ts statistics TS_Flow
    ------------------------------------------------------------------------------------------------
    FLOW           ACL TABLE      ACL RULE       PACKET COUNT   BYTE COUNT
    ------------------------------------------------------------------------------------------------
    TS_Flow        T1             R1             8              8000

    sonic# show tam int-ifa-ts statistics all
    ------------------------------------------------------------------------------------------------
    FLOW           ACL TABLE      ACL RULE       PACKET COUNT   BYTE COUNT
    ------------------------------------------------------------------------------------------------
    TS_Flow        T1             R1             8              8000

### 3.7.4 Debug Commands
The debug commands are needed to dump:

TBD

### 3.7.5 REST API Support

TBD

# 4 Flow Diagrams
## 4.1 Config call flow

***When ACL configuration is created first followed by IFA-TS configuration***

![TS config call flow 1](images/TS_Call_Flow_TS_ACL.JPG)

***When IFA-TS configuration is created first followed by ACL configuration***

![TS config call flow 2](images/TS_Call_Flow_ACL_TS.JPG)

# 5 Error Handling

## CLI
- CLI configuration sanity will be enforced by the CLI handler and any invalid configuration is rejected. An error is displayed to the user notifying the reason for rejection of the configuration.

- Feature not supported is returned when IFA-TS feature is not supported by underlying silicon.

## IFA-TS manager daemon
- Any error occuring in tsmgrd is logged appropriately in syslog.

## IFA-TS Orch
- Any error occurring in the orchestration agent is logged appropriately via SWSS logging.
- Errors or failures of SAI APIs will be logged by TSOrch.
- On failure of a SAI TAM API in the config sequence of section 3.6, the previously configured steps will be rolled back.

# 6 Serviceability and Debug
TBD

# 7 Warm Boot Support
No special handling is done for the warm boot case. The IFA-TS configuration is restored from the Config DB and IFA-TS functionality will continue to work as it is through a warm boot. 

# 8 Scalability
N/A

# 9 Corequisite

1. Adding a 'IFA' match criterion for matching IFA packets both in SAI and SONiC.
2. REST API changes in Broadview to accommodate TS.

# 10 Unit Test
## CLI
1.  Verify CLI command to configure TAM INT IFA IFA-TS feature enable.
2.  Verify CLI command to configure TAM INT IFA IFA-TS feature disable.
3.  Verify ACL configuration with packet action type as int_insert for IPv4 type ACL.
4.  Verify ACL configuration with packet action type as int_insert for IPv6 type ACL.
5.  Verify CLI command to configure IFA-TS flow with ACL table name and ACL rule name.
6.  Verify CLI clear command to clear TAM INT IFA IFA-TS flow.
7.  Verify CLI show command to show TAM INT IFA IFA-TS status.
8.  Verify CLI show command to show TAM INT IFA IFA-TS statistics for all flows.
9.  Verify CLI show command to show TAM INT IFA IFA-TS statistics for a specific flows.
10. Verify CLI show command to show TAM INT IFA IFA-TS flow.

## REST
TBD

## IFA-TS Manager
1. Verify if IFA-TS configuration from CONFIG DB is received by IFA-TS manager.
2. Verify if TSOrch is able to create IFA-TS table entries in APPL DB successfully.
3. Verify if TSOrch is able to delete IFA-TS table entries in APPL DB successfully.

## TSOrch
1. Verify if IFA-TS configuration from APPL DB is received by TSOrch.
2. Verify if TSOrch is able to create TAM objects for IFA-TS configuration via SAI TAM APIs successfully.
3. Verify if TSOrch is able to delete existing IFA-TS configuration via SAI TAM APIs successfully.
4. Verify if TSOrch is able to use existing TAM objects for IFA-TS config.
5. Verify if TSOrch rolls back config in a clean way if there is a SAI API failure.
6. Verify if TSOrch is able to set TAM INT attribute for IFA-TS ACL.
7. Verify if TSOrch is able to reset TAM INT attribute for IFA-TS ACL.

## Functional Tests
1. Verify if IPv4 traffic matching IFA-TS flow is time stamped.
2. Verify if IPv6 traffic matching IFA-TS flow is time stamped.
3. Verify if IFA-TS headers are inserted in correct order.
4. Verify that there is no crash encountered at any of the layers with an invalid IFA-TS configuration. 
5. Verify that an invalid configuration is rejected gracefully at appropriate layers.
6. Verify that IFA-TS configuration is restored after warmboot.
7. Verify that switched traffic matching IFA-TS flow is time stamped.
8. Verify that routing traffic matching IFA-TS flow is time stamped.

## Negative Tests
1. Verify if CLI throws error when a user tries to create a duplicate IFA-TS flow.
2. Verify if CLI returns error if CLI is unable to write the IFA-TS config to config DB.
3. Verify if CLI returns entry not found when a clear command is issued on non-existent flow.
4. Verify if IFA-TS manager logs an error on receipt of an incorrect IFA-TS table entries from CONFIG DB.
5. Verify if TSOrch logs an error on receipt of an incorrect IFA-TS table entries from APPL DB.
6. Verify if TSOrch logs an error if it is unable to read IFA-TS table data from APPL DB.
7. Verify if TSOrch logs all errors encountered during processing of the incoming IFA-TS config request.
8. Verify if TSOrch logs any errors arising out of SAI API failure.
9. Verify if TSOrch logs an error when no further IFA-TS configuration can be configured to hardware.
10. Verify if feature not supported is returned when IFA-TS feature is not supported by underlying silicon.

# 11 Broadcom Only

1. IFA and IFA-TS can be jointly deployed in a network. IFA ingress and IFA egress device functionalities can be achieved on platforms that support IFA and have licensed IFA firmware. IFA-TS can be enabled on all intermediate devices and does not need any licensed IFA firmware and ingress and egress tail-timestamps will be inserted.

2. Below are the platform specific limitations of IFA - Tail Timestamping feature.
    1. Only TCP and UDP traffic can be timestamped.
    2. Timestamp is not part of the L3 packet. Any checks that assume the L3+ packet length field represents the total frame length will not be accurate.
    3. Systems that need to subject packets to such checks must disable timestamping for the corresponding system, port or flow.
    4. Header length fields or checksum fields(e.g UDP checksum) will not be updated upon insertion of the timestamp.
    5. IEEE 802.3 frames(e.g SNAP LLC) are not supported.
    6. No switches across the timestamping path should do pad stripping or otherwise adjust frame content based on the IP header payload/total length fields for Ethernet II frames.
    7. Platforms using the HiGig2 extension header cannot coexist with packet timestamping.     
