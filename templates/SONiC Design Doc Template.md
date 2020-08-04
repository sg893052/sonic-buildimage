*Notes: -*
- *This HLD is subject to a 2-stage development and Gerrit review process*
- *The first stage only includes section 1. It should appear for review within 1 to 2 weeks of feature start, as part of the "Define" process stage. The purpose is to get the functional overview and detailed requirements quickly in place and agreed. This is to ensure that the feature target content is consistent with the product or customer need, and is then used as the basis for the sizing that goes into the committed schedule.*
- *The second-stage completes the rest of the document. It is done as part of the "Implementation" process stage, and can take several weeks to appear. If requirements change (or add/del) relative to the first stage review then they should be discussed as part of the second stage review.*
- *Note that, for a small feature, where the second stage is completed within 1 to 2 weeks, then it is OK to go straight to the second-stage review.*

# Feature Name
*e.g. BFD*

# High Level Design Document
# Table of Contents
- [1 Feature Overview](#1-Feature-Overview)
    - [1.1 Target Deployment Use Cases](#11-Target-Deployment-Use-Cases)
    - [1.2 Requirements](#12-Requirements)
    - [1.3 Design Overview](#13-Design-Overview)
        - [1.3.1 Basic Approach](#131-Basic-Approach)
        - [1.3.2 Container](#132-Container)
        - [1.3.3 SAI Overview](#133-SAI-Overview)
- [2 Functionality](#2-Functionality)
- [3 Design](#3-Design)
    - [3.1 Overview](#31-Overview)
        - [3.1.1 Service and Docker Management](#311-Service-and-Docker-Management)
    - [3.2 DB Changes](#32-DB-Changes)
        - [3.2.1 CONFIG DB](#321-CONFIG-DB)
        - [3.2.2 APP DB](#322-APP-DB)
        - [3.2.3 STATE DB](#323-STATE-DB)
        - [3.2.4 ASIC DB](#324-ASIC-DB)
        - [3.2.5 COUNTER DB](#325-COUNTER-DB)
        - [3.2.6 ERROR DB](#326-ERROR-DB)
    - [3.3 Switch State Service Design](#33-Switch-State-Service-Design)
        - [3.3.1 Orchestration Agent](#331-Orchestration-Agent)
        - [3.3.2 Other Processes](#332-Other-Processes)
    - [3.4 SyncD](#34-SyncD)
    - [3.5 SAI](#35-SAI)
    - [3.6 User Interface](#36-User-Interface)
        - [3.6.1 Data Models](#361-Data-Models)
        - [3.6.2 CLI](#362-CLI)
        - [3.6.2.1 Configuration Commands](#3621-Configuration-Commands)
        - [3.6.2.2 Show Commands](#3622-Show-Commands)
        - [3.6.2.3 Exec Commands](#3623-Exec-Commands)
        - [3.6.3 REST API Support](#363-REST-API-Support)
        - [3.6.4 gNMI Support](#364-gNMI-Support)
- [4 Flow Diagrams](#4-Flow-Diagrams)
- [5 Error Handling](#5-Error-Handling)
- [6 Serviceability and Debug](#6-Serviceability-and-Debug)
- [7 Warm Boot Support](#7-Warm-Boot-Support)
- [8 Scalability](#8-Scalability)
- [9 Platform](#9-Platform)
- [10 Unit Test](#10-Unit-Test)
- [11 Internal Design Information](#11-Internal-Design-Information)
    - [11.1 IS-CLI Compliance](#111-IS-CLI-Compliance)

# List of Tables
[Table 1: Abbreviations](#table-1-Abbreviations)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | <mm/dd/yyyy>|   <name>           | Initial version                   |

# About this Manual
This document provides comprehensive functional and design information about the *feature name* feature implementation in SONiC.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| XYZ                      | Term description                    |

# 1 Feature Overview

*Describe the overall intent and purpose of the feature. What are we adding and why?*

## 1.1 Target Deployment Use Cases

*This section should discuss any user-centric deployment details, including (but not limited to): -*  
    - *Target use-cases*  
    - *Generalized target topologies (pictures please)*  
    - *Configuration and Administration*  

*Note that some features are general in nature, and so the above specifics may not apply. The goal here is to set a context for the detailed requirements.*

*Note: A library of baseline pictures will be provided to make life easier here - the idea is that the author can choose from one or more of these, make the edits, and bring them into here.*

## 1.2 Requirements

*Fill out with detailed, immutably numbered requirements from which test cases can be generated. A structured numbering scheme is used using the following sections. Some sections may be omitted according to the needs of the feature: -*

1. *Overview - Overview of the feature and its purpose and usage*
2. *Functionality - This is the main body of the detailed requirements, and contains most of the functionality statements. This section may be further sub-divided into sub-categories as makes sense, each covering a different aspect of the functionality. Numbering like this allows requirements to be later inserted into their natural position without renumbering*
3. *Interfaces - Which interfaces does the feature run on? Cover physical ports (incl. dynamic port breakout), port channels, routing interfaces (port, VLAN, loopback), tunnel interfaces (VXLAN), Management port etc*
4. *Configurability - What configuration operations will the feature have? Describe these at a general level.*
5. *User Interfaces - Which UIs will be available for managing the feature (Klish, REST, gNMI, Click, vtysh, Linux shell etc). On these: -*
    - *SONiC Management Framework UIs (YANG, REST/gNMI, Klish) is basically mandatory for all new features (full coverage)*
        - *Please also state where the underlying Northbound YANG model will come from (standards, standard augmentations/deviations, proprietary)*
    - *Click - generally only added when extending an existing Click feature*
    - *vtysh - only when adding or extending an FRR feature*
6. *Serviceability - Which serviceability features are provided to allow the feature to be debugged in development, QA, and the field (e.g. debug commands, logs, counters, state dumps etc)?*
7. *Scaling - Key scaling factors (e.g. instances, interfaces, neighbors, table entries etc)*
8. *Warm Boot/ISSU - How should the feature behave through a Warm Boot or Upgrade?* 
   - *Forwarding plane features are required to maintain consistent forwarding through and after the restart, and for the system to arrive at a fully consistent state (SW, HW) afterwards.* 
   - *For Control Plane features, what provisions (if any) are required to manage our relationships with other devices in the network to avoid forwarding plane disruption?*
9. *Platforms - In general, all SONiC features should be available on all SONiC hardware platforms. However in some cases there may be some limitations, and these can be documented here.*
10. *Limitations - Any limitations relative to what might be expected of the feature? This can include future enhancements.*

*Some general guidance for the detailed requirements: -*
- *These requirements should be written in sufficient detail to allow: -*
   - *The reviewers to confirm that the feature will meet customer and product needs*
   - *The test case developer to start writing a test plan (tracing back to these requirements)*
   - *The developer (and their manager) to come up with an accurate sizing of the work effort and schedule*

*Below is an example for the BFD feature - these are hypothetical, and only relate loosely to the existing SONiC BFD feature!*

1 Overview  
1.0 - BFD (Bidirectional Forwarding Detection) is an OAM protocol used to detect the health (or otherwise) of a forwarding path. It is used as a detection mechanism by other protocols, typically because it can detect failures faster than the protocol-native detection mechanisms.  
1.1 - BFD is widely used in Routing environments, and is broadly supported in OEM products.  
1.2 - It is standardized through RFC 5880 and a set of related RFCs (RFC 5881 to RFC 5885.  


2 Functionality

*In this example, sub-categories (with additional numbering) are used to get natural grouping and ordering, and to allow requirement insertion without re-numbering.*

2.0 Overview  
2.0.1 - Compliant with RFC 5880 (BFD) and RFC 5881 (BFD for IPv4 and IPv6 (Single Hop)) unless otherwise stated  
2.1 Protocol Functions  
2.1.1 - Support Asynchronous mode  
2.1.2 - Support Demand mode  
2.1.3 - Support Echo function  
2.2 Security Functions  
2.2.1 - Support MD5 Authentication (incl. meticulous)  
2.2.2 - Support SHA1 Authentication (incl. meticulous)  
2.3 Encapsulation Functions  
2.3.1 - IPv4 encapsulation  
2.3.1 - IPv6 encapsulation  
2.4 Timer interval range  
2.4.1 - Support >= 100ms Tx interval  
2.5 Client Protocols  
2.5.1 - Operate with BGP. BGP uses BFD to inform on the state of the forwarding path to a neighbor  
2.5.2 - When BGP Graceful Restart is enabled, helper mode is triggered when a BFD session with a neighbor goes down  

3 Interfaces  
3.0 - Supported on loopback interfaces  
3.1 - Supported on Port-based routing interfaces  
3.2 - Supported on VLAN-based routing interfaces  
3.3 - In the case of a VLAN-based interface, physical path selection is consistent with VLAN forwarding - no special measures are taken to steer to a given physical path  
3.4 - In the case of a LAG on the physical path, BFD runs at the LAG-level and not at the member port level  
3.5 - BFD can be used on routing interfaces in any VRF except for the Management VRF  

4 Configuration  
4.0 - BFD is enabled on a per-interface basis  
4.1 - BFD session parameters are managed per session  
4.2 - Session Tx intervals are configurable  
4.3 - Configure operational mode (Async, Demand, Echo)  
4.4 - A session may be administratively set to down  
4.5 - Detect Multiplier is configurable  
4.6 - Associate a BFD session to a BGP neighbor relationship  

5 User Interfaces  
5.0 The feature is managed through the SONiC Management Framework, including full support for Klish, REST and gNMI  
5.1 Base data model is OpenConfig BFD with extensions - https://github.com/openconfig/public/blob/master/release/models/bfd/openconfig-bfd.yang  

6 Serviceability  
6.0 - UI show commands are provided to show session states  
6.1 - Per-session statistics are provided on packet Tx, Rx and stats change events  
6.2 - All session events are logged  

7 Scaling  
7.0 Support up to 64 BFD sessions at an interval of 100 milliseconds (total 6400 pps)   
7.1 Number of sessions can scale up or down according to the packet (pps) limit (e.g. 128 sessions at 200 ms)  

8 Warm Boot/ISSU  
8.0 BFD allows Warm Boot to be supported in conjunction with client protocol Graceful Restart methods (e.g. BGP GR)  
8.1 During a Warm Boot, BFD packet processing stops. This would normally cause the session neighbors to detect the path as down and route around the Warm Booting switch. However, when used in conjunction with BGP GR, BFD sets a flag to state that the forwarding plane does not share fate with the control plane, enabling the neighbors to continue using the Warm Boot data path.  
8.2 After Warm Boot is completed, BGP will re-establish all the sessions and trigger BFD to re-establish corresponding BFD sessions.  

9 Platforms  
9.0 - BFD is supported on all SONiC platforms  

10 Limitations  
10.0 - BFD is not supported on the out-of-band management port  
10.1 - BFD is not supported on VXLAN tunnels  
10.2 - Multi-hop BFD is not required  
10.3 - Password session Authentication is not supported  

## 1.3 Design Overview
### 1.3.1 Basic Approach
*Where is the code coming from? Existing Open-source project? Internal Broadcom codebase? New development? Somewhere else? Mixture?*

*Example for BFD: -*

This is a software implementation. In this, the BFD state machines and session termination uses existing FRR functionality. This is adapted to the SONiC system.

Integration with the Management Framework uses existing methods for FRR adaption, including CONFIG_DB definitions as required.

### 1.3.2 Container
*Which container(s) will the code go into? Will there be new container(s)?*

*Example for BFD: -*

No new containers are introduced for this feature. Existing FRR, Mgmt and Redis containers are updated.

### 1.3.3 SAI Overview
*Do we expect to need a new SAI API specification? If so, provide rough semantics (information flow across the API)?* 
*If using existing SAI APIs, then which area? For non-hardware features this section will be N/A*

*Example for BFD: -*

No new or existing SAI services are required

Note that the SAI specification includes a BFD capability for SAI acceleration of BFD - this is not used in this feature

*---------------------------------- Stop here for the first stage review -----------------------------------------*

# 2 Functionality

*Feature specific. Much of the overview and usage should already be covered in section 1. This section is for more detailed functional descriptions as required.*

# 3 Design
## 3.1 Overview
*Big picture view of the actors involved - containers, processes, DBs etc. What's being added, what's being changed, how will they interact etc. A diagram is strongly encouraged.*

### 3.1.1 Service and Docker Management

*Discuss the dockers affected by the feature. If a new service and/or a new docker is introduced, please follow the guidelines below:*

- *Identify the dependencies on other services. This includes the starting order, restart dependencies on other services, etc. Please take the multiple images (Cloud Base, Enterprise Advanced) into consideration - where is this feature included/excluded?*
- *Identify the processes in the docker and their starting order (if applicable); specify process restartability needs and dependencies*
## 3.2 DB Changes
*Describe changes to existing DB tables or any new tables being added. Cover schema and defaults.*

*Note that changes to existing DB contents (keys, fields) should be avoided where possible, and handled very carefully where necessary. Need to consider forward/backward migration etc.*

### 3.2.1 CONFIG DB
### 3.2.2 APP DB
### 3.2.3 STATE DB
### 3.2.4 ASIC DB
### 3.2.5 COUNTER DB
### 3.2.6 ERROR DB

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
*List/describe all the orchagents that are added/changed - sub-section for each.*

### 3.3.2 Other Processes 
*Describe adds/changes to other processes within SwSS (if applicable) - e.g. \*mgrd, \*syncd*

## 3.4 SyncD
*Describe changes to syncd (if applicable).*

## 3.5 SAI
*Describe SAI APIs used by this feature. State whether they are new or existing.*

## 3.6 User Interface
*Please follow the SONiC Management Framework Developer Guide - https://drive.google.com/drive/folders/1J5_VVuwoJBa69UZ2BoXLYW8PZCFIi76K*

### 3.6.1 Data Models
*Include at least the short tree form here (as least for standard extensions/deviations, or proprietary content). The full YANG model can be a reference another file as desired.*

### 3.6.2 CLI
*Describe the type (Klish, Click etc) and content of the CLI. Klish is the preferred choice in almost all cases, and we are aiming for 100% coverage. Generally other choices would only be used where you are extending an existing feature with other prior command support.*

- *Klish commands must be added in the appropriate manner, including:*
   - *full command syntax, with descriptions*
   - *command modes*
   - *Follow IS-CLI syntax (incl "no" form)*
- *Klish command specs also ideally include:*
   - *Usage / Help string*
   - *Command line completion options*
   - *Error messages*
   - *show output formats*
   - *show running-config outputs*

*This content should go into the following sub-sections.*

#### 3.6.2.1 Configuration Commands
#### 3.6.2.2 Show Commands
#### 3.6.2.3 Exec Commands
*e.g. "Clear" commands*

### 3.6.3 REST API Support
*URL-based view*

### 3.6.4 gNMI Support
*Generally this is covered by the YANG specification - there may not be much else to add - this is a placeholder section in case there is something else.*

# 4 Flow Diagrams
*Provide flow diagrams for inter-container and intra-container interactions.*

# 5 Error Handling
*Provide details about incorporating error handling feature into the design and functionality of this feature.*

# 6 Serviceability and Debug
***This section is important and due attention should be given to it**. Topics include:*

- *Commands: Debug commands are those that are not targeted for the end user, but are more for Dev, Support and QA engineers. They are not a replacement for user show commands, and don't necessarily need to comply with all command style rules. Many features will not have these.*
- *Logging: Please state specific, known events that will be logged (and at what severity level)*
- *Counters: Ensure that you add counters/statistics for all interesting events (e.g. packets rx/tx/drop/discard)*
- *Trace: Please make sure you have incorporated the debugging framework feature (or similar) as appropriate. e.g. ensure your code registers with the debugging framework and add your dump routines for any debug info you want to be collected.*
# 7 Warm Boot Support
*Describe expected behavior and any limitations.*

# 8 Scalability
*Describe key scaling factors and considerations.*

# 9 Platform
*Describe any platform support considerations (e.g. supported/not, scaling, deviations etc)*

# 10 Unit Test
*List unit test cases added for this feature (one-liners). These should ultimately align to SPytests that can be shared with the Community.*

# 11 Internal Design Information
*Internal BRCM information to be removed before sharing with the community.*

#### 11.1 IS-CLI Compliance
*This is here because we don't want to be too externally obvious about a "follow the leader" strategy. However it must be filled in for all Klish commands.*

*The following table maps SONIC CLI commands to corresponding IS-CLI commands. The compliance column identifies how the command comply to the IS-CLI syntax:*

- ***IS-CLI drop-in replace**  – meaning that it follows exactly the format of a pre-existing IS-CLI command.*
- ***IS-CLI-like**  – meaning that the exact format of the IS-CLI command could not be followed, but the command is similar to other commands for IS-CLI (e.g. IS-CLI may not offer the exact option, but the command can be positioned is a similar manner as others for the related feature).*
- ***SONIC** - meaning that no IS-CLI-like command could be found, so the command is derived specifically for SONIC.*

|CLI Command|Compliance|IS-CLI Command (if applicable)| Link to the web site identifying the IS-CLI command (if applicable)|
|:---:|:-----------:|:------------------:|-----------------------------------|
| | | | |
| | | | |
| | | | |
| | | | |
| | | | |
| | | | |
| | | | |

***Deviations from IS-CLI:** If there is a deviation from IS-CLI, Please state the reason(s).*

