# EVPN Logical VXLAN Tunnel End Point
# High Level Design Document
Rev 1.0

# Table of Contents

- [EVPN Logical VXLAN Tunnel End Point](#EVPN-Logical-VXLAN-Tunnel-End-Point)
- [High Level Design Document](#High-Level-Design-Document)
- [List of Tables](#List-of-Tables)
- [Revision](#Revision)
- [About this Manual](#About-this-Manual)
- [Scope](#Scope)
- [Definition/Abbreviation](#Definition/Abbreviation)
    - [Table 1: Abbreviations](#Table-1:-Abbreviations)
- [1 Feature Overview](#1-Feature-Overview)
    - [1.1 Requirements](#1_1-Requirements)
        - [1.1.1 Functional Requirements](#1_1_1-Functional-Requirements)
        - [1.1.2 Configuration and Management Requirements](#1_1_2-Configuration-and-Management-Requirements)
        - [1.1.3 Scalability Requirements](#1_1_3-Scalability-Requirements)
        - [1.1.4 Warm Boot Requirements](#1_1_4-Warm-Boot-Requirements)
        - [1.1.5 Notes:](#1_1_5-Notes:)
    - [1.2 Design Overview](#1_2-Design-Overview)
        - [1.2.1 Basic Approach](#1_2_1-Basic-Approach)
        - [1.2.2 Container](#1_2_2-Container)
        - [1.2.3 SAI Overview](#1_2_3-SAI-Overview)
- [2 Functionality](#2-Functionality)
    - [2.1 Target Deployment Use Cases](#2_1-Target-Deployment-Use-Cases)
    - [2.2 Functional Description](#2_2-Functional-Description)
        - [2.2.1 Traffic Forwarding Behavior](#2_2_1-Traffic-Forwarding-Behavior)
        - [2.2.2 MAC Management](#2_2_2-MAC-Management)
        - [2.2.3 Uplink Interface Tracking](#2_2_3-Uplink-Interface-Tracking)
- [3 Design](#3-Design)
    - [3.1 Overview](#3_1-Overview)
    - [3.2 DB Changes](#3_2-DB-Changes)
        - [3.2.1 CONFIG DB](#3_2_1-CONFIG-DB)
            - [3.2.1.1 Interface Tracking Table](#3_2_1_1-Interface-Tracking-Table)
        - [3.2.2 APP DB](#3_2_2-APP-DB)
        - [3.2.3 STATE DB](#3_2_3-STATE-DB)
        - [3.2.4 ASIC DB](#3_2_4-ASIC-DB)
        - [3.2.5 COUNTER DB](#3_2_5-COUNTER-DB)
    - [3.3 Switch State Service Design](#3_3-Switch-State-Service-Design)
        - [3.3.1 VxlanOrch Changes](#3_3_1-VxlanOrch-Changes)
        - [3.3.2 FdbOrch Changes](#3_3_2-FdbOrch-Changes)
        - [3.3.3 IntfTrackOrch](#3_3_3-IntfTrackOrch)
            - [3.3.3.1 IntfTrackOrch class](#3_3_3_1-IntfTrackOrch-class)
            - [3.3.3.2 IntfTrackGroup class](#3_3_3_2-IntfTrackGroup-class)
        - [3.3.4 PortsOrch changes](#3_3_4-PortsOrch-changes)
        - [3.3.5 FdbSyncD](#3_3_5-FdbSyncD)
    - [3.4 SAI](#3_4-SAI)
    - [3.5 CLI](#3_5-CLI)
        - [3.5.1 IS-CLI Compliance](#3_5_1-IS-CLI-Compliance)
        - [3.5.2 Data Models](#3_5_2-Data-Models)
        - [3.5.3 Configuration Commands](#3_5_3-Configuration-Commands)
        - [3.5.4 Show Commands](#3_5_4-Show-Commands)
            - [3.5.4.1 show interface enhancements](#3_5_4_1-show-interface-enhancements)
            - [3.5.4.2 show interface tracking ](#3_5_4_2-show-interface-tracking-)
        - [3.5.5 Debug Commands](#3_5_5-Debug-Commands)
        - [3.5.6 REST API Support](#3_5_6-REST-API-Support)
- [4 Flow Diagrams](#4-Flow-Diagrams)
    - [4.1.1 LVTEP Tunnel Add](#4_1_1-LVTEP-Tunnel-Add)
        - [4.1.2 LVTEP Tunnel Delete](#4_1_2-LVTEP-Tunnel-Delete)
        - [4.1.3 MAC Addition from EVPN and ICCP](#4_1_3-MAC-Addition-from-EVPN-and-ICCP)
        - [4.1.4 MAC Deletion from BGP EVPN and ICCP](#4_1_4-MAC-Deletion-from-BGP-EVPN-and-ICCP)
    - [4.2 Interface tracking group create](#4_2-Interface-tracking-group-create)
    - [4.3 Handling upstream interface operational status](#4_3-Handling-upstream-interface-operational-status)
- [5 Error Handling](#5-Error-Handling)
- [6 Serviceability and Debug](#6-Serviceability-and-Debug)
- [7 Warm Boot Support](#7-Warm-Boot-Support)
- [8 Scalability](#8-Scalability)
- [9 Unit Test](#9-Unit-Test)
    - [9.1 Functional Test Cases](#9_1-Functional-Test-Cases)
    - [9.2 Negative Test Cases](#9_2-Negative-Test-Cases)
    - [9.3 Warm boot Test Cases](#9_3-Warm-boot-Test-Cases)
- [10 Configuration Example](#10-Configuration-Example)


# List of Tables

[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev  | Date       | Author              | Change Description                      |
| ---- | ---------- | ------------------- | --------------------------------------- |
| 0.1  | 08/13/2019 | Lisa Nguyen         | Initial version                         |
| 0.2  | 08/20/2019 | Abhishek Dharwadkar | Add Interface tracking details          |
| 0.3  | 08/25/2019 | Praveen Elagala     | Add Fdb handling details                |
| 0.4  | 8/26/2019  | Hasan Naqvi         | Add Fdbsyncd details                    |
| 1.0  | 8/30/2019  | Lisa Nguyen         | Add MAC Management diagram              |
| 1.1  | 12/18/2019 | Lisa Nguyen         | Update Serviceability and Debug section |

# About this Manual
This document provides general information about EVPN Logical VTEP feature in SONiC.

# Scope
This document describes the high level design of EVPN Logical VTEP feature which is built upon MC-LAG and EVPN VXLAN features. This document references the existing MC-LAG HLD and EVPN VXLAN HLD where applicable. Hence, it is recommended that the users read those documents before this document. 

# Definition/Abbreviation
## Table 1: Abbreviations
| Term  | Meaning                                 |
| ----- | --------------------------------------- |
| BUM   | Broadcast Unknown Unicast and Multicast |
| EVI   | Ethernet VPN Instance                   |
| ICCP  | Inter-Chassis Communication Protocol    |
| IMET  | Include Multicast Ethernet Tag          |
| IRB   | Integrated Routing and Bridging         |
| LVTEP | Logical VXLAN Tunnel End Point          |
| MCLAG | Multi-Chassis Link Aggregation Group    |
| MHD   | Multi-Homed Device                      |
| NVO   | Network Virtualization Overlay          |
| SHD   | Single-Homed Device                     |
| VTEP  | VXLAN Tunnel End Point                  |
| VXLAN | Virtual Extended LAN                    |

# 1 Feature Overview

Logical VTEP (LVTEP) is used to refer to a set of multi-homing devices acting as a single logical device for VXLAN Tunnel End Point. With EVPN LVTEP, only auto-created VXLAN tunnel based on EVPN routes as specified in EVPN VXLAN HLD will be supported. The manually created tunnels and the L2 and L3 services over them as described in L2 VXLAN and VXLAN (VNET) HLDs are not supported. Dual-homing is supported using ICCP-based MCLAG as described in MC-LAG HLD. 

The term LVTEP will be used to refer to EVPN LVTEP and single VTEP will be used to refer to EVPN VXLAN throughout the document.

The diagram below provides a simple example of how LVTEP can be deployed in a leaf and spine IP fabric:

![EVPN LVTEP Overview](images/LVTEPOverview.png "EVPN LVTEP Overview")

In the above diagram:

- Leaf1 and Leaf2 forms a MCLAG pair as described in MCLAG HLD. 
- Leaf1 and Leaf2 acts as one logical leaf to remote leaf nodes (Leaf3 and Leaf4) with a common and reachable source VTEP IP address of 10.10.10.10
- Leaf 1 and Leaf2 each forms a BGP-EVPN session with remote leaf nodes (Leaf3 and Leaf4) using loopback address 1.1.1.1 and 2.2.2.2 respectively.
- Each remote leaf (Leaf3 and Leaf4) forms a single VXLAN tunnel to destination 10.10.10.10

Compare to physical VTEP where the two MCLAG nodes acts as two separate VTEPs to remote leaf nodes, LVTEP provides the following benefits:
- Provide better VXLAN tunnel scalability since remote leaf node has less number of VXLAN tunnel to manage. In addition, it also has less number of tunnels' ECMP nexthops to manage. 
- Provide better utilization of bandwidth since remote leaf node only floods one copy instead of N copies of BUM traffic where N is number of multi-homing leaves.
- Minimize BGP EVPN control plane changes since MCLAG leaf nodes do not need to advertise Ethernet Auto-Discovery (AD) per EVI routes and the remote leaf nodes did not need to process them. This in turn helps simplify network upgrade procedure since MCLAG leaf nodes and remote leaf node do not need to be upgraded at the same time.
- Provide better BGP EVPN control plane scalability in L2 VPN deployment since a large number of AD per EVI routes is not required to be exchanged in order to load balancing traffic to MHDs across MCLAG leaf nodes. 


## 1.1 Requirements

### 1.1.1 Functional Requirements

Since LVTEP is built upon EVPN VXLAN, it supports all the functionalities supported by EVPN VXLAN as described in EVPN VXLAN HLD. In addition, it has the following additional requirements that are applicable to both L2 and L3 traffic:
1. Support two MCLAG leaf nodes act as one logical VTEP.
2. Support MCLAG leaf nodes directly connect using a peer-link in a L2 deployment environment.
3. Support local forwarding of traffic received from SHDs and MHDs to remote leaf nodes without using the peer link. For L3 deployment, this is typically known as Static Anycast Gateway feature.
4. Support forwarding of traffic received from SHDs and MHDs to remote leaf nodes via peer-link if VXLAN tunnel to remote leaf node is down on MCLAG leaf node.
5. Support local termination of traffic received from remote leaf nodes and forward the traffic locally to SHD or MHD. 
6. Support local termination of traffic received from remote leaf nodes and forward the traffic via peer-link if and only if MCLAG interface to MHD is down. 
7. Support flooding of BUM traffic received from remote leaf nodes to MHD from a single MCLAG leaf node only.
8. Support uplink tracking feature where configured uplink interfaces will be monitored for their operational status. When all monitored interfaces are down, all local MCLAG interfaces will be brought down accordingly.
9. Support advertisement of locally learned MAC addresses on MCLAG leaf node to remote leaf nodes via BGP similar to single VTEP case.
10. Support learning of remote MAC addresses from remote leaf nodes via BGP similar to single VTEP case.
11. Support programming of remote MAC addresses learned via BGP but do not synchronize them to remote MCLAG peer via ICCP.
12. Support MAC move from SHD/MHD on MCLAG leaf node to SHD/MHD on a remote leaf node and vice versa.
13. Support ingress replication when sending BUM traffic across VXLAN tunnels and to MCLAG peer node

### 1.1.2 Configuration and Management Requirements
This feature supports CLI and other management interfaces supported in SONiC. It leverages existing CLIs specified in VXLAN and EVPN VXLAN HLDs. In addition, it has the following requirements on MCLAG leaf node:
1. Support configuration of the same IP address as tunnel source IP address and reference the tunnel name in the EVPN NVO configuration using existing CLIs and management interfaces defined in VXLAN and EVPN VXLAN HLDs.
2. Support configuration of uplink interfaces to be tracked for their operational status
3. Support enhancement to show interface status to indicate the interface is shutdown due to uplink  tracking feature. 

### 1.1.3 Scalability Requirements
LVTEP leaf scalability requirements are the same as single VTEP and MCLAG requirements. For TD3 platform, 
1. Max number of VTEP termination: 1

2. Max number of VTEP origination: 512

3. Max number of VLANs extended across MCLAG peers and to remote leaf nodes: 4K

4. Max number of L2 VNI: 4K

5. Max number of VRF/L3 VNI: TBD (to be added by L3/Protocol teams)

6. Max number of MACs: 40K

7. Max number of IPv4 and IPv4 routes: TBD (To be added by L3/Protocol teams)

8. Max number of MCLAG interfaces depends on max number of front-panel ports and max port-channels a platform can support 


### 1.1.4 Warm Boot Requirements
Warm reboot is intended to be supported for the following cases:
1. Planned system warm reboot
2. Planned SwSS docker restart
3. Planned BGP docker restart
4. Planned ICCP docker restart
5. Planned Teamd docker restart  

### 1.1.5 Notes:

1. It is required that all of the VLAN, Subnet, and VRF configurations are symmetric between the MCLAG peers. There is no validation check to detect configuration inconsistencies.

2. ICCP does not provide synchronization of L3 prefix routes between MLAG peers.

3. In the cases of asymmetric ip address configurations, orphan L3 ports, or dynamic routing confiugred in VRF on a given MCLAG pair, BGP VRF session is required between the MCLAG peers to synchronize VRF's L3 prefix routes.

   

## 1.2 Design Overview
### 1.2.1 Basic Approach
The code changes for LVTEP are newly developed.

### 1.2.2 Container
No new container will be added. The changes will be added to existing containers such as SwSS and Syncd. The details of the changes will be described in the Design section below.

### 1.2.3 SAI Overview
There is no new SAI API or SAI attribute required for this feature. The existing port isolation SAI API will be used to achieve BUM flooding behavior described in subsequent section.

# 2 Functionality

## 2.1 Target Deployment Use Cases
LVTEP can be deployed in the following use cases:
- Leaf nodes in IP Fabric Leaf and Spine Topology
- Data Center Interconnect (DCI) nodes where VXLAN is used to tunnel the traffic to remote data center and incoming traffic to DCI nodes can be L2 and L3.

## 2.2 Functional Description
### 2.2.1 Traffic Forwarding Behavior

Encapsulation and decapsulation for L2 and L3 traffic in symmetric and asymmetric IRB forwarding over VXLAN tunnel is the same as described in EVPN VXLAN HLD. Hence, it is not repeated in this document. 

The following diagram describes the traffic forwarding behavior for known unicast traffic:

![EVPN LVTEP L2/L3 Traffic Forwarding](images/LVTEPUnicastTraffic.png "EVPN LVTEP L2/L3 Traffic Forwarding")

In the above diagram:

- Known unicast traffic from MHD1 to SHD3 can be LAG load-balanced to either MCLAG Leaf1 or Leaf2 as shown in Flow1 and Flow2 respectively. Leaf1 or Leaf2 will directly forward the traffic over VXLAN tunnel to Leaf3.
- Known unicast traffic from SHD4 to MHD1 can be ECMP load-balanced to either Leaf1 or Leaf2 who will directly forward the traffic to MHD1.



In the following digram, the table on the left describes the Broadcast, Unknown Unicast and Multicast traffic flooding behavior for Leaf1 node in the diagram on the right.

![EVPN LVTEP BUM Traffic Flooding](images/LVTEPBUMTraffic.png "EVPN LVTEP BUM Traffic Flooding")

In the above diagram, the table describes the BUM traffic flooding behavior on Leaf1 node in the topology on the right. Note that:

- BUM traffic received from local SHD, MHD, or VXLAN tunnel is always locally flooded to MHD unless its MCLAG interface is down
- BUM traffic received on peer-link is never flooded back to VXLAN tunnel. It is flooded to MHD only if the corresponding MCLAG interface is down on remote MCLAG peer node.

### 2.2.2 MAC Management

Loally learned MAC addresses on each LVTEP node are exchanged with remote leaf nodes using BGP EVPN control plane similar to single VTEP case as described in EVPN VXLAN HLD. In addition, they are also exchanged with MCLAG peer node using ICCP control plane as described in MC-LAG HLD. As part of LVTEP support, MAC move between MHD on LVTEP node and SHD/MHD on remote leaf node is also supported.

![EVPN LVTEP MAC Management](images/LVTEPFDBManagement.png "EVPN LVTEP MAC Management")

### 2.2.3 Uplink Interface Tracking

In the following diagram,  if all the upstream links are down on Leaf1 node, all the traffic must be sent over to MCLAG peer node to reach Spine1 node. This would require the ICL link bandwidth to be large enough to handle the additional traffic as show in following figure.

![Traffic flow without Interface Tracking](images/LVTEPIntfTrack1.png "Traffic flow without Interface Tracking")

Interface tracking introduces a mechanism with which the traffic from MHD1 and MHD2 can be sent to Leaf2 which has reachability to the spine by shutting down the MCLAG interfaces as shown in the following figure.

![Traffic flow with Interface Tracking](images/LVTEPIntfTrack2.png "Traffic flow with Interface Tracking")

# 3 Design

## 3.1 Overview
Since LVTEP is built upon EVPN VXLAN and MCLAG, the design from these two features are also extended with the following changes:
- Port-isolation group used in MCLAG to achieve source port suppression to MHD is extended to include VXLAN tunnel bridge port. The extension is required to achieve BUM traffic flooding behavior described in the EVPN LVTEP BUM Traffic Flooding Behavior diagram above.
- FdbOrch is extended to handle MAC move between LVTEP MHD and SHD/MHD attached to remote leaf node.
- Uplink interface tracking feature is introduced to direct traffic from MHDs to MCLAG peer node when all uplink interfaces from the MCLAG node have gone down.

## 3.2 DB Changes

### 3.2.1 CONFIG DB
#### 3.2.1.1 Interface Tracking Table

The following table is used to configure the interface tracking feature

```
key              = INTF_TRACKING:name            ; name must be unique
;field           = value
UPSTREAM         = [0-max_intfs]*intf_name
DOWNSTREAM       = "all-mclag"
TIMEOUT          = 1*3DIGIT    ; Timeout in seconds to bring up the downstream interfaces
                               ; when atleast 1 of the upstream interface is up.
DESCRIPTION      = 1*255VCHAR  ; Description for group

;value annotations
max_intfs = 1*5DIGIT ; Maximum number of interfaces supported by the switch
intf_name = "Ethernet"1-2DIGIT / "PortChannel"1*3DIGIT / "Vlan"1*4DIGIT
```

### 3.2.2 APP DB
A new field will be added to PORT_TABLE, VLAN_TABLE and LAG_TABLE called ADMIN_DOWN_REASON to indicate any reason if the interface is forcefully admin down even if its configured as Admin UP in the config DB.

```
key              = PORT_TABLE:name
key              = VLAN_TABLE:name
key              = LAG_TABLE:name

;field           = value
ADMIN_DOWN_REASON = 1*255VCHAR
```

### 3.2.3 STATE DB

There is no change to STATE DB

### 3.2.4 ASIC DB

There is no change to ASIC DB

### 3.2.5 COUNTER DB

There is no change to COUNTER DB

## 3.3 Switch State Service Design

### 3.3.1 VxlanOrch Changes
VxlanOrch processing for dynamic tunnel creation and deletion logic is updated to add and remove the VXLAN tunnel bridge port to and from the peer-link port isolation group respectively if the isolation group exists. If peer-link port isolation group is not created by MCLAG yet, the first tunnel creation should trigger port isolation group creation. Similarly, if port isolation group is no longer used by MCLAG when the last tunnel is deleted, the port isolation group should be deleted.

### 3.3.2 FdbOrch Changes
To support the MAC move scenarios between the remote leaf nodes and MCLAG leaf nodes, FdbOrch will be extended to keep track of the learn source for the advertised MAC addresses. Below are the rules that FdbOrch will follow for the MAC address programming when the same MAC address is learned from multiple sources. Precedence is considered in the order below starting with highest precedence. 

1. In presence of local static MAC address along with other advertised MAC addresses , FdbOrch gives highest precedence  to the local static MAC which will be programmed in the hardware. Mac move is not allowed when the local static MAC is the chose MAC. 
2. In presence of advertised static MAC address learned from remote VTEP node via BGP EVPN or learned from MCLAG peer node via ICCP, FdbOrch will program the remote static MAC. MAC move is not allowed when the advertised MAC is chosen MAC for programming.
3. In presence of dynamic MAC address locally learned on connected SHD or MHD  or learned as advertised MAC address from remote leaf nodes via BGP EVPN or ICCP,  the MAC addresses that is processed last on the FdbOrch will be programmed in hardware. 

When MAC address delete notification is processed, FdbOrch will mark the delete only for the corresponding learn source , if the MAC is learned from other sources then FdbOrch will reprogram the MAC address with the port information of new learn source chosen. For example, when a MAC address is initially learned from McLAG peer node, and later learned from from remote leaf node via BGP EVPN. EVPN MAC would have been chosen by FdbOrch for programming. When remote leaf node withdraws the MAC address then FdbOrch to re-program the MAC as McLAG advertised MAC.   

### 3.3.3 IntfTrackOrch

A new Orch agent called IntfTrackOrch (Interface Tracking Orch Agent) will be introduced to support interface tracking feature. IntfTrackOrch will utilize the  operation status information from PortOrch to bring down the downstream ports. IntfTrackOrch will subscribe to `MC_LAG` table and build a list of MCLAG interfaces using value of `mclag_interface` column.

#### 3.3.3.1 IntfTrackOrch class

```c++
class IntfTrackOrch: public Orch
{
    public:
        void doTask();
        
    ...
    
    private:
        map<string, IntfTrackGroup> m_trackGrps;
};
```

#### 3.3.3.2 IntfTrackGroup class

```c++
class IntfTrackGroup
{
    public:
    
    private:
        string m_name;
        map<string, bool> m_upstream; // Upstream interfaces
        vector<string> m_downsteam; // Downstream interfaces
        bool m_all_mclag; // All MCLAG intf. m_downstream will be populated internally
        uint32_t m_timeout; // Timeout to bringup the downstream links. Default=60s
};
```

### 3.3.4 PortsOrch changes
No change to PortsOrch. Shutdown of the downstream interfaces will be achieved by setting admin_status in Config DB to down.

### 3.3.5 FdbSyncD

FdbsyncD will take care of synchronizing the Local and ICCP remote MAC addresses to Linux L2 FDB table. A new ICCP_FDB_TABLE table is introduced to synchronize L3 MAC addresses (i.e. bound to ARP/ND) received from ICCPd to the Linux L2 Fdb table.

![MAC Handling in FdbsyncD](images/FdbsyncdLvtepMacHandling.png "MAC Handling in FdbsyncD")

In the above diagram, Fdbsyncd listens to Kernel L2 Fdb events and programs VxLAN remote MAC addresses into VXLAN_FDB_TABLE. When a MAC is learned in the forwarding plane, Fdbsyncd will receive notification from STATE_FDB_TABLE and will program Local MAC address into Linux L2 Fdb. Zebra in BGP docker listens to Kernel L2 FDB events and will redistribute the local MAC into EVPN control plane.

Typical MAC move scenarios handled by Fdbsyncd are listed below:

1. VxLAN to local/ICCP

   When remote EVPN MAC is installed in Kernel L2 Fdb table by Zebra and Fdbsyncd receives MAC add notification from STATE_FDB_TABLE or ICCP_FDB_TABLE, it will remove the previously installed remote EVPN MAC address from VXLAN_FDB_TABLE and will install Local/ICCP MAC into Kernel. FRR will treat this event as MAC move (to Local) and will re-advertise EVPN MAC route after incrementing the sequence number in Type-2 BGP update messages.

2. Local to ICCP

   When Local MAC address is already installed by Fdbsyncd based on STATE_FDB_TABLE, and MAC add notification is received from ICCP_FDB_TABLE, Fdbsyncd will replace the entry in Kernel L2 Fdb table with ICCP MAC. Similarly, when MAC delete notification is received from STATE_FDB_TABLE, Fdbsyncd will install ICCP MAC into Kernel FDB if it had previously received same MAC from ICCP_FDB_TABLE.

3. ICCP to Local

   When MAC delete notification is received from ICCP_FDB_TABLE, and MAC add notification had been previously received from STATE_FDB_TABLE, Fdbsyncd will install Local MAC into Kernel.

4. Local/ICCP to VxLAN

   In case Local or ICCP MAC address is installed in Linux L2 Fdb table, and VxLAN MAC add notification is received from Kernel, Fdbsyncd will install VxLAN MAC into VXLAN_FDB_TABLE. If same MAC had been previously received from ICCP_FDB_TABLE or STATE_FDB_TABLE, those entries will not be modified. Fdborch will take care of moving the Local/ICCP MAC to remote and removal of Local MAC from STATE_FDB_TABLE.

In all of the above cases, Fdbsyncd updates the Kernel L2 Fdb table based on the event as it is received. It does not perform any best MAC selection to chose which MAC address to prefer for installation when same MAC add is received from multiple sources. The last event would reflect the MAC state.

## 3.4 SAI

Broadcom SAI implementation for port isolation group will be extended to support VXLAN bridge port as one of the members of the isolation group. On TD3, the split horizon table will be used to achieve the BUM flooding behavior described in previous section. The normal attachement circuit (AC), peer link AC, and VXLAN tunnel logical interfaces will be assigned unique network group IDs. The incoming and outgoing network group IDs are used to decide whether a packet can be forwarded to dropped. 

## 3.5 CLI
### 3.5.1 IS-CLI Compliance
### 3.5.2 Data Models
### 3.5.3 Configuration Commands

The following commands will be used to configure tracking group. The group will be created to track L3 interface.

```
sonic(config)# link state track NAME
sonic(config-link-track)# description STRING
sonic(config-link-track)# timeout <1-999>
sonic(config-link-track)# downstream all-mclag

sonic(config)# interface (Ethernet|PortChannel|Vlan) PORTNUM
sonic(config-if) link state group NAME upstream
```

### 3.5.4 Show Commands

#### 3.5.4.1 show interface enhancements

show interface command will be enhanced to show the reason an interface is disabled including the interface group(s) name(s).

```
sonic# show interface status
------------------------------------------------------------------------------------------
Name                Description         Admin          Oper           Speed          MTU  
------------------------------------------------------------------------------------------
Ethernet0           -                   up             down           100GB          9100 
Ethernet4           -                   up             down           100GB          9100 
Ethernet8           -                   up             down           100GB          9100 
Ethernet12          -                   up             down           100GB          9100 
Ethernet16          -                   up             down           100GB          9100 
Ethernet20          -                   up             down           100GB          9100 
Ethernet24          -                   up             down           100GB          9100 
Ethernet28          -                   up             down           100GB          9100 
Ethernet32          -                   up             down           100GB          9100 
Ethernet36          -                   up             down           100GB          9100 
Ethernet40          -                   up             down           100GB          9100 
Ethernet44          -                   up             down           100GB          9100 
Ethernet48          -                   up             down           100GB          9100 
Ethernet52          -                   up             down           100GB          9100 
Ethernet56          -                   up             down           100GB          9100 
Ethernet60          -                   up             down           100GB          9100 
Ethernet64          -                   up             down           100GB          9100 
Ethernet68          -                   up             down           100GB          9100 
Ethernet72          -                   up             down           100GB          9100 
Ethernet76          -                   up             down           100GB          9100 
Ethernet80          -                   up             down           100GB          9100 
Ethernet84          -                   up             down           100GB          9100 
Ethernet88          -                   up             down           100GB          9100 
Ethernet92          -                   up             down           100GB          9100 
Ethernet96          -                   up             down           100GB          9100 
Ethernet100         -                   up             down           100GB          9100 
Ethernet104         -                   up             down           100GB          9100 
Ethernet108         -                   up             down           100GB          9100 
Ethernet112         -                   up             down           100GB          9100 
Ethernet116         -                   up             down           100GB          9100 
Ethernet120         -                   up             down           100GB          9100 
Ethernet124         -                   up             down           100GB          9100 
PortChannel1        -                   up             down*          400GB          9100

* Operationally disabled due to: Interface tracking

```

#### 3.5.4.2 show interface tracking 

The following command shows the configured interface tracking groups

```
sonic# show link state group
Name: MclagLinkTracking
Description: Interface tracking for all MCLAGs
Timeout: 120 seconds

sonic# show link state group MclagLinkTracking
Name: MclagLinkTracking
Description: Interface tracking for all MCLAGs
Timeout: 120 seconds
Upstream Interfaces:
    Ethernet0 (Up)
    Ethernet4 (Up)
    Vlan100   (Up)
Downstream Interfaces:
    PortChannel1 (Up)
    PortChannel2 (Up)

sonic# show link state group MclagLinkTracking
Name: MclagLinkTracking
Description: Interface tracking for all MCLAGs
Timeout: 60 seconds
Upstream:
    Ethernet0 (Down)
    Ethernet4 (Down)
    Vlan100   (Down)
Downstream:
    PortChannel1 (Disabled)
    PortChannel2 (Disabled)
```

### 3.5.5 Debug Commands

The existing debug commands from MCLAG and EVPN VXLAN will be used to debug LVTEP also.

### 3.5.6 REST API Support



# 4 Flow Diagrams

### 4.1.1 LVTEP Tunnel Add
The following diagram shows how VXLAN tunnel add handling is extended for LVTEP tunnel

![EVPN LVTEP Tunnel Add](images/LVTEPTunnelAdd.png "EVPN LVTEP Tunnel Add")

### 4.1.2 LVTEP Tunnel Delete
The following diagram show how VXLAN tunnel delete handling is extended for LVTEP tunnel

![EVPN LVTEP Tunnel Delete](images/LVTEPTunnelDel.png "EVPN LVTEP Tunnel Delete")


### 4.1.3 MAC Addition from EVPN and ICCP

![FdbOrch Handling Mac ADD  from Multiple Sources](images/FdbOrchHandlingMacADDFromMultipleSources.png "FdbOrch Handling Mac ADD from Multiple Sources")

### 4.1.4 MAC Deletion from BGP EVPN and ICCP

![FdbOrch Handling Mac Del from Multiple Sources](images/FdbOrchHandlingMacDelFromMultipleSources.png "FdbOrch Handling Mac Del from Multiple Sources")



## 4.2 Interface tracking group create

The following diagram shows how interface tracking group create is handled.

![Handling interface tracking group create](images/IntfTrackCreate.png "Handling interface tracking group create")



## 4.3 Handling upstream interface operational status

The following diagram shows how operational status events from Ports Orch is handled by Intf Tracking Orch agent.

![Handling interface operational status updates](images/IntfTrackOperStatus.png "Handling interface operational status updates")


# 5 Error Handling

Error returned by SAI for the added SAI calls will be added to syslog with ERROR log level. 

# 6 Serviceability and Debug
The existing debug commands from MCLAG HDL and EVPN VXLAN HLD will be used to debug LVTEP. Below are the links to the MCLAG and EVPN VXLAN HLDs respectively:
http://gerrit-lvn-07.lvn.broadcom.net:8083/plugins/gitiles/sonic/documents/+/refs/changes/75/12075/12/MCLAG/MCLAG_HLD.md#5-Serviceability-and-Debug

http://gerrit-lvn-07.lvn.broadcom.net:8083/plugins/gitiles/sonic/documents/+/refs/changes/05/9005/10/L3/EVPN/EVPN%20VXLAN%20HLD.md#6-serviceability-and-debug

In addition, two new debug shell commands can be used to display MCLAG ISL bridge port attribute programming status from OrchAgent and SAI for LVTEP:

* show system internal orchagent lvtep global
* show system internal sai lvtep global

# 7 Warm Boot Support
LVTEP warmboot depends on the successful warm boot support in EVPN VXLAN and MCLAG. During the following warm boot scenarios, there should be no traffic loss for traffic between SHD/MHD attached to MCLAG leaf nodes and SHD/MHD attached to remote leaf node

1. Planned system warm reboot
2. Planned SwSS docker restart
3. Planned BGP docker restart
4. Planned ICCP docker restart

# 8 Scalability

The feature will be tested with the following scaling numbers:

- 4 VXLAN tunnels
- 2K VLANs 
- 2K VNIs
- 20K MACs

# 9 Unit Test

## 9.1 Functional Test Cases

1. Verify BUM traffic received from VxLAN tunnel T1 is not flooded back to another VxLAN tunnel T2 
2. Verify BUM traffic received from VxLAN tunnel T1 is flooded over peer link 
3. Verify BUM traffic received from VxLAN tunnel T1 is flooded to SHD and MHD connected to MLAG leaf1 
4. Verify BUM traffic received from peer-link is not flooded back to VxLAN tunnel T1 
5. Verify BUM traffic received from peer-link is flooded to SHD 
6. Verify BUM traffic received from peer-link is not flooded to MHD when MCLAG interface is up on MLAG peer 
7. Verify BUM traffic received from peer-link is flooded to MHD when MCLAG interface is down on MLAG peer 
8. Verify BUM traffic received from peer-link is not flooded back to peer-link 
9. Verify BUM trafifc from SHD is flooded to SHD, MHD, all VxLAN tunnels and peer-link 
10. Verify BUM traffic received from MHD is flooded to SHD, other MHD, all VxLAN tunnels and peer-link
11. Configure Link tracking group and verify when all monitored ports are down, action port is brought down
12. Configure Link tracking group and verify when all monitored LAGs are down, action LAG is brought down
13. Verify that muliple groups containing the same monitored link
14. Verify link events not part of the link tracking group have no impact on link tracking
15. Verify debouncing logic to make sure rapid link toggling of monitored links have no impact on action link state
16. Verify admin state is honoured when bringing up the action port/lag.
17. Verify that L3 creates a single VxLAN tunnel to the common logical VTEP IP configured on L1 and L2  
18. Verify that L1 and L2 have VxLAN tunnels to L3
19. Verify that bringing down BGP session between L2 and L3 does not cause LVTEP tunnel to go down on L3 
20. Verify LVTEP tunnel on L3 is re-added after BGP session to L1 and L2 are flapped
21. Verify tunnel is re-added on L1 after BGP session between L1 and L3 is flapped
22. Verify MACs learned on remote leaf L3 are advertised to MLAG leaves L1/L2 correctly
23. Verify MACs learned from MHD1 on leaf L1 are advertised to L2 and L3 correctly
24. Verify MACs learned from SHD1 on Leaf L1 are advertised to L2 and L3 correctly
25. Verify MAC move from remote leaf L3 to MHD1 is handled correctly
26. Verify MAC move from MHD1 to remote leaf L3 is handled correctly
27. Verify L1 and L2 advertise all the MACs to L3 correctly after L3 is rebooted
28. Verify L1 advertises its MACs to L3 correctly after BGP session between L1 and L3 flap. Verify no MAC re-sync between L1 and L2
29. Verify L1 re-advertises SHD MAC to both L2 and L3 after SHD link flap
30. Verify L1 relearns and re-advertises MHD MAC to L2 and L3 after MCLAG interface flap
31. Verify L1 relearns and re-advertises SHD, MHD MACs to L2 and L3 after L1 is rebooted
32. Verify L1 locally forwards traffic received from L3 to MHD
33. Verify L1 sends traffic received from L3 to peer-link to MHD when MCLAG interface to MHD is down on L1
34. Verify L1 locally forwards traffic received from SHD to L3
35. Verify L1 locally forwards traffic received from MHD to L3
36. Verify L1 forwards traffic received from L3 to peer-link to SHD attached to L2
37. Verify L3 can load-balanced traffic to either L1 and L2
38. Verify L3 only sends a single copy of BUM packet to either L1 or L2
39. Verify L1 locally floods BUM traffic to MHD if MCLAG interface is up 
40. Verify L1 floods BUM traffic to peer-link to L2 to MHD when MCLAG interface is down on L1  
41. Verify L1 always floods BUM traffic over peer-link regardless of MCLAG interface status on L1 
42. Verify L1 never floods traffic receives on peer-link to VxLAN tunnel but floods traffic to MHD when MCLAG interface is down on L2 
43. Verify BUM traffic from MHD is flooded to L3 by either L1 or L2 directly

## 9.2 Negative Test Cases

1. Verify traffic from MDH to L3 is sent via L2 to L3 when MCLAG interface between L1 and MHD goes down.
2. Verify MCLAG interface down on L1 does not impact how traffic from L3 is load-balanced to L1 and L2
3. Verify ICCP session flap does not cause traffic drop for traffic from MHD to L3 and vice versa
4. Verify peer-link flap does not cause any traffic drop for traffic from MHD to L3
5. Verify peer-link flap does not cause any traffic drop for traffic from L3 to MHD when all MCLAG interface is up
6. Verify that traffic to SHD that is forwarded by either L1 or L2 to peer-link to SHD resumes after peer-link flap
7. Verify unicast traffic between MHD1 and L3 is resumed correctly after L3 is rebooted  
8. Verify L1 shutdowns all MCLAG interfaces when all uplinks to spines are down

## 9.3 Warm boot Test Cases

1. Verify traffic between MLAG server and server on remote leaf recovers correctly after ICCP warmboot
2. Verify traffic between MLAG server and server on remote leaf recovers correctly after FRR warmboot
3. Verify traffic between MLAG server and server on remote leaf recovers correctly after SwSS warmboot
4. Verify traffic between MLAG server and server on remote leaf recovers correctly after system warmboot
5. Verify traffic between MLAG server and server on remote leaf recovers correctly after MLAG peer node warmboot

# 10 Configuration Example

Below is the sample configuration on Leaf1 and Leaf2 nodes described in the LVTEP Overview diagram. Note that both Leaf1 and Leaf2 has the same source VTEP IP address 10.10.10.10

- Leaf1 Configuration

```
"VXLAN": {
    "San_jose_vtep": {
        "src_ip": "10.10.10.10"
    }
}

"EVPN_NVO": {
    "San_jose_nvo": {
        "source_vtep": "San_jose_vtep"
    }
}

"MC_LAG": {
    "5": {
        "local_ip": "1.1.1.1",
        "mclag_interface": "PortChannel50",
        "peer_ip": "2.2.2.2",
        "peer_link": "PortChannel30"
    }
}
```

- Leaf2 Configuration

```
"VXLAN": {
    "San_jose_vtep" {
        "src_ip": "10.10.10.10"
    }
}

"EVPN_NVO": {
    "San_jose_nvo": {
        "source_vtep": "San_jose_vtep"
    }
}

"MC_LAG": {
    "5": {
        "local_ip": "2.2.2.2",
        "mclag_interface": "PortChannel50",
        "peer_ip": "1.1.1.1",
        "peer_link": "PortChannel30"
    }
}
```

