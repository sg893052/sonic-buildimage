
# OSPFv2 Routing Protocol 

# High Level Design Document
#### Rev 0.3

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
| 0.1 | 21-OCT-2019 |  Naveena Suvarna   | Initial version                   |
| 0.2 | 29-OCT-2019 |  Naveena Suvarna   | Updated per review comments       |
| 0.3 | 29-OCT-2019 |  Naveena Suvarna   | Updated per review comments       |
| 0.4 | 29-DEC-2019 |  Naveena Suvarna   | Updated scaling numbers           |

# About this Manual
This document provides general information about supporting Open Shortest Path First version-2 (OSPFv2) Routing protocol in SONiC.

# Scope
This document describes the high level description of OSPFv2 support in SONiC. 

FRR package OSPFv2 protocol will be enabled in SONiC BGP container.

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term**    | **Meaning**                             |
|-------------|-----------------------------------------|
| ABR         | Area Border Router                      |
| ASBR        | Autonomous System Border Router         |
| BFD         | Bidirectional Forwarding Detection      |
| BGP         | Border Gateway protocol                 |
| CLI         | Command Line Interface                  |
| FRR         | FR Routing software package             |
| IGP         | Interior Gateway Protocol               |
| IP          | Internet Protocol                       |
| IS-IS       | Intermediate System Intermediate System |
| LSA         | Link State Advertisement                |
| L3VPN       | Layer-3 Virtual Private Network         |
| LSDB        | Link State Database                     |
| MCLAG       | Multi Chassis Link Aggregation          |
| MPLS        | Multiprotocol Label Switching           |
| OSPFv2      | Open Shortest Path First version 2      |
| PIM         | Protocol Independent Multicast          |
| RIB         | Routing Information Base                |
| SNMP        | Simple Network Management Protocol      |
| SPF         | Shortest Path First                     |
| SR          | Segment Routing                         |
| VE          | Virtual Ethernet                        |
| VRF         | Virtual Routing and Forwarding          |
| vtysh       | FRR provided Command Line Interface     |


# 1 Requirement Overview

## 1.1 Functional Requirements

  - Support One OSPF process (single instance) per SONiC system
  - Support Multi-VRF OSPF (on default and non-default VRFs)
  - Support OSPF over SONiC Ethernet, PortChannel, VLAN IPv4 interfaces.
  - Support OSPF in MCLAG Peers with unique IPv4 Addresses on LAG ports.
  - Support Type-1 to Type-5 OSPF LSAs 
  - Support Multi-Area OSPF 
  - Support OSPF Stub areas
  - Support OSPF router ABR and ASBR roles
  - Support OSPF packets simple password authentication
  - Support OSPF packets authentication using MD5 HMACs  
  - Support OSPF Passive interfaces
  - Support BFD on OSPF numberred interfaces  
  - Support Type-3 Summary LSA prefix substitution
  - Support Type-3 Summary LSA filtering (using prefix/import/export lists)
  - Support Route redistribution to/from OSPF
  - Support type-1 and type-2 metric for imported external protocol routes
  - Support OSPF ECMP routes


  FR Routing package documentation can be found at: 

	http://docs.frrouting.org/en/latest/
	http://docs.frrouting.org/en/stable-7.1/
        http://docs.frrouting.org/en/stable-7.2/


## 1.2 Configuration and Management Requirements

  - Support OSPFv2 configuration commands using vtysh
  - Support OSPFv2 show commands using vtysh
  - Support SNMP AgentX protocol, providing MIB read-only access
  - Support SNMP MIB as per RFC 1850

  All Configuration and Management commands are as per FRR OSPFv2 configuration and management support. 

## 1.3 Scalability Requirements

  - Support 50 OSPFv2 routers per OSPF area
  - Support Maximum of 128 L3 interfaces with OSPF enabled
  - Support Maximum of 5000 Intra area routes
  - Support Maximum of 5000 Inter area summary routes
  - Support Maximum of 40000 external (type-5) routes

  Scale varies depending on Platform and CPU of the system. Exact scale numbers will be updated upon scale tests. 

## 1.4 Warm Boot Requirements

  - Support OSPF route resynchronization over planned and unplanned restart of BGP container.
  - OSPF Graceful Restart is not supported yet by FRR.

  More details can be found at warm reboot section 6.

# 2 Functionality

## 2.1 Target Deployment Use Cases

OSPF is widely used as an Interior Gateway Protocol in Service Provider and Enterprise Networks. MPLS, Segment Routing, L3VPN services make use of OSPF.


## 2.2 Functional Description

Open Shortest Path First version 2 (OSPFv2) is a routing protocol which is described in RFC 2328. It is an Link State Interior Gateway Protocol.

OSPF describes link-state information in a message known as a LSA (Link State Advertisement), which is then propagated through to all other routers in a link-state routing domain, by a process called flooding. Each OSPF router thus builds up an LSDB (Link State Database) of all the link-state messages. From this collection of LSAs in the LSDB, each router can then calculate the shortest path to any other router, based on some common metric, by using an algorithm such as Edgar Djikstras SPF (Shortest Path First) algorithm.

# 3 Design
## 3.1 Overview

FR Routing(FRR) is an IP routing protocol suite for Linux/Unix platforms. Suite supports routing protocol daemons like BGP, OSPF, IS-IS, PIM.  These set of routing daemons work together to build the routing table. There is a middleman daemon called Zebra, which serves as the broker between the routing daemons and the kernel. 

SONiC uses FRR package for running routing protocols. FRR package is integrated into SONiC as a separate container called BGP container. At present BGP, PIM, StaticD and BFD protocol daemons are enabled in SONiC FRR container. SONiC added two more daemons namely fpmsyncd and ipmcfpmsyncd into BGP container. These daemons support route updates to SONiC Database tables which in turn push them into Switching ASICs as defined by SONiC software architecture. FRR integrated SONiC architecture is as given below.

	
	     +-----+      +----------+  +---------+      +---------+  -+
	     |FRR  |  +-->| ospfd    |  |   bgpd  |  ... |  . . .  |   |
	     |SNMP |  |   +----^-----+  +----^----+      +----^----+   |BGP
	     |Agent|  |        |             |                |        |con
	     +--^--+  |   +----v-------------v----------------v----+   |tai
	        |     +-->|                 Zebra                  |   |ner
	        |     |   +----------^-------------^-----------^---+   |
	        |     |              |             |           |       | 
	        |     |         +----v-----+  +----v----+  +---v---+   | 
	        |     |Kernel   |   FPM    |  |  IPMFPM |  |       |   |
	        |     +-------->|   Syncd  |  |  Syncd  |  |       |   |
	        |     |calls    +--------^-+  +----^----+  | vtysh |  -+
	        |     |                  |         |       |       |
	     +--v--+  |  +------------+  |         |       +---^---+   
	     |SONiC|  |  |            |  +---------+           |
	     |SNMP |  |  |    SwSS    |  |           +---------v---+
	     |Agent|  |  |            |  |   +------>|  SONiC CLI  |<-- User
	     +--^--+  |  +-^--------^-+  |   |       +-------------+
	        |     |    |        |    |   |
	     +--v-----v----v-+    +-v----v---v--+    +-------------+            
	     |               |    |             |<-->| OrchAgent   |
	     |               |    |  CONFIGDB   |    +-------------+    
	     |               |<-->|  APPDB      |        
	     |               |    |  STATEDB    |    +-------------+    
	     |  SONiC        |    |  ASICDB     |<-->| SyncD, SAI  |
	     |  Host         |    +-------------+    |     SDK     |
	     |  Linux        |                       +------^------+
	     |               +------------------+           |
	     |                                  |    +------v------+
	     |                                  |<-->| ASIC Driver |
	     +----------------------------------+    +-------------+
	
	     +-------------------------+  +------------------------+
	     |    Host system Hw       |  |     Switching ASIC     |  
	     +-------------------------+  +------------------------+
	             |.  |.                 |.  |.  |.  |.  |.  |.
	        Management Ports               Physical Ports
  

This feature enables one OSPF process/daemon per SONiC System within BGP container. Enabled OSPF daemon will be supporting OSPF on default as well as multiple user VRFs. OSPF daemon will be interacting with Zebra process to get information about the router L3 interfaces and provides routes to Zebra to add the routes into routing table. Zebra will interact with fpmsyncd to update the routes to SONiC database tables. 

OSPF will be supported only on Ethernet, VLAN and Portchannel (including MCLAG) IPv4 interfaces of SONiC system. FRR container in SONiC supports 16 ECMP routes.

FRR SNMP sub-agent support will also be enabled for OSPF. FRR SNMP sub agent will interact with SONiC SNMP master Agent to service external SNMP requests.

OSPFv2 will support those capabilities (sub features) of OSPF for which there is SONiC protocol support. For example, MPLS is not supported in SONiC, hence MPLS related sub features of OSPFv2 will not be supported.

OSPF Router Id selection will be as per below preference
 - OSPF router mode configured router id value
 - If user configured router id value is not present, then choose the most recently used router id value
 - Zebra daemon recommended value of Router Id. Zebra daemon will choose router id in below order 
    - FRR global mode configured router id value
    - Highest IPv4 address value among SONiC physical and loopback interface IPv4 addresses.

## 3.2 DB Changes

There will not be any SONiC Database changes for OSPFv2. 
   
### 3.2.1 CONFIG DB

Config DB will not be used to save any OSPF user configurations.
All OSPFv2 configurations will be saved/updated into '/etc/frr/frr.conf' file.

Using Config DB for saving OSPF configurations is a future effort. 

### 3.2.2 APP DB
Not applicable

### 3.2.3 STATE DB
Not applicable

### 3.2.4 ASIC DB
Not applicable

### 3.2.5 COUNTER DB
Not applicable

### 3.2.6 Configuration save and restart

All OSPFv2 configurations will be saved/updated into '/etc/frr/frr.conf' file. OSPFv2 configurations will not be saved in SONiC '/etc/sonic/config_db.json' file.

In SONiC split config mode, frr.conf saved OSPF configurations will be replayed along with other FRR related configurations (like BGP, Routemap config) over system restart or BGP container restart.

In Extended unified config mode, config_db.json supported FRR configurations will be merged with frr.conf saved configurations. This merged configuration will be replayed over system restart and BGP container restart.


## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
No new changes.

### 3.3.2 Other Process 
No new changes.

## 3.4 SyncD
All Route programming into Switching ASIC will be as per existing mechanism.

## 3.5 SAI

All Route programming into Switching ASIC will be as per existing mechanism.

OSPFv2 control packet trapping (snoop and forward) will be enabled.

## 3.6 CLI

OSPF can be configured only using vtysh CLI. All configuration and show commands are per FRR Configuration guide. 

There are two OSPF configuration modes namely OSPF router config mode and OSPF Interface config mode.
    
L3 interface and its IP address configuration shall be done from SONiC configuration. All Physical and Logical interfaces configured in SONiC platform will be populated as interfaces in BGP container Zebra module. Same interfaces have to be used by the user to configure OSPF related parameters under OSPF interface config mode. Manual interface configuration under vtysh CLI shall be avoided. Manual configuring of any interface in vtysh will be visible as pseudo interface in Vtysh mode and same will not be visible in SONiC system.


### 3.6.1 Data Models
Not Applicable

### 3.6.2 Configuration Commands

OSPF router configuration will be as below

	[no] router ospf [vrf NAME]


Below list of OSPF router mode config commands shall the considered for this release.

	[no] ospf router-id [A.B.C.D]
	
	[no] area <A.B.C.D|(0-4294967295)> authentication message-digest
	[no] area <A.B.C.D|(0-4294967295)> authentication
	[no] area <A.B.C.D|(0-4294967295)> default-cost (0-16777215)
	[no] area <A.B.C.D|(0-4294967295)> export-list NAME
	[no] area <A.B.C.D|(0-4294967295)> filter-list prefix WORD <in|out>
	[no] area <A.B.C.D|(0-4294967295)> import-list NAME
	[no] area <A.B.C.D|(0-4294967295)> range A.B.C.D/M [advertise [cost (0-16777215)]]
	[no] area <A.B.C.D|(0-4294967295)> range A.B.C.D/M cost (0-16777215)
	[no] area <A.B.C.D|(0-4294967295)> range A.B.C.D/M not-advertise
	[no] area <A.B.C.D|(0-4294967295)> range A.B.C.D/M substitute A.B.C.D/M
	[no] area <A.B.C.D|(0-4294967295)> shortcut <default|enable|disable>
	[no] area <A.B.C.D|(0-4294967295)> stub no-summary
	[no] area <A.B.C.D|(0-4294967295)> stub
	
	[no] auto-cost reference-bandwidth (1-4294967)
	[no] default-information originate [{always|metric (0-16777214)|metric-type (1-2)|route-map WORD}]
	[no] default-metric (0-16777214)
	[no] distribute-list WORD out <kernel|connected|static|bgp>
	[no] distance (1-255)
	[no] distance ospf {intra-area (1-255)|inter-area (1-255)|external (1-255)}
	
	[no] log-adjacency-changes detail
	[no] log-adjacency-changes
	[no] max-metric router-lsa administrative
	[no] max-metric router-lsa on-shutdown (5-100)
	[no] max-metric router-lsa on-startup (5-86400)
	
	[no] neighbor A.B.C.D [priority (0-255) [poll-interval (1-65535)]]
	[no] neighbor A.B.C.D poll-interval (1-65535) [priority (0-255)]
	
	[no] network A.B.C.D/M area <A.B.C.D|(0-4294967295)>
	
	[no] ospf router-id [A.B.C.D]
	[no] passive-interface <IFNAME [A.B.C.D]|default>
	
	
	[no] redistribute <kernel|connected|static|bgp> [{metric (0-16777214)|metric-type (1-2)|route-map WORD}]
	[no] redistribute <ospf|table> (1-65535)[{metric (0-16777214)|metric-type (1-2)|route-map WORD}]
	[no] refresh timer [(10-1800)]
	[no] timers lsa min-arrival [(0-600000)]
	[no] timers throttle lsa all [(0-5000)]
	[no] timers throttle spf [(0-600000)(0-600000)(0-600000)]
	
	
Below list of OSPF interface config commands shall be considered for this release

	[no] ip ospf [(1-65535)] area <A.B.C.D|(0-4294967295)> [A.B.C.D]
	[no] ip ospf authentication <null|message-digest> [A.B.C.D]
	[no] ip ospf authentication [A.B.C.D]
	[no] ip ospf authentication-key AUTH_KEY [A.B.C.D]
	[no] ip ospf bfd
	[no] ip ospf cost (1-65535) [A.B.C.D]
	[no] ip ospf dead-interval (1-65535) [A.B.C.D]
	[no] ip ospf dead-interval minimal hello-multiplier (1-10) [A.B.C.D]
	[no] ip ospf hello-interval (1-65535) [A.B.C.D]
	[no] ip ospf message-digest-key (1-255) md5 KEY [A.B.C.D]
	[no] ip ospf network <broadcast|non-broadcast|point-to-multipoint|point-to-point>
	[no] ip ospf priority (0-255) [A.B.C.D]
	[no] ip ospf retransmit-interval (3-65535) [A.B.C.D]
	[no] ip ospf transmit-delay (1-65535) [A.B.C.D]
	

For more details of above list of config commands, please refer to FRR User Manual.


### 3.6.3 Show Commands

Below list of OSPF show commands shall the considered for this release

	show ip ospf
	show ip ospf interface [INTERFACE]
	show ip ospf neighbor
	show ip ospf neighbor INTERFACE
	show ip ospf neighbor detail
	show ip ospf neighbor INTERFACE detail
	show ip ospf database
	show ip ospf database (asbr-summary|external|network|router|summary)
	show ip ospf database (asbr-summary|external|network|router|summary) LINK-STATE-ID
	show ip ospf database (asbr-summary|external|network|router|summary)
	                LINK-STATE-ID adv-router show ip ospf database
	                (asbr-summary|external|network|router|summary) adv-router ADV-ROUTER
	show ip ospf database (asbr-summary|external|network|router|summary) LINK-STATE-ID self-originate
	show ip ospf database (asbr-summary|external|network|router|summary) self-originate
	show ip ospf database max-age
	show ip ospf database self-originate
	show ip ospf route

  For more details of above list of show commands, please refer to FRR User Manual.

### 3.6.4 Debug Commands

Below list of OSPF debug configurations will be available

	[no] debug ospf event   
	[no] debug ospf ism       
	[no] debug ospf lsa         
	[no] debug ospf nsm        
	[no] debug ospf packet      
	[no] debug ospf zebra      

Debug file path can be configured under configuration mode as below

	log file <file_path_and_name>
	
	Example : log file /var/log/frr/frr.log


### 3.6.5 REST API Support

# 4 Flow Diagrams
Not applicable

# 5 Serviceability and Debug

Syslog level can be configured as below

	[no] log syslog
	[no] log syslog alerts          
	[no] log syslog critical        
	[no] log syslog debugging       
	[no] log syslog emergencies     
	[no] log syslog errors          
	[no] log syslog informational   
	[no] log syslog notifications   
	[no] log syslog warnings   

	[no] log facility syslog

Syslog logging levels can be set by above commands. OSPF internal sub-component debug logging can be enabled by configuring component level debug flags as per section 3.6.4. 

Debug logs can be redirected to user configured log file or syslog. Syslog redirection can be done by configuring 'log syslog' and 'log facility syslog'. Syslog log file can be seen at /var/log/syslog. Debug log user file redirection can be done by configuring 'log file <file_path_and_name>'.

If user has not configured any file logging, and a daemon crashes due to a signal or an assertion failure, container will attempt to save the crash information in a file named /var/tmp/frr.\<daemon name\>.crashlog


# 6 Warm Boot Support

When warm restart is not enabled, upon BGP container restart, routes populated before the restart will be retained till Zebra overwrites or deletes those routes explicity. This is as per existing SONiC behavior. 

When warm restart is not enabled, upon system restart, all the routes will be newly populated.

When warm restart is enabled, upon BGP container restart or system warm reboot, FPMSyncd will reconcile all routes with Switching Fabric. All route reconcilation will be as per existing BGP docker FPMSyncd warm reboot support.

OSPF Graceful Restart is not supported in FRR. Over OSPF restart (i.e. BGP container restart), sessions to neighboring routers may go down. This will lead to redirection of traffic by OSPF neighbor routers. If the OSPF process comes up before the neighbor timeout then the neighbor OSPF will have a progressive state change in its neighbor state and routes will be as per newly exchanged information between the neighbors.


# 7 Scalability

 Support scalability as per section  1.3

 As best practice each area, including the backbone, should contain no more than 50 OSPFv2 routers.
 Route summarization is extremely desirable for a reliable and scalable OSPF network.

# 8 Unit Test
Unit test cases for this specification are as listed below:


1.  Verify OSPF single instance router configuration.
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
14.  Verify OSPF router neighbour priority configuration
15.  Verify OSPF router passive interface configuration
16.  Verify OSPF router redistribute configurations
17.  Verify OSPF router refresh timer configuration
18.  Verify OSPF router LSA timer configurations
19.  Verify OSPF router SPF timer configurations
20.  Verify OSPF router configuration on non default VRF
21.  Verify OSPF router adjacency change logging configuration
22.  Verify OSPF ASBR router default route originate configuration
23.  Verify OSPF debug (debug ospf) configurations
24.  Verify OSPF network configurations
25.  Verify OSPF configuration on MCLAG peer nodes
26.  Verify OSPF interface area configuration
27.  Verify OSPF interface BFD configuration
28.  verify OSPF interface cost configuration
29.  Verify OSPF interface authentication configuration
30.  Verify OSPF interface Dead interval and multiplier configuration
31.  Verify OSPF interface Hello interval configuration
32.  Verify OSPF interface Message Digest Key configuration
33.  Verify OSPF interface MTU ignore configuration
34.  Verify OSPF interface network type configuration
35.  Verify OSPF interface priority configuration
36.  Verify OSPF interface retransmit interval configuration
37.  Verify OSPF interface retransmit delay configuration
38.  Verify OSPF interface configurations on VLAN L3 interfaces
39.  Verify OSPF interface configurations on Portchannel interfaces
40.  Verify OSPF interface configurations on VRF based Ethernet interfaces
41.  Verify OSPF interface configurations on VRF based VLAN interfaces
42.  Verify OSPF interface configurations on VRF based port channel interfaces
43.  Verify OSPF interface configurations on MCLAG peed node dedicated L3 interfaces
44.  Verify single area Two or more node OSPF sessions
45.  Verify OSPF Sessions with Ethernet interfaces
46.  Verify OSPF Sessions with VLAN interfaces
47.  Verify OSPF Sessions with port channel interfaces
48.  Verify OSPF sessions with multiple links between two or more nodes
49.  Verify OSPF sessions on point-to-point and broadcast links
50.  Verify OSPF DR selection on broadcast link sessions
51.  Verify OSPF router id selection order of config, loopback, interface ip address
52.  Verify OSPF ip route and LSDb entries for single area configurations
53.  Verify OSPF ip route and LSDb entries for multi area configurations
54.  Verify OSPF ip route and LSDB entries for intra area destinations
55.  Verify OSPF ip route and LSDB entries for inter area destinations
56.  Verify OSPF ip route and LSDB entries for inter domain ASBR destinations
57.  Verify OSPF ip route and LSDb entries for Stub area
58.  Verify OSPF ip route and LSDB entries for totally stub area
59.  Verify OSPF session flap due to interface up down events
60.  Verify OSPF session flap due to hello/keepalive timeout
61.  Verify OSPF ip route next hop change based on topology interface cost changes
62.  Verify OSPF ip route next hop change based on topology interface up down events
63.  Verify OSPF sessions with BFD enabled on link
64.  Verify OSPF session flap due to BFD flap
65.  Verify OSPF max-metric functionality
66.  Verify OSPF distance setting and its effect on ip routing table
67.  Verify OSPF link cost calculation as per reference bandwidth
68.  Verify OSPF Type-3 LSA substitute summarized prefix with another
69.  Verify OSPF Type-3 LSA filter using export-list access-list
70.  Verify OSPF Type-3 LSA filter using import-list access-list
71.  Verify OSPF Type-3 LSA filter out using out filter-list
72.  Verify OSPF Type-3 LSA filter in using in filter-list
73.  Verify Route redistribution into OSPF from kernel routes
74.  Verify Route redistribution into OSPF from connected links
75.  Verify Route redistribution into OSPF from static routes
76.  Verify Route redistribution into OSPF from bgp routes
77.  Verify Route redistribution into OSPF using route map metric set
78.  Verify Route redistribution into OSPF with with metric type 1 and 2
79.  Verify Route redistribution into OSPF with with metric
80.  Verify access list filtering for route distribution into OSPF
81.  Verify Default route origination with metric and metric type

# 9 Internal Design Information

