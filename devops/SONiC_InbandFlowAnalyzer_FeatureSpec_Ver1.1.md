# Feature Name
Inband flow analyzer feature.

# High Level Design Document
#### Rev 0.2

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definition-abbreviation)
  * [1 Feature Overview](#1-feature-overview)
      * [1.1 Requirements](#11-requirements)
          * [1.1.1 Functional Requirements](#111-functional-requirements)
          * [1.1.2 Configuration and Management Requirements](#112-configuration-and-management-requirements)
          * [1.1.3 Scalability Requirements](#113-scalability-requirements)
      * [1.2 Design Overview](#12-design-overview)
          * [1.2.1 Basic Approach](#121-basic-approach)
          * [1.2.2 Container](#122-container)
          * [1.2.3 SAI Overview](#123-sai-overview)
  * [2 Functionality](#2-functionality)
      * [2.1 Target Deployment Use Cases](#21-target-deployment-use-cases)
      * [2.2 Functional Description](#22-functional-description)
  * [3 Design](#3-design)
      * [3.1 Overview](#31-overview)
      * [3.2 DB Changes](#32-db-changes)
          * [3.2.1 CONFIG DB](#321-config-db)
          * [3.2.2 APP DB](#322-app-db)
          * [3.2.3 STATE DB](#323-state-db)
          * [3.2.4 ASIC DB](#324-asic-db)
          * [3.2.5 COUNTER DB](#325-counter-db)
      * [3.3 IFA Manager Daemon](#33-ifa-manager-daemon)
      * [3.4 Switch State Service Design](#34-switch-state-service-design)
          * [3.4.1 Orchestration Agent](#341-orchestration-agent)
          * [3.4.2 Other Process](#342-other-process)
      * [3.5 Syncd](#35-syncd)
      * [3.6 SAI](#36-sai)
      * [3.7 CLI](#37-cli)
          * [3.7.1 Data Models](#371-data-models)
          * [3.7.2 Configuration Commands](#372-configuration-commands)
          * [3.7.3 Show Commands](#373-show-commands)
          * [3.7.4 Clear Commands](#374-clear-commands)
          * [3.7.5 Debug Commands](#375-debug-commands)
          * [3.7.6 REST API Support](#376-rest-api-support)
  * [4 Flow Diagrams](#4-flow-diagrams)
      * [4.1 IFA Config Call Flow](#41-config-call-flow)
  * [5 Error Handling](#5-error-handling)
  * [6 Serviceability And Debug](#6-serviceability-and-debug)
  * [7 Warm Boot Support](#7-warm-boot-support)
  * [8 Scalability](#8-scalability)
  * [9 Unit Test](#9-unit-test)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 06/14/2019  | Naveen Kumar Aketi | Initial version                   |
| 0.2 | 10/16/2019  | Naveen Kumar Aketi | Version 0.2 as per new design     |

# About This Manual
This document provides general information about the Inband Flow Analyzer (IFA) feature implementation in SONiC.

# Scope
This document describes the high level design of Inband flow analyzer feature.

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| SAI                      | Switch abstraction interface        |
| TAM                      | Telemetry and monitoring            |
| IFA                      | Inband flow analyzer                |

# 1 Feature Overview
The Inband Flow Analyzer (IFA) feature allows configuration of an IFA session that could provide Inband-telemetry over sampled live traffic to collectors. A network monitoring application can analyze the telemetry information provided by IFA feature and provide full visibility of a network by providing metrics such as latency, packet loss and full network path that packet travels. IFA feature in SONiC will be compliant to RFC [https://datatracker.ietf.org/doc/draft-kumar-ippm-ifa](https://datatracker.ietf.org/doc/draft-kumar-ippm-ifa).

## 1.1 Requirements
### 1.1.1 Functional Requirements

1.0 IFA feature allows user to configure IFA session in a given network and provide Inband-telemetry data to collectors. IFA session is defined by flow classifiers that are used to identify a flow that should be monitored for inband telemetry.   

1.1 IFA feature is accomplished by configuring IFA session on various nodes that act as ingress, intermediate and egress devices. Device role is per flow in a node and a single node can act as ingress device for one flow and intermediate device for another flow.

1.2 Ingress device makes a sample of a flow and tags them for analysis and data collection.

1.3 Intermediate device is responsible for identifying IFA tagged flows and insertion of telemetry data.

1.4 Egress device is responsible for terminating the IFA flow by summarizing the telemetry data of the entire path and sending it to collector.

2.0.0 IFA session needs configuration as mentioned below.

2.0.0.1 TAM device identifier to uniquely identify a device in network and insert the same in INT header.

2.0.0.2 ACL configuration to identify a flow and sample packets from that flow to insert IFA headers.

2.0.0.3 TAM collector configuration that can be attached to IFA flow on egress device to forward telemetry data.

2.0.1 The IFA feature will be a part of the TAM container along with other TAM features.

3.0 UI commands available to configure TAM device identifier, TAM collector and IFA configuration.

3.1 UI commands available to show TAM device identifier, TAM collector, IFA configuration, IFA status and IFA statistics.

3.2 UI commands available to clear IFA configuration.

4.0 The maximum number of IFA flows are platform dependent.

4.1 Only one collector can be configured in a device.

4.2 The IFA feature is supported on physical and LAG ports.

4.3 The maximum packet length supported by IFA is platform dependent.

4.4 The IFA feature does not support dynamic port breakout feature, however it can be manually configured on broken-out ports. The IFA feature does not automatically adjust configuration in response to dynamic port breakout events.

4.5 Some platforms may require provisioning to enable IFA. 'ifa -config -enable' command to be issued to provision such platforms for IFA functionality. 'ifa -config -disable' command can be issued to disable provisioning of IFA on such platforms.

### 1.1.2 Configuration and Management Requirements

The Inband flow analyzer feature will use the Python click framework for CLI. Once the management framework for CLI is available, CLI will be changed to align to the same.

### 1.1.3 Scalability Requirements

IFA is scaled based on availability of resources in hardware such as ACLs, TAM object ids etc.

## 1.2 Design Overview
### 1.2.1 Basic Approach

The Inband flow analyzer feature is newly developed.

### 1.2.2 Container
No new containers are added for Inband Flow Analyzer (IFA) feature.

### 1.2.3 SAI Overview

The SAI TAM spec specifies the TAM APIs to be used to configure the IFA functionality. Please refer to SAI-Proposal-TAM2.0-v2.0.docx in [https://github.com/opencomputeproject/SAI/tree/master/doc/TAM](https://github.com/opencomputeproject/SAI/tree/master/doc/TAM) for more details.

# 2 Functionality
## 2.1 Target Deployment Use Cases

IFA is used to proactively monitor the network for faults and performance bottlenecks, it aids in detecting data plane faults and isolating them to a given device or location and assists in planning network capacity and projecting future load.

## 2.2 Functional Description

The IFA feature allows user to configure a device as ingress and egress nodes through PACKET\_ACTION field in ACL rule. PACKET\_ACTION field values INT\_INSERT and INT\_DELETE specifies ingress and egress devices respectively. Each device type performs its own unique operations as mentioned below.

By default, when IFA feature is enabled; a node will behave as intermediate device and perform IFA intermediate device actions on all flows, except for IFA ingress flows and IFA egress flows.

Ingress node matches an incoming flow based on the ACL configuration specified in an IFA flow and makes a copy of a sampled packet from the flow based upon the configured sampling rate. The copied packet has the IFA meta-data instructions and IFA header and IFA meta-header data added, and is then forwarded along the same path as the sampled packet. 

At intermediate nodes, IFA probe packets are identified and IFA meta-data is inserted and forwarded to next hop. IFA meta-data consists of fields such as IFA probe packet's ingress port, egress port, ingress time-stamp and egress time-stamp etc.

At egress node, IFA probe packets are identified and IFA meta-data is inserted. Egress node terminates IFA session and summarizes the telemetry data for the entire path, encapsulates the telemetry data as payload and sends it to collector.

At all other non-IFA nodes, IFA sampled packet is forwarded as a normal packet in the network.

***Example IFA multinode topology***

![IFA multinode topology](images/IFA_Multinode_Topology.JPG)

***Example CLI configuration***

    ***Ingress node***

        IFA configuration
            config tam device-id 2345
            config tam-int-ifa feature enable
            config tam-int-ifa flow flow1 IFA Flow1 sampling-rate 1000

        ACL configuration
            "ACL_TABLE": {
                "IFA": {
                    "policy_desc" : "IFA Ingress Device Policy",
                    "stage" : "INGRESS",
                    "type" : "TAM" ,
                    "ports" : "Ethernet20"
                    }
            }

            "ACL_RULE": {
                "IFA|Flow1": {
                    "PRIORITY" : "55",
                    "IP_TYPE" : "ipv4any",
                    "IP_PROTOCOL": "17",
                    "SRC_IP" : "10.10.0.26/32",
                    "DST_IP" : "10.10.1.26/32",
                    "PACKET_ACTION" : "int_insert"
                }
            }

    ***Intermediate node***

        IFA configuration
            config tam device-id 2346
            config tam-int-ifa feature enable

    ***Egress node***

        IFA configuration
            config tam device-id 2347
            config tam-int-ifa feature enable
            config tam collector collector1 ipv4 11.12.13.14 9070
            config tam-int-ifa flow flow1 IFA Flow1 collector collector1 

        ACL configuration
            "ACL_TABLE": {
                "IFA": {
                    "policy_desc" : "IFA Egress Device Policy",
                    "stage" : "INGRESS",
                    "type" : "TAM" ,
                    "ports" : "Ethernet20"
                    }
            }

            "ACL_RULE": {
                "IFA|Flow1": {
                    "PRIORITY" : "55",
                    "IP_TYPE" : "ipv4any",
                    "IP_PROTOCOL": "17",
                    "SRC_IP" : "10.10.0.26/32",
                    "DST_IP" : "10.10.1.26/32",
                    "PACKET_ACTION" : "int_delete"
                }
            }


***Example JSON configuration***

    {
        "TAM_DEVICE_TABLE": {
            "device": {
                "deviceid": "54325"
            }
        },
        "TAM_COLLECTOR_TABLE": {
            "c1": {
                "ipaddress": "10.20.30.40",
                "ipaddress-type": "ipv4",
                "port": "9070"
            }
        },
        "TAM_INT_IFA_FEATURE_TABLE": {
            "feature": {
                "enable": "true"
            }
        },
        "TAM_INT_IFA_FLOW_TABLE": {
            "f1": {
                "acl-rule-name": "rule1",
                "acl-table-name": "table1",
                "collector": "collector1"
            },
            "f2": {
                "acl-rule-name": "rule2",
                "acl-table-name": "table2",
                "sampling-rate": "1000"
            }
        }
    }

    ***Example REST configuration***

    curl -X POST "https://10.59.143.180/restconf/data/sonic-acl:sonic-acl" -H "accept: application/yang-data+json" -H "authorization: Basic YWRtaW46YnJvYWRjb20=" -H "Content-Type: applica    tion/yang-data+json" -d "{ \"sonic-acl:ACL_TABLE\": { \"ACL_TABLE_LIST\": [ { \"aclname\": \"IFA\", \"policy_desc\": \"IFA Egress Device Policy\", \"ports\": [ \"Ethernet20\" ], \"st     age\": \"INGRESS\", \"type\": \"TAM\" } ] }, \"sonic-acl:ACL_RULE\": { \"ACL_RULE_LIST\": [ { \"IP_PROTOCOL\": 17,\t\t \"IP_TYPE\": \"IPV4ANY\",\t\t \"SRC_IP\": \"10.10.0.26/32\", \"D    ST_IP\": \"10.10.1.26/32\", \"PACKET_ACTION\": \"INT_DELETE\", \"PRIORITY\": 55, \"aclname\": \"IFA\", \"rulename\": \"Flow1\" } ] }}"

# 3 Design
## 3.1 Overview

***IFA Architecture***

![IFA architecture](images/IFA_Arch.JPG)

The above diagram illustrates the architecture of the IFA feature within SONiC.

Below is the call flow sequence specified in above architecture diagram

1 IFA and ACL configuration from CLI is saved to CONFIG DB.

2 IFA Manager reads IFA configuration from CONFIG DB, processes and validates the same.

3 IFA Manager updates valid IFA configuration to APPL DB.

4 ACL ORCH reads ACL configuration from CONFIG DB.

5 IFA ORCH reads IFA configuration from APPL DB and creates/deletes TAM INT IFA objects.

6 IFA ORCH checks for ACL table and ACL rule specified in IFA configuration.

7 ACL ORCH will make SAI API calls to update ASIC DB.

8 ACL ORCH notifies IFA ORCH about ACL rule creation/deletion.

9 IFA ORCH attaches/detaches the TAM INT IFA object to/from ACL ENTRY.

10, 11, 12 SYNCD reads IFA and ACL information from ASIC DB and configures ASIC accordingly.

A CLI reads IFA ACL counters from COUNTER DB and displays it as output of show commands.


## 3.2 DB Changes
### 3.2.1 CONFIG DB

TAM\_DEVICE\_TABLE

    ;Defines TAM device configuration

    key                = device         ; Only one instance and has a fixed key "device".
    deviceid           = 1 * 5DIGIT     ; Uniquely identifies a device on the network to be analyzed

    Example:
    127.0.0.1:6379[4]> keys *TAM_DEVICE*
    1) "TAM_DEVICE_TABLE|device"

    127.0.0.1:6379[4]> HGETALL "TAM_DEVICE_TABLE|device"
    1) "deviceid"
    2) 54325

TAM\_COLLECTOR\_TABLE

    ;Defines TAM collector configuration

    key                 = name                      ; name is collector name and should be unique.
    ipaddress-type      = "ipv4" / "ipv6"           ; Collector IP address type.
    ipaddress           = ipv4_prefix / ipv6_prefix ; Collector IP address.
    port                = 1 * 4DIGIT                ; Collector UDP port number

    Example:
    127.0.0.1:6379[4]> keys *TAM_COLLECTOR*
    1) "TAM_COLLECTOR_TABLE|collector1"

    127.0.0.1:6379[4]> HGETALL "TAM_COLLECTOR_TABLE|collector1"
    1) "ipaddress-type"
    2) "ipv4"
    3) "ipaddress"
    4) 10.20.30.40
    5) "port"
    6) 9070

TAM\_INT\_IFA\_FEATURE\_TABLE

    ;Defines TAM INT IFA feature configuration

    key                = feature          ; Only one instance and has a fixed key "feature".
    enable             = "true" / "false" ; Specifies whether TAM INT IFA feature is enabled or not.

    Example:
    127.0.0.1:6379[4]> keys *TAM_INT_IFA_FEATURE*
    1) "TAM_INT_IFA_FEATURE_TABLE|feature"

    127.0.0.1:6379[4]> HGETALL "TAM_INT_IFA_FEATURE_TABLE|feature"
    1) "enable"
    2) "true"

TAM\_INT\_IFA\_FLOW TABLE

    ;Defines a TAM INT IFA flow configuration

    key                 = name           ; name is flow name and should be unique
    acl-table-name      = table-name     ; Parameter to map to acl table to the
                                           flow.
    acl-rule-name       = rule-name      ; Parameter to map to acl rule to the
                                           flow.
    collector-name      = collector-name ; Optional parameter to map to collector
                                           to the flow.
    sampling-rate       = 1 * 5DIGIT     ; Optional parameter that indicates the
                                           sampling rate for the packets belonging
                                           to the flow that need to be tagged with
                                           IFA meta-data.
                                           One packet in every ‘sampling-rate’ 
                                           packets will be tagged with IFA
                                           Metadata.
                                           Note: This parameter is applicable for 
                                           switches configured as the type 
                                           ingress-node, and will be ignored for
                                           the other types. If this field is
                                           not specified, it indicates a 1:1 
                                           Sampling.
                                           Range is 1 to 10000. A value of 1
                                           indicates that all packets belonging to
                                           this flow will be sampled.


    Example:
    127.0.0.1:6379[4]> keys *TAM_INT_IFA_FLOW*
    1) "TAM_INT_IFA_FLOW_TABLE|f1"
    2) "TAM_INT_IFA_FLOW_TABLE|f2"

    127.0.0.1:6379[4]> HGETALL "TAM_INT_IFA_FLOW_TABLE|f1"
    1) "acl-table-name"
    2) "table1"
    3) "acl-rule-name"
    4) "rule1"
    5) "collector-name"
    6) "collector1"

    127.0.0.1:6379[4]> HGETALL "TAM_INT_IFA_FLOW_TABLE|f2"
    1) "acl-table-name"
    2) "table2"
    3) "acl-rule-name"
    4) "rule2"
    5) "sampling-rate"
    6) 1000

### 3.2.2 APP DB

TAM\_INT\_IFA\_FEATURE\_TABLE

    ;Defines TAM INT IFA feature configuration

    key                = feature        ; Only one instance and has a fixed key "feature".
    deviceid           = 1 * 5DIGIT     ; Uniquely identifies a device on the network to be analyzed

    Example:
    127.0.0.1:6379> keys *TAM_INT_IFA_FEATURE*
    1) "TAM_INT_IFA_FEATURE_TABLE:feature"

    127.0.0.1:6379> HGETALL "TAM_INT_IFA_FEATURE_TABLE:feature"
    1) "deviceid"
    2) 54325

TAM\_INT\_IFA\_FLOW\_TABLE

    ;Defines a TAM INT IFA flow configuration

    key                = name                      ; name is flow name and should be 
                                                     unique
    acl-table-name     = table-name                ; Parameter to map to acl table to 
                                                     the flow.
    acl-rule-name      = rule-name                 ; Parameter to map to acl rule to 
                                                     the flow.
    dst-ipaddress-type = "ipv4" / "ipv6"           ; Optional parameter that indicates
                                                     collector IP address type.
    dst-ipaddress      = ipv4_prefix / ipv6_prefix ; Optional parameter that indicates
                                                     collector IP address.
    dst-port           = 1 * 4DIGIT                ; Optional parameter that indicates
                                                     collector UDP port number
    src-ipaddress      = ipv4_prefix / ipv6_prefix ; Optional parameter that indicates
                                                     source IP address.
    src-port           = 1 * 4DIGIT                ; Optional parameter that indicates
                                                     source port number
    sampling-rate      = 1 * 5DIGIT                ; Optional parameter that indicates 
                                                     the sampling rate for the packets 
                                                     belonging to the flow that need 
                                                     to be tagged with IFA meta-data.
                                                     One packet in every 
                                                     ‘sampling-rate’ packets will be  
                                                     tagged with IFA Metadata.
                                                     Note: This parameter is 
                                                     applicable for switches 
                                                     configured as the type 
                                                     ingress-node, and will be ignored
                                                     for the other types. If this  
                                                     field is not specified, it 
                                                     indicates a 1:1 sampling.
                                                     Range is 1 to 10000. A value of 1
                                                     indicates that all packets 
                                                     belonging to this flow will be 
                                                     sampled.

    Example:
    127.0.0.1:6379> KEYS *TAM_INT_IFA_FLOW*                                  
    1) "TAM_INT_IFA_FLOW_TABLE:F1"
    2) "TAM_INT_IFA_FLOW_TABLE:F2"

    127.0.0.1:6379> HGETALL TAM_INT_IFA_FLOW_TABLE:F1
    1)  "acl-table-name"
    2)  "T1"
    3)  "acl-rule-name"
    4)  "R1"
    5)  "dst-ipaddress-type"
    6)  "ipv4"
    7)  "dst-ipaddress"
    8)  "10.20.30.40"
    9)  "dst-port"
    10) "2233"
    11) "src-port"
    12) "9070"
    13) "src-ipaddress"
    14) "10.52.141.231"

    127.0.0.1:6379> HGETALL TAM_INT_IFA_FLOW_TABLE:F2
    1) "acl-table-name"
    2) "T2"
    3) "acl-rule-name"
    4) "R2"
    5) "sampling-rate"
    6) "1000"

### 3.2.3 STATE DB
N/A

### 3.2.4 ASIC DB
The ASIC DB is updated by SAI REDIS upon invocation of SAI REDIS APIs by IFAOrch.

### 3.2.5 COUNTER DB
N/A

## 3.3 IFA Manager Daemon

IFA manager daemon runs as part of TAM docker. IFA manager processes IFA configuration from CONFIG DB, validates for consistency and fullness of IFA configuration and updates valid IFA configuration to APPL DB. 

## 3.4 Switch State Service Design
### 3.4.1 Orchestration Agent

A new orchestration agent class, IFAOrch is added to convert the incoming IFA config to ASIC configuration. IFAOrch subscribes to the IFA tables of APPL DB and converts the configuration to the SAI TAM API call sequence described in section 3.6.

IFAOrch maintains data pertaining to all the currently configured IFA entities and the associated TAM object bindings. TAM object bindings are re-used wherever possible.

### 3.4.2 Other Process
N/A

## 3.5 SyncD
N/A

## 3.6 SAI

The SAI TAM API spec defines all TAM APIs supported in SAI. Please refer to SAI-Proposal-TAM2.0-v2.0.docx in [https://github.com/opencomputeproject/SAI/tree/master/doc/TAM](https://github.com/opencomputeproject/SAI/tree/master/doc/TAM) for more details.

***Below diagram provides details about various TAM objects needed to support IFA and their correlation***

![IFA TAM objects correlation](images/IFA_TAM_correlation.JPG)

## 3.7 CLI
### 3.7.1 Data Models
NA

### 3.7.2 Configuration Commands

1) config tam device-id {value}

This command is used to configure an TAM device identifier.

2) config tam collector {collector-name} {ipv4 \| ipv6} {collector-ip} {collector-port}

This command is used to configure TAM collector and IFA report will be forwarded to the collector accordingly. TAM collector configuration is valid only on IFA egress type node.

3) config tam-int-ifa feature {enable \| disable}

This command is used to enable or disable IFA feature.

4) config tam-int-ifa flow {flow_name} {acl_table_name} {acl_rule_name} {{sampling-rate {value}} \| {collector {collector_name}}}

This command is used to specify flow criteria to match against incoming flow and tag with IFA data accordingly. When sampling rate is specified, one packet will be sampled out of the number of packets specified in sampling-rate. When collector is specified, IFA report will be forwarded to the collector.

### 3.7.3 Show Commands
1) show tam device

This command is used to show TAM device information.

![show tam device](images/IFA_CLI_Show_Tam_Device.JPG)

2) show tam collector {{collector-name} \| all }

This command is used to show TAM collector information.

![show tam collector](images/IFA_CLI_Show_Tam_Collector.JPG)

3) show tam-int-ifa status

This show command shows the current status of IFA, like deviceid, number of flows etc.,

![show tam-int-ifa status](images/IFA_CLI_Show_Tam_Int_Ifa_Status.JPG)

4) show tam-int-ifa flow {{flow-name} \| all }

This command is used to show IFA flow information.

![show tam-int-ifa flow](images/IFA_CLI_Show_Tam_Int_Ifa_Flow.JPG)

5) show tam-int-ifa statistics {{flow-name} \| all }

This show command shows the IFA statistics per flow or all flows.

![show tam-int-ifa statistics](images/IFA_CLI_Show_Tam_Int_Ifa_Statistics.JPG)

6) show tam-int-ifa supported

This show command shows whether IFA is supported or not.

![show tam-int-ifa supported](images/IFA_CLI_Show_Tam_Int_Ifa_Supported.JPG)

### 3.7.4 Clear commands

1) sonic-clear tam device-id

This command can be used to clear user configured TAM device identifier information. Default device identifier will be used for any further configurations.

2) sonic-clear tam collector {collector-name}

This command can be used to clear TAM collector configuration.

3) sonic-clear tam-int-ifa flow {flow-name}

This command can be used to clear IFA flow configuration.

### 3.7.5 Debug Commands
Debug commands will be available once the debug framework is available. The debug commands are needed to dump:

- IFA related entries and TAM object bindings maintained by IFAOrch.

### 3.7.6 REST API Support
N/A


 
 ## 3.8 Klish Configuration Commands

The configuration commands are split across TAM and INT-IFA modes in CLI. TAM mode hosts common TAM configuration like collector configuration.

### 3.8.1  [no] collector <name> type <ipv4/ipv6> ip <collector_ip> port <collector_port>

This command is used to configure the external collector IP address and port. Drop reports are sent by the configured external collector in protobuf format by the silicon.

    sonic(config)# tam 
    sonic(config-tam)# collector cr1 type ipv4 ip 10.10.10.10 port 9070

    sonic(config-tam)# no collector cr1

### 3.8.2  [no] device-id <device_id>

This command is used to configure the deviceId of the switch. Last three bytes of mac-address are used as default deviceId

    sonic(config)# tam 
    sonic(config-tam)# device-id 12345

    sonic(config-tam)# no device-id


### 3.8.3 INT-IFA mode

A new mode has been introduced within TAM mode for all IFA feature configuration:
 
     sonic(config-tam)# int-ifa
     sonic(config-tam-int-ifa)#

#### 3.8.3.1 feature <enable/disable>

This command is used to enable/disable the IFA feature functionality.
  
     sonic(config-tam-int-ifa)# feature enable

     sonic(config-tam-int-ifa)# feature disable
           
#### 3.8.3.2 [no] flow <flow-name> acl-table <acl-table-name> acl-rule <acl-rule-name> [collector <collector-name>] [sampling-rate <sample-rate>]

This command is used to configure a IFA flow. The CLI command associates a flow configuration (specified by the ACL table and ACL rule) with a collector and sampling configuration. 

      sonic(config-tam-int-ifa)#flow f1 acl-table a1 acl-rule r1 sampling-rate 100
      
      sonic(config-tam-int-ifa)#flow f1 acl-table a1 acl-rule r1 collector cr1
      
      sonic(config-tam-int-ifa)# no flow f1


  
### 3.8.4 Show Commands

#### 3.8.4.1 show tam collector [collector-name]

This command is used to show all the currently configured TAM collectors.


    sonic# show tam collector all
    ------------------------------------------------------------------------------------------------
    NAME           IP TYPE        IP ADDRESS     PORT           
    ------------------------------------------------------------------------------------------------
    cr1            ipv4           10.10.10.2     9070           

#### 3.8.4.2  show tam device

    ---------------------------------------------------------
    TAM Device Information
    ---------------------------------------------------------
    device-id: 1234


#### 3.8.4.3  show tam int-ifa supported

This command is used to determine if IFA feature functionality is supported.

    sonic# show tam int-ifa supported
    ---------------------------------------------------------
    TAM IFA Feature Information
    ---------------------------------------------------------
    Feature Supported      : True


#### 3.8.4.4  show tam drop-monitor flow [flow-name>]

This command is used to show information about all the configured flows or a specific flow identified by flow-name.

    sonic# show tam int-ifa flow
    ----------------------------------------------------------------------------
    FLOW           ACL TABLE      ACL RULE         SAMPLING RATE       COLLECTOR   
    -----------------------------------------------------------------------------
    f1             a1             r1               100                 cr1              

#### 3.8.4.5  show tam drop-monitor statistics [flow-name>]

This command is used to show per flow statistics for IFA. These statistics indicate the number of packets matching the flow.

    sonic# show tam int-ifa statistics
    ------------------------------------------------------------------------------------------------
    FLOW           ACL TABLE      ACL RULE       PACKET COUNT   BYTE COUNT
    ------------------------------------------------------------------------------------------------
    f1             t1             t1             0              0

          


 
 # 4 Flow Diagrams
## 4.1 Config call flow

***ACL configuration is created first followed by IFA configuration***

![IFA config call flow 1](images/IFA_Call_Flow_IFA_ACL.JPG)

***IFA configuration is created first followed by ACL configuration***

![IFA config call flow 2](images/IFA_Call_Flow_ACL_IFA.JPG)

# 5 Error Handling

## CLI
- CLI configuration sanity will be enforced by the CLI handler and any invalid configuration is rejected. An error is displayed to the user notifying the reason for rejection of the configuration.

## IFAOrch
- Any error occurring in the orchestration agent is logged appropriately via SWSS logging.
- Errors or failures of SAI APIs will be logged by IFAOrch.
- On failure of a SAI TAM API in the config sequence of section 3.6, the previously configured steps will be rolled back i.e previously created intermediate TAM objects for IFA etc will be destroyed.

# 6 Serviceability and Debug
Debug commands specified in section 3.7.5 will be supported once the debug framework is available.

# 7 Warm Boot Support
No special handling is done for the warm boot case. The IFA configuration is restored from the Config DB and IFA functionality will continue to work as it is through a warm boot. 

# 8 Scalability
N/A

# 9 Unit Test
## CLI

1.  Verify CLI command to configure TAM device for device id.
2.  Verify CLI command to configure TAM collector config with IPv4 type.
3.  Verify CLI command to configure TAM collector config with IPv6 type.
4.  Verify CLI command to configure TAM INT IFA feature enable.
5.  Verify CLI command to configure TAM INT IFA feature disable.
6.  Verify ACL configuration with packet action type as int_insert for IPv4 type ACL.
7.  Verify ACL configuration with packet action type as int_delete for IPv4 type ACL.
8.  Verify ACL configuration with packet action type as int_insert for IPv6 type ACL.
9.  Verify ACL configuration with packet action type as int_delete for IPv6 type ACL.
10. Verify CLI command to configure IFA flow with acl table name, acl rule name and sampling rate.
11. Verify CLI command to configure IFA flow with acl table name, acl rule name and collector.
12. Verify CLI clear command to clear TAM device.
13. Verify CLI clear command to clear TAM collector.
14. Verify CLI clear command to clear TAM INT IFA flow.
15. Verify CLI show command to show TAM device.
16. Verify CLI show command to show TAM collector.
17. Verify CLI show command to show TAM INT IFA status.
18. Verify CLI show command to show TAM INT IFA statistics for all flows.
19. Verify CLI show command to show TAM INT IFA statistics for a specific flows.
20. Verify CLI show command to show TAM INT IFA flow.

## IFA Manager
1. Verify if IFA configuration from CONFIG DB is received by IFA manager.
2. Verify if IFAOrch is able to create IFA table entries in APPL DB successfully.
3. Verify if IFAOrch is able to delete IFA table entries in APPL DB successfully.

## IFAOrch
1. Verify if IFA configuration from APPL DB is received by IFAOrch.
2. Verify if IFAOrch is able to create TAM objects for IFA configuration via SAI TAM APIs successfully.
3. Verify if IFAOrch is able to delete existing IFA configuration via SAI TAM APIs successfully.
4. Verify if IFAOrch is able to use existing TAM objects for IFA config that uses a previously configured IFA collector or IFA flow.
5. Verify if IFAOrch rolls back config in a clean way if there is a SAI API failure.
6. Verify if IFAOrch is able to notify ACL orch for IFA ACL creation.

## Functional Tests
1. Verify if ingress traffic matching IFA flow is sampled as per sampling rate.
2. Verify if IFA headers are inserted correctly at ingress device.
3. Verify if IFA headers are inserted correctly at intermediate device.
4. Verify if IFA headers are inserted correctly at egress device.
5. Verify if IFA report is sent to IFA collector from egress device.
6. Verify that IFA flow matching egress device ACL is terminated correctly, when a node has both intermediate device ACL and egress device ACL. 
7. Verify that there is no crash encountered at any of the layers with an invalid IFA configuration. 
8. Verify that an invalid configuration is rejected gracefully at appropriate layers.
9. Verify that IFA configuration is restored after warmboot.
10. Verify if IFA configuration of a session is cleared from hardware when the attached acl is removed.

## Negative Tests
1. Verify if CLI throws error when a user tries to create a duplicate IFA flow.
2. Verify if CLI throws error when a user tries to create a duplicate IFA collector.
3. Verify if CLI returns error if CLI is unable to write the IFA config to config DB.
4. Verify if CLI returns error when a IPv4 address is configured for collector type IPv6.
5. Verify if CLI returns error when a IPv6 address is configured for collector type IPv4.
6. Verify if CLI returns entry not found when a clear command is issued on non-existent flow.
7. Verify if IFA manager logs an error on receipt of an incorrect IFA table entries from CONFIG DB.
8. Verify if IFAOrch logs an error on receipt of an incorrect IFA table entries from APPL DB.
9. Verify if IFAOrch logs an error if it is unable to read IFA table data from APPL DB.
10. Verify if IFAOrch logs all errors encountered during processing of the incoming IFA config request.
11. Verify if IFAOrch logs any errors arising out of SAI API failure.
12. Verify if IFAOrch logs an error when no further IFA configuration can be configured to hardware.
13. Verify if error is logged when IFA collector is not reachable or collector is not running on UDP port.
14. Verify that crash is not seen when IFA is configured on a platform that does not support IFA.
15. Verify that IFA telemetry packet is dropped when packet length exceeds the limit defined by platform. 
