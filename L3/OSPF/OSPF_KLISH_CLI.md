# OSPFv2 KLISH Support 

# High Level Design Document
#### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definitionabbreviation)
  * [1 Requirement Overview](#1-requirement-overview)
     * [1.1 Functional Requirements](#11-functional-requirements)
     * [1.2 Configuration and Management Requirements](#12-configuration-and-management-requirements)
     * [1.3 Scalability Requirements](#13-scalability-requirements)
     * [1.4 Warm Boot Requirements](#14-warm-boot-requirements)
  * [2 Functionality](#2-functionality)
     * [2.1 Target Deployment Use Cases](#21-target-deployment-use-cases)
     * [2.2 Functional Description](#22-functional-description)
  * [3 Design](#3-design)
     * [3.1 Overview](#31-overview)
     * [3.2 DB Changes](#32-db-changes)
     * [3.3 Switch State Service Design](#33-switch-state-service-design)
     * [3.4 SyncD](#34-syncd)
     * [3.5 SAI](#35-sai)
     * [3.6 CLI](#36-cli)
  * [4 Flow Diagrams](#4-flow-diagrams)
  * [6 Warm Boot Support](#6-warm-boot-support)
  * [7 Scalability](#7-scalability)
  * [8 Unit Test](#8-unit-test)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 21-APR-2010 |  Naveena Suvarna   | Initial version                   |

# About this Manual
This document provides general information about Open Config yang based KLISH feature support for OSPFv2 feature in SONiC. 

# Scope
This document describes the high level description of OSPFv2 feature KLISH CLI support in SONiC.

FRR package OSPFv2 protocol will be enabled in SONiC BGP container.

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term**    | **Meaning**                             |
|-------------|-----------------------------------------|
| ABR         | Area Border Router                      |
| BFD         | Bidirectional Forwarding Detection      |
| Bgpcfgd     | BGP docker configuration daemon         |   
| CLI         | Command Line Interface                  |
| FRR         | Free Range Routing software package     |
| IP          | Internet Protocol                       |
| LSA         | Link State Advertisement                |
| LSDB        | Link State Database                     |
| OSPFv2      | Open Shortest Path First version 2      |
| SPF         | Shortest Path First                     |
| VRF         | Virtual Routing and Forwarding          |
| vtysh       | FRR provided Command Line Interface     |




# 1 Requirement Overview

## 1.1 Functional Requirements

  - Support Open Config based KLISH CLIs to OSPFv2 protocol


## 1.2 Configuration and Management Requirements

  - Support Open Config based KLISH CLIs to manage OSPFv2 protocol


## 1.3 Scalability Requirements

  - Not applicable



## 1.4 Warm Boot Requirements

  - Not applicable.


# 2 Functionality

## 2.1 Target Deployment Use Cases

KLISH based configuration management will be supported for OSPFv2 protocol in this release. At present OSPF configuration management is done only through 'vtysh' command line interface.


## 2.2 Functional Description

KLISH command line support will be based on Open Config Yang modelling. Open config Yang Model and been Modified and extended ignorer to support all the configuration and operation data show operations.

OSPFV2 configuration data will be saved in SONiC Config DB. Config DB configuration will be transformed into 'vtysh' commands and issue to BGP docker OSPFv2 commands. OSPFv2 configuration runtime, save, replay inside BGP docker will be as per existing BGP docker integrated or separated mode.

OSPF operational data for show commands and REST requests will be achieved through bgpcfd retrieval of OSPFv2 vtysh show commands.

Management Framework provide transformers callbacks will be used to convert REST requests to Config DB. Similarly transformers along with 'bgpcfgd' will be used to convert 'vtysh' given operational data to REST operational data.

# 3 Design
## 3.1 Overview

OSPFv2 Yang data Model used in SONiC is an extension of OSPFv2 OCYang model.

Below is a high level hierarchy of OSPFv2 router mode OC yang extended yang model. Router mode OSPFv2 yang data will be under network instance (or VRF) modules protocol subtree.  This OSPFv2 yang model primarily contains config and state data for OSPFv2 router.

	module: openconfig-network-instance
	  +--rw network-instances
	     +--rw network-instance* [name]
	        +--rw name                             
	        |
	        +--rw protocols
	           +--rw protocol* [identifier name]
	              | 
	              +--rw ospfv2
	                 +--rw global
	                 |  +--rw config
	                 |  |  +--
	                 |  +--rw timers
	                 |  |  +--rw spf
	                 |  |  |  +--rw config and state
	                 |  |  +--rw max-metric
	                 |  |  |  +--rw config and state
	                 |  |  +--rw lsa-generation
	                 |  |     +--rw config and state
	                 |  +--rw route-distribution-policies
	                 |  |  +--rw distribute-list* [protocol direction]
	                 |  |  |  +--rw config and state  
	                 |  |  +--rw distance
	                 |  |     +--rw config and state
	                 |  +--rw inter-area-propagation-policies
	                 |  |  +--rw inter-area-policy* [src-area]
	                 |  |     +--rw config and state
	                 |  |     +--rw ranges
	                 |  |     |  +--rw range* [address-prefix]
	                 |  |     |     +--rw config and state
	                 |  |     +--rw export-list
	                 |  |     |  +--rw config  and state
	                 |  |     +--rw import-list
	                 |  |     |  +--rw config and state  
	                 |  |     +--rw filter-list-in
	                 |  |     |  +--rw config and state  
	                 |  |     +--rw filter-list-out
	                 |  |        +--rw config and state
	                 |  +--rw oc-ospfv2-ext:passive-interfaces
	                 |      +--rw config and state
	                 |        +--rw oc-ospfv2-ext:name 
	                 |        +--rw oc-ospfv2-ext:address 
	                 +--rw areas
	                    +--rw area* [identifier]
	                       +--rw config and state
	                       +--rw stub
	                       |  +--rw config and state
	                       +--rw networks
	                       |  +--rw network* [address-prefix]
	                       |     +--rw config and state
	                       +--rw interfaces
	                       |  +--ro interface* [id]
	                       |     +--ro state 
	                       |     +--rw timers
	                       |     |  +--ro state
	                       |     +--ro neighbors-list
	                       |     |  +--ro neighbor* [neighbor-id]
	                       |     |     +--priority
	                       |     |     +--...                              
	                       |     +--ro message-statistics
	                       |        +--ro hello-recieve             
	                       +--rw virtual-links
	                       |  +--rw virtual-link* [remote-router-id]
	                       |     +--rw remote-router-id     
	                       |     +--rw config and state
	                       +--ro lsdb
	                          +--ro state 

Below is a high level hierarchy of OSPFv2 interface mode OC yang extended yang model. Interface mode OSPFv2 yang data will be under OC Yang interfaces module. This OSPFv2 yang model contains interface level OSPFv2 configurations.
	
	module: openconfig-interfaces
	  +--rw interfaces
	     +--rw interface* [name]
	        +-- 
	        +--rw subinterfaces
	           +--rw subinterface* [index]
	              +-- 
	              +--rw ipv4
	                 +-- 
	                 +--rw oc-ospfv2-ext:ospfv2
	                    +--rw oc-ospfv2-ext:if-addresses* [address]
	                       +--rw oc-ospfv2-ext:config
	                          +--rw oc-ospfv2-ext:address?                             
	                          +--rw oc-ospfv2-ext:network-type?                    
	                          +--rw oc-ospfv2-ext:priority?                      
                                  +--...

With above OCYang model support, user will use REST URL APIs corresponding to above OCYang model and retrieve configuration and operational/state data of OSPFv2 from SONiC systems.

KLISH CLIs will be provided for OPSFv2 configuration and show commands. Please refer to CLI section for more details.

SONiC Yang definitions will be written to support OSPFv2 configuration data.  OC Yang model of OSPFv2 will be mapped to corresponding SONiC Yang data model. 


## 3.2 DB Changes

SONiC Yang definitions for OSPFv2 will be newly defined.  This Yang definition represents OSPFv2 configuration data in SONiC Config DB. OSPFv2 configurations will be stored in config_db.json as per OSPFv2 SONiC yang defined.


### 3.2.1 CONFIG DB

OSPFv2 Configuration data will be as per SONiC yang model. There will be five tables as below. All the OSPFv2 yang definitions will be present in sonic-ospfv2 module.

 	1. OSPFV2_ROUTER  
	2. OSPFV2_ROUTER_AREA  
	3. OSPFV2_ROUTER_AREA_NETWORK  
	4. OSPFV2_ROUTER_AREA_VIRTUAL_LINK  
	5. OSPFV2_ROUTER_AREA_POLICY_ADDRESS_RANGE  
	6. OSPFV2_ROUTER_DISTRIBUTE_ROUTE 
	7. OSPFV2_ROUTER_PASSIVE_INTERFACE
	8. OSPFV2_INTERFACE
	    
	    
### 3.2.1.1 OSPFV2_ROUTER Table

This table contains all OSPF router Global mode configurations. VRF (network instance) name will be the Key for this table

	+--rw OSPFV2_ROUTER
	   +--rw OSPFV2_ROUTER_LIST* [vrf_name]
	      +--rw vrf_name                         union
	      +--rw enable?                          boolean
	      +--rw description?                     string
	      +--rw router-id?                       ietf-yang:dotted-quad
	      +--rw abr-type?                        sonic-ospf-cmn:ospf-abr-type
	      +--rw auto-cost-reference-bandwidth?   uint32
	      +--rw log-adjacency-changes?           sonic-ospf-cmn:ospf-log-type
	      +--rw default-metric?                  uint32
	      +--rw opaque-lsa-capability?           boolean
	      +--rw ospf-rfc1583-compatible?         boolean
	      +--rw passive-interface-default?       boolean
	      +--rw write-multiplier?                uint8
	      +--rw spf-initial-delay?               uint32
	      +--rw spf-maximum-delay?               uint32
	      +--rw spf-throttle-delay?              uint32
	      +--rw lsa-min-interval-timer?          uint32
	      +--rw lsa-min-arrival-timer?           uint32
	      +--rw lsa-refresh-timer?               uint32
	      +--rw max-metric-administrative?       boolean
	      +--rw max-metric-on-startup?           uint32
	      +--rw max-metric-on-shutdown?          uint32
	      +--rw distance-all?                    sonic-ospf-cmn:ospf-distance
	      +--rw distance-intra-area?             sonic-ospf-cmn:ospf-distance
	      +--rw distance-inter-area?             sonic-ospf-cmn:ospf-distance
	      +--rw distance-external?               sonic-ospf-cmn:ospf-distance


### 3.2.1.2 OSPFV2_ROUTER_AREA Table
 
This table contains all OSPF router area common configurations. VRF name and area id will be the Key for this table

	+--rw OSPFV2_ROUTER_AREA
	   +--rw OSPFV2_ROUTER_AREA_LIST* [vrf_name area-id]
	      +--rw vrf_name             -> ../../../OSPFV2_ROUTER/OSPFV2_ROUTER_LIST/vrf_name
	      +--rw area-id              sonic-ospf-cmn:ospf-area-identifier
	      +--rw description?         string
	      +--rw authentication?      string
	      +--rw shortcut             sonic-ospf-cmn:ospf-config-type
	      +--rw stub?                boolean
	      +--rw stub-no-summary?     boolean
	      +--rw stub-default-cost?   uint32
	      +--rw import-list?         -> /sonic-rmap:sonic-route-map/ROUTE_MAP_SET/ROUTE_MAP_SET_LIST/name
	      +--rw export-list?         -> /sonic-rmap:sonic-route-map/ROUTE_MAP_SET/ROUTE_MAP_SET_LIST/name
	      +--rw filter-list-in?      -> /sonic-rpolicy:sonic-routing-policy-sets/PREFIX_SET/PREFIX_SET_LIST/name
	      +--rw filter-list-out?     -> /sonic-rpolicy:sonic-routing-policy-sets/PREFIX_SET/PREFIX_SET_LIST/name 


### 3.2.1.2 OSPFV2_ROUTER_AREA_NETWORK Table
 
This table contains all OSPF router per area network prefix configurations. VRF  name, area id and Network prefix will be the Key for this table.

	+--rw OSPFV2_ROUTER_AREA_NETWORK
	   +--rw OSPFV2_ROUTER_AREA_NETWORK_LIST* [vrf_name area-id prefix]
	      +--rw vrf_name       -> ../../../OSPFV2_ROUTER/OSPFV2_ROUTER_LIST/vrf_name
	      +--rw area-id        sonic-ospf-cmn:ospf-area-identifier
	      +--rw prefix         ietf-inet:ipv4-prefix
	      +--rw description?   string


### 3.2.1.2 OSPFV2_ROUTER_AREA_VIRTUAL_LINK Table
 
This table contains all OSPF router per area virtual link configurations. VRF  name, area id and virtual link remote router id will be Key for this table.

	+--rw OSPFV2_ROUTER_AREA_VIRTUAL_LINK
	   +--rw OSPFV2_ROUTER_AREA_VIRTUAL_LINK_LIST* [vrf_name area-id remote-router-id]
	      +--rw vrf_name                   -> ../../../OSPFV2_ROUTER/OSPFV2_ROUTER_LIST/vrf_name
	      +--rw area-id                    sonic-ospf-cmn:ospf-area-identifier
	      +--rw description?               string
	      +--rw remote-router-id           ietf-yang:dotted-quad
	      +--rw authentication-type?       sonic-ospf-cmn:ospf-authentication-type
	      +--rw authentication-key?        string
	      +--rw authentication-key-id?     uint8
	      +--rw authentication-md5-key?    string
	      +--rw dead-interval?             uint32
	      +--rw hello-interval?            uint32
	      +--rw retransmission-interval?   uint32
	      +--rw transmit-delay?            uint32


### 3.2.1.2 OSPFV2_ROUTER_AREA_POLICY_ADDRESS_RANGE Table
 
This table contains all OSPF router per area policy address range configurations. VRF  name, area id and range prefix will be  key for this table.
	
	+--rw OSPFV2_ROUTER_AREA_POLICY_ADDRESS_RANGE
	   +--rw OSPFV2_ROUTER_AREA_POLICY_ADDRESS_RANGE_LIST* [vrf_name area-id prefix]
	      +--rw vrf_name            -> ../../../OSPFV2_ROUTER/OSPFV2_ROUTER_LIST/vrf_name
	      +--rw area-id             sonic-ospf-cmn:ospf-area-identifier
	      +--rw prefix              ietf-inet:ipv4-prefix
	      +--rw description?        string
	      +--rw advertise?          boolean
	      +--rw metric?             sonic-ospf-cmn:ospf-metric
	      +--rw substitute?         boolean
	      +--rw substitue-prefix?   ietf-inet:ipv4-prefix


### 3.2.1.2 OSPFV2_ROUTER_DISTRIBUTE_ROUTE Table
 
This table contains all OSPF router route distribution configurations. VRF name,  protocol Id and distribution direction will be Key for this table.      

	+--rw OSPFV2_ROUTER_DISTRIBUTE_ROUTE
	   +--rw OSPFV2_ROUTER_DISTRIBUTE_ROUTE_LIST* [vrf_name protocol direction]
	      +--rw vrf_name       -> ../../../OSPFV2_ROUTER/OSPFV2_ROUTER_LIST/vrf_name
	      +--rw protocol       sonic-ospf-cmn:ospf-policy-protocol
	      +--rw direction      sonic-ospf-cmn:ospf-policy-direction
	      +--rw description?   string
	      +--rw metric?        sonic-ospf-cmn:ospf-metric
	      +--rw metric-type?   sonic-ospf-cmn:ospf-metric-type
	      +--rw table-id?      uint32
	      +--rw always?        boolean
	      +--rw route-map?     -> /sonic-rmap:sonic-route-map/ROUTE_MAP_SET/ROUTE_MAP_SET_LIST/name


### 3.2.1.2 OSPFV2_ROUTER_PASSIVE_INTERFACE Table

This table contains all OSPF router passive interface configurations. VRF name and interface name will be Key for this table.

	+--rw OSPFV2_ROUTER_PASSIVE_INTERFACE
	+--rw OSPFV2_ROUTER_PASSIVE_INTERFACE_LIST* [vrf_name name address]
	   +--rw vrf_name    -> ../../../OSPFV2_ROUTER/OSPFV2_ROUTER_LIST/vrf_name
	   +--rw name        string
	   +--rw address?    ietf-inet:ipv4-address-no-zone

	    	
### 3.2.1.2 OSPFV2_INTERFACE Table
 
This table contains all OSPF interface level configurations. Interface name will be the Key for this table.      
        
	+--rw OSPFV2_INTERFACE
	   +--rw OSPFV2_INTERFACE_LIST* [name address]
	   +--rw name                         sonic-ospf-cmn:ospf-interface
	   +--rw area-id?                     sonic-ospf-cmn:ospf-area-identifier
	   +--rw address?                     ietf-inet:ipv4-address-no-zone
	   +--rw network-type?                sonic-ospf-cmn:ospf-network-type
	   +--rw metric?                      sonic-ospf-cmn:ospf-metric
	   +--rw priority?                    uint8
	   +--rw mtu-ignore?                  boolean
	   +--rw bfd-enable?                  boolean
	   +--rw authentication-type?         sonic-ospf-cmn:ospf-authentication-type
	   +--rw authentication-key?          string
	   +--rw authentication-key-id?       uint8
	   +--rw authentication-md5-key?      string
	   +--rw dead-interval?               uint32
	   +--rw hello-interval?              uint32
	   +--rw hello-multiplier?            uint32
	   +--rw retransmission-interval?     uint32
	   +--rw transmit-delay?              uint32

   
### 3.2.2 APP DB

OSPFv2 will be under FRR BGP docker. Hence there will not be any application data of OSPFv2 in SONiC APP DB.


### 3.2.3 STATE DB
Not applicable

### 3.2.4 ASIC DB
Not applicable

### 3.2.5 COUNTER DB
Not applicable

### 3.2.6 Configuration save and restart

All OSPFv2 configuration done through KLISH CLI or REST API will be saved as running configuration in SONiC config_db.json running config file.  

Upon saving SONiC running config, all OSPFv2 configurations will be save to config_db.json file.

Over an System restart or BGP docker restart, all saved configurations will be replayed from config_db.json


## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
Not applicable

### 3.3.2 Other Process
Not applicable

## 3.4 SyncD
Not applicable

## 3.5 SAI
Not applicable


## 3.6 CLI

OSPFv2 commands will be supported in KLISH command line interface. SONiC Supported FRR OSPFv2 commands will be available in KLISH CLI modes with  minor modifications as described in upcoming sections.

For OSPFv2 command details please refer to FRR package reference manual at http://docs.frrouting.org/en/latest . 

All no form of CLI commands will not accept data value in KLISH unless it is required for extended subcommands. Hence all no form of commands will assume that configured value at that moment will be removed from configuration.

Configuring a parameter, with an already configured value present, will overwrite the existing value of the parameter without warning.

Please note that all KLISH CLI command 'no' forms (unconfig) will not be accepting command parameter values unless those values are used to identify the configuration record uniquely. For example vtysh command 'no refresh timer 20' will be 'no refresh timer' in KLISH.

### 3.6.1 Data Models

Data modeling is as described in section 3.2 Config DB.


### 3.6.2 Configuration Commands


Sub sections below describe KLISH supported CLI commands for OSPFv2. Command syntax uses below formatting. 
        
	< > : Mandatory
	[ ] : Optional parameters
	( ) : Value in the range
	{ } : one or more parameters
	A.B.C.D : IPv4 dotted format 
	A.B.C.D/M : IPv4 prefix	with mask lenghth
	AREAID : A.B.C.D|(0-4294967295)
	IFNAME : Interface Name
	KEY : Authentication password string
	NAME : Name (for example route map, prefixlist)


#### 3.6.2.1 OSPF router configuration


OSPF router configuration command will be below

	        [no] router ospf [vrf <vrf-name>]


	
#### 3.6.2.2 OSPF router global commands

OSPF router mode global commands will be as below

	        [no] ospf router-id [A.B.C.D]
	        [no] ospf abr-type <cisco|ibm|shortcut|standard>
	        [no] compatible rfc1583
	        [no] write-multiplier (1-100)

	        [no] auto-cost reference-bandwidth (1-4294967)
	        [no] log-adjacency-changes [detail]

	        [no] max-metric router-lsa administrative
	        [no] max-metric router-lsa on-shutdown (5-100)
	        [no] max-metric router-lsa on-startup (5-86400)

	        [no] distance (1-255)
	        [no] distance ospf {intra-area (1-255) | inter-area (1-255) | external (1-255)}

	        [no] passive-interface <IFNAME [A.B.C.D]|default>

	        [no] refresh timer (10-1800)
	        [no] timers lsa min-arrival (0-600000)
	        [no] timers throttle lsa all (0-5000)
	        [no] timers throttle spf {(0-600000)|(0-600000)|(0-600000)} 

    

#### 3.6.2.3 OSPF router area commands

OSPF area  commands will be as below

		[no] area <AREAID> authentication [message-digest]
		[no] area <AREAID> shortcut <default|enable|disable>
		[no] area <AREAID> default-cost (0-16777215)
		[no] area <AREAID> stub [no-summary]


#### 3.6.2.4 OSPF router area network commands

OSPF area mode network commands will be as below.

	        [no] network A.B.C.D/M area <AREAID>


#### 3.6.2.5 OSPF router area virtual link commands

OSPF area  virtual link commands will be as below

	        [no] area <AREAID> virtual-link A.B.C.D [authentication [<message-digest|null>]] 
		                   [<message-digest-key (1-255) md5 KEY> | <authentication-key KEY>]
	        [no] area <AREAID> virtual-link A.B.C.D hello-interval (1-65535)
	        [no] area <AREAID> virtual-link A.B.C.D retransmit-interval (1-65535)
	        [no] area <AREAID> virtual-link A.B.C.D transmit-delay (1-65535)
	        [no] area <AREAID> virtual-link A.B.C.D dead-interval (1-65535)


#### 3.6.2.6 OSPF router area policy commands

OSPF area  inter area propagation policy commands will be as below

	        [no] area <AREAID> import-list NAME
	        [no] area <AREAID> export-list NAME
	        [no] area <AREAID> filter-list prefix NAME <in|out>

	        [no] area <AREAID> range A.B.C.D/M [advertise [cost (0-16777215)]]
	        [no] area <AREAID> range A.B.C.D/M cost (0-16777215)
	        [no] area <AREAID> range A.B.C.D/M not-advertise
	        [no] area <AREAID> range A.B.C.D/M substitute A.B.C.D/M


#### 3.6.2.7 OSPF router route distribution commands

OSPF router  route distribution policy commands will be as below

	        [no] distribute-list NAME out <kernel|connected|static|bgp>

	        [no] redistribute <kernel|connected|static|bgp> [{metric (0-16777214)|metric-type (1-2)|route-map NAME}]

	        [no] default-information originate [{always|metric (0-16777214)|metric-type (1-2)|route-map NAME}]
	        [no] default-metric (0-16777214)

        
#### 3.6.2.8 OSPF router interface commands

OSPF Interface mode commands will be as below

	        [no] ip ospf area <AREAID> [A.B.C.D]
	        [no] ip ospf network <broadcast|point-to-point>
	        [no] ip ospf priority (0-255) [A.B.C.D]

	        [no] ip ospf authentication <null|message-digest> [A.B.C.D]
	        [no] ip ospf authentication [A.B.C.D]
	        [no] ip ospf authentication-key KEY [A.B.C.D]
	        [no] ip ospf message-digest-key (1-255) md5 KEY [A.B.C.D]

	        [no] ip ospf bfd
	        [no] ip ospf cost (1-65535) [A.B.C.D]
		[no] ip ospf mtu-ignore [A.B.C.D]

	        [no] ip ospf hello-interval (1-65535) [A.B.C.D]
	        [no] ip ospf transmit-delay (1-65535) [A.B.C.D]
	        [no] ip ospf retransmit-interval (3-65535) [A.B.C.D]
	        [no] ip ospf dead-interval (1-65535) [A.B.C.D]
	        [no] ip ospf dead-interval minimal hello-multiplier (1-10) [A.B.C.D]

	        no ip ospf interface
		


There are few minor differences in the way interface mode OSPF configurations have to be done in KLISH. Those deviations are as detailed below.   

KLISH mode interface config does not provide vrf option in the same command line as interface. For example command of  format 'interface Ethernet0 vrf <vrf-name>'  will not be available in KLISH. In SONiC KLISH interface VRF binding will be done by sepereate VRF binding command 'ip vrf forwarding <vrf-name>'.
 
Interface mode will provide an additional command to Unconfigure ospf interface explicitely. Command is 'no ip ospf interface'. This command will delete all interface mode OSPF configurations and disable OSPF in that interface.

Most of the commands under interface mode accepts optional Interface IP address as parameter. Whenever user doesnot provide any interface IP address in CLI command, such parameters will be associated with default '0.0.0.0' ip address. Hence all configured interface attributes without IP address associated with them will be with key 'InterfaceName|0.0.0.0' in config database OSPFV4_INTERFACE table.

In 'vtysh' CLI, if there are no explicit ospf configurations on an interface then ospf will be disabled automatically on that interface. With KLISH, same behaviour is maintained in FRR BGP conatiner, however SONiC config DB will have OSPF interface configured in it with no parameters. User has to explicit issue a 'no ip ospf interface' to delete the interface from SONiC config DB.

For more details of above list of config commands, please refer to FRR User Manual.


### 3.6.3 Show Commands

Below list of KLISH OSPF show commands will be supported

        	show ip ospf [vrf <vrf-name>]
        	show ip ospf [vrf <vrf-name>] interface [IFNAME]
        	show ip ospf [vrf <vrf-name>] interface traffic [IFNAME]
        	show ip ospf [vrf <vrf-name>] neighbor [IFNAME] [detail]
        	show ip ospf [vrf <vrf-name>] route

        	show ip ospf database [detail]
      
In all show command 'vrf all' options will not be supported.

For more details of above list of show commands, please refer to FRR User Manual.



### 3.6.4 Debug Commands

Not applicable 


### 3.6.5 REST API Support


#### 3.6.2.1 OSPF router configuration

REST URI for OSPF router configuration command will be below


	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2/global/config/openconfig-ospfv2-ext:enable"  -d "{  \"openconfig-ospfv2-ext:enable\": true}"
			
	DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2" -H  "accept: application/yang-data+json"	


#### 3.6.5.2 OSPF router global commands

REST URI for OSPF router mode global commands will be as below
 

	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2"  -d "{  \"openconfig-network-instance:ospfv2\": {\"global\": {\"config\": {\"router-id\": \"3.3.3.3\"}}}}"

	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2"  -d "{  \"openconfig-network-instance:ospfv2\": {\"global\": {\"config\": {\"log-adjacency-changes\": \"DETAIL\"}}}}"

REST URI data fields can be

	"openconfig-network-instance:ospfv2": {
	  "global": {
	    "config": {
	      "router-id": "string",
	      "log-adjacency-changes": "string",
	      "openconfig-ospfv2-ext:enable": true,
	      "openconfig-ospfv2-ext:description": "string",
	      "openconfig-ospfv2-ext:default-metric": 0,
  	      "openconfig-ospfv2-ext:auto-cost-reference-bandwidth": 0,
	      "openconfig-ospfv2-ext:write-multiplier": 0,
	      "openconfig-ospfv2-ext:abr-type": "string",
	      "openconfig-ospfv2-ext:ospf-rfc1583-compatible": true,
	      "openconfig-ospfv2-ext:passive-interface-default": true
            },
            "timers": {
	      "spf": {
	        "config": {
	        "initial-delay": 0,
	        "maximum-delay": 0,
	        "openconfig-ospfv2-ext:throttle-delay": 0
	      }
	    },
	    "max-metric": {
	      "config": {
	        "openconfig-ospfv2-ext:administrative": true,
	        "openconfig-ospfv2-ext:on-startup": 0,
	        "openconfig-ospfv2-ext:on-shutdown": 0
	      }
	    },
	    "lsa-generation": {
	      "config": {
	        "openconfig-ospfv2-ext:minimum-interval": 0,
	        "openconfig-ospfv2-ext:minimum-arrival": 0,
	        "openconfig-ospfv2-ext:refresh-timer": 0
	      }
	    }
          },
          "openconfig-ospfv2-ext:route-distribution-policies": {
	    "distance": {
	      "config": {
	        "all": 0,
	        "intra-area": 0,
	        "inter-area": 0,
	        "external": 0
	      }
	    }
	  }
	}


#### 3.6.5.3 OSPF router area commands

REST URI for OSPF area commands will be as below


	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2/areas/area=0.0.0.1/config/openconfig-ospfv2-ext:authentication-type"  -d "{  \"openconfig-ospfv2-ext:authentication-type\" : \"MD5HMAC\"}"

Area level configs data can be

	"area": [
        {
          "identifier": "string",
          "config": {
            "identifier": "string",
            "openconfig-ospfv2-ext:authentication-type": "string"
	    "openconfig-ospfv2-ext:shortcut": "string"
          },
          "openconfig-ospfv2-ext:stub": {
            "config": {
              "enable": true,
              "no-summary": true,
              "default-cost": 0
            }
          }
        }
      ]

#### 3.6.5.4 OSPF router area network commands

REST URI for OSPF area mode network commands will be as below.


	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2/areas/area=0/openconfig-ospfv2-ext:networks/network=1.2.3.4%2F24/config/description"  -d "{ \"openconfig-ospfv2-ext:description\": \"Interface Address 1.2.3.4\"}"

Data fields can be


          "openconfig-ospfv2-ext:networks": {
            "network": [
              {
                "address-prefix": "string",
                "config": {
                  "address-prefix": "string",
                  "description": "string"
                }
              }
            ]



#### 3.6.5.5 OSPF router area virtual link commands

REST URI for OSPF area virtual link commands will be as below


	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2/areas/area=0.0.0.0/virtual-links/virtual-link=6.6.6.6"  -d "{  \"openconfig-network-instance:virtual-link\": [ { \"remote-router-id\": \"6.6.6.6\", \"config\": { \"remote-router-id\": \"6.6.6.6\"} }]}"

Data fields can be

            "virtual-link": [
              {
                "remote-router-id": "string",
                "config": {
                  "remote-router-id": "string",
                  "openconfig-ospfv2-ext:dead-interval": 0,
                  "openconfig-ospfv2-ext:hello-interval": 0,
                  "openconfig-ospfv2-ext:retransmission-interval": 0,
                  "openconfig-ospfv2-ext:transmit-delay": 0,
                  "openconfig-ospfv2-ext:authentication": {
                    "authentication-type": "string",
                    "authentication-key": "string",
                    "authentication-key-id": 0,
                    "authentication-md5-key": "string"
                  }
                }
              }
            ]

#### 3.6.5.6 OSPF router area policy commands

REST URI for OSPF area inter area propagation policy commands will be as below


	 PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2/global/inter-area-propagation-policies/openconfig-ospfv2-ext:inter-area-policy=0.0.0.1/export-list" -d "{  \"openconfig-ospfv2-ext:export-list\": {\"config\": {\"name\": \"ospf-rmap\" }}}"
	 PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2/global/inter-area-propagation-policies/openconfig-ospfv2-ext:inter-area-policy=0.0.0.1/import-list" -d "{  \"openconfig-ospfv2-ext:import-list\": {\"config\": {\"name\": \"ospf-rmap\" }}}"
	 PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2/global/inter-area-propagation-policies/openconfig-ospfv2-ext:inter-area-policy=0.0.0.1/filter-list-in" -d "{  \"openconfig-ospfv2-ext:filter-list-in\": {\"config\": {\"name\": \"ospf-prefix-list1\" }}}"
	 PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2/global/inter-area-propagation-policies/openconfig-ospfv2-ext:inter-area-policy=0.0.0.1/filter-list-out" -d "{  \"openconfig-ospfv2-ext:filter-list-out\": {\"config\": {\"name\": \"ospf-prefix-list1\" }}}"
	 PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2/global/inter-area-propagation-policies/openconfig-ospfv2-ext:inter-area-policy=0.0.0.0/ranges/range=10.3.2.3%2F24"  -d "{ \"openconfig-ospfv2-ext:range\": [{ \"address-prefix\": \"10.3.2.3/24\", \"config\": {\"address-prefix\": \"10.3.2.3/24\"}}]}"

Address range Data fields can be

	       range": [
                {
                  "address-prefix": "string",
                  "config": {
                    "address-prefix": "string",
                    "advertise": true,
                    "metric": 0,
                    "substitute": true,
                    "substitue-prefix": "string"
                  }
                }
              ]"


#### 3.6.5.7 OSPF router route distribution commands

Redistribure, distribute-list and default-route commands are considered as route distribution with protocol and direction as key values. Protocol for redistribution and distribute-list will be the command specified protocol. For default route protocol will be default-route. Direction for distribute-list is always EXPORT,  direction for default-route and redistribute is always IMPORT.

REST URI for OSPF router route distribution policy commands will be as below


	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2/global/openconfig-ospfv2-ext:route-distribution-policies/distribute-list=BGP,IMPORT/config/description" -H "accept: application/yang-data+json" -H "Content-Type: application/yang-data+json" -d "{ \"openconfig-ospfv2-ext:description\": \"BGP Import policy\"}"
	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2/global/openconfig-ospfv2-ext:route-distribution-policies/distribute-list=DEFAULT_ROUTE,IMPORT/config/metric" -H "accept: application/yang-data+json" -H "Content-Type: application/yang-data+json" -d "{ \"openconfig-ospfv2-ext:metric\": 80}"

Route distribution data fields can be

	"distribute-list": [
          {
            "protocol": "string",
            "direction": "string",
            "config": {
              "protocol": "string",
              "direction": "string",
              "description": "string",
              "table-id": 0,
              "always": true,
              "metric": 0,
              "metric-type": "string",
              "route-map": "string"
            }
          }
        ]


#### 3.6.5.8 OSPF router passive interface commands

REST URI for OSPF router  passive interface commands will be as below

	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2/global/openconfig-ospfv2-ext:passive-interfaces" -d "{  \"openconfig-ospfv2-ext:passive-interfaces\": { \"passive-interface\": [ {\"name\": \"Ethernet4\",\"config\": { \"name\": \"Ethernet4\" } }]}}"
	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-network-instance:network-instances/network-instance=Vrf-blue/protocols/protocol=OSPF,ospfv2/ospfv2/global/openconfig-ospfv2-ext:passive-interfaces"  -d "{  \"openconfig-ospfv2-ext:passive-interfaces\": { \"passive-interface\": [ {\"name\": \"Ethernet4\",\"config\": { \"name\": \"Ethernet4\",  \"address\": \"2.3.4.5\" } }]}}"



#### 3.6.5.9 OSPF interface commands

REST URI for OSPF Interface mode commands will be as below

	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface=Ethernet8/subinterfaces/subinterface=0/openconfig-if-ip:ipv4/openconfig-ospfv2-ext:ospfv2/if-addresses"  -d "{  \"openconfig-ospfv2-ext:if-addresses\": [{ \"address\": \"0.0.0.0\",\"config\": {\"priority\": 10}}]}"
	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface=Ethernet8/subinterfaces/subinterface=0/openconfig-if-ip:ipv4/openconfig-ospfv2-ext:ospfv2/if-addresses"  -d "{  \"openconfig-ospfv2-ext:if-addresses\": [{ \"address\": \"1.1.1.1\",\"config\": {\"priority\": 10}}]}"
	PATCH "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface=Ethernet8/subinterfaces/subinterface=0/openconfig-if-ip:ipv4/openconfig-ospfv2-ext:ospfv2/if-addresses"  -d "{  \"openconfig-ospfv2-ext:if-addresses\": [{ \"address\": \"2.2.2.2\",\"config\": {\"priority\": 10}}]}" 

	DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface=Ethernet8/subinterfaces/subinterface=0/openconfig-if-ip:ipv4/openconfig-ospfv2-ext:ospfv2/if-addresses=2.2.2.2/config/priority" 
	DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface=Ethernet8/subinterfaces/subinterface=0/openconfig-if-ip:ipv4/openconfig-ospfv2-ext:ospfv2/if-addresses" 
	DELETE "<REST-SERVER:PORT>/restconf/data/openconfig-interfaces:interfaces/interface=Ethernet8/subinterfaces/subinterface=0/openconfig-if-ip:ipv4/openconfig-ospfv2-ext:ospfv2" 


Ospf interface data fields can be

	"openconfig-ospfv2-ext:ospfv2": {
	    "if-addresses": [
	      {
	        "address": "string",
	        "config": {
	          "id": "string",
	          "network-type": "string",
	          "priority": 0,
	          "multi-area-adjacency-primary": true,
	          "metric": 0,
	          "area-id": "string",
	          "address": "string",
	          "hello-multiplier": 0,
	          "mtu-ignore": true,
	          "bfd-enable": true,
	          "authentication": {
	            "authentication-type": "string",
	            "authentication-key": "string",
	            "authentication-key-id": 0,
	            "authentication-md5-key": "string"
	          },
	          "dead-interval": 0,
	          "hello-interval": 0,
	          "retransmission-interval": 0,
	          "transmit-delay": 0
	        }
	      }
	    ]
	  }

While using REST URI for interface configuration, it is mandatory to provide IP address as 0.0.0.0 or the interface IP address associated with the commands.


### 3.6.6 Operational Commands

Below operational command will be supported from KLISH. 

	clear ip ospf [vrf <name>] interface [IFNAME]


### 3.6.7 Limitations

Below are few limitation with respect to KLISH CLI Interface

If there are area-id configurations under ospf interface and ospf router is configured for that interface VRF then before performing 'no router ospf' it is recomended to unconfigure the area-id under interface configuration. If this is not done, then the interface area id will not be taking effect in any upcoming router ospf configuration for the same interface.

Max-metric router-lsa on-shutdown command will not be working in Klish mode. Hence its recomended to use max-metric router-lsa administrative option before any transit route avoidance use cases like software upgrade.


# 4 Flow Diagrams
Not applicable

# 5 Serviceability and Debug
Not applicable. 


# 6 Warm Boot Support

Upon warm reboot, saved OSPF configurations in config_db.json shall be replayed.


# 7 Scalability

Not applicable.

# 8 Unit Test
Unit test cases for this specification are as listed below:


1.  Verify OSPF router configuration.
2.  Verify OSPF router Id configuration
3.  Verify OSPF router area authentication configurations
4.  Verify OSPF router area default cost configuration
5.  Verify OSPF router area export list configurations
6.  Verify OSPF router area prefix list configurations
7.  Verify OSPF router area import-list configurations
8.  Verify OSPF router area stub configurations
9.  Verify OSPF router refrence bandwidth configuration
10.  Verify OSPF router default metric configuration
11.  Verify OSPF router distance configuration
12.  Verify OSPF router distribute list configurations
13.  Verify OSPF router max metric configurations
14.  Verify OSPF router virtual link configurations
15.  Verify OSPF router passive interface configuration
16.  Verify OSPF router redistribute configurations
17.  Verify OSPF router refresh timer configuration
18.  Verify OSPF router LSA timer configurations
19.  Verify OSPF router SPF timer configurations
20.  Verify OSPF router configuration on non default VRF
21.  Verify OSPF router adjacency change logging configuration
22.  Verify OSPF ASBR router default route originate configuration
23.  Verify OSPF network configurations
24.  Verify OSPF interface area configuration
25.  Verify OSPF interface BFD configuration
26.  verify OSPF interface cost configuration
27.  Verify OSPF interface authentication configuration
28.  Verify OSPF interface Dead interval and multiplier configuration
29.  Verify OSPF interface Hello interval configuration
30.  Verify OSPF interface Message Digest Key configuration
31.  Verify OSPF interface MTU ignore configuration
32.  Verify OSPF interface network type configuration
33.  Verify OSPF interface priority configuration
34.  Verify OSPF interface retransmit interval configuration
35.  Verify OSPF interface retransmit delay configuration
36.  Verify OSPF interface configurations on VLAN L3 interfaces
37.  Verify OSPF interface configurations on Portchannel interfaces
38.  Verify OSPF interface configurations on VRF based Ethernet interfaces
39.  Verify OSPF interface configurations on VRF based VLAN interfaces
40.  Verify OSPF interface configurations on VRF based port channel interfaces
41.  Verify single area Two or more node OSPF sessions
42.  Verify OSPF Sessions with Ethernet interfaces
43.  Verify OSPF Sessions with VLAN interfaces
44.  Verify OSPF Sessions with port channel interfaces
45.  Verify OSPF sessions with multiple links between two or more nodes
46.  Verify OSPF sessions on point-to-point and broadcast links
47.  Verify OSPF router id selection order of config, loopback, interface ip address
48.  Verify OSPF ip route and LSDB entries for single area configurations
49.  Verify OSPF session flap due to BFD flap
50.  Verify OSPF max-metric functionality
51.  Verify OSPF distance setting and its effect on ip routing table
52.  Verify Default route origination with metric and metric type
53.  Verify 'show ip ospf [vrf <name>]' command outputs
54.  Verify 'show ip ospf [vrf <name>] interface [IFNAME]' command outputs
55.  Verify 'show ip ospf [vrf <name>] neighbor  [IFNAME] [detail]' command outputs
56.  Verify 'show ip ospf [vrf <name>] route' command outputs
57.  Verify CRUD URIs for OSPFv2 router and its global level fields
58.  Verify CRUD URIs for OSPFv2 router Area its sub fields
59.  Verify CRUD URIs for OSPFv2 router Area Network its sub fields
60.  Verify CRUD URIs for OSPFv2 router Area Virtual Link its sub fields
61.  Verify CRUD URIs for OSPFv2 router Area Passive Interface its sub fields
62.  Verify CRUD URIs for OSPFv2 router Area Address Range and its sub fields
63.  Verify CRUD URIs for OSPFv2 router Area Route distribution and its sub fields
64.  Verify CRUD URIs for OSPFv2 Interface and its sub fields




# 9 Internal Design Information
