
# ACL and Flow Based Services in SONiC

High level design document version 0.4

# Table of Contents
- **[List of Tables](#list-of-tables)**
- **[Revision](#revision)**
- **[About this Manual](#about-this-manual)**
- **[Scope](#scope)**
- **[Definition / Abbreviation](#definition-_-abbreviation)**
	- [Table 1 Abbreviations](#table-1-abbreviations)
- **[1 Feature Overview](#1-feature-overview)**
	- [1.1 Access control Lists](#11-access-control-lists)
	- [1.2 Flow Based Services](#12-flow-based-services)
	- [1.3 Requirements](#13-requirements)
		- [1.3.1 Functional Requirements](#131-functional-requirements)
		- [1.3.2 Configuration and Management Requirements](#132-configuration-and-management-requirements)
		- [1.3.3 Scalability Requirements](#133-scalability-requirements)
		- [1.3.4 Warm Boot Requirements](#134-warm-boot-requirements)
	- [1.4 Design Overview](#14-design-overview)
		- [1.4.1 Basic Approach](#141-basic-approach)
			- [1.4.1.1 ACL Enhancements](#1411-acl-enhancements)
			- [1.4.1.2 Flow Based Services](#1412-flow-based-services)
		- [1.4.2 Container](#142-container)
		- [1.4.3 SAI Overview](#143-sai-overview)
- **[2 Functionality](#2-functionality)**
	- [2.1 Target Deployment Use Cases](#21-target-deployment-use-cases)
	- [2.2 Functional Description](#22-functional-description)
		- [2.2.1 ACL](#221-acl)
			- [2.2.1.1 ACL enhancements for table of type l3 and l3v6](#2211-acl-enhancements-for-table-of-type-l3-and-l3v6)
			- [2.2.1.2 L2 ACLs](#2212-l2-acls)
			- [2.2.1.3 VLAN ACLs](#2213-vlan-acls)
			- [2.2.1.4 Switch ACLs](#2214-switch-acls)
			- [2.2.1.5 ACL Lookup mode](#2215-acl-lookup-mode)
			- [2.2.1.6 Default rule for ACL tables of type l2, l3 and l3v6](#2216-default-rule-for-acl-tables-of-type-l2-l3-and-l3v6)
			- [2.2.1.7 Evaluation of ACLs applied on different interfaces](#2217-evaluation-of-acls-applied-on-different-interfaces)
			- [2.2.1.8 Interaction of L2 and IPv4 / IPv6 ACLs](#2218-interaction-of-l2-and-ipv4-_-ipv6-acls)
		- [2.2.2 Flow based services](#222-flow-based-services)
			- [2.2.2.1 Classifiers](#2221-classifiers)
				- *[2.2.2.1.1 Classification using ACLs](#22211-classification-using-acls)*
					- *[2.2.2.1.1.1 ACL Rules with permit action](#222111-acl-rules-with-permit-action)*
					- *[2.2.2.1.1.2 ACL Rules with deny action](#222112-acl-rules-with-deny-action)*
				- *[2.2.2.1.2 Classification using L2-L4 header fields](#22212-classification-using-l2-l4-header-fields)*
			- [2.2.2.2 Policies](#2222-policies)
				- *[2.2.2.2.1 Policy of type QoS](#22221-policy-of-type-qos)*
				- *[2.2.2.2.2 Policy of type Monitoring](#22222-policy-of-type-monitoring)*
				- *[2.2.2.2.3 Policy of type Forwarding](#22223-policy-of-type-forwarding)*
			- [2.2.2.3 Applying policies to interfaces](#2223-applying-policies-to-interfaces)
				- *[2.2.2.3.1 Evaluation of traffic within the same policy](#22231-evaluation-of-traffic-within-the-same-policy)*
				- *[2.2.2.3.2 Evaluation of traffic across interfaces of same types](#22232-evaluation-of-traffic-across-interfaces-of-same-types)*
				- *[2.2.2.3.3 Evaluation of traffic across interfaces of different types](#22233-evaluation-of-traffic-across-interfaces-of-different-types)*
				- *[2.2.2.3.4 Evaluation of traffic across policies of different types](#22234-evaluation-of-traffic-across-policies-of-different-types)*
	- [2.3 Feature support matrix](#23-feature-support-matrix)
		- [2.3.1 Policy Type support](#231-policy-type-support)
		- [2.3.2 QoS policy actions support](#232-qos-policy-actions-support)
		- [2.3.3 Monitoring policy actions support](#233-monitoring-policy-actions-support)
		- [2.3.4 Forwarding policy actions support](#234-forwarding-policy-actions-support)
- **[3 Design](#3-design)**
	- [3.1 Overview](#31-overview)
	- [3.2 DB Changes](#32-db-changes)
		- [3.2.1 Config DB](#321-config-db)
			- [3.2.1.1 Hardware Table](#3211-hardware-table)
			- [3.2.1.2 ACL Table](#3212-acl-table)
			- [3.2.1.3 ACL Rule](#3213-acl-rule)
				- *[3.2.1.3.1 ACL Rule of type L2](#32131-acl-rule-of-type-l2)*
			- [3.2.1.4 ACL Rule of type l3 or l3v6](#3214-acl-rule-of-type-l3-or-l3v6)
			- [3.2.1.5 Classifier table](#3215-classifier-table)
			- [3.2.1.6 Policy table](#3216-policy-table)
			- [3.2.1.7 Policy sections table](#3217-policy-sections-table)
			- [3.2.1.8 Policy binding table](#3218-policy-binding-table)
		- [3.2.2 App DB](#322-app-db)
			- [3.2.2.1 ACL Table](#3221-acl-table)
			- [3.2.2.2 ACL Rule Table](#3222-acl-rule-table)
			- [3.2.2.3 Policy based forwarding group table](#3223-policy-based-forwarding-group-table)
		- [3.2.3 State DB](#323-state-db)
			- [3.2.3.1 Policy based forwarding group state](#3231-policy-based-forwarding-group-state)
		- [3.2.4 ASIC DB](#324-asic-db)
		- [3.2.5 Counter DB](#325-counter-db)
	- [3.3 Switch State Service Design](#33-switch-state-service-design)
		- [3.3.1 Orchestration Agent](#331-orchestration-agent)
			- [3.3.1.1 Policy Based Forwarding Orchestration agent](#3311-policy-based-forwarding-orchestration-agent)
		- [3.3.2 ACL Services daemon](#332-acl-services-daemon)
			- [3.3.2.1 ACL Manager](#3321-acl-manager)
			- [3.3.2.2 Policy Manager](#3322-policy-manager)
		- [3.3.3 Other Process](#333-other-process)
	- [3.4 SyncD](#34-syncd)
	- [3.5 SAI](#35-sai)
	- [3.6 Manageability](#36-manageability)
		- [3.6.1 Data Models](#361-data-models)
		- [3.6.2 Configuration Commands](#362-configuration-commands)
			- [3.6.2.1 Configuring ACL Counter mode](#3621-configuring-acl-counter-mode)
				- *[3.6.2.1.1 Configuring ACL Counter mode using Sonic-CLI](#36211-configuring-acl-counter-mode-using-sonic-cli)*
				- *[3.6.2.1.2 Configuring ACL lookup mode using Click CLI (Deprecated)](#36212-configuring-acl-lookup-mode-using-click-cli-deprecated)*
			- [3.6.2.2 Creating/Deleting a MAC/IPv4/IPv6 ACL](#3622-creating_deleting-a-mac_ipv4_ipv6-acl)
			- [3.6.2.3 Creating/Deleting a MAC ACL Rule](#3623-creating_deleting-a-mac-acl-rule)
			- [3.6.2.4 Creating/Deleting a IP ACL Rule](#3624-creating_deleting-a-ip-acl-rule)
			- [3.6.2.5 Creating/Deleting a IPv6 ACL Rule](#3625-creating_deleting-a-ipv6-acl-rule)
			- [3.6.2.6 Adding/Deleting ACL remark](#3626-adding_deleting-acl-remark)
			- [3.6.2.7 Adding/Deleting ACL Rule Remark](#3627-adding_deleting-acl-rule-remark)
			- [3.6.2.8 Applying ACL](#3628-applying-acl)
				- *[3.6.2.8.1 Applying ACL to different interfaces](#36281-applying-acl-to-different-interfaces)*
				- *[3.6.2.8.2 Applying ACL globally](#36282-applying-acl-globally)*
				- *[3.6.2.8.3 Applying ACL to Control Plane](#36283-applying-acl-to-control-plane)*
			- [3.6.2.9 Create classifier](#3629-create-classifier)
				- *[3.6.2.9.1 Creating classifier through Sonic-CLI](#36291-creating-classifier-through-sonic-cli)*
				- *[3.6.2.9.2 Creating classifier through click cli (Deprecated)](#36292-creating-classifier-through-click-cli-deprecated)*
			- [3.6.2.10 Update classifier match parameters](#36210-update-classifier-match-parameters)
				- *[3.6.2.10.1 Update classifier match parameters using Sonic-CLI](#362101-update-classifier-match-parameters-using-sonic-cli)*
					- *[3.6.2.10.1.1 Add or delete match ACL to classifier](#3621011-add-or-delete-match-acl-to-classifier)*
					- *[3.6.2.10.1.2 Add or delete match on source MAC](#3621012-add-or-delete-match-on-source-mac)*
					- *[3.6.2.10.1.3 Add or delete match on destination MAC](#3621013-add-or-delete-match-on-destination-mac)*
					- *[3.6.2.10.1.4 Add or delete match on ethertype](#3621014-add-or-delete-match-on-ethertype)*
					- *[3.6.2.10.1.5 Add or delete match on PCP](#3621015-add-or-delete-match-on-pcp)*
					- *[3.6.2.10.1.6 Add or delete match on VLAN ID](#3621016-add-or-delete-match-on-vlan-id)*
					- *[3.6.2.10.1.7 Add or delete match on source IPv4 Address](#3621017-add-or-delete-match-on-source-ipv4-address)*
					- *[3.6.2.10.1.8 Add or delete match on destination IPv4 Address](#3621018-add-or-delete-match-on-destination-ipv4-address)*
					- *[3.6.2.10.1.9 Add or delete match on source IPv6 Address](#3621019-add-or-delete-match-on-source-ipv6-address)*
					- *[3.6.2.10.1.10 Add or delete match on destination IPv4 Address](#36210110-add-or-delete-match-on-destination-ipv4-address)*
					- *[3.6.2.10.1.11 Add or delete match on IP Protocol](#36210111-add-or-delete-match-on-ip-protocol)*
					- *[3.6.2.10.1.12 Add or delete match on source TCP or UDP Port](#36210112-add-or-delete-match-on-source-tcp-or-udp-port)*
					- *[3.6.2.10.1.13 Add or delete match on destination TCP or UDP Port](#36210113-add-or-delete-match-on-destination-tcp-or-udp-port)*
					- *[3.6.2.10.1.14 Add or delete match on TCP flags](#36210114-add-or-delete-match-on-tcp-flags)*
				- *[3.6.2.10.2 Update classifier match parameters using Click CLI (Deprecated)](#362102-update-classifier-match-parameters-using-click-cli-deprecated)*
			- [3.6.2.11 Add classifier description](#36211-add-classifier-description)
			- [3.6.2.12 Delete classifier description](#36212-delete-classifier-description)
			- [3.6.2.13 Delete classifier](#36213-delete-classifier)
				- *[3.6.2.13.1 Delete classifier using Sonic-CLI](#362131-delete-classifier-using-sonic-cli)*
				- *[3.6.2.13.2 Delete classifier using Click CLI (Deprecated)](#362132-delete-classifier-using-click-cli-deprecated)*
			- [3.6.2.14 Add policy](#36214-add-policy)
				- *[3.6.2.14.1 Add policy using Sonic-CLI](#362141-add-policy-using-sonic-cli)*
				- *[3.6.2.14.2 Add policy using Click CLI (Deprecated)](#362142-add-policy-using-click-cli-deprecated)*
			- [3.6.2.15 Delete policy](#36215-delete-policy)
				- *[3.6.2.15.1 Deleting policy using Sonic-CLI](#362151-deleting-policy-using-sonic-cli)*
				- *[3.6.2.15.2 Deleting policy using Click CLI (Deprecated)](#362152-deleting-policy-using-click-cli-deprecated)*
			- [3.6.2.16 Add policy description](#36216-add-policy-description)
			- [3.6.2.17 Delete policy description](#36217-delete-policy-description)
			- [3.6.2.18 Add flow identified by a classifier to a policy](#36218-add-flow-identified-by-a-classifier-to-a-policy)
				- *[3.6.2.18.1 Add flow using Sonic-CLI](#362181-add-flow-using-sonic-cli)*
				- *[3.6.2.18.2 Add flow using Click CLI (Deprecated)](#362182-add-flow-using-click-cli-deprecated)*
			- [3.6.2.19 Delete flow identified by a classifier to a policy](#36219-delete-flow-identified-by-a-classifier-to-a-policy)
			- [3.6.2.20 Deleting flow using Sonic-CLI](#36220-deleting-flow-using-sonic-cli)
			- [3.6.2.21 Deleting flow using Click CLI (Deprecated)](#36221-deleting-flow-using-click-cli-deprecated)
			- [3.6.2.22 Add flow description](#36222-add-flow-description)
			- [3.6.2.23 Delete flow description](#36223-delete-flow-description)
			- [3.6.2.24 Add action(s) to flows](#36224-add-actions-to-flows)
				- *[3.6.2.24.1 Add QoS actions to the flow using Sonic-CLI](#362241-add-qos-actions-to-the-flow-using-sonic-cli)*
					- *[3.6.2.24.1.1 Add DSCP remarking action](#3622411-add-dscp-remarking-action)*
					- *[3.6.2.24.1.2 Delete DSCP remarking action](#3622412-delete-dscp-remarking-action)*
					- *[3.6.2.24.1.3 Add PCP remarking action](#3622413-add-pcp-remarking-action)*
					- *[3.6.2.24.1.4 Delete PCP remarking action](#3622414-delete-pcp-remarking-action)*
					- *[3.6.2.24.1.5 Add policer action](#3622415-add-policer-action)*
					- *[3.6.2.24.1.6 Delete policer action](#3622416-delete-policer-action)*
					- *[3.6.2.24.1.7 Add set traffic-class action](#3622417-add-set-traffic-class-action)*
					- *[3.6.2.24.1.8 Delete set traffic-class action](#3622418-delete-set-traffic-class-action)*
				- *[3.6.2.24.2 Adding monitoring actions to the flow](#362242-adding-monitoring-actions-to-the-flow)*
					- *[3.6.2.24.2.1 Adding mirror session action](#3622421-adding-mirror-session-action)*
					- *[3.6.2.24.2.2 Deleting mirror session action](#3622422-deleting-mirror-session-action)*
				- *[3.6.2.24.3 Adding forwarding actions to the flow](#362243-adding-forwarding-actions-to-the-flow)*
					- *[3.6.2.24.3.1 Adding / Deleting IPv4 next-hop](#3622431-adding-_-deleting-ipv4-next-hop)*
					- *[3.6.2.24.3.2 Adding / Deleting IPv6 next-hop](#3622432-adding-_-deleting-ipv6-next-hop)*
					- *[3.6.2.24.3.3 Adding / Deleting egress interface](#3622433-adding-_-deleting-egress-interface)*
					- *[3.6.2.24.3.4 Adding default drop action](#3622434-adding-default-drop-action)*
				- *[3.6.2.24.4 Add flow actions using Click CLIs (Deprecated)](#362244-add-flow-actions-using-click-clis-deprecated)*
			- [3.6.2.25 Applying the policy to an interface](#36225-applying-the-policy-to-an-interface)
				- *[3.6.2.25.1 Applying policy to an interface using Sonic-CLI](#362251-applying-policy-to-an-interface-using-sonic-cli)*
				- *[3.6.2.25.2 Applying policy to an interface using Click CLI (Deprecated)](#362252-applying-policy-to-an-interface-using-click-cli-deprecated)*
			- [3.6.2.26 Removing policy from an interface](#36226-removing-policy-from-an-interface)
				- *[3.6.2.26.1 Removing policy from an interface using Sonic-CLI](#362261-removing-policy-from-an-interface-using-sonic-cli)*
				- *[3.6.2.26.2 Removing policy from an interface using Click CLI (Deprecated)](#362262-removing-policy-from-an-interface-using-click-cli-deprecated)*
		- [3.6.3 Show Commands](#363-show-commands)
			- [3.6.3.1 Show ACL binding summary](#3631-show-acl-binding-summary)
			- [3.6.3.2 Show ACL Rules and statistics](#3632-show-acl-rules-and-statistics)
			- [3.6.3.3 Clear ACL statistics](#3633-clear-acl-statistics)
			- [3.6.3.4 Show classifier details](#3634-show-classifier-details)
				- *[3.6.3.4.1 Show classifier details using Sonic-CLI](#36341-show-classifier-details-using-sonic-cli)*
				- *[3.6.3.4.2 Show classifier details using Click CLI (Deprecated)](#36342-show-classifier-details-using-click-cli-deprecated)*
				- *[3.6.3.4.3 Show classifier sample output](#36343-show-classifier-sample-output)*
			- [3.6.3.5 Show policy details](#3635-show-policy-details)
				- *[3.6.3.5.1 Show policy details using Sonic-CLI](#36351-show-policy-details-using-sonic-cli)*
				- *[3.6.3.5.2 Show policy details using Click-CLI (Deprecated)](#36352-show-policy-details-using-click-cli-deprecated)*
				- *[3.6.3.5.3 Sample output](#36353-sample-output)*
			- [3.6.3.6 Show policy binding summary](#3636-show-policy-binding-summary)
				- *[3.6.3.6.1 Show policy binding summary using Sonic-CLI](#36361-show-policy-binding-summary-using-sonic-cli)*
				- *[3.6.3.6.2 Show policy binding summary using Click CLI (Deprecated)](#36362-show-policy-binding-summary-using-click-cli-deprecated)*
				- *[3.6.3.6.3 Show policy binding summary sample output](#36363-show-policy-binding-summary-sample-output)*
			- [3.6.3.7 Show/Clear policy binding and counters for an interface](#3637-show_clear-policy-binding-and-counters-for-an-interface)
				- *[3.6.3.7.1 Show/Clear policy binding and counters using SONiC-CLI](#36371-show_clear-policy-binding-and-counters-using-sonic-cli)*
				- *[3.6.3.7.2 Show/Clear policy binding and counters using Click CLI (Deprecated)](#36372-show_clear-policy-binding-and-counters-using-click-cli-deprecated)*
				- *[3.6.3.7.3 Show policy binding and counters sample output](#36373-show-policy-binding-and-counters-sample-output)*
			- [3.6.3.8 TCAM Allocation](#3638-tcam-allocation)
				- *[3.6.3.8.1 Available predefined TCAM profiles](#36381-available-predefined-tcam-profiles)*
				- *[3.6.3.8.2 Predefined TCAM profile details](#36382-predefined-tcam-profile-details)*
				- *[3.6.3.8.3 Setting the predefined profile](#36383-setting-the-predefined-profile)*
				- *[3.6.3.8.4 Checking the current TCAM Allocation](#36384-checking-the-current-tcam-allocation)*
				- *[3.6.3.8.5 Clearing the TCAM Allocation scheme.](#36385-clearing-the-tcam-allocation-scheme)*
				- *[3.6.3.8.6 Modifying the current TCAM allocation](#36386-modifying-the-current-tcam-allocation)*
				- *[3.6.3.8.7 Setting a custom TCAM allocation](#36387-setting-a-custom-tcam-allocation)*
		- [3.6.4 REST / gNMI / IS CLI API Support](#364-rest-_-gnmi-_-is-cli-api-support)
- **[4 Flow Diagrams](#4-flow-diagrams)**
	- [4.1 Create a Classifier](#41-create-a-classifier)
	- [4.2 Create a QoS Policy and Section](#42-create-a-qos-policy-and-section)
	- [4.3 Bind QoS policy to an interface](#43-bind-qos-policy-to-an-interface)
	- [4.4 Creating ACL rules with policer](#44-creating-acl-rules-with-policer)
	- [4.5 Deleting ACL Rules with policer](#45-deleting-acl-rules-with-policer)
- **[5 Error Handling](#5-error-handling)**
- **[6 Serviceability and Debug](#6-serviceability-and-debug)**
- **[7 Warm Boot Support](#7-warm-boot-support)**
- **[8 Scalability](#8-scalability)**
	- [8.1 Software scalability](#81-software-scalability)
	- [8.2 ACL Table Scalability](#82-acl-table-scalability)
- **[9 Limitation](#9-limitation)**
- **[10 Unit Test](#10-unit-test)**
- **[11 Appendix: Sample configuration](#11-appendix-sample-configuration)**
- **[12 Internal Design Information](#12-internal-design-information)**
	- [12.1 Future Design Enhancements](#121-future-design-enhancements)

# List of Tables
[Table 1 Abbreviations](#table-1-abbreviations)

# Revision
| Rev  | Date       | Author              | Change Description                       |
| ---- | ---------- | ------------------- | ---------------------------------------- |
| 0.1  | 07/12/2019 | Abhishek Dharwadkar | Initial version                          |
| 0.2  | 10/15/2019 | Abhishek Dharwadkar | Add ACL enhancement and policing details |
| 0.3  | 11/10/2019 | Abhishek Dharwadkar | Add FBS support for mirroring            |
| 0.4  | 03/25/2020 | Abhishek Dharwadkar | Add FBS support for forwarding           |

# About this Manual
This document provides general information about the ACL enhancements and Flow Based Services feature in SONiC.

# Scope
This document provides general information about the ACL enhancements and Flow Based Services feature implementation in SONiC.

# Definition / Abbreviation

## Table 1 Abbreviations
| **Term** | **Meaning**          |
| -------- | -------------------- |
| ACL      | Access Control List  |
| PBR      | Policy Based Routing |
| SPAN     | Switch Port ANalyzer |
| sFlow    | Sampled flow         |
| MQC      | Modular QoS CLIs     |
| CIR      | Commited Information Rate |
| CBS      | Commited Burst Size |
| PIR      | Peak Information Rate |
| PBS      | Peak Burst Size |

# 1 Feature Overview

## 1.1 Access control Lists
Access-control lists (ACLs) are used to filter traffic based on the contents of the Layer 2 and/or Layer 3 packet header. User ACLs are bound to interfaces on which traffic filtering is intended.

The primary benefits of an ACL are 

1. Provide a measure of security.
2. Save network resources by reducing unwanted traffic.
3. Block unwanted traffic or users from accessing the network resources
4. Reduce the chance of DOS attacks
5. Use as classification for QoS Actions like Rate-limiting, PCP Remarking, DSCP Remarking etc.
6. Use as classification to make forwarding decisions like PBR.

## 1.2 Flow Based Services
Flow based services enhance the switches capability to have enhanced and better control over the traffic by providing a generic framework for "Match and Set" features. Incoming packets can be classified according to match rules using fields from L2-L4 headers and defined actions can be taken accordingly. 

Example features might be: -

1. QoS remarking and policing
2. Monitoring (e.g. SPAN, sFlow)
3. Forwarding (e.g. PBR, L2 redirect)

This feature provides a common infrastructure service for such features. The common infrastructure service can itself use the SONiC ACL feature for packet match rule definition, or can use it's own UI for more sophisticated classifiers. 

## 1.3 Requirements

### 1.3.1 Functional Requirements

The following are the requirements for ACL enhancements and Flow Based Services feature:
1. Support Layer 2 ACL i.e. support match on Layer 2 fields like Source MAC, Destination MAC, PCP, etc.
2. Support L3 header fields like DSCP, ICMP type and ICMP code.
3. Support ACL application at Port/LAG, VLAN and Switch level.
4. Support hierarchical filtering of traffic across interfaces.
5. Support for dedicated TCAMs for ACL binding for better utilization of the ACL Tables and for getting detailed counter information.
3. Provide a industry standard MQC equivalent framework for fine grained classification of the traffic via ACL or fields of L2-L4 header and take specific actions on the classified traffic.
4. Support DSCP, COS Remarking and Policing QoS action. 
8. Support mirroring/SPAN action.
9. Support flow based forwarding/routing.
10. Enhance monitoring capabilities by supporting Flow based sFlow in future.
11. Ability to bind multiple policies of different types to Ports/LAGs, VLANs and Switch.
12. Independent ingress and egress policy binding for a given interface.
13. Merge non conflicting actions from different policies using ASIC capabilities to simplify user configuration. 

### 1.3.2 Configuration and Management Requirements
1. Provide config commands to support configuration and application of Flow based Services Policies.
4. Provide statistics support

### 1.3.3 Scalability Requirements
Flow based services will use the same resources as ACL. The exact numbers will be silicon specific.

### 1.3.4 Warm Boot Requirements
Flow based services should work seamlessly across warmboot. Statistics must be preserved across warmboot.

## 1.4 Design Overview

### 1.4.1 Basic Approach

#### 1.4.1.1 ACL Enhancements
A new ACL services daemon will be implemented to assist ACL orchagent for ACL. ACL Services daemon will support multiple ACL based features. The ACL manager, which is part of ACL services daemon will consume the user configuration from Config DB and build an aggregated ACL configuration in App DB. ACL Orchagent will consume the aggregated ACL from App DB. The schema for ACL table and ACL rule will remain identical to that of Config DB to minimize the impact on ACL orchagent.

#### 1.4.1.2 Flow Based Services
Flow based services will be implemented as part of ACL Services Daemon in SWSS. Flow based services is new code added to SONiC repository. Manageability related code will be added to manageability related  container.

### 1.4.2 Container
This code will be part of ACL Services daemon in switch state service (swss) container.

### 1.4.3 SAI Overview

Existing [ACL SAI](https://github.com/opencomputeproject/SAI/blob/master/inc/saiacl.h) will be used and no enhancements are necessary.

# 2 Functionality

## 2.1 Target Deployment Use Cases
1. Data traffic filtering using L2, L3 and L4 header fields.
2. Mirroring traffic flows using VLAN ID.
3. Increased ACL table application when there are less number of rules per ACL.
3. QoS policies can be used to fine tune traffic by classifying the traffic via L2-L4 ACL/Header fields and taking actions like Remarking, Policing etc.
4. Forwarding policies can used to control the traffic path in the network for a flow.

## 2.2 Functional Description

### 2.2.1 ACL

#### 2.2.1.1 ACL enhancements for table of type l3 and l3v6

DSCP, ICMP(v6) Type and ICMP(v6) Code keys are enabled on tables of type l3(v6) along with mirror table.

#### 2.2.1.2 L2 ACLs

Layer 2 ACL can be used to filter traffic based on MAC header fields like Source MAC, Destination MAC, VLAN etc. Layer 2 ACLs can be used to filter traffic of any ether type including IPv4 and IPv6. Layer 2 ACLs can be applied to all interfaces ie Ethernet, PortChannel, VLAN or Switch. 

#### 2.2.1.3 VLAN ACLs

VLAN ACLs provide traffic filtering for all traffic that is bridged within the same VLAN or routed in or out of a VLAN. VLAN ACLs are applied to both bridged and routed traffic. VLAN ACLs supports MAC, IPv4 or IPv6 ACLs.

#### 2.2.1.4 Switch ACLs

Switch ACLs provide traffic filtering for all bridged or routed traffic in the switch. Switch ACLs supports MAC, IPv4 or IPv6 ACLs.

#### 2.2.1.5 ACL Lookup mode

A new mode called ***optimized*** mode is available to help optimize TCAM utilization. The older ACL behavior is new called as ***legacy*** mode. In Legacy mode, each ACL Table of a given type consumes 1 SAI ACL table ( i.e. 1 to 1 mapped). Since each silicon only supports a small number of SAI ACL tables (typically single digit) this may lead to under utilization of TCAM resources of all entries in the Table are not used. In optimized mode, all ACL tables used for the same functionality will be using the same SAI ACL table. Example all datapath ACL bindings for L3 ACL will use 1 SAI table. Similarly all QoS Flow based services policies applied will use the same SAI ACL Table. This leads to better TCAM utilization in certain cases.

Lookup mode is only applicable for datapath ACL. Flow based services will always use lookup mode as ***optimized*** with no TCAM sharing, as actions like policing cant be supported in legacy mode.

#### 2.2.1.6 Default rule for ACL tables of type l2, l3 and l3v6

All ACLs of type l2, l3 and l3v6 has a default deny any rule appended at the end with lowest priority internally for datapath ACL. The default deny any rule is not applicable for Flow based services.

#### 2.2.1.7 Evaluation of ACLs applied on different interfaces

The following diagram shows the different ACLs supported and the location where the ACLs will be applied.

![ACL application at different stages](images/ACLStages.png "ACL application at different stages")

**Figure 1: ACL application at different stages**

The following diagram shows the evaluation order for datapath ACLs. The same is applicable for Flow based services also and will be captured in upcoming sections. Datapath ACLs have a default deny any rule. This rule will be applied only after user configured ACL rules are evaluated at Port/LAG, VLAN and Switch Level.

![ACL Evaluation order](images/ACLEvalMultiIntf.png "ACL Evaluation order")

**Figure 2: ACL Evaluation order**

#### 2.2.1.8 Interaction of L2 and IPv4 / IPv6 ACLs

Security ACL match can result the following actions

**Permit** : This action allows forwarding in data plane and trap to CPU. 
**Deny** : This action disallows forwarding in data plane but allows traps to CPU.

An incoming traffic can match both L2 and L3 (IPv4/IPv6) datapath ACLs. The following table shows the combined result. The counters for both ACLs will be incremented to indicate the match.

| Result from L2 ACL | Result from L3 ACL (IPv4 or IPv6) | Final Result |
| ------------------ | --------------------------------- | ------------ |
| FORWARD            | FORWARD                           | FORWARD      |
| DROP               | FORWARD                           | DROP         |
| FORWARD            | DROP                              | DROP         |
| DROP               | DROP                              | DROP         |

Please note that in the above table, its assumed that the L2ACL result is also applicable for L3 traffic. 

### 2.2.2 Flow based services
Flow based services provide a modular and extensible framework to classify traffic  and take appropriate action for the traffic. Flow based services can be used for different features like QoS, Forwarding and Monitoring. 

Flow based services configuration is made up of 3 parts
1. Classification of traffic via classifiers
2. Configuring actions to be taken for each classified flow via a Policy
3. Application of Policy to different interfaces

#### 2.2.2.1 Classifiers

Classifiers are used to identify interesting traffic flows by using parts of L2-L4 headers directly or indirectly via an ACL. The criterion for identification is configured by the `MATCH_TYPE` key. 

##### 2.2.2.1.1 Classification using ACLs

A classifier uses ACL for flow identification if the `MATCH_TYPE` is set to `"acl"`. The name of the ACL is specified by `ACL_NAME` key. Each classifier can use only 1 ACL of type L2, IPv4 or IPv6 to identify a traffic flow. The ACL must be already configured for the classifier to be considered as complete. If the ACL is not configured then the classifier is considered incomplete and acts as no-op. Classifiers support forward referencing of ACLs, so the configuration will be retained and no error will be displayed to the user when ACL is not present. When the ACL is configured, the classifier becomes active and applies any actions configured for it in the policies.

###### 2.2.2.1.1.1 ACL Rules with permit action

Actions like DSCP remarking will be applied for ACL rules if they are configured with `PACKET_ACTION` of `FORWARD`. 

###### 2.2.2.1.1.2 ACL Rules with deny action

Actions will be not taken for ACL rules if they are configured with `PACKET_ACTION` of `DROP`. This provides a mechanism to exclude certain specific sub flows, which makes it easier for the user to configure ACLs in certain scenarios. For example if an action say DSCP remarking needs to be taken if the packet has Source IP address which matches prefix 10.1.1.x/24, but DSCP remarking should not be applied to 2 specific IP addresses say 10.1.1.1 and 10.1.1.00 then user can configure 2 ACL rules with `DROP` as `PACKET_ACTION`  for the 2 excluded IPs and 3rd rule can be generic rule for the subnet with `FORWARD` as `PACKET_ACTION`. If this mechanism is unavailable then user has to configure 100s of rules for each specific IP address for which DSCP remarking needs to be applied.

##### 2.2.2.1.2 Classification using L2-L4 header fields

Classification of the flow can also be done by directly specifying the header fields from L2-L4. This gives user ability to select combination of different fields from various headers for more fine grained classification of the flow. For example user can choose say Source MAC Address, VLAN, Destination IP address, TCP flags to identify a flow and take some action like Policing on it. This is not supported by ACLs. Configuration of mutually exclusive header fields like IPv4 address and IPv6 addresses is considered as invalid. If none of the L2-L4 match fields are specified then its considered as "Match Any". Modifying the match criterion may need reprogramming of the ASIC entry and can have impact on the traffic.

#### 2.2.2.2 Policies

A policy provides information on the action that needs to be applied to the flows identified by the classifiers.  A policy consists of multiple sections or stanzas, where each section consists of a classifier and its corresponding actions. Each section has a priority associated with it which will indicate the order in which the ACL with appropriate will be programmed. 

Policies can be of different types based on the kind of actions supported. Policies of type QoS and monitoring are supported in SONiC 3.0.

##### 2.2.2.2.1 Policy of type QoS

QoS Policies only support the following actions

1. DSCP Remarking : Set DSCP in IPv4 or IPv6 header to the new value specified in the configuration matches.
2. PCP Remarking : Set PCP value in 802.1Q VLAN header to the new value specified in the configuration.
3. Policing : Apply rate-limiting parameters.

##### 2.2.2.2.2 Policy of type Monitoring

Monitoring policies only support the following actions

1. SPAN/ERSPAN session using session name

Monitoring policies are supported only at ingress.

##### 2.2.2.2.3 Policy of type Forwarding

Forwarding policies only support the following actions

1. Route IPv4 traffic to a IPv4 next-hop.
2. Route IPv6 traffic to a IPv6 next-hop.
3. Forward L2 traffic to specified interface.
4. Drop action if none of the next-hops are reachable or egress interface is not L2 and link up.

Forwarding policies are supported only at ingress.

#### 2.2.2.3 Applying policies to interfaces

When a policy is applied to an interface at ingress or egress, action will be taken in case of match, only traffic is ingressing or egressing from that interface. Traffic ingressing or egressing from other interfaces will not be affected. 

A policy can be applied in the following direction 

1. Ingress
2. Egress

A policy can be applied to the following interface types

1. Port or LAG
2. VLAN : Traffic tagged with or classified into the VLAN will be affected if matched.
3. Switch : All traffic flowing through the switch will be affect if matched

Only 1 policy of a given type (e.g QoS) can be applied to an interface in a given direction. For example on Ethernet0 only 1 QoS policy say P1 can be applied at ingress. Its not possible to apply another QoS policy say P2 for Ethernet0 at ingress.  However user can apply policies of different types to the same interface. This gives user ability to classify the same traffic (using same or different classifier) and apply multiple actions of type qos, monitoring and forwarding etc to it.

The same policy can be also be applied to different interfaces and both ingress and egress (if supported).

##### 2.2.2.3.1 Evaluation of traffic within the same policy

As mentioned in [section 2.2.2.2](#2_2_2_2-policies), a policy can have multiple sections. Each section has a classifier, priority and actions associated with it. The policies are programmed in the ASIC in the order of the priority or the sections. The order of the evaluation is based on the numerical value of `PRIORITY`.

The following diagram shows the order in which the policy sections are evaluated and final results when only 1 section matches the traffic. The final result is picked up only from the matching section.

![Policy evaluation with single match](images/PolicyEval.png "Policy evaluation with single match")

**Figure 3: Policy evaluation with single match**

The following diagram shows the order in which the policy sections are evaluated and final results when multiple sections match the traffic. The final result is picked up from the section with highest numerical value of the priority.

![Policy evaluation with multiple match](images/PolicyEvalMultiHit.png "Policy evaluation with multiple match")

**Figure 4: Policy evaluation with multiple match**

##### 2.2.2.3.2 Evaluation of traffic across interfaces of same types

Policies applied to interfaces of same types are always non conflicting hence there is no conflict resolution between them.

##### 2.2.2.3.3 Evaluation of traffic across interfaces of different types

A policy can be applied at Port level, VLAN level and Switch level. A given packet can match all the 3 policies.  All interface types have a implicit priority associated with them which is in the order Port/LAG > VLAN > Switch. When a packet matches Port Policy, VLAN Policy and Switch policy, only Port policy will be applied. The evaluation of the port policy is  same as described in [section 2.2.2.3.1](#2_2_2_3_1-evaluation-of-traffic-within-the-same-policy). Counter of only Port policy will increment.

![Policy evaluation across interface types](images/PolicyEvalMultiIntf.png "Policy evaluation across interface types")

**Figure 5: Policy evaluation across interface types**

##### 2.2.2.3.4 Evaluation of traffic across policies of different types

Data path ACLs, Mirror ACLs and Flow based services policies are internally converted into ACL tables and are added as members of a parallel lookup ACL table group. This results in all ACL tables to be looked up in parallel and results merged in ASIC. The maximum number of tables that can be looked up in parallel is ASIC dependent. CRM statistics for ACL table will provide this information.

Policies of different types are designed to take specific actions. QoS Polices are meant to take only QoS actions like DSCP remarking. Data path ACLs only support forward and drop actions and mirror ACL supports only mirror action. These actions are non conflicting so when a packet matches a QoS Policy, Data Path ACL to permit and mirror ACL, the packet will be permitted, QoS actions will be applied and a mirror copy will also be generated. Counters of Data path ACL, Mirror ACL and QoS policy may increment depending on the capability of the ASIC.

![Result merge across interfaces of different types](images/PolicyEvalMultiPolicy.png "Result merge across interfaces of different types")

**Figure 6: Result merge across interfaces of different types**

## 2.3 Feature support matrix

### 2.3.1 Policy Type support

| Policy Type | Release supported |
| ----------- | ----------------- |
| QoS         | SONiC 3.0         |
| Monitoring  | SONiC 3.0         |
| Forwarding  | SONiC 3.1         |

### 2.3.2 QoS policy actions support

| Feature        | Release supported |
| -------------- | ----------------- |
| DSCP Remarking | SONiC 3.0         |
| PCP Remarking  | SONiC 3.0         |
| Policing       | SONiC 3.0         |

### 2.3.3 Monitoring policy actions support

| Feature             | Release supported |
| ------------------- | ----------------- |
| SPAN/ERSPAN session | SONiC 3.0         |

### 2.3.4 Forwarding policy actions support

| Feature              | Release supported |
| -------------------- | ----------------- |
| IPv4 / IPv6 Next Hop | SONiC 3.1         |
| L2 Egress interface  | SONiC 3.1         |
| Default drop action  | SONiC 3.1         |


# 3 Design

## 3.1 Overview
The following diagram shows the high level design overview of ACL enhancements and flow based services in SONiC. ACL Services Daemon is  composed of multiple sub-modules. The main module i.e. ACL Manager provides the ACL related APIs to create an ACL, delete an ACL, bind to an interface etc. Flow based services manager will handle the flow based services configuration and invoke appropriate ACL Manager APIs to program the polices in hardware.

![Flow Based Service Overview](images/Overview.png "Flow Based Service Overview")

**Figure 7: Flow Based Service Overview**

## 3.2 DB Changes

### 3.2.1 Config DB

#### 3.2.1.1 Hardware Table

A new table called HARDWARE is introduced to configure ACL lookup parameters

```
key = HARDWARE:ACCESS_LIST
;field       = value
LOOKUP_MODE  = "optimized" / "legacy"
COUNTER_MODE = "per-rule" / "per-interface-rule"
```

#### 3.2.1.2 ACL Table

A new ACL type called l2 is introduced to support MAC ACLs.

```
key           = ACL_TABLE:name            ; acl_table_name must be unique
;field        = value
policy_desc   = 1*255VCHAR                ; name of the ACL policy table description
stage         = "INGRESS"/"EGRESS"        ; stage of ACL lookup
type          = "l2"/"l3"/"l3v6"/"mirror" ; type of acl table, every type of
                                          ; table defines the match/action a
                                          ; specific set of match and actions.
ports         = [0-max_ports]*port_name   ; the ports to which this ACL
                                          ; table is applied, can be entry
                                          ; value annotations

;value annotations
max_ports     = 1*5DIGIT                ; number of ports supported on the chip
port_name     = 1*64VCHAR               ; name of the port, must be unique
                                        ; port name can be Ethernetxxx or
                                        ; PortChannelxxx, Vlanxxxx or Switch
```

#### 3.2.1.3 ACL Rule

##### 3.2.1.3.1 ACL Rule of type L2

```
key = ACL_RULE:table_name:rule_name  ; key of the rule entry in the table,
                                     ; seq is the order of the rules
                                     ; when the packet is filtered by the
                                     ; ACL "policy_name".
                                     ; A rule is always assocaited with a
                                     ; policy.
;field        = value
PRIORITY      = acl_priority         ; rule priority. Supported range is 1-65535
PACKET_ACTION = "forward"/"drop"/"redirect:"redirect_action 
                           ; action when the fields are matched
                           ; In case of packet_action="redirect" a parameter is required
                           ; This parameter defines a destination for redirected packets
                           ; it could be:
                           ; name of physical port.          Example: "Ethernet10"
                           ; name of LAG port                Example: "PortChannel5"

SRC_MAC       = mac_addr ["/" mac_mask ] ; Source MAC address
DST_MAC       = mac_addr ["/" mac_mask ] ; Destination MAC address
ETHER_TYPE    = ethtype                  ; Ethernet type field
PCP           = pcp_val [ "/"" pcp_val]  ; PCP Value in range of 0-7
DEI           = BIT ["/" BIT ]           ; DEI Value. 0 or 1.
VLAN          = vlan_id                  ; VLAN ID. Supported range is 1-4094.
                                         ; valid only if the ACL is applied to Port or
                                         ; LAG or Switch. For VLAN binding this will be
                                         ; ignored.
    
;value annotations
acl_priority = 1*4DIGIT / %x31-36 %x30-35 %x30-35 %x30-33 %x30-35
ethtype      = "0x"3*4HEXDIG
mac_addr     = 2HEXDIG "-" 2HEXDIG "-" 2HEXDIG "-" 2HEXDIG "-" 2HEXDIG "-" 2HEXDIG 
               / 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG
vlan_id      = %x31-39                     ; 1-9
               / %x31-39 DIGIT             ; 10-99
               / %x31-39 2DIGIT            ; 100-999
               / %x31-33 3DIGIT            ; 1000-3999
               / %x34 %x30 %x30-39 %x30-34 ; 4000 - 4094
pcp_val      = %x30-37
```

#### 3.2.1.4 ACL Rule of type l3 or l3v6

The following fields are supported for ACL Rule of type l3 or l3v6

```
key: ACL_RULE:table_name:rule_name         ; key of the rule entry in the table,
                                           ; priority is the order of the rules
                                           ; when the packet is filtered by the
                                           ; ACL Table.
                                           ; A rule is always assocaited with a
                                           ; ACL table.

;field        = value

DSCP          = dscp_val["/"dscp_val]  ; DSCP field. Valid range is 0-63
ICMP_TYPE     = h8/dec-octet         ; ICMP Type Value
ICMP_CODE     = h8/dec-octet         ; ICMP Code value
VLAN          = vlan_id              ; VLAN ID. Supported range is 1-4094. This is
                                     ; valid only if the ACL is applied to Port or
                                     ; LAG or Switch. For VLAN binding this will be
                                     ; ignored.

;value annotations
dscp_val    = DIGIT / %x31-36 %x30-33
h8          = 1*2HEXDIG
dec-octet   = DIGIT                   ; 0-9
              / 2DIGIT                ; 10-99
              / "1" 2DIGIT            ; 100-199
              / "2" %x30-34 DIGIT     ; 200-249
vlan_id      = %x31-39                     ; 1-9
               / %x31-39 DIGIT             ; 10-99
               / %x31-39 2DIGIT            ; 100-999
               / %x31-33 3DIGIT            ; 1000-3999
               / %x34 %x30 %x30-39 %x30-34 ; 4000 - 4094
```

#### 3.2.1.5 Classifier table

A classifier is used to setup the match criterion to identify a traffic flow. A flow can be either identified by an ACL or part of L2-L4 header.

```
key           = CLASSIFIER_TABLE:name      ; name must be unique
                                           ; name must be 1-63 chars long

;field            = value
DESCRIPTION       = 1*255VCHAR
MATCH_TYPE        = "acl" / "fields"       ; Match on ACL or Match using individual
                                           fields.
ACL_NAME          = 1*255VCHAR             ; ACL name as present in ACL_TABLE
                                           ; valid when match_type = "acl"
SRC_MAC       = mac_addr ["/" mac_mask ]   ; Source MAC address
DST_MAC       = mac_addr ["/" mac_mask ]   ; Destination MAC address
ETHER_TYPE    = h16                        ; Ethernet type field
IP_PROTOCOL   = h8                         ; options of the l3_protocol_type field
SRC_IP        = ipv4_prefix                ; options of the source ipv4
                                           ; address (and mask) field
DST_IP        = ipv4_prefix                ; options of the destination ipv4
                                           ; address (and mask) field
SRC_IPV6      = ipv6_prefix                ; options of the source ipv4
                                           ; address (and mask) field
DST_IPV6      = ipv6_prefix                ; options of the destination ipv4
L4_SRC_PORT   = port_num                   ; source L4 port. Valid if IP Protocol is TCP
                                           ; or UDP 
L4_SRC_PORT_RANGE = port_num_L-port_num_H  ; source ports range of L4 ports field
L4_DST_PORT   = port_num                   ; destination L4 port. Valid if IP Protocol is
                                           ; TCP or UDP 
L4_DST_PORT_RANGE = port_num_L-port_num_H  ; destination ports range of L4 ports field
TCP_FLAGS     = h8/h8                      ; TCP flags field and mask
DSCP          = dscp_val[/dscp_val]        ; DSCP field. Valid range is 0-63
                                           ; address (and mask) field

;value annotations
h16           = 1*4HEXDIG
mac_addr      = 2HEXDIG "." 2HEXDIG "." 2HEXDIG "." 2HEXDIG "." 2HEXDIG "." 2HEXDIG 
                / 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG
port_num      = 1*5DIGIT   ; a number between 0 and 65535
port_num_L    = 1*5DIGIT   ; a number between 0 and 65535,
                           ; port_num_L < port_num_H
port_num_H    = 1*5DIGIT   ; a number between 0 and 65535,
                           ; port_num_L < port_num_H
dscp_val      = DIGIT / %x31-36 %x30-33
ipv4_prefix = dec-octet "." dec-octet "." dec-octet "." dec-octet "/" %d1-32

ipv6_prefix   =                               6( h16 ":" ) ls32
                 /                       "::" 5( h16 ":" ) ls32
                 / [               h16 ] "::" 4( h16 ":" ) ls32
                 / [ *1( h16 ":" ) h16 ] "::" 3( h16 ":" ) ls32
                 / [ *2( h16 ":" ) h16 ] "::" 2( h16 ":" ) ls32
                 / [ *3( h16 ":" ) h16 ] "::"    h16 ":"   ls32
                 / [ *4( h16 ":" ) h16 ] "::"              ls32
                 / [ *5( h16 ":" ) h16 ] "::"              h16
                 / [ *6( h16 ":" ) h16 ] "::"
   
dec-octet   = DIGIT                   ; 0-9
              / 2DIGIT                ; 10-99
              / "1" 2DIGIT            ; 100-199
              / "2" %x30-34 DIGIT     ; 200-249
h8          = 1*2HEXDIG
h16         = 1*4HEXDIG
ls32        = ( h16 ":" h16 ) / IPv4address
```

#### 3.2.1.6 Policy table

Policy table is used to configure the policy parameters. 

```
key           = POLICY_TABLE:name            ; name must be unique
                                             ; name must be 1-63 chars long

;field      = value
TYPE        = "qos" / "monitoring" / "forwarding" ;
DESCRIPTION = 1*255VCHAR          ; Policy Description

;value annotations
```

#### 3.2.1.7 Policy sections table

Policy details table provides information on the classifiers to use and their corresponding actions. A policy can have up to 128 classifiers

```
key = POLICY_SECTIONS_TABLE:policy_name:classifier_name ; name must be unique
                                                        ; name must be 1-63 chars long

;field             = value
PRIORITY           = 1*4DIGIT     ; Valid Range is 0-1023
DESCRIPTION        = 1*255VCHAR   ; Policy Description
SET_DSCP           = dscp_val     ; Valid only when policy is of type "qos"
SET_PCP            = pcp_val      ; Valid only when policy is of type "qos"
SET_POLICER_CIR    = 1*12DIGIT    ; Valid only when policy is of type "qos"
SET_POLICER_CBS    = 1*12DIGIT    ; Valid only when policy is of type "qos"
SET_POLICER_PIR    = 1*12DIGIT    ; Valid only when policy is of type "qos"
SET_POLICER_PBS    = 1*12DIGIT    ; Valid only when policy is of type "qos"
SET_MIRROR_SESSION = 1*72VCHAR   ; Valid only when policy is of type "monitoring"
SET_IP_NEXT_HOP    = [1-64]*nh-entry ; Valid only when policy is of type "forwarding"
SET_IPV6_NEXT_HOP  = [1-64]*v6nh-entry ; Valid only when policy is of type "forwarding"
SET_INTERFACE      = [1-64]*port-entry ; Valid only when policy is of type "forwarding"
DEFAULT_PACKET_ACTION = "DROP" / "FORWARD" ; Valid only when policy is of type "forwarding"

;value annotations
dscp_val = DIGIT / %x31-36 %x30-33
pcp_val  = %x30-37
d8       =   DIGIT               ; 0-9
           / %x31-39 DIGIT       ; 10-99
           / "1" 2DIGIT          ; 100-199
           / "2" %x30-34 DIGIT   ; 200-249
           / "25" %x30-35        ; 250-255
ip-addr  = d8 "." d8 "." d8 "." d8
vrf-name = "default" / "Vrf"1*63VCHAR
priority = 1*4DIGIT / %x31-36 %x30-35 %x30-35 %x30-33 %x30-35
h16      = 1*4HEXDIG
ipv6-addr =                            7(h16 ":") h16
            /                     "::" 6(h16 ":") h16
            / [             h16 ] "::" 5(h16 ":") h16
            / [ *1(h16 ":") h16 ] "::" 4(h16 ":") h16
            / [ *2(h16 ":") h16 ] "::" 3(h16 ":") h16
            / [ *3(h16 ":") h16 ] "::" 2(h16 ":") h16
            / [ *4(h16 ":") h16 ] "::"   h16 ":"  h16
            / [ *5(h16 ":") h16 ] "::"            h16
            / [ *6(h16 ":") h16 ] "::"
port-name = "Ethernet"1*3DIGIT / "PortChannel"1*3DIGIT

nh-entry = ip-addr "|" vrf-name "|" priority
v6nh-entry = ipv6-addr "|" vrf-name "|" priority
port-entry = port-name "|" priority
```

#### 3.2.1.8 Policy binding table

This provides information on Policy application on ports

```
key           = POLICY_BINDING_TABLE:port_name   ; port_name is the name of the Port or
                                                 ; LAG or VLAN or "switch"

;field                     = value
INGRESS_QOS_POLICY         = 1*63VCHAR
INGRESS_MONITORING_POLICY  = 1*63VCHAR
INGRESS_FORWARDING_POLICY  = 1*63VCHAR
EGRESS_QOS_POLICY          = 1*63VCHAR

;value annotations
```

### 3.2.2 App DB

#### 3.2.2.1 ACL Table

ACL Table in the App DB will follow the same schema as that of Config DB. ACL Table in App DB is produced by ACL services daemon based on the Config DB information. The information will be consumed by ACL Orchagent to populate the ASIC DB. The ACL Table and Rule names will be different and will be populated by ACL Services daemon

#### 3.2.2.2 ACL Rule Table

ACL Rule in App DB will contain additional packet actions for DSCP, PCP remarking and rate limiting.

```
key: ACL_RULE_TABLE:table_name:rule_name   ; key of the rule entry in the table,
                                           ; priority is the order of the rules
                                           ; when the packet is filtered by the
                                           ; ACL Table.
                                           ; A rule is always assocaited with a
                                           ; ACL table.

;field              = value
SET_DSCP            = dscp_val            
SET_PCP             = pcp_val
SET_POLICER         = 1*63VCHAR            ; Name of the policer
SET_MIRROR_SESSION  = 1*63VCHAR            ; Name of the mirror session
SET_PBF_GROUP       = 1*63VCHAR            ; Name of the policy based forwarding group

;value annotations
dscp_val      = DIGIT / %x31-36 %x30-33
pcp_val       = %x30-37
```

The SET_POLICER action uses the policer name as defined in POLICER_TABLE schema at  https://github.com/Azure/sonic-swss/blob/master/doc/swss-schema.md 

#### 3.2.2.3 Policy based forwarding group table

A new table called `PBF_GROUP_TABLE` is added. This table will be referenced by the ACL rule. The `CONFIGURED_EGRESS` contains egress information (Next-hop/interface) as configured by the user. `DERIVED_EGRESS` contains egress information (Next-hop/interface) derived by using interface VRF when VRF is not configured explicitly by the user. This table will be populated by aclsvcd.

```
key: PBF_GROUP_TABLE:group_name ; group_name will be internally generated by aclsvcd

;field                = value
TYPE                  = "IPV4" / "IPV6" / "L2"
DEFAULT_PACKET_ACTION = "DROP"
CONFIGURED_EGRESS     = [0-64]*nh-entry / [0-64]*v6nh-entry / [0-64]*port-entry
DERIVED_EGRESS        = [0-64]*nh-entry / [0-64]*v6nh-entry / [0-64]*port-entry

;value annotations
The details of nh-entry or v6nh-entry or port-entry is same as described in Config DB.
```

### 3.2.3 State DB

#### 3.2.3.1 Policy based forwarding group state

The PBF_GROUP_STATE_TABLE contains the information about the currently selected egress for forwarding. This information can be used to show the same information to the user. This table will be populated by PBF orch agent.

```
key: PBF_GROUP_TABLE:group_name ; group_name will be internally generated by aclsvcd

;field                = value
TYPE                  = "IPV4" / "IPV6" / "L2"
SELECTED_CONFIGURED   = nh-entry / v6nh-entry / port-entry / "DROP"
SELECTED_DERIVED      = nh-entry / v6nh-entry / port-entry / "DROP"

;value annotations
The details of nh-entry or v6nh-entry or port-entry is same as described in Config DB.
```

### 3.2.4 ASIC DB

None

### 3.2.5 Counter DB

When ACL lookup mode is legacy, the counters will be stored in counters table as its done currently. When ACL lookup is in **optimized** mode, ACL counters will be stored in ACL_COUNTERS table. The schema for the same will be.

```
ACL_COUNTERS: rule_counter_name 
            ; rule_counter_name is a unique name generated in the following format
            ; <ACL_TABLE_NAME>:<ACL_RULE_NAME>:<INTERFACE_NAME>:<ACL_STAGE>

Packets : <packets_counter_value>
Bytes : <bytes_counter_value>
```

 For FBS policies the counters will be saved in

```
FBS_COUNTERS: flow_counter_name
             ; flow_counter_name is a unique name generated in the following format
             ; <POLICY_NAME>:<CLASSIFIER_NAME>:<INTERFACE_NAME>:<ACL_STAGE>

Packets : <packets_counter_value>
Bytes : <bytes_counter_value>
```

The Policer counters will be stored in

```
POLICER_COUNTERS: policer_counter_name
                ; policer_counter_name is a unique name generated in the following format 
                ; <POLICY_NAME>:<CLASSIFIER_NAME>:<INTERFACE_NAME>:<ACL_STAGE>

GreenPackets : <packets_counter_value>
GreenBytes : <bytes_counter_value>
YellowPackets : <packets_counter_value>
YellowBytes : <bytes_counter_value>
RedPackets : <packets_counter_value>
RedBytes : <bytes_counter_value>
```

## 3.3 Switch State Service Design

### 3.3.1 Orchestration Agent

Flow based services manager will utilize and extend the ACL Orchagent. ACL Orchagent will be extended to support DSCP, PCP remarking and policing action. To support policer as of the actions, Acl orchagent has to interact with Policer orchagent to get the SAI object ID associated with the policer name. This will be done using the existing APIs exposed by the policer orchagent namely `policerExists`, `getPolicerOid`, `increaseRefCount`, `decreaseRefCount`.

#### 3.3.1.1 Policy Based Forwarding Orchestration agent

To add support for Forwarding in Policy based services, a new Orchagent called Policy Based Forwarding (PBF) orchagent will be added. PBF orch will consume `PBF_GROUP_TABLE` and will listen to Route Orch, Neighbor Orch , Port Orch and FDB Orch to decide when a Next-hop or Interface is ready for forwarding.  ACL Orchagent will register with PBF orchagent for any changes in the selected egress. PBF orchagent will notify the current selected egress per group and ACL Orchagent will update the ACL entries if necessary.

### 3.3.2 ACL Services daemon

#### 3.3.2.1 ACL Manager

ACL Manager handles the notification from Config DB ACL tables and ACL rules and populates them in App DB. When the ACL Lookup mode is configured as **optimized**,  ACL manager consolidates all ACL bindings for the ACL tables of the same type and populates the information in the same App DB thereby reducing the number of SAI ACL tables needed. If ACL lookup mode is configured as **legacy**, ACL manager acts as passthrough for all ACL tables and rules and populates them in App DB without any modifications.

ACL Manager also provides APIs to Create, Delete ACLs, Rules, and bind and unbind to various interfaces. No change is necessary to ACL manager is generic and can be used for datapath ACLs and Flow based services.

#### 3.3.2.2 Policy Manager

A new submodule called policy manager will be added to ACL services daemon to handle configuration set in Config DB. Policy manager maintains a copy of various classifier, policies and bindings and call appropriate ACL managers APIs. ACL manager will populate necessary ACL tables and rules in App DB which will be picked up and programmed by ACL orchagent. The details of ACL manager is captured in ACL HLD.

### 3.3.3 Other Process

No change to other process.

## 3.4 SyncD
No changes to SyncD.

## 3.5 SAI
No change to SAI.

## 3.6 Manageability

### 3.6.1 Data Models
Openconfig ACL and Openconfig Flow based services models are supported. Openconfig flow based services is a proprietary yang model following openconfig style. Openconfig QoS and Openconfig Policy Forwarding model are not supported as they do not support all SONiC functionality like Flow prioritization, Match on ACL, Layer 2 forwarding etc.

### 3.6.2 Configuration Commands

The following commands are used to configure Policy based services

#### 3.6.2.1 Configuring ACL Counter mode

##### 3.6.2.1.1 Configuring ACL Counter mode using Sonic-CLI
| Mode | Config |
| ---- | ------ |
| Syntax | sonic(config)# **hardware**<br/>sonic(config-hardware)# **access-list**<br/>sonic(config-hardware-acl)# **counters** { **per-entry** \| **per-interface-entry** } <br/>&nbsp;***per-entry*** : ACL counters are aggregated over all interfaces, and reported only per ACL entry. <br/>&nbsp;***per-interface-entry*** : ACL counters are reported per ACL entry and per interface for all ACL bindings. |
| Change history | SONiC 3.1 - Introduced |

##### 3.6.2.1.2 Configuring ACL lookup mode using Click CLI (Deprecated)
```
root@sonic:/home/admin# config hardware access-list --help
Usage: config hardware access-list [OPTIONS]

  Configure ACL Hardware parameters

Options:
  -l, --lookup <lookup_mode>    Lookup mode. Valid options are optimized,
                                legacy
  -c, --counter <counter_mode>  Counter mode. Valid options are per-rule, per-
                                interface-rule
  -?, -h, --help                Show this message and exit.
```

#### 3.6.2.2 Creating/Deleting a MAC/IPv4/IPv6 ACL
| Mode | Config |
| ---- | ------ |
| Syntax | \[no\] { **mac** \| **ip** \| **ipv6** } **access-list** *NAME* |
| Arguments | ***NAME***: Name of the ACL. String max 63 characters. Must begin with alpha numeric character |
| Change history | SONiC 3.1 - Introduced |


#### 3.6.2.3 Creating/Deleting a MAC ACL Rule
| Mode | ACL |
| ---- | ------ |
| Syntax | sonic(config-mac-acl)# **seq** *<1-65535>* { **permit** \| **deny** } { **any** \| **host** *SMAC* \| *SMAC* *SMAC_MASK* } { *any* \| **host** *DMAC* \| *DMAC* *DMAC_MASK* }  \[ *ETHERTYPE* \| **ipv4** \| **ipv6** \| **arp** \] \[ **vlan** *VLANID* \] \[ **pcp** *<0-7>* \] \[ **dei** *<0-1>* \] **remark** *DESCRIPTION*<br/>sonic(config-mac-acl)# **no seq** *<1-65535>* |
| Arguments | ***SMAC***: Source MAC address in xxxx.xxxx.xxxx or xx:xx:xx:xx:xx:xx format<br/>***SMAC_MASK***: Source MAC address mask in xxxx.xxxx.xxxx or xx:xx:xx:xx:xx:xx format<br/>***DMAC***: Destination MAC address in xxxx.xxxx.xxxx or xx:xx:xx:xx:xx:xx format<br/>***DMAC_MASK***: Destination MAC address mask in xxxx.xxxx.xxxx or xx:xx:xx:xx:xx:xx format<br/>***ETHERTYPE***: Ethertype value in range 1536-65536 in hex or decimal format.<br/>***VLANID***: VLAN ID in range 1-4094 in decimal format<br/>***DESCRIPTION***: A string describing the rule. Must be in double quotes if it contains spaces. |
| Change history | SONiC 3.1 - Introduced |

#### 3.6.2.4 Creating/Deleting a IP ACL Rule
| Mode | ACL |
| ---- | ------ |
| Syntax | sonic(config-ipv4-acl)# **seq** *<1-65535>* { **permit** \| **deny** } **tcp** { **any** \| **host** *SIP* \| *SIP* \[ / *SIP_PREFIX_LEN* \] } \[ { **eq** \| **gt** \| **lt** } *PORT* \| **range** *BEGIN* *END* } \] { **any** \| **host** *DIP* \| *DIP* \[ / *DIP_PREFIX_LEN* \] } \[ { **eq** \| **gt** \| **lt** } *PORT* \| **range** *BEGIN* *END* } \] \[ **dscp** *DSCP_VAL* \] \[ **fin** \| **syn** \| **rst** \| **psh** \| **ack** \| **urg** \] \[ **vlan** *VLANID* \] [ **remark** *DESCRIPTION* ]<br/><br/>sonic(config-ipv4-acl)# **seq** *<1-65535>* { **permit** \| **deny** } **udp** { **any** \| **host** *SIP* \| *SIP* \[ / *SIP_PREFIX_LEN* \] } \[ { **eq** \| **gt** \| **lt** } *PORT* \| **range** *BEGIN* *END* } \] { **any** \| **host** *DIP* \| *DIP* \[ / *DIP_PREFIX_LEN* \] } \[ { **eq** \| **gt** \| **lt** } *PORT* \| **range** *BEGIN* *END* } \] \[ **dscp** *DSCP_VAL* \] \[ **vlan** *VLANID* \] [ **remark** *DESCRIPTION* ]<br/><br/>sonic(config-ipv4-acl)# **seq** *<1-65535>* { **permit** \| **deny** } **icmp** { **any** \| **host** *SIP* \| *SIP* \[ / *SIP_PREFIX_LEN* \] } { **any** \| **host** *DIP* \| *DIP* \[ / *DIP_PREFIX_LEN* \] } \[ **dscp** *DSCP_VAL* \] \[ **type** *ICMP_TYPE* \] \[ **code** *ICMP_CODE* \] \[ **vlan** *VLANID* \] [ **remark** *DESCRIPTION* ]<br/><br/>sonic(config-ipv4-acl)# **seq** *<1-65535>* { **permit** \| **deny** } { **ip** \| *IP_PROTOCOL* } { **any** \| **host** *SIP* \| *SIP* \[ / *SIP_PREFIX_LEN* \] } { **any** \| **host** *DIP* \| *DIP* \[ / *DIP_PREFIX_LEN* \] } \[ **dscp** *DSCP_VAL* \] \[ **vlan** *VLANID* \] [ **remark** *DESCRIPTION* ]<br/><br/>sonic(config-ip-acl)# **no seq** *<1-65535>* |
| Arguments | ***IP_PROTOCOL***: IP Protocol value in decimal format<br/>***SIP***: Source IPv4 address<br/>***SIP_PREFIX_LEN***: Source IPv4 address prefix length<br/>***DIP***: Destination IPv4 address<br/>***DIP_PREFIX_LEN***: Destination IPv4 address prefix length<br/>***PORT, BEGIN, END***: TCP or UDP Port number in decimal format. END > BEGIN. Valid only when IP_PROTOCOL is 6, 17 ie TCP or UDP<br/>***DSCP_VAL***: DSCP value in decimal format<br/>***ICMP_TYPE***: ICMP type in decimal format. Valid only when IP_PROTOCOL is 1 i.e. ICMP<br/>***ICMP_CODE***: ICMP code in decimal format. Valid only when IP_PROTOCOL is 1 i.e. ICMP<br/>***VLANID***: VLAN ID in range 1-4094 in decimal format<br/>***DESCRIPTION***: A string describing the rule. Must be in double quotes if it contains spaces. |
| Change history | SONiC 3.1 - Introduced |

#### 3.6.2.5 Creating/Deleting a IPv6 ACL Rule
| Mode | ACL |
| ---- | ------ |
| Syntax | sonic(config-ipv6-acl)# **seq** *<1-65535>* { **permit** \| **deny** } **tcp** { **any** \| **host** *SIPV6* \| *SIPV6* \[ / *SIPV6_PREFIX_LEN* \] } \[ { **eq** \| **gt** \| **lt** } *PORT* \| **range** *BEGIN* *END* } \] { **any** \| **host** *DIP* \| *DIP* \[ / *DIP_PREFIX_LEN* \] } \[ { **eq** \| **gt** \| **lt** } *PORT* \| **range** *BEGIN* *END* } \] \[ **dscp** *DSCP_VAL* \] \[ **fin** \| **syn** \| **rst** \| **psh** \| **ack** \| **urg** \] \[ **vlan** *VLANID* \] [ **remark** *DESCRIPTION* ]<br/><br/>sonic(config-ipv6-acl)# **seq** *<1-65535>* { **permit** \| **deny** } **udp** { **any** \| **host** *SIPV6* \| *SIPV6* \[ / *SIPV6_PREFIX_LEN* \] } \[ { **eq** \| **gt** \| **lt** } *PORT* \| **range** *BEGIN* *END* } \] { **any** \| **host** *DIPV6* \| *DIPV6* \[ / *DIPV6_PREFIX_LEN* \] } \[ { **eq** \| **gt** \| **lt** } *PORT* \| **range** *BEGIN* *END* } \] \[ **dscp** *DSCP_VAL* \] \[ **vlan** *VLANID* \] [ **remark** *DESCRIPTION* ]<br/><br/>sonic(config-ipv6-acl)# **seq** *<1-65535>* { **permit** \| **deny** } **icmp** { **any** \| **host** *SIPV6* \| *SIPV6* \[ / *SIPV6_PREFIX_LEN* \] } { **any** \| **host** *DIPV6* \| *DIPV6* \[ / *DIPV6_PREFIX_LEN* \] } \[ **dscp** *DSCP_VAL* \] \[ **type** *ICMP_TYPE* \] \[ **code** *ICMP_CODE* \] \[ **vlan** *VLANID* \] [ **remark** *DESCRIPTION* ]<br/><br/>sonic(config-ipv6-acl)# **seq** *<1-65535>* { **permit** \| **deny** } *IPV6_PROTOCOL* { **any** \| **host** *SIPV6* \| *SIPV6* \[ / *SIPV6_PREFIX_LEN* \] } { **any** \| **host** *DIPV6* \| *DIPV6* \[ / *DIPV6_PREFIX_LEN* \] } \[ **dscp** *DSCP_VAL* \] \[ **vlan** *VLANID* \] [ **remark** *DESCRIPTION* ]<br/><br/>sonic(config-ipv6-acl)# **no seq** *<1-65535>* |
| Arguments | ***IPV6_PROTOCOL***: IPv6 Protocol value in decimal format<br/>***SIPV6***: Source IPv6 address<br/>***SIPV6_PREFIX_LEN***: Source IPv6 address prefix length<br/>***DIPV6***: Destination IPv6 address<br/>***DIPV6_PREFIX_LEN***: Destination IPv6 address prefix length<br/>***PORT, BEGIN, END***: TCP or UDP Port number in decimal format. END > BEGIN. Valid only when IP_PROTOCOL is 6, 17 ie TCP or UDP<br/>***DSCP_VAL***: DSCP value in decimal format<br/>***ICMP_TYPE***: ICMP type in decimal format. Valid only when IP_PROTOCOL is 58 i.e. ICMPv6<br/>***ICMP_CODE***: ICMP code in decimal format. Valid only when IP_PROTOCOL is 58 i.e. ICMPv6<br/>***VLANID***: VLAN ID in range 1-4094 in decimal format<br/>***DESCRIPTION***: A string describing the rule. Must be in double quotes if it contains spaces. |
| Change history | SONiC 3.1 - Introduced |

#### 3.6.2.6 Adding/Deleting ACL remark
| Mode | ACL |
| ---- | ------ |
| Syntax | sonic(config-xxx-acl)# **remark** *DESCRIPTION*<br/>sonic(config-xxx-acl)# **no remark** |
| Arguments | ***DESCRIPTION***: A string describing the ACL. Must be in double quotes if it contains spaces. |
| Change history | SONiC 3.1 - Introduced |

#### 3.6.2.7 Adding/Deleting ACL Rule Remark
| Mode | ACL |
| ---- | ------ |
| Syntax | sonic(config-xxx-acl)# **seq** *<1-65535>* **remark** *DESCRIPTION*<br/>sonic(config-xxx-acl)# **no seq** *<1-65535>* **remark** |
| Arguments | ***DESCRIPTION***: A string describing the rule. Must be in double quotes if it contains spaces. Remark can be set to an existing rule only. |
| Change history | SONiC 3.1 - Introduced |

#### 3.6.2.8 Applying ACL
##### 3.6.2.8.1 Applying ACL to different interfaces
| Mode | Config |
| ---- | ------ |
| Syntax | sonic(config-if-xxx)# \[ **no** \] { **mac** \| **ip** \| **ipv6** } **access-group** *NAME* { **in** \| **out** } |
| Change history | SONiC 3.1 - Introduced |

##### 3.6.2.8.2 Applying ACL globally
| Mode | Config |
| ---- | ------ |
| Syntax | sonic(config)# \[ **no** \] { **mac** \| **ip** \| **ipv6** } **access-group** *NAME* { **in** \| **out** } |
| Change history | SONiC 3.1 - Introduced |

##### 3.6.2.8.3 Applying ACL to Control Plane
| Mode | Config |
| ---- | ------ |
| Syntax | sonic(config)# **line vty** <br/>sonic(config-line-vty)# \[ **no** \] { **ip** \| **ipv6** } **access-group** *NAME* **in** |
| Change history | SONiC 3.1 - Introduced |

#### 3.6.2.9 Create classifier

##### 3.6.2.9.1 Creating classifier through Sonic-CLI

| Mode | Config |
| ---- | --------------------------------------------------- |
| Syntax         | SONiC(config)# **classifier** *NAME* **match-type** **acl**  |
| Syntax         | SONiC(config)# **classifier** *NAME* **match-type** **fields** **match-all** |
| Arguments      | ***NAME***: String of 1-63 characters in length. Must begin with a alpha numeric character. Rest of the characters can be alpha numeric or hyphen (-) or underscore (\_). |
| Change history | SONiC 3.1 - Introduced       |

##### 3.6.2.9.2 Creating classifier through click cli (Deprecated)

```
root@sonic:~# config classifier add --help
Usage: config classifier add [OPTIONS] <name>

  Add a Classifier

Options:
  -m, --match-type <match-type>   Set match type to acl or fields  [required]
  -d, --description <description>
                                  Set classifier description
  --help                          Show this message and exit.

```

#### 3.6.2.10 Update classifier match parameters

##### 3.6.2.10.1 Update classifier match parameters using Sonic-CLI

###### 3.6.2.10.1.1 Add or delete match ACL to classifier

| Mode   | Classifier                                              |
| ------ | ------------------------------------------------------- |
| Syntax | SONiC(config-classifier)# **match access-group** { **mac** \| **ip** \| **ipv6** } *NAME* |
| Syntax | SONiC(config-classifier)# **no match access-group** |
| Arguments | ***NAME***: String of 1-63 characters in length. Must begin with a alpha numeric character. Rest of the characters can be alpha numeric or hyphen (-) or underscore (\_). |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.10.1.2 Add or delete match on source MAC

| Mode   | Classifier                                                   |
| ------ | ------------------------------------------------------------ |
| Syntax | SONiC(config-classifier)# **match source-address mac** *MAC* [ / *MAC_MASK*] |
| Syntax | SONiC(config-classifier)# **no match source-address mac** |
| Arguments | ***MAC***: MAC address in xxxx.xxxx.xxxx or xx:xx:xx:xx:xx:xx format <br/>***MAC_MASK***: MAC address mask in xxxx.xxxx.xxxx or xx:xx:xx:xx:xx:xx format |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.10.1.3 Add or delete match on destination MAC

| Mode   | Classifier                                                   |
| ------ | ------------------------------------------------------------ |
| Syntax | SONiC(config-classifier)# **match destination-address mac** *MAC* [ / *MAC_MASK*] |
| Syntax | SONiC(config-classifier)# **no match destination-mac** |
| Arguments | ***MAC***: MAC address in xxxx.xxxx.xxxx or xx:xx:xx:xx:xx:xx format <br/>***MAC_MASK***: MAC address mask in xxxx.xxxx.xxxx or xx:xx:xx:xx:xx:xx format |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.10.1.4 Add or delete match on ethertype

| Mode   | Classifier                                                   |
| ------ | ------------------------------------------------------------ |
| Syntax | SONiC(config-classifier)# **match ether-type** { **ip** \| **ipv6** \| *ETHER_TYPE* } |
| Syntax | SONiC(config-classifier)# **no match ether-type** |
| Arguments | ***ETHER_TYPE***: Ethertype value in hex format in range 0x600 - 0xFFFF |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.10.1.5 Add or delete match on PCP

| Mode           | Classifier                                                   |
| -------------- | ------------------------------------------------------------ |
| Syntax         | SONiC(config-classifier)# **match pcp** { **be** \| **bk** \| **ee** \| **ca** \| **vi** \| **vo** \| **ic** \| **nc** \| *PCP_VAL* } |
| Syntax         | SONiC(config-classifier)# **no match pcp**                   |
| Arguments      | ***be***: Best effort (0)<br/>***bk***: Background (1)<br/>***ee***: Excellent effort (2)<br/>***ca***: Critical applications (3)<br/>***vi***: Video, < 100 ms latency and jitter (4)<br/>***vo***: Voice, < 10 ms latency and jitter (5)<br/>***ic***: Internetwork control (6)<br/>***nc***: Network control (7)<br/>***PCP_VAL***: PCP Value in range 0-7 |
| Change history | SONiC 3.1 - Introduced                                       |

###### 3.6.2.10.1.6 Add or delete match on VLAN ID
| Mode   | Classifier|
| ------ | ----------------------------------------------------------- |
| Syntax | SONiC(config-classifier)# **match vlan** *VLAN_ID* |
| Syntax | SONiC(config-classifier)# **no match vlan** |
| Arguments | ***VLAN_ID***: VLAN ID in range 1-4094 |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.10.1.7 Add or delete match on source IPv4 Address

| Mode   | Classifier |
| ------ | ---------------------------------------------------------- |
| Syntax | SONiC(config-classifier)# **match source-address ip** { **host** *IP_ADDR* \| *IP_ADDR/PREFIX* } |
| Syntax | SONiC(config-classifier)# **no match source-address ip** |
| Arguments | ***IP_ADDR***: IPv4 address<br/>***PREFIX***: Prefix in range 1-31 |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.10.1.8 Add or delete match on destination IPv4 Address

| Mode   | Classifier |
| ------ | ---------------------------------------------------------- |
| Syntax | SONiC(config-classifier)# **match destination-address ip** { **host** *IP_ADDR* \| *IP_ADDR/PREFIX* } |
| Syntax | SONiC(config-classifier)# **no match destination-address ip** |
| Arguments | ***IP_ADDR***: IPv4 address<br/>***PREFIX***: Prefix in range 1-31 |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.10.1.9 Add or delete match on source IPv6 Address

| Mode   | Classifier |
| ------ | ---------------------------------------------------------- |
| Syntax | SONiC(config-classifier)# **match source-address ipv6** { **host** *IPV6_ADDR* \| *IPV6_ADDR/PREFIX* } |
| Syntax | SONiC(config-classifier)# **no match source-address ipv6** |
| Arguments | ***IPV6_ADDR***: IPv6 address<br/>***PREFIX***: Prefix in range 1-127 |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.10.1.10 Add or delete match on destination IPv4 Address

| Mode   | Classifier |
| ------ | ---------------------------------------------------------- |
| Syntax | SONiC(config-classifier)# **match destination-address ipv6** { **host** *IPV6_ADDR* \| *IPV6_ADDR/PREFIX* } |
| Syntax | SONiC(config-classifier)# **no match destination-address ipv6** |
| Arguments | ***IPV6_ADDR***: IPv6 address<br/>***PREFIX***: Prefix in range 1-127 |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.10.1.11 Add or delete match on IP Protocol

| Mode   | Classifier |
| ------ | ----------------------------------------------------------- |
| Syntax | SONiC(config-classifier)# **match ip protocol** { **tcp** \| **udp** \| **icmp** \| **icmpv6** \| *NUMBER* } |
| Syntax | SONiC(config-classifier)# **no match protocol** |
| Arguments | ***NUMBER***: IP Protocol number in range 0-255 |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.10.1.12 Add or delete match on source TCP or UDP Port
Match on source port is allowed only when IP protocol is set to TCP or UDP.

| Mode   | Classifier |
| ------ | ----------------------------------------------------------- |
| Syntax | SONiC(config-classifier)# **match l4-port source** { **eq** *NUMBER* \| **range** *BEGIN* *END*} |
| Syntax | SONiC(config-classifier)# **no match l4-port source** |
| Arguments | ***NUMBER***: Port number 0-65535<br/>***BEGIN***,***END***: Port number 0-65535. END must be greater than BEGIN |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.10.1.13 Add or delete match on destination TCP or UDP Port
Match on destination port is allowed only when IP protocol is set to TCP or UDP.

| Mode   | Classifier |
| ------ | ----------------------------------------------------------- |
| Syntax | SONiC(config-classifier)# **match l4-port destination** { **eq** *NUMBER* \| **range** *BEGIN* *END*} |
| Syntax | SONiC(config-classifier)# **no match l4-port destination** |
| Arguments | ***NUMBER***: Port number 0-65535<br/>***BEGIN***,***END***: Port number 0-65535. END must be greater than BEGIN |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.10.1.14 Add or delete match on TCP flags
Match on TCP flags is allowed only when IP protocol is set to TCP. `not-xxx` keyword can be used to match the corresponding flag set to 0.

| Mode   | Classifier |
| ------ | ----------------------------------------------------------- |
| Syntax | SONiC(config-classifier)# **match tcp-flags** { **syn** \| **not-syn** } { **ack** \| **not-ack** } { **fin** \| **not-fin** } { **ack** \| **not-ack** } { **psh** \| **not-psh** } { **urg** \| **not-urg** } |
| Syntax | SONiC(config-classifier)# **no** **match tcp-flags** [ { **syn** \| **not-syn** } { **ack** \| **not-ack** } { **fin** \| **not-fin** } { **ack** \| **not-ack** } { **psh** \| **not-psh** } { **urg** \| **not-urg** } ] |
| Change history | SONiC 3.1 - Introduced |

##### 3.6.2.10.2 Update classifier match parameters using Click CLI (Deprecated)
```
root@sonic:~# config classifier update --help
Usage: config classifier update [OPTIONS] <name>

  Update a Classifier with flow identification criterion/criteria.

Options:
  --acl <acl-name>                Add ACL table name for flow classification
  --no-acl                        Delete ACL Table name used for flow
                                  classification
  --src-mac <src-mac>             Match source mac address (xx-xx-xx-xx-xx-xx
                                  [/xx-xx-xx-xx-xx-xx] format)
  --no-src-mac                    Delete match on source mac address
  --dst-mac <dst-mac>             Match destination mac address (xx-xx-xx-xx-
                                  xx-xx[/xx-xx-xx-xx-xx-xx] format)
  --no-dst-mac                    Delete match on destination mac address
  --ether-type <ether-type>       Match ethertype (hex or decimal format in
                                  1536-65535 range)
  --no-ether-type                 Delete match on ethertype
  --pcp <pcp>                     Match on PCP (0-7)
  --no-pcp                        Delete match on PCP
  --src-ip <src-ip>               Match source IP address (A.B.C.D[/M] format)
  --no-src-ip                     Delete match on source IP address
  --dst-ip <dst-ip>               Match on destination IP address (A.B.C.D[/M]
                                  format)
  --no-dst-ip                     Delete match on destination IP address
  --src-ipv6 <src-ipv6>           Match source IPv6 address (X:X::X.X[/M]
                                  format)
  --no-src-ipv6                   Delete match on source IPv6 address
  --dst-ipv6 <dst-ipv6>           Match on destination IPv6 address
                                  (X:X::X.X[/M] format)
  --no-dst-ipv6                   Delete match on destination IPv6 address
  --ip-proto <ip-proto>           Match on IP protocol/next Header (0-255)
  --no-ip-proto                   Delete match on IP protocol/next header
  --src-port <value or begin-end>
                                  Match on source port or source port range
                                  (0-65535)
  --no-src-port                   Delete match on source port or source port
                                  range
  --dst-port <dst-port>           Match on destination port or destination
                                  port range (0-65535)
  --no-dst-port                   Delete match on destination port or
                                  destination port range
  --tcp-flags <tcp-flags>         Match on TCP flags (fin not-fin syn not-syn
                                  rst not-rst psh not-psh ack not-ack urg not-
                                  urg ece not-ece cwr not-cwr in comma
                                  separated format)
  --no-tcp-flags <tcp-flags>      Delete match on TCP flags (fin not-fin syn
                                  not-syn rst not-rst psh not-psh ack not-ack
                                  urg not-urg ece not-ece cwr not-cwr in comma
                                  separated format)
  --dscp <dscp>                   Match on DSCP (0-63)
  --no-dscp                       Delete match on DSCP
  -d, --description <description>
  --help                          Show this message and exit.
```
#### 3.6.2.11 Add classifier description

| Mode   | Classifier |
| ------ | ----------------------------------------------------------- |
| Syntax | SONiC(config-classifier)# **description** *STRING* |
| Arguments | ***STRING***: A string describing the classifier. Max 256 characters. Description should be in double quotes if it has spaces. |
| Change history | SONiC 3.1 - Introduced |

#### 3.6.2.12 Delete classifier description

| Mode   | Classifier |
| ------ | ----------------------------------------------------------- |
| Syntax | SONiC(config-classifier)# **no description** |
| Change history | SONiC 3.1 - Introduced |

#### 3.6.2.13 Delete classifier

##### 3.6.2.13.1 Delete classifier using Sonic-CLI

| Mode | Config |
| ---- | --------------------------------------------------- |
| Syntax | SONiC(config)# **no classifier** *NAME* |
| Arguments | ***NAME***: String of 1-63 characters in length. Must begin with a alpha numeric character. Rest of the characters can be alpha numeric or hyphen (-) or underscore (\_). |
| Change history | SONiC 3.1 - Introduced       |

##### 3.6.2.13.2 Delete classifier using Click CLI (Deprecated)

```
root@sonic:~# config classifier del --help
Usage: config classifier del [OPTIONS] <name>

  Delete a Classifier

Options:
  --help  Show this message and exit.
```

#### 3.6.2.14 Add policy

##### 3.6.2.14.1 Add policy using Sonic-CLI

| Mode | Config |
| ---- | ------ |
| Syntax | SONiC(config)# **policy** *NAME* **type** { **qos** \| **monitoring** \| **forwarding** } |
| Arguments | ***NAME***: Name of the policy to be created. String of 1-63 characters in length. Must begin with a alpha numeric character. Rest of the characters can be alpha numeric or hyphen (-) or underscore (\_). |
| Change history | SONiC 3.1 - Introduced       |

NOTE: **forwarding** policies can be created only using Sonic-CLI.

##### 3.6.2.14.2 Add policy using Click CLI (Deprecated)

```
root@sonic:~# config policy add --help
Usage: config policy add [OPTIONS] <name>

  Add a Flow Based Services Policy

Options:
  -t, --type <type>               Policy type (qos or monitoring)  [required]
  -d, --description <description>
                                  Set policy description
  --help                          Show this message and exit.
```

#### 3.6.2.15 Delete policy

##### 3.6.2.15.1 Deleting policy using Sonic-CLI
| Mode | Config |
| ---- | ------ |
| Syntax | SONiC(config)# **no policy** *NAME* |
| Arguments | ***NAME***: Name of the policy to be deleted. String of 1-63 characters in length. Must begin with a alpha numeric character. Rest of the characters can be alpha numeric or hyphen (-) or underscore (\_). |
| Change history | SONiC 3.1 - Introduced |

##### 3.6.2.15.2 Deleting policy using Click CLI (Deprecated)
```
root@sonic:~# config policy del --help
Usage: config policy del [OPTIONS] <name>

  Delete a Flow Based Services Policy

Options:
  --help  Show this message and exit.
```

#### 3.6.2.16 Add policy description

| Mode   | Policy |
| ------ | ------ |
| Syntax | SONiC(config-policy)# **description** *STRING* |
| Arguments | ***STRING***: A string describing the policy. Max 256 characters. Description should be in double quotes if it has spaces. |
| Change history | SONiC 3.1 - Introduced |

#### 3.6.2.17 Delete policy description

| Mode   | Policy |
| ------ | ------ |
| Syntax | SONiC(config-policy)# **no description** |
| Change history | SONiC 3.1 - Introduced |

#### 3.6.2.18 Add flow identified by a classifier to a policy

##### 3.6.2.18.1 Add flow using Sonic-CLI

| Mode   | Policy |
| ------ | ------ |
| Syntax | SONiC(config-policy)# **class** *NAME* **priority** *PRIORITY* |
| Arguments | ***NAME***: Classifier name. String of 1-63 characters in length. Must begin with a alpha numeric character. Rest of the characters can be alpha numeric or hyphen (-) or underscore (\_).<br/>***PRIORITY***: Priority number in range 0-1023 |
| Change history | SONiC 3.1 - Introduced |

##### 3.6.2.18.2 Add flow using Click CLI (Deprecated)

```
root@sonic:~# config flow add --help
Usage: config flow add [OPTIONS] <policy_name> <classifier_name>

  Add a Flow to Policy

Options:
  -p, --priority <type>           Set flow priority (0-1023)  [required]
  -d, --description <description>
                                  Set flow description
  --help                          Show this message and exit.
```

#### 3.6.2.19 Delete flow identified by a classifier to a policy

#### 3.6.2.20 Deleting flow using Sonic-CLI

| Mode   | Policy |
| ------ | ------ |
| Syntax | SONiC(config-policy)# **no class** *NAME* |
| Arguments | ***NAME***: Classifier name. String of 1-63 characters in length. Must begin with a alpha numeric character. Rest of the characters can be alpha numeric or hyphen (-) or underscore (\_). |
| Change history | SONiC 3.1 - Introduced |

#### 3.6.2.21 Deleting flow using Click CLI (Deprecated)

```
root@sonic:~# config flow del --help
Usage: config flow del [OPTIONS] <policy_name> <classifier_name>

  Delete a Flow from Policy

Options:
  --help  Show this message and exit.
```
#### 3.6.2.22 Add flow description

| Mode   | Flow |
| ------ | ------ |
| Syntax | SONiC(config-classifier)# **description** *STRING* |
| Arguments | *STRING*: A string describing the flow. Max 256 characters. Description should be in double quotes if it has spaces. |
| Change history | SONiC 3.1 - Introduced |

#### 3.6.2.23 Delete flow description

| Mode   | Flow |
| ------ | ------ |
| Syntax | SONiC(config-classifier)# **no description** |
| Change history | SONiC 3.1 - Introduced |

#### 3.6.2.24 Add action(s) to flows

##### 3.6.2.24.1 Add QoS actions to the flow using Sonic-CLI
The following QoS actions can be added to the flow. QoS actions can be added/enabled only if the policy is of type **qos**.

###### 3.6.2.24.1.1 Add DSCP remarking action

| Mode   | Flow |
| ------ | ---- |
| Syntax | SONiC(config-policy-flow)# **set dscp** *\<0-63\>* |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.24.1.2 Delete DSCP remarking action

| Mode   | Flow |
| ------ | ---- |
| Syntax | SONiC(config-policy-flow)# **no set dscp** |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.24.1.3 Add PCP remarking action

| Mode   | Flow |
| ------ | ---- |
| Syntax | SONiC(config-policy-flow)# **set pcp** *\<0-7\>* |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.24.1.4 Delete PCP remarking action

| Mode   | Flow |
| ------ | ---- |
| Syntax | SONiC(config-policy-flow)# **no set pcp** |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.24.1.5 Add policer action

| Mode   | Flow |
| ------ | ---- |
| Syntax | SONiC(config-policy-flow)# **police cir** *CIR* \[**cbs** *CBS* \] \[**pir** *PIR* \] \[**pbs** *PBS* \] |
| Arguments | ***CIR***: Committed information rate in bits per second. CIR is mandatory. The value can be optionally suffixed with kbps(1000), mbps(1000000), gbps (1000000000) or tbps (1000000000000).<br/>***CBS***: Committed burst size in bytes. The value can be suffixed with KB(1000), MB(1000000), GB(1000000000) or TB(1000000000000). The default value is 20% of the CIR in bytes. If configured by the user, it must be greater than or equal to CIR in bytes.<br/>***PIR***: Peak information rate in bits per second. The value can be optionally suffixed with kbps(1000), mbps(1000000), gbps (1000000000) or tbps (1000000000000). If configured by the user, it must be greater than CIR<br/>***PBS***: Peak burst size. The value can be suffixed with KB(1000), MB(1000000), GB(1000000000) or TB(1000000000000). The default value is 20% of the PIR value in bytes. If configured by the user, it must be greater than PIR value in bytes and also CBS value. |
| Change history | SONiC 3.1 - Introduced |

If only CIR is configured, then its 1 rate, 2 color policer. Any traffic exceeding CIR value will be marked as red and will be dropped.

If both CIR and PIR is configured, then is 2 rate 3 color policer. Any traffic that exceeds CIR but less than PIR will be marked as yellow. Any traffic that is more than PIR will be marked as red and will be dropped.  

###### 3.6.2.24.1.6 Delete policer action
| Mode   | Flow |
| ------ | ---- |
| Syntax | SONiC(config-policy-flow)# **no police** \[ **cir** \] \[**cbs** \] \[**pir** \] \[**pbs** \] |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.24.1.7 Add set traffic-class action

| Mode   | Flow |
| ------ | ---- |
| Syntax | SONiC(config-policy-flow)# **set traffic-class** *\<0-7\>* |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.24.1.8 Delete set traffic-class action

| Mode   | Flow |
| ------ | ---- |
| Syntax | SONiC(config-policy-flow)# **no set traffic-class** |
| Change history | SONiC 3.1 - Introduced |

##### 3.6.2.24.2 Adding monitoring actions to the flow
The following monitoring actions can be added to the flow. Monitoring actions can be added/enabled only if the policy is of type **monitoring**.

###### 3.6.2.24.2.1 Adding mirror session action
| Mode   | Flow |
| ------ | ---- |
| Syntax | SONiC(config-policy-flow)# **set mirror-session** *SESSION_NAME* |
| Arguments | *SESSION_NAME*: Mirror session name |
| Change history | SONiC 3.1 - Introduced |

###### 3.6.2.24.2.2 Deleting mirror session action
| Mode   | Flow |
| ------ | ---- |
| Syntax | SONiC(config-policy-flow)# **no set mirror-session** |
| Change history | SONiC 3.1 - Introduced |

##### 3.6.2.24.3 Adding forwarding actions to the flow
The following forwarding actions can be added to the flow. Forwarding actions can be added/enabled only if the policy is of type **forwarding**. This configuration is only available in Sonic-CLI.

###### 3.6.2.24.3.1 Adding / Deleting IPv4 next-hop
| Mode   | Flow |
| ------ | ---- |
| Syntax | SONiC(config-policy-flow)# \[ **no** \] **set ip next-hop** *IP_ADDR* \[ vrf *VRF_NAME* \] \[ priority *PRIORITY* \] |
| Arguments | ***IP_ADDR***: IPv4 Address<br/>***VRF_NAME***: VRF name. If the VRF name is not specified then it will be derived from the VRF of the interface on which the policy is applied or default will be used for global application.<br/>***PRIORITY***: Priority of the next-hop. Range is 1-65535. Default is 0 ie lowest priority if not configured by the user. The next-hop with the higher priority will be picked up for forwarding first. If more than 1 next-hops have the same priority then the next-hop which is configured first will be used. |
| Change history | SONiC 3.1 - Introduced |

IPv4 next-hops are valid only if the classifier uses IPv4 ACL for match. Only IPv4 routed traffic will be forwarded to the configured next-hop. Combining IPv4 next-hops with IPv6 next-hops or egress interface (except NULL) is not permitted. The next-hop must be reachable for it to be selected for routing. NULL egress can be configured to select drop as egress action if none of the next-hops are reachable. If NULL egress is not configured then the traffic will be routed normally.

###### 3.6.2.24.3.2 Adding / Deleting IPv6 next-hop
| Mode   | Flow |
| ------ | ---- |
| Syntax | SONiC(config-policy-flow)# \[ **no** \] **set ipv6 next-hop** *IPV6_ADDR* \[ vrf *VRF_NAME* \] \[ priority *PRIORITY* \] |
| Arguments | ***IPV6_ADDR***: IPv6 Address<br/>***VRF_NAME***: VRF name. If the VRF name is not specified then it will be derived from the VRF of the interface on which the policy is applied or default will be used for global application.<br/>***PRIORITY***: Priority of the next-hop. Range is 1-65535. Default is 0 ie lowest priority if not configured by the user. The next-hop with the higher priority will be picked up for forwarding first. If more than 1 next-hops have the same priority then the next-hop which is configured first will be used. |
| Change history | SONiC 3.1 - Introduced |

IPv6 next-hops are valid only if the classifier uses IPv6 ACL for match. Only IPv6 routed traffic will be forwarded to the configured next-hop. Combining IPv6 next-hops with IPv4 next-hops or egress interface (except NULL) is not permitted. The next-hop must be reachable for it to be selected for routing. NULL egress can be configured to select drop as egress action if none of the next-hops are reachable. If NULL egress is not configured then the traffic will be routed normally.

###### 3.6.2.24.3.3 Adding / Deleting egress interface

| Mode   | Flow |
| ------ | ---- |
| Syntax | SONiC(config-policy-flow)# \[ **no** \] **set interface** { **Ethernet***ID* \| **PortChannel***ID* } \[ priority *PRIORITY* \] |
| Arguments | ***ID***: Ethernet or PortChannel number.<br/>***PRIORITY***: Priority of the egress port. Range is 1-65535. Default is 0 ie lowest priority if not configured by the user. The port with the higher priority will be picked up for forwarding first. If more than 1 ports have the same priority then the port which is configured first will be used. |
| Change history | SONiC 3.1 - Introduced |

Egress interfaces configuration is valid only if the classifier uses MAC/L2 ACL for match. Only L2 switched traffic will be forwarded to the configured egress interface. Combining egress interface with IPv4 or IPv6 next-hops is not permitted. The egress interface must be a switchport and online for it to be selectable for forwarding, else it will be forward referenced. User is expected to make sure egress interface is part of necessary VLANs. NULL egress can be configured to select drop as egress action if none of the egress interfaces are online. If NULL egress is not configured then the traffic will be forwarded normally.

###### 3.6.2.24.3.4 Adding default drop action

| Mode   | Flow |
| ------ | ---- |
| Syntax | SONiC(config-policy-flow)# \[ **no** \] **set interface null** |
| Change history | SONiC 3.1 - Introduced |

Drop action if configured will be of the lowest priority and will be chosen if none of the configured next-hops or egress interfaces can be used for forwarding.

##### 3.6.2.24.4 Add flow actions using Click CLIs (Deprecated)

```
root@sonic:~# config flow update --help
Usage: config flow update [OPTIONS] <policy_name> <classifier_name>

  Add or Update flow results

Options:
  -p, --priority <type>           Set flow priority (0-1023)
  --set-dscp <Value>              DSCP remark to value (0-63)
  --no-set-dscp                   Delete DSCP remarking action
  --set-pcp <Value>               PCP remark to value (0-7)
  --no-set-pcp                    Delete PCP Remarking action
  --police                        Add rate limiting action
  --cir <cir>                     Conform rate
  --cbs <cbs>                     Conform burst size
  --pir <pir>                     Peak rate
  --pbs <pbs>                     Peak burst size
  --no-police                     Delete rate limiting action
  --mirror-session <session_name>
                                  Set mirror destination
  --no-mirror-session             Delete mirror destination
  -d, --description <description>
                                  Set flow description
  --help                          Show this message and exit.
```

If only CIR is configured, then its 1 rate, 2 color policer. Any traffic exceeding CIR value will be marked as red and will be dropped.

If both CIR and PIR is configured, then is 2 rate 3 color policer. Any traffic that exceeds CIR but less than PIR will be marked as yellow. Any traffic that is more than PIR will be marked as red and will be dropped. 

Forwarding actions are supported only in Sonic-CLI.

#### 3.6.2.25 Applying the policy to an interface

##### 3.6.2.25.1 Applying policy to an interface using Sonic-CLI
| Mode   | Config or Interface |
| ------ | ------------------- |
| Syntax | SONiC(config)# **service-policy type qos** { **in** \| **out** } *NAME* <br/>SONiC(config)# **service-policy type** { **monitoring** \| **forwarding** } **in** *NAME* |
| Syntax | SONiC(config-if-xxxx)# **service-policy type qos** { **in** \| **out** } *NAME* <br/>SONiC(config-if-xxxx)# **service-policy type** { **monitoring** \| **forwarding** } **in** *NAME* |
| Arguments | *NAME*: Name of the policy to be applied |
| Change history | SONiC 3.1 - Introduced |

NOTE: Forwarding policy can be applied only using Sonic-CLI. When a forwarding policy is applied globally, the next-hops
are assumed to be in default VRF unless user has specified the VRF explicitly.

##### 3.6.2.25.2 Applying policy to an interface using Click CLI (Deprecated)

```
root@sonic:~# config service-policy bind --help
Usage: config service-policy bind [OPTIONS] <interface_name> <type> <stage> <policy_name>

  Apply policy to interface

Options:
  --help  Show this message and exit.
```

#### 3.6.2.26 Removing policy from an interface

##### 3.6.2.26.1 Removing policy from an interface using Sonic-CLI
| Mode   | Config or Interface |
| ------ | ------------------- |
| Syntax | SONiC(config)# **no service-policy type qos** { **in** \| **out** } <br/>SONiC(config)# **no service-policy type** { **monitoring** \| **forwarding** } **in** |
| Syntax | SONiC(config-if-xxxx)# **no service-policy type qos** { **in** \| **out** }<br/>SONiC(config-if-xxxx)# **service-policy type** { **monitoring** \| **forwarding** } **in** |
| Change history | SONiC 3.1 - Introduced |

NOTE: Forwarding policy can be removed only using Sonic-CLI.

##### 3.6.2.26.2 Removing policy from an interface using Click CLI (Deprecated)

```
root@sonic:~# config service-policy unbind --help
Usage: config service-policy unbind [OPTIONS] <interface_name> <type> <stage>

  Remove policy from interface

Options:
  --help  Show this message and exit.
```

### 3.6.3 Show Commands

#### 3.6.3.1 Show ACL binding summary
| Mode   | Exec |
| ------ | ------------------- |
| Syntax | SONiC# **show** { **mac** \| **ip** \| **ipv6** } **access-group** |
| Change history | SONiC 3.1 - Introduced |

***Sample Output:***

```
sonic# show ip access-group 
Ingress IP access-list ipacl on Ethernet0
Ingress IP access-list ipacl on PortChannel1
Ingress IP access-list ipacl on Vlan100
```

#### 3.6.3.2 Show ACL Rules and statistics
| Mode   | Exec |
| ------ | ------------------- |
| Syntax | SONiC# **show** { **mac** \| **ip** \| **ipv6** } **access-list** \[ *NAME* \] |
| Change history | SONiC 3.1 - Introduced |

***Sample Output:***

```
sonic# show ip access-lists 
ip access-list ipacl
    seq 1 permit ip host 10.1.1.1 host 20.1.1.1 (0 packets) [0 bytes]
    seq 2 permit ip host 10.1.1.2 host 20.1.1.2 (0 packets) [0 bytes]
    seq 3 permit ip host 10.1.1.3 host 20.1.1.3 (0 packets) [0 bytes]
    seq 4 permit ip host 10.1.1.4 host 20.1.1.4 (0 packets) [0 bytes]
```

#### 3.6.3.3 Clear ACL statistics

| Mode   | Exec |
| ------ | ------------------- |
| Syntax | SONiC# **clear { **mac** \| **ip** \| **ipv6** } counters** **access-list**  \[ *NAME* \] |
| Change history | SONiC 3.1 - Introduced |

#### 3.6.3.4 Show classifier details

##### 3.6.3.4.1 Show classifier details using Sonic-CLI

| Mode   | Exec |
| ------ | ------------------- |
| Syntax | SONiC# **show classifier** [ *NAME* \| **match-type** { **acl** \| **fields** } ] |
| Change history | SONiC 3.1 - Introduced |

##### 3.6.3.4.2 Show classifier details using Click CLI (Deprecated)

```
root@sonic:~# show classifier --help
Usage: show classifier [OPTIONS] [NAME]

  Show flow based services classifiers related information

Options:
  -m, --match-type <match-type>  Classifier type acl or fields
  -?, -h, --help                 Show this message and exit.
```

##### 3.6.3.4.3 Show classifier sample output

| CLI Type | CLI Syntax |
| -------- | ---------- |
| Sonic-CLI | SONiC# show classifier class0 |
| Click-CLI *(Deprecated)* | root@sonic:~# show classifier class0 |
| Sample Output | Classifier class0 match-type acl<br/>&nbsp;&nbsp;match-acl l3_ACL_0<br/>&nbsp;&nbsp;&nbsp;&nbsp;Referenced in flows:<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;policy policy0 at priority 200 |

| CLI Type | CLI Syntax |
| -------- | ---------- |
| Sonic-CLI | SONiC# show classifier match-type fields |
| Click-CLI *(Deprecated)* | root@sonic:~# show classifier -m fields |
| Sample Output | Classifier fields_class_0 match-type fields<br/>&nbsp;&nbsp;Description:<br/>&nbsp;&nbsp;Match:<br/>&nbsp;&nbsp;&nbsp;&nbsp;src-ip 40.1.1.100/32<br/>&nbsp;&nbsp;Referenced in flows:<br/>&nbsp;&nbsp;&nbsp;&nbsp;policy mon_policy_0 at priority 999<br/>&nbsp;&nbsp;&nbsp;&nbsp;policy qos_policy_0 at priority 999 |

#### 3.6.3.5 Show policy details

##### 3.6.3.5.1 Show policy details using Sonic-CLI
| Mode   | Exec |
| ------ | ------------------- |
| Syntax | SONiC# **show policy** [ *NAME* \| **type** { **qos** \| **monitoring** \| **forwarding** } ] |
| Change history | SONiC 3.1 - Introduced |

##### 3.6.3.5.2 Show policy details using Click-CLI (Deprecated)

```
root@sonic:~# show policy --help
Usage: show policy [OPTIONS] [NAME]

  Show flow based services policies related information

Options:
  -f, --flow <flow>  Show information only for flow identified by classifer
  -t, --type <type>  Policy type (qos)
  -?, -h, --help     Show this message and exit.
```

##### 3.6.3.5.3 Sample output

| CLI Type | CLI Syntax |
| -------- | ---------- |
| Sonic-CLI | SONiC# show policy qos_policy_0 |
| Click-CLI (Deprecated) | root@sonic~# show policy qos_policy_0 |
| Sample Output | Policy qos_policy_0 Type qos<br/>&nbsp;&nbsp;Description:<br/>&nbsp;&nbsp;Flow fields_class_0 at priority 999<br/>&nbsp;&nbsp;&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;set-pcp 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;set-pcp 1<br/>&nbsp;&nbsp;&nbsp;&nbsp;police cir 10000000 cbs 1000000 pir 0 pbs 0<br/>&nbsp;&nbsp;Flow fields_class_1 at priority 998<br/>&nbsp;&nbsp;&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;set-pcp 2<br/>&nbsp;&nbsp;&nbsp;&nbsp;set-pcp 2<br/>&nbsp;&nbsp;&nbsp;&nbsp;police cir 20000000 cbs 2000000 pir 0 pbs 0<br/>&nbsp;&nbsp;Flow fields_class_2 at priority 997<br/>&nbsp;&nbsp;&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;set-pcp 3<br/>&nbsp;&nbsp;&nbsp;&nbsp;set-pcp 3<br/>&nbsp;&nbsp;&nbsp;&nbsp;police cir 30000000 cbs 3000000 pir 0 pbs 0<br/>&nbsp;&nbsp;Flow fields_class_3 at priority 996<br/>&nbsp;&nbsp;&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;set-pcp 4<br/>&nbsp;&nbsp;&nbsp;&nbsp;set-pcp 4<br/>&nbsp;&nbsp;&nbsp;&nbsp;police cir 40000000 cbs 4000000 pir 0 pbs 0<br/>&nbsp;&nbsp;Applied to:<br/>&nbsp;&nbsp;&nbsp;&nbsp;Ethernet0 at ingress |


| CLI Type | CLI Syntax |
| -------- | ---------- |
| Sonic-CLI | SONiC# show policy type monitoring |
| Click-CLI (Deprecated) | root@sonic~# show policy -t monitoring |
| Sample Output | Policy mon_policy_0 Type monitoring<br/>&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;Flow fields_class_0 at priority 999<br/>&nbsp;&nbsp;&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;mirror-session ERSPAN_DestIP_50.1.1.2<br/>&nbsp;&nbsp;Flow fields_class_1 at priority 998<br/>&nbsp;&nbsp;&nbsp;&nbsp;Description:<br/>&nbsp;&nbsp;&nbsp;&nbsp;mirror-session ERSPAN_DestIP_60.1.1.2<br/>&nbsp;&nbsp;Flow fields_class_2 at priority 997<br/>&nbsp;&nbsp;&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;mirror-session ERSPAN_DestIP_50.1.1.2<br/>&nbsp;&nbsp;Flow fields_class_3 at priority 996<br/>&nbsp;&nbsp;&nbsp;&nbsp;Description:<br/>&nbsp;&nbsp;&nbsp;&nbsp;mirror-session ERSPAN_DestIP_60.1.1.2<br/>&nbsp;&nbsp;Applied to:<br/>&nbsp;&nbsp;&nbsp;&nbsp;Ethernet0 at ingress |

#### 3.6.3.6 Show policy binding summary

##### 3.6.3.6.1 Show policy binding summary using Sonic-CLI

| Mode      | Exec |
| --------- | ---- |
| Syntax    | SONiC# **show service-policy summary** \[ **interface** { **Ethernet** *ID* \| **PortChannel** *ID* \| **Vlan** *ID* \| **Switch** } \] \[ **type** { **qos** \| **monitoring** \| **forwarding** } \] |
| Arguments | *ID*: Number of Ethernet or PortChannel or Vlan |
| Change history | SONiC 3.1 - Introduced |

##### 3.6.3.6.2 Show policy binding summary using Click CLI (Deprecated)

```
root@sonic:~# show service-policy summary --help
Usage: show service-policy summary [OPTIONS]

  Show summary of applied flow based services policies

Options:
  -i, --interface <interface>  Interface name
  -t, --type <type>            Policy type (qos)
  -?, -h, --help               Show this message and exit.
```

##### 3.6.3.6.3 Show policy binding summary sample output

| CLI Type | CLI Syntax |
| -------- | ---------- |
| Sonic-CLI | SONiC# show service-policy summary |
| Click-CLI (Deprecated) | root@sonic~# show service-policy summary |
| Sample Output | Ethernet0<br/>&nbsp;&nbsp;qos policy qos_policy0 at ingress<br/>&nbsp;&nbsp;monitoring policy mon_policy_0 at ingress<br/>PortChannel100<br/>&nbsp;&nbsp;qos policy policy0 at egress<br/>Vlan100<br/>&nbsp;&nbsp;forwarding policy pbr0 at ingress |

#### 3.6.3.7 Show/Clear policy binding and counters for an interface

##### 3.6.3.7.1 Show/Clear policy binding and counters using SONiC-CLI
| Mode      | Exec |
| --------- | ---- |
| Syntax    | SONiC# **show service-policy** { **interface** { **Ethernet** *ID* \| **PortChannel** *ID* \| **Vlan** *ID* \| **Switch** } \[ **type** { **qos** \| **monitoring** \| **forwarding** } \] \| **policy** *NAME* \[ **interface** { **Ethernet** *ID* \| **PortChannel** *ID* \| **Vlan** *ID* \| **Switch** \] } <br/><br/>SONiC# **clear counters service-policy** { **interface** { **Ethernet** *ID* \| **PortChannel** *ID* \| **Vlan** *ID* \| **Switch** } \[ **type** { **qos** \| **monitoring** \| **forwarding** } \] \| **policy** *NAME* \[ **interface** { **Ethernet** *ID* \| **PortChannel** *ID* \| **Vlan** *ID* \| **Switch** \] } |
| Arguments | *ID*: Number of Ethernet or PortChannel or Vlan<br/>*NAME*: Name of the policy applied. |
| Change history | SONiC 3.1 - Introduced |

##### 3.6.3.7.2 Show/Clear policy binding and counters using Click CLI (Deprecated)

```
root@sonic:~# show service-policy interface --help
Usage: show service-policy interface [OPTIONS] <interface_name>

  Show details by interface name

Options:
  -t, --type <type>    Policy type (qos or monitoring)
  -s, --stage <stage>  Stage (in or out)
  -c, --clear          Clear statistics
  -?, -h, --help       Show this message and exit.
```

```
root@sonic:~# show service-policy policy --help
Usage: show service-policy policy [OPTIONS] <policy_name>

  Show flow based services applied polcies information by policies name

Options:
  -t, --type <type>  Policy type (qos)
  -c, --clear        Clear statistics
  -?, -h, --help     Show this message and exit.
```

##### 3.6.3.7.3 Show policy binding and counters sample output
| CLI Type | CLI Syntax |
| -------- | ---------- |
| Sonic-CLI | SONiC# show service-policy interface Ethernet0 |
| Click-CLI (Deprecated) | root@sonic:~# show service-policy interface Ethernet0 |
| Sample Output | Ethernet0<br/>&nbsp;&nbsp;Policy qos_policy_0 Type qos at ingress<br/>&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;Flow fields_class_3 at priority 996 (Active)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set-pcp 4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set-dscp 4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;police: cir 40000000 cbs 4000000 pir 0 pbs 0<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;type bytes mode color-blind<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;operational cir 40000000 cbs 4000000 pir 0 pbs 0<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conformed 0 packets 0 bytes action forward<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;exceed 0 frames 0 bytes action forward<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;violated 0 frames 0 bytes action drop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Packet matches: 0 frames 0 bytes<br/>&nbsp;&nbsp;&nbsp;&nbsp;Flow fields_class_2 at priority 997 (Active)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set-pcp 3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set-dscp 3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;police: cir 30000000 cbs 3000000 pir 0 pbs 0<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;type bytes mode color-blind<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;operational cir 30000000 cbs 3000000 pir 0 pbs 0<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;conformed 0 packets 0 bytes action forward<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;exceed 0 frames 0 bytes action forward<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;violated 0 frames 0 bytes action drop<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Packet matches: 0 frames 0 bytes |



| CLI Type | CLI Syntax |
| -------- | ---------- |
| Sonic-CLI | SONiC# show service-policy policy mon_policy_0 |
| Click-CLI (Deprecated) | root@sonic:~# show service-policy policy mon_policy_0 |
| Sample Output | Ethernet0<br/>&nbsp;&nbsp;Policy mon_policy_0 Type monitoring at ingress<br/>&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;Flow fields_class_3 at priority 996 (Active)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;mirror-session ERSPAN_DestIP_60.1.1.2<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Packet matches: 0 frames 0 bytes<br/>&nbsp;&nbsp;&nbsp;&nbsp;Flow fields_class_2 at priority 997 (Active)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;mirror-session ERSPAN_DestIP_50.1.1.2<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Packet matches: 0 frames 0 bytes<br/>&nbsp;&nbsp;&nbsp;&nbsp;Flow fields_class_1 at priority 998 (Active)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;mirror-session ERSPAN_DestIP_60.1.1.2<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Packet matches: 0 frames 0 bytes<br/>&nbsp;&nbsp;&nbsp;&nbsp;Flow fields_class_0 at priority 999 (Active)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;mirror-session ERSPAN_DestIP_50.1.1.2<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Packet matches: 0 frames 0 bytes |



| CLI Type | CLI Syntax |
| -------- | ---------- |
| Sonic-CLI | SONiC# show service-policy interface Vlan 100 type forwarding |
| Sample Output | Vlan100<br/>&nbsp;&nbsp;Policy pbr_policy_example Type forwarding at ingress<br/>&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;Flow acl_class_1000 at priority 1000 (Active)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set ip next-hop 10.1.1.1 vrf default<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set ip next-hop 20.1.1.1 vrf VrfRed<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set ip next-hop 30.1.1.1 (Selected)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set interface null<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Packet matches: 128 frames 128000 bytes<br/>&nbsp;&nbsp;&nbsp;&nbsp;Flow acl_class_999 at priority 999 (Active)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set ip next-hop 11.1.1.1 vrf default (Selected)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set ip next-hop 21.1.1.1 vrf VrfRed<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set ip next-hop 31.1.1.1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set interface null<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Packet matches: 0 frames 0 bytes<br/>&nbsp;&nbsp;&nbsp;&nbsp;Flow fields_class_0 at priority 999 (Active)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Description: <br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set ip next-hop 1111::1 vrf default<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set ip next-hop 2222::1 vrf VrfRed (Selected)<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set ip next-hop 3333::1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;set interface null<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Packet matches: 0 frames 0 bytes |

#### 3.6.3.8 TCAM Allocation

By default the TCAM allocation is set to First come first serve. When certain features are configured beyond scale, after reboot they may consume more resources than pre-reboot which may affect other features. The feature behavior becomes unpredictable across reboots. A TCAM allocation utility is provided to partition the TCAMs as per users requirements. This ensures that all TCAM based features only consume the resources allocated to them and not impact others when they are configured beyond what's allocation in the allocation scheme.

**NOTE: Its recommended that the TCAM allocation is done with clean configuration i.e. none of the features using TCAMs are configured.**  The following features use TCAMs

1) ACLs and ACL based features like QoS, Monitoring, Telemetry etc

2) MCLAG

3) PFC Watchdog

##### 3.6.3.8.1 Available predefined TCAM profiles

The following command can be used to view the predefined profile names.

```
admin@sonic:~$ sudo tcamutil show profile all
================================================================================
Name             Description
================================================================================
DEFAULT-L2       Optimized for Layer 2 ACLs
DEFAULT-L3       Optimized for Layer 3 ACLs
```

##### 3.6.3.8.2 Predefined TCAM profile details

The following command can be used to see the predefined profile details. The following is an example and the output will be different on different platforms depending on the capabilities of the ASIC used.

```
admin@sonic:~$ sudo tcamutil show profile <ProfileName>

Example:
admin@sonic:~$ sudo tcamutil show profile DEFAULT-L2
=========================================================================================
   Ingress features  Width      Entries Description
=========================================================================================
             l2-acl  160bit(1)  1x1024  MAC ACLs
           ipv4-acl  320bit(2)  1x256   IPv4 ACLs
           ipv6-acl  480bit(3)  0x0     IPv6 ACLs
             ip-acl  480bit(3)  0x0     IPv4 and IPv6 ACLs
           l2-fbqos  160bit(1)  1x512   Flow based QoS using MAC ACL/fields
         ipv4-fbqos  320bit(2)  0x0     Flow based QoS using IPv4 ACL/fields
         ipv6-fbqos  480bit(3)  0x0     Flow based QoS using IPv6 ACL/fields
       l2ipv4-fbqos  320bit(2)  0x0     Flow based QoS using MAC and IPv4 ACL/fields
           ip-fbqos  480bit(3)  0x0     Flow based QoS using IPv4 and IPv6 ACL/fields
    l2-fbmonitoring  160bit(1)  0x0     Flow based monitoring using MAC ACL/fields
  ipv4-fbmonitoring  320bit(2)  0x0     Flow based monitoring using IPv4 ACL/fields
  ipv6-fbmonitoring  480bit(3)  0x0     Flow based monitoring using IPv6 ACL/fields
l2ipv4-fbmonitoring  480bit(3)  0x0     Flow based monitoring using MAC and IPv4 ACL/fields
    ip-fbmonitoring  480bit(3)  0x0     Flow based monitoring using IPv4 and IPv6 ACL/fields
              pfcwd  160bit(1)  0x0     PFC Watchdog
                tam  320bit(2)  0x0     Telemetry
              mclag  160bit(1)  1x256   MCLAG Port Isolation
-----------------------------------------------------------------------------------------
Total 9 TCAM slices of 9 allocated. Each slice has 256 entries
=========================================================================================
    Egress features  Width      Entries Description
=========================================================================================
             l2-acl  160bit(1)  1x512   MAC ACLs
           ipv4-acl  160bit(1)  0x0     IPv4 ACLs
           ipv6-acl  320bit(2)  0x0     IPv6 ACLs
             ip-acl  320bit(2)  0x0     IPv4 and IPv6 ACLs
           l2-fbqos  160bit(1)  0x0     Flow based QoS using MAC ACL/fields
         ipv4-fbqos  160bit(1)  0x0     Flow based QoS using IPv4 ACL/fields
         ipv6-fbqos  320bit(2)  0x0     Flow based QoS using IPv6 ACL/fields
       l2ipv4-fbqos  320bit(2)  0x0     Flow based QoS using MAC and IPv4 ACL/fields
           ip-fbqos  320bit(2)  0x0     Flow based QoS using IPv4 and IPv6 ACL/fields
              pfcwd  160bit(1)  0x0     PFC Watchdog
-----------------------------------------------------------------------------------------------
Total 2 TCAM slices of 2 allocated. Each slice has 256 entries
```

##### 3.6.3.8.3 Setting the predefined profile

The following command is used to set the TCAM profile. A `--startup` option is also available to modify the TCAM allocation in startup configuration. `--startup` requires the system to be rebooted/config reload for the TCAM allocation to take effect.

```
admin@sonic~$ sudo tcamutil set profile <ProfileName>

Example:
admin@sonic:~$ sudo tcamutil set profile DEFAULT-L2
```

##### 3.6.3.8.4 Checking the current TCAM Allocation

The following command can be used to check the current TCAM allocation

```
admin@sonic:~$ sudo tcamutil show current

Example:
admin@sonic:~$ sudo tcamutil show current
=========================================================================================
   Ingress features  Width      Entries Description
=========================================================================================
             l2-acl  160bit(1)  1x1024  MAC ACLs
           ipv4-acl  320bit(2)  1x256   IPv4 ACLs
           ipv6-acl  480bit(3)  0x0     IPv6 ACLs
             ip-acl  480bit(3)  0x0     IPv4 and IPv6 ACLs
           l2-fbqos  160bit(1)  1x512   Flow based QoS using MAC ACL/fields
         ipv4-fbqos  320bit(2)  0x0     Flow based QoS using IPv4 ACL/fields
         ipv6-fbqos  480bit(3)  0x0     Flow based QoS using IPv6 ACL/fields
       l2ipv4-fbqos  320bit(2)  0x0     Flow based QoS using MAC and IPv4 ACL/fields
           ip-fbqos  480bit(3)  0x0     Flow based QoS using IPv4 and IPv6 ACL/fields
    l2-fbmonitoring  160bit(1)  0x0     Flow based monitoring using MAC ACL/fields
  ipv4-fbmonitoring  320bit(2)  0x0     Flow based monitoring using IPv4 ACL/fields
  ipv6-fbmonitoring  480bit(3)  0x0     Flow based monitoring using IPv6 ACL/fields
l2ipv4-fbmonitoring  480bit(3)  0x0     Flow based monitoring using MAC and IPv4 ACL/fields
    ip-fbmonitoring  480bit(3)  0x0     Flow based monitoring using IPv4 and IPv6 ACL/fields
              pfcwd  160bit(1)  0x0     PFC Watchdog
                tam  320bit(2)  0x0     Telemetry
              mclag  160bit(1)  1x256   MCLAG Port Isolation
-----------------------------------------------------------------------------------------
Total 9 TCAM slices of 9 allocated. Each slice has 256 entries
=========================================================================================
    Egress features  Width      Entries Description
=========================================================================================
             l2-acl  160bit(1)  1x512   MAC ACLs
           ipv4-acl  160bit(1)  0x0     IPv4 ACLs
           ipv6-acl  320bit(2)  0x0     IPv6 ACLs
             ip-acl  320bit(2)  0x0     IPv4 and IPv6 ACLs
           l2-fbqos  160bit(1)  0x0     Flow based QoS using MAC ACL/fields
         ipv4-fbqos  160bit(1)  0x0     Flow based QoS using IPv4 ACL/fields
         ipv6-fbqos  320bit(2)  0x0     Flow based QoS using IPv6 ACL/fields
       l2ipv4-fbqos  320bit(2)  0x0     Flow based QoS using MAC and IPv4 ACL/fields
           ip-fbqos  320bit(2)  0x0     Flow based QoS using IPv4 and IPv6 ACL/fields
              pfcwd  160bit(1)  0x0     PFC Watchdog
-----------------------------------------------------------------------------------------
Total 2 TCAM slices of 2 allocated. Each slice has 256 entries

```

##### 3.6.3.8.5 Clearing the TCAM Allocation scheme.

The following command can be used to clear the TCAM allocation scheme and set it to First Come First Serve.  A `--startup` option is also available to modify the TCAM allocation in startup configuration. `--startup` requires the system to be rebooted/config reload for the TCAM allocation to take effect.

```
admin@sonic:~$ sudo tcamutil clear 
Info: TCAM Allocation cleared from the running config. Please save the config before doing reboot or config reload
```

##### 3.6.3.8.6 Modifying the current TCAM allocation

The following command is used to modify the current TCAM allocation scheme. A `--startup` option is also available to modify the TCAM allocation in startup configuration. `--startup` requires the system to be rebooted/config reload for the TCAM allocation to take effect.

```
admin@sonic:~$ sudo tcamutil modify {ingress,egress,both} ...


admin@sonic:~$ sudo tcamutil modify ingress --help
usage: tcamutil modify ingress [-h] [--startup] [-f] [--l2-acl SIZE]
                               [--ipv4-acl SIZE] [--ipv6-acl SIZE]
                               [--ip-acl SIZE] [--l2-fbqos SIZE]
                               [--ipv4-fbqos SIZE] [--ipv6-fbqos SIZE]
                               [--l2ipv4-fbqos SIZE] [--ip-fbqos SIZE]
                               [--l2-fbmonitoring SIZE]
                               [--ipv4-fbmonitoring SIZE]
                               [--ipv6-fbmonitoring SIZE]
                               [--l2ipv4-fbmonitoring SIZE]
                               [--ip-fbmonitoring SIZE] [--tam SIZE]
                               [--mclag SIZE] [--ip-helper SIZE]



admin@Belgrade2:~$ sudo tcamutil modify egress --help
usage: tcamutil modify egress [-h] [--startup] [-f] [--l2-acl SIZE]
                              [--ipv4-acl SIZE] [--ipv6-acl SIZE]
                              [--ip-acl SIZE] [--l2-fbqos SIZE]
                              [--ipv4-fbqos SIZE] [--ipv6-fbqos SIZE]
                              [--l2ipv4-fbqos SIZE] [--ip-fbqos SIZE]

optional arguments:
  -h, --help           show this help message and exit
  --startup            Modify startup config. (Requires reboot/config reload
                       for changes to take effect).
  -f, --force          Force TCAM allocation modification even when TCAM based
                       features are configured
  --l2-acl SIZE        MAC ACLs
  --ipv4-acl SIZE      IPv4 ACLs
  --ipv6-acl SIZE      IPv6 ACLs
  --ip-acl SIZE        IPv4 and IPv6 ACLs
  --l2-fbqos SIZE      Flow based QoS using MAC ACL/fields
  --ipv4-fbqos SIZE    Flow based QoS using IPv4 ACL/fields
  --ipv6-fbqos SIZE    Flow based QoS using IPv6 ACL/fields
  --l2ipv4-fbqos SIZE  Flow based QoS using MAC and IPv4 ACL/fields
  --ip-fbqos SIZE      Flow based QoS using IPv4 and IPv6 ACL/fields

SIZE should be in format NumTablesxNumEntries if the feature supports multiple
tables or NumEntries if the feature supports single table. Example 2x256 or
256
```

A TCAM allocation must be set currently to modify it. If no current TCAM allocation is set then use the **set** option described below.

##### 3.6.3.8.7 Setting a custom TCAM allocation

The following command is used to set the current TCAM allocation scheme. This can be used when the predefined profile fit the requirement and  and its not desired to set a predefined profile and modify it multiple times to fit the need. A `--startup` option is also available to modify the TCAM allocation in startup configuration. `--startup` requires the system to be rebooted/config reload for the TCAM allocation to take effect.

```
admin@sonic:~$ sudo tcamutil set allocation ingress --help
usage: tcamutil set allocation ingress [-h] [--l2-acl SIZE] [--ipv4-acl SIZE]
                                       [--ipv6-acl SIZE] [--ip-acl SIZE]
                                       [--l2-fbqos SIZE] [--ipv4-fbqos SIZE]
                                       [--ipv6-fbqos SIZE]
                                       [--l2ipv4-fbqos SIZE] [--ip-fbqos SIZE]
                                       [--l2-fbmonitoring SIZE]
                                       [--ipv4-fbmonitoring SIZE]
                                       [--ipv6-fbmonitoring SIZE]
                                       [--l2ipv4-fbmonitoring SIZE]
                                       [--ip-fbmonitoring SIZE] [--tam SIZE]
                                       [--mclag SIZE] [--startup] [-f]

optional arguments:
  -h, --help            show this help message and exit
  --l2-acl SIZE         MAC ACLs
  --ipv4-acl SIZE       IPv4 ACLs
  --ipv6-acl SIZE       IPv6 ACLs
  --ip-acl SIZE         IPv4 and IPv6 ACLs
  --l2-fbqos SIZE       Flow based QoS using MAC ACL/fields
  --ipv4-fbqos SIZE     Flow based QoS using IPv4 ACL/fields
  --ipv6-fbqos SIZE     Flow based QoS using IPv6 ACL/fields
  --l2ipv4-fbqos SIZE   Flow based QoS using MAC and IPv4 ACL/fields
  --ip-fbqos SIZE       Flow based QoS using IPv4 and IPv6 ACL/fields
  --l2-fbmonitoring SIZE
                        Flow based monitoring using MAC ACL/fields
  --ipv4-fbmonitoring SIZE
                        Flow based monitoring using IPv4 ACL/fields
  --ipv6-fbmonitoring SIZE
                        Flow based monitoring using IPv6 ACL/fields
  --l2ipv4-fbmonitoring SIZE
                        Flow based monitoring using MAC and IPv4 ACL/fields
  --ip-fbmonitoring SIZE
                        Flow based monitoring using IPv4 and IPv6 ACL/fields
  --tam SIZE            Telemetry
  --mclag SIZE          MCLAG Port Isolation
  --startup             Modify startup config. (Requires reboot/config reload
                        for changes to take effect).
  -f, --force           Force TCAM allocation modification even when TCAM
                        based features are configured

SIZE should be in format NumTablesxNumEntries if the feature supports multiple
tables or NumEntries if the feature supports single table. Example 2x256 or
256
```


```

admin@sonic:~$ sudo tcamutil set allocation egress --help
usage: tcamutil set allocation egress [-h] [--l2-acl SIZE] [--ipv4-acl SIZE]
                                      [--ipv6-acl SIZE] [--ip-acl SIZE]
                                      [--l2-fbqos SIZE] [--ipv4-fbqos SIZE]
                                      [--ipv6-fbqos SIZE]
                                      [--l2ipv4-fbqos SIZE] [--ip-fbqos SIZE]
                                      [--startup] [-f]

optional arguments:
  -h, --help           show this help message and exit
  --l2-acl SIZE        MAC ACLs
  --ipv4-acl SIZE      IPv4 ACLs
  --ipv6-acl SIZE      IPv6 ACLs
  --ip-acl SIZE        IPv4 and IPv6 ACLs
  --l2-fbqos SIZE      Flow based QoS using MAC ACL/fields
  --ipv4-fbqos SIZE    Flow based QoS using IPv4 ACL/fields
  --ipv6-fbqos SIZE    Flow based QoS using IPv6 ACL/fields
  --l2ipv4-fbqos SIZE  Flow based QoS using MAC and IPv4 ACL/fields
  --ip-fbqos SIZE      Flow based QoS using IPv4 and IPv6 ACL/fields
  --startup            Modify startup config. (Requires reboot/config reload
                       for changes to take effect).
  -f, --force          Force TCAM allocation modification even when TCAM based
                       features are configured

SIZE should be in format NumTablesxNumEntries if the feature supports multiple
tables or NumEntries if the feature supports single table. Example 2x256 or
```



```
admin@sonic:~$ sudo tcamutil set allocation both --help
usage: tcamutil set allocation both [-h] [--pfcwd SIZE] [--startup] [-f]

optional arguments:
  -h, --help    show this help message and exit
  --pfcwd SIZE  PFC Watchdog
  --startup     Modify startup config. (Requires reboot/config reload for
                changes to take effect).
  -f, --force   Force TCAM allocation modification even when TCAM based
                features are configured

SIZE should be in format NumTablesxNumEntries if the feature supports multiple
tables or NumEntries if the feature supports single table. Example 2x256 or
```

### 3.6.4 REST / gNMI / IS CLI API Support

Flow based services does not support Rest / gNMI / IS CLIs.

L2 ACLs doesn't support Rest / gNMI / IS CLIs.

# 4 Flow Diagrams

## 4.1 Create a Classifier

![Creating classifier](images/CreateClassifier.png "Create a Classifier")

**Figure 8: Create a Classifier**

## 4.2 Create a QoS Policy and Section

![Create a QoS Policy and Section](images/CreatePolicy.png "Create a QoS Policy and Section")

**Figure 9: Create a QoS Policy and Section**

## 4.3 Bind QoS policy to an interface

![Bind QoS policy to an interface](images/ApplyPolicy.png "Bind QoS policy to an interface")

**Figure 10: Bind QoS policy to an interface**

## 4.4 Creating ACL rules with policer

![Adding Rule with policer](images/RuleAddPolicer.png "Adding Rule with policer")

**Figure 11: Adding Rule with policer**

## 4.5 Deleting ACL Rules with policer

![Deleteing Rule with policer](images/RuleDelPolicer.png "Deleting Rule with policer")

**Figure 12: Deleting Rule with policer**

# 5 Error Handling

ACL Orchagent handles the scaled scenarios currently. No enhancements necessary.

# 6 Serviceability and Debug

1. show commands will help to see if the policy is active.
2. All processing errors will be captured in syslog for ACL services daemon and ACL orchagent. 
4. Debug command output will be captured as part of tech support for offline analysis.

# 7 Warm Boot Support
Configured actions and counters should continue to work across warm reboot.

# 8 Scalability

## 8.1 Software scalability

The following is the support scale for Flow based services. The following numbers are recommended numbers and are not enforced by the software. Its expected that the user will not configure beyond the recommended scale.

| Configuration                         | Scale |
| ------------------------------------- | ----- |
| Maximum number of policies            | 128   |
| Maximum number of classifiers         | 128   |
| Maximum number of sections per policy | 64    |

## 8.2 ACL Table Scalability

The applied policies will share the ACL resources of data path ACLs. Details on how the ACL resources will be shared among various ACL features will be captured in ACL Enhancements HLD.

# 9 Limitation

The following the ASIC limitations that must be noted when configuring the policies.

1. Only the following combination of ACLs are supported in a policy due to key width limitations at ingress
   1. L2 + IPv4
   2. IPv4 + IPv6
2. At egress all sections of the policy must have the same type of ACLs ie L2 or IPv4 or IPv6. Applying a policy which has different combinations will be inactive.
3. All applied policies of the same type must have the same ACL key combinations across all interfaces. Example its not valid to apply QoS Policy P1 on Ethernet0 which uses L2 ACL1 and IPv4 ACL2 and QoS Policy P2 on Ethernet4 which uses IPv4 ACL3 and IPv6 ACL4.
4. Flow counters are not available for QoS Policies at egress. 
5. Only policer Green and Red counters will be supported due to ASIC limitation at egress.
6. BCM56980 (TH3) ASIC based platform does not support egress policers.
7. For Trident2 based platforms, QoS policies do not support Match counters. Only Red and Green counters are supported.

# 10 Unit Test
1. Verify Classifier creation with ACL
2. Verify classifier creation with Header fields
3. Verify Policy creation of Type QoS
4. Verify adding multiple classifiers with match as ACL and Header fields.
5. Verify DSCP remarking action add, delete and update
6. Verify PCP remarking action add, delete and update
7. Verify Policy binding at ingress and egress for Ethernet interface
8. Verify Policy binding at ingress and egress for PortChannel interface
9. Verify Policy binding at ingress and egress for Vlan interface
10. Verify Policy binding at ingress and egress for Switch interface
11. Verify DSCP remarking with switched and routed traffic
12. Verify PCP remarking with switched and routed traffic
13. Verify PCP remarking with Untagged to Tagged traffic
14. Verify Policer with Switch and routed traffic
15. Verify Policer parameter update takes effect
16. Verify Policer at ingress and egress
17. Verify Policer on Port, LAG, VLAN and Switch
18. Add Policy section with non existing classifier
19. Apply non existing policy to interface
20. Add non existing classifier to policy
21. Verify MAC ACL creating and deletion
22. Verify MAC ACL application on Ethernet, VLAN, PortChannel and Switch at ingress and egress
23. Verify monitoring policy with active mirror session
24. Verify monitoring policy with inactive mirror session and later activate the session
25. Verify monitoring policy with active mirror session and verify deactivate and reactivate


# 11 Appendix: Sample configuration

The following example shows configuration for Policy to take QoS, Monitoring and Forwarding actions.

**Using Sonic-CLI**

```
# Create classifier class0
SONiC(config)# classifier class0 match-type acl
SONiC(config-classifier)# match access-group ip l3_ACL_0

# Create classifier class1
SONiC(config)# classifier class1 match-type acl
SONiC(config-classifier)# match access-group mac l2_ACL_0

# -------------------------------------
# Create policy policy0 for QoS actions
# -------------------------------------
SONiC(config)# policy policy0 type qos 

# Create flow using classifier class0 and set results
SONiC(config-policy)# class class0 priority 200 
SONiC(config-policy-flow)# set pcp 5
SONiC(config-policy-flow)# set dscp 15

# Create flow using classifier class0 and set results
SONiC(config-policy)# class class1 priority 100 
SONiC(config-policy-flow)# police cir 10mbps cbs 20MB pir 50mbps pbs 100MB


# --------------------------------------------
# Create policy policy1 for Monitoring actions
# --------------------------------------------
SONiC(config)# policy policy1 type monitoring

# Create flow using class1 and set results
SONiC(config-policy)# class class1 priority 100
SONiC(config-policy-flow)# set mirror-sesion test_session


# ------------------------------------
# Create policy policy2 for Forwarding
# ------------------------------------
SONiC(config)# policy policy2 type forwarding
SONiC(config-policy)# class class0 priority 100
SONiC(config-policy-flow)# set ip next-hop 10.1.1.1 priority 900
SONiC(config-policy-flow)# set ip next-hop 100.1.1.1 vrf default priority 800
SONiC(config-policy-flow)# set ip next-hop 132.45.2.100 vrf VrfOrange priority 700
SONiC(config-policy-flow)# set ip next-hop 100.10.20.30
SONiC(config-policy-flow)# set interface null


# ------------------------------------
# Apply policy to required interfaces.
# ------------------------------------
SONiC(config)# interface Ethernet 0
SONiC(conf-if-Ethernet0)# service-policy type qos in policy0
SONiC(conf-if-Ethernet4)# service-policy type monitoring in policy1
SONiC(conf-if-Ethernet4)# service-policy type forwarding in policy2
SONiC(conf-if-Ethernet0)# exit

SONiC(config)# interface Ethernet 4
SONiC(conf-if-Ethernet4)# service-policy type monitoring in policy1
SONiC(conf-if-Ethernet4)# exit

SONiC(config)# interface Ethernet 8
SONiC(conf-if-Ethernet8)# service-policy type forwarding in policy2
```

# 12 Internal Design Information

Internal BRCM information to be removed before sharing with the community

## 12.1 Future Design Enhancements

1. Once ACLs supports UDF, provide an option to use UDF also as part of fields classifier.
2. Use monitoring policy for more actions like sFlow etc.
3. Optimized mode can also support TCAM sharing for ports and VLANs.
