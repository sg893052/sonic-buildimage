# EVPN Multi-Site DCI

# High Level Design Document

Rev 0.1

# Table of Contents

- [EVPN Multi-Site DCI](#EVPN-Multi-Site-DCI)
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
    - [1.1.4 Convergence Requirements](#1_1_4-Convergence-Requirements)
    - [1.1.5 Warm Boot Requirements](#1_1_5-Warm-Boot-Requirements)
  - [1.2 Design Overview](#1_2-Design-Overview)
    - [1.2.1 Basic Approach](#1_2_1-Basic-Approach)
    - [1.2.2 Container](#1_2_2-Container)
    - [1.2.3 SAI Overview](#1_2_3-SAI-Overview)
- [2 Functionality](#2-Functionality)
  - [2.1 Target Deployment Use Cases](#2_1-Target-Deployment-Use-Cases)
  - [2.2 Functional Description](#2_2-Functional-Description)
- [3 Design](#3-Design)
  - [3.1 Overview](#3_1-Overview)
  - [3.2 DB Changes](#3_2-DB-Changes)
    - [3.2.1 CONFIG DB](#3_2_1-CONFIG-DB)
    - [3.2.2 APP DB](#3_2_2-APP-DB)
    - [3.2.3 STATE DB](#3_2_3-STATE-DB)
    - [3.2.4 ASIC DB](#3_2_4-ASIC-DB)
    - [3.2.5 COUNTER DB](#3_2_5-COUNTER-DB)
  - [3.3 FRR](3_3-FRR)
  - [3.4 SAI](#3_4-SAI)
  - [3.5 CLI](#3_5-CLI)
    - [3.5.1 IS-CLI Compliance](#3_5_1-IS-CLI-Compliance)
    - [3.5.2 Data Models](#3_5_2-Data-Models)
    - [3.5.3 Configuration Commands](#3_5_3-Configuration-Commands)
    - [3.5.4 Show Commands](#3_5_4-Show-Commands)
    - [3.5.5 Debug Commands](#3_5_5-Debug-Commands)
    - [3.5.6 REST API Support](#3_5_6-REST-API-Support)
- [4 Flow Diagrams](#4-Flow-Diagrams)
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

[Table 1: Abbreviations](#Table-1-abbreviations)

# Revision

| Rev  | Date      | Author           | Change Description |
| ---- | --------- | ---------------- | ------------------ |
| 0.1  | 4/30/2021 | Syed Hasan Naqvi | Initial version    |
# About this Manual

This document provides design details of EVPN Multi-Site DCI feature planned for Broadcom SONiC 4.0.0 release.



# Scope

This document describes the high level design and changes required for supporting Multi-Site DCI for EVPN VxLAN. This feature allows data traffic for EVPN L3VNI (Type-5) and L2VNI (Type-2, Type-3) routes to be decapsulated and re-encapsulated at the Border Leaf (BL).




# Definition/Abbreviation

## Table 1: Abbreviations

| Term     | Meaning                                   |
| -------- | ----------------------------------------- |
| DCI      | Data Center Interconnect                  |
| BL       | Border Leaf                               |
| MCLAG    | Multi-Chassis LAG                         |
| LVTEP    | Logical VTEP                              |
| PIP, VIP | Primary IP Address, Virtual IP Address    |
| MHD, SHD | Multi-Homed Device, Single-Homed Device   |
| VNI      | (VxLAN) Virtual Network Identifier        |
| L3VNI    | (VxLAN) Layer3 Virtual Network Identifier |
| IRB VLAN | Integrated Routing and Bridging VLAN      |

# 1 Feature Overview

EVPN DCI architectures are categorized as:

1. Multi-PoD
2. Multi-Fabric
3. Multi-Site
   

Multi-PoD DCI architecture is the most simplest, but not scalable. The Border Leaf (BL) nodes connecting the PoDs/Sites merely act as Route-reflector or Route-Server. And VxLAN tunnels are stretched from ToR in one PoD/site to ToR in other PoD/site. This becomes a major scaling challenge as the number of sites/PoDs increase in the network.



Multi-Fabric DCI architecture shown in the diagram below does not have the above scaling issue. 

<figure><img src="evpn_multisite_images/evpn_multi_fabric_design.png" align="center"><figcaption align="center">Figure: EVPN VxLAN Multi-Fabric architecture</figcaption></img></figure>

In this architecture, VxLAN tunnels originating from within the site/PoD are terminated at the Border Leaf (BL) node. However, VxLAN tunnel towards remote site are not originated at the same BL node. Instead, the data-plane and control-plane undergo handoff to another BL' node from where the VxLAN tunnels to remote sites/PoDs are originated.

Even though Multi-Fabric DCI architecture provides scalable solution for interconnecting multiple PoDs/sites, it imposes limitation of provisioning another pair of BL' nodes in each PoD/site making this design not so appealing.



The Multi-Site DCI architecture avoids the above limitation with Multi-Fabric design but requires extra intelligence at the BL nodes to import and re-originate BGP updates, and also decapsulate and re-encapsulate VxLAN data traffic, crossing the BL.

<figure><img src="evpn_multisite_images/evpn_multi_site_design.png" align="center"><figcaption align="center">Figure: EVPN VxLAN Multi-Site architecture</figcaption></img></figure>

In the subsequent sections we will list down the requirements and provide design details for Multi-Site DCI solution in SONiC architecture.



## 1.1 Requirements

### 1.1.1 Functional Requirements

Following are functional requirements for EVPN VxLAN Multi-Site DCI:

1. Ability to terminate and re-originate VxLAN tunnels at the BL for the traffic crossing PoD/Site.
2. Support for Type-2, Type-3, and Type-5 EVPN routes.
3. MCLAG redundancy for BL
4. Ability to support termination of traffic for services locally attached to BL.
5. Co-existence with advertise-PIP feature on BL, while advertise-PIP not being mandatory for multi-site.
6. Support for downstream assigned VNI for connecting to remote sites.
7. Ability to support this feature on ToR for terminating VxLAN tunnels originating from server, and re-originating tunnel towards BL.
8. Support all above on all available platforms on which EVPN VxLAN is supported.
9. No impact to existing EVPN functionality if this feature is not configured.



Following are non-goals for the EVPN Multi-site DCI feature:

1. Support for Type-1 and Type4 routes. These routes will not be crossing the BL.
2. ESI based multi-homing for BL
3. Anycast Border Gateway. Only MCLAG Border Gateway is supported.
4. Provider Backbone Bridging
5. Conversational MAC
6. Conversational ARP



### 1.1.2 Configuration and Management Requirements

Following are configuration and management requirements for EVPN Multi-site DCI:

1. Ability to enable or disable this feature dynamically without impact to existing VxLAN configuration.
2. Support for REST/KLISH for all of the configuration and show.



### 1.1.3 Scalability Requirements

This feature is required to work for the claimed route/port scaling numbers for any given platform. 



EVPN Multi-site DCI increases the hw scale requirements on BL for Type-2 (MAC/ARP) entries as these cannot be aggregated/summarized. **Conversational-MAC** and **Conversational-ARP** are out of scope of this document.



### 1.1.4 Convergence Requirements

This feature should not negatively impact EVPN VxLAN traffic convergence and performance numbers established with previous releases.



### 1.1.5 Warm Boot Requirements

Warm-boot is required to be supported when this feature is enabled.




## 1.2 Design Overview

### 1.2.1 Basic Approach

The Border Leaf splits the VxLAN network into two split horizon groups:
		(a) Site-internal
		(b) Site-external

The site-internal network comprises the VxLAN tunnels to VTEPs within the site. And site-external network comprises the VxLAN tunnels to BL of the remote sites.

The site-internal and site-external networks belong to separate split-horizon groups allowing BL to terminate and re-originate routes, and the data traffic, crossing the BL.

<figure><img src="evpn_multisite_images/evpn_multisite_split_horizon.png" align="center"><figcaption align="center">Figure: VxLAN split-horizon for internal and external VxLAN tunnels.</figcaption></img></figure>



As shown in the diagram above, BL in site-1 terminates VxLAN traffic originated from the Leaf nodes within the PoD/DC. After the termination, the L2 traffic gets re-encapsulated and switched to VxLAN tunnels towards remote sites. However, due to split horizon, the L2 traffic is not switched back to the VxLAN tunnels towards VTEP within the PoD/DC. Same holds for the traffic arriving from remote BLs. Note that within a PoD/DC, VTEPs have direct tunnels to other VTEPs, and therefore BL is not required switch back the traffic.

The BGP control plane is also required to be aware of the site-internal and site-external neighbors. BGP on the BL will perform following tasks:

1. Re-advertise routes from internal to external (or vice versa) neighbors with next-hop and RMAC as self.
2. Re-advertise only those routes which are imported on the BL.
3. Do not allow routes to be propagated between internal and external neighbors which have local scope, e.g. Type-1 and Type4.



### 1.2.2 Container
No new container is added by this feature.

### 1.2.3 SAI Overview
No changes to the Tunnel SAI specification. 

# 2 Functionality

## 2.1 Target Deployment Use Cases

Following are the potential use-cases for EVPN Multi-site feature:

- Connecting multiple DC/sites.
- Connecting multiple PoDs within the DC/site.
- Scaling VxLAN on server use-case by allowing VxLAN tunnels from server to be terminated on ToRs instead of BL.



## 2.2 Functional Description

### 

### 2.2.1 VxLAN External IP address

In order to distinguish between internal and external VxLAN tunnels, separate External VTEP IP address is required to be specified by the administrator on the Border Leaf nodes:

```
sonic(config)# interface vxlan vtep-1
sonic(conf-if-vxlan-vtep-1)# source-ip 192.168.10.1
sonic(conf-if-vxlan-vtep-1)# external-ip 10.1.1.1
```

In case a pair of BLs are forming LVTEP, the same external-ip VTEP address is required to be configured on MCLAG peer.

The source-ip address is the existing configuration and is mandatory. Both source-ip and external-ip addresses are required to be configured before creating any VLAN-VNI mappings.



### 2.2.2 BGP EVPN Fabric External neighbors

In order to identify BGP neighbors peering to remote PoD/DC/site, fabric-external configuration option is introduced under BGP EVPN address-family.

```
sonic(config)# router bgp <asn>
sonic(config-router)# address-family l2vpn evpn
sonic(config-router-af)# neighbor <remote-ip> fabric-external
```

Once a neighbor is configured as *fabric-external*, it will belong to separate rib-out group isolated from the non-fabric-external group of neighbors.

Following will be the behavior for routes received/advertised between non-fabric-external and fabric-external neighbors:

| Route Type                                 | Advertised to<br />Non-Fabric-External Neighbor | Advertised to<br />Fabric-Externa-Neighbor |
| :----------------------------------------- | ----------------------------------------------- | ------------------------------------------ |
| Locally originated                         | NH=VIP, RMAC=local                              | NH=external-ip, RMAC=local                 |
| Received from Non-fabric-external neighbor | nexthop unchanged                               | NH=external-ip, RMAC=local                 |
| Received from Fabric-external neighbor     | NH=VIP, RMAC=local                              | nexthop unchanged                          |

The route-distinguisher (RD) and route-targets (RT) in the received routes will be retained as is while re-advertising the routes between fabric-external and non-fabric-external BGP neighbors. User can apply inbound and outbound policies on the fabric-external neighbors to send special communities / extended-communities in order to have custom control on the routes crossing the BL.



### 2.2.3 Achieving split-horizon in Linux Bridge

SONiC design requires forwarding path via the kernel to work exactly the same how it would work in the hw fast-path. In case of EVPN multi-site, VxLAN encapsulated L2 BUM/unicast traffic requires decapsulation and re-encapsulation while passing through the Linux Bridge on the BL.

The diagram below highlights that VxLAN decapsulation and re-encapsulation for L2 packets will not work with the current vxlan design.

<figure><img src="evpn_multisite_images/linux_bridge_vxlan_hairpin.png" align="center"><figcaption align="center">Figure: VxLAN L2 packet hairpin forwarding via Linux Bridge</figcaption></img></figure>

In the above example, the vxlan encapsulated L2 packet enters the switch through Ethernet0 interface. Assuming the VNI in vxlan header is 100, packet is delivered to vtep-100 netdevice which removes the vxlan header and delivers the packet to Linux Bridge. Assuming the packet requires L2 flooding to VxLAN tunnel to remote site, the L2 lookup in the Bridge points back to the same vtep-100 vxlan netdevice. However, this is the case of hairpin forwarding which is disabled by default and cannot be enabled as it would break split-horizon between vxlan tunnels within the site.



The solution to the above problem is to create separate vxlan netdevices for internal and external attachments. In the diagram below, along with vtep-100, another vxlan netdevice vtep-ext-100 is created with the same VNI but with external-ip as the source VTEP IP. The Linux kernel is patched to look at the destination IP address in the outer header of the vxlan encapsulated packet, and deliver the packet to the netdevice matching the VNI and the destination VTEP IP.



<figure><img src="evpn_multisite_images/linux_bridge_internal_external_vxlan_netdevice.png" align="center"><figcaption align="center">Figure: VxLAN L2 packet forwarding via internal and external vxlan netdevices</figcaption></img></figure>

Since BGP on BL advertises EVPN routes with VIP as next-hop to internal peers, and external-ip as next-hop to external peers, the traffic would also follow the same pattern. That is, traffic originated within the site will be sent with DIP=VIP, and vxlan packet will ingress the vtep-100 netdevice. Similarly, traffic originated on remote BLs will be sent with DIP=external-ip of the BL, and will ingress vtep-ext-100 netdevice.  The L2 lookup in the Linux bridge will point to the external netdevice for the traffic entering from internal peers, and vice-versa. Since traffic crossing between internal and external peers will enter and exit separate vxlan netdevices, split-horizon is achieved.



When VxLAN external-ip address is configured, VxlanMgr will automatically create the external vxlan netdevice and configure it with the external VTEP IP address. The netdevices for internal and external attachment are shown below.

```
2041: vtep1-100: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master Bridge state UNKNOWN mode DEFAULT group default qlen 1000
    link/ether 18:5a:58:89:3a:60 brd ff:ff:ff:ff:ff:ff promiscuity 1
    vxlan id 100 local 192.168.10.1 srcport 0 0 dstport 4789 nolearning ageing 300 udpcsum noudp6zerocsumtx noudp6zerocsumrx
    bridge_slave state forwarding priority 4 cost 100 hairpin off guard off root_block off fastleave off learning off flood on port_id 0x8002 port_no 0x2 designated_port 32770 designated_cost 0 designated_bridge 8000.18:5a:58:89:3a:60 designated_root 8000.18:5a:58:89:3a:60 hold_timer    0.00 message_age_timer    0.00 forward_delay_timer    0.00 topology_change_ack 0 config_pending 0 proxy_arp off proxy_arp_wifi off mcast_router 1 mcast_fast_leave off mcast_flood on addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535

2042: vtep1-ext-100: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master Bridge state UNKNOWN mode DEFAULT group default qlen 1000
    link/ether 18:5a:58:89:3a:60 brd ff:ff:ff:ff:ff:ff promiscuity 1
    vxlan id 100 local 10.1.1.1 srcport 0 0 dstport 4789 nolearning ageing 300 udpcsum noudp6zerocsumtx noudp6zerocsumrx
    bridge_slave state forwarding priority 4 cost 100 hairpin off guard off root_block off fastleave off learning off flood on port_id 0x8002 port_no 0x2 designated_port 32770 designated_cost 0 designated_bridge 8000.18:5a:58:89:3a:60 designated_root 8000.18:5a:58:89:3a:60 hold_timer    0.00 message_age_timer    0.00 forward_delay_timer    0.00 topology_change_ack 0 config_pending 0 proxy_arp off proxy_arp_wifi off mcast_router 1 mcast_fast_leave off mcast_flood on addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535

```



### 2.2.4 Downstream assigned VNI

BGP EVPN route updates carry L2/L3 VNI along with Route-Targets (RTs.) In the existing implementation of EVPN in SONiC, it is always assumed that VNIs in the network are symmetrically configured, i.e. remote and local VNIs for a given IP/MAC VRF are assumed to be the same -- even though VNIs may map to different VLANs on the VTEPs. In other words, if the EVPN (Type-2, 3, or 5) route is imported into MAC/IP VRF instance, the local VNI configured for the IP/MAC VRF is used to send the traffic to remote VTEP. However, this may not be always true.

Specifically, while connecting multiple sites, which will be in separate administrative control, VNI assigned to IP/MAC VRF cannot be assumed to be the same. In such a case, the routes from remote sites are imported into IP/MAC VRF by matching RTs configured locally for the IP/MAC VRF with the RTs in the incoming route. And VNI in the incoming route may not be same as the VNI configured locally for the IP/MAC VRF.



In the example below, administrator is importing routes matching 1000:1 and 2000:1 RTs, where RT 2000:1 is configured on the remote site.

```
vni 1000
    rd 1000:1
    route-target export 1000:1
    route-target import 1000:1
    route-target import 2000:1
```



The VNI in the incoming BGP update will be carried in the control plane and remote MAC/ARP and IMET entries will be programmed in the Linux kernel and hw with the remote VNI.

```
00:09:01:0b:00:01 dev vtep1-1000 vlan 1000 offload master Bridge
00:09:01:0b:00:01 dev vtep1-1000 dst 1.0.3.1 vni 2000 self offload
```


Assuming L2VNI in the route with RT 2000:1 is 2000, the corresponding MAC entry is programmed in Linux against vxlan netdevice of VNI 1000 with remote VNI 2000. Same will be achieved in the hw. The VXLAN_FDB_TABLE and VXLAN_REMOTE_VNI tables have VNI field that is effectively the downstream VNI.




# 3 Design

## 3.1 Overview


## 3.2 DB Changes

### 3.2.1 CONFIG DB

**VXLAN_TUNNEL_TABLE**

Producer:  config manager

Consumer: VxlanMgr

Description: Updated existing table to store external IP.

Schema:

```
;Existing table
;defines VTEP. Updated to store external IP associated with VTEP.
;
;Status: stable

key = VXLAN_TUNNEL:VTEP_NAME ; VTEP name as a string
external_ip = ipv4 ; external IP associated with VTEP.

```



### 3.2.2 APP DB

**VXLAN_TUNNEL_TABLE**

Producer:  VxlanMgr

Consumer: VxlanOrch

Description: Updated existing table to store primary IP.

Schema:

```
; Existing table
; Updated to store external IP associated with VTEP.

key = VXLAN_TUNNEL_TABLE:VTEP_NAME
                          ; VTEP name as a string
; field = value
external_ip = ipv4 ; external IP associated with VTEP.
```



**VXLAN_FDB_TABLE**

Producer:  Fdbsyncd

Consumer: FdbOrch

Description: Updated existing table to store split-horizon group.

Schema:

```
; Existing table
; Updated to group to identify split horizon group the mac belongs to.

key = VXLAN_FDB_TABLE:"Vlan"vlanid:mac_address
                          ; MAC Address and VLAN ID
; field = value
group = "internal" / "external" ; split-horizon group fdb entry belongs to. If this field is absent, it means "internal".
```



**VXLAN_REMOTE_VNI_TABLE**

Producer:  Fdbsyncd

Consumer: VxlanOrch

Description: Updated existing table to store split-horizon group.

Schema:

```
; Existing table
; Updated to group to identify split horizon group the IMET route belongs to.

key = VXLAN_REMOTE_VNI_TABLE:"Vlan"vlanid:remote_vtep_ip
                  ; Vlan ID and Remote VTEP IP 
; field = value
group = "internal" / "external" ; split-horizon group IMET route belongs to. If this field is absent, it means "internal".
```



### 3.2.3 STATE DB

No Changes

### 3.2.4 ASIC DB

No Changes

### 3.2.5 COUNTER DB

No Changes



## 3.3 FRR

Following is the high level behavior of Zebra:

1. 



## 3. 4 SAI

TBD



## 3.5 CLI

### 3.5.1 IS-CLI Compliance


### 3.5.2 Data Models
### 3.5.3 Configuration Commands

#### 3.5.3.1 KLISH commands

TBD


#### 3.5.3.2 Click commands

TBD

### 3.5.4 Show Commands

TBD

### 3.5.5 Debug Commands

TBD

### 3.5.6 REST API Support

TBD



# 4 Flow Diagrams

TBD



# 5 Error Handling
No explicit error handling requirement introduced due to this feature.




# 6 Serviceability and Debug
Existing serviceability and debug applicable to this feature. No new debugging commands are added.



# 7 Warm Boot Support
Warm reboot will be supported. No specific changes required for supporting warm-reboot.



# 8 Scalability

No impact to scalability. Existing scale numbers will be supported with this feature.



# 9 Unit Test

## 9.1 Functional Test Cases

## 9.2 Negative Test Cases

TBD

## 9.3 Scale Test Cases

TBD


## 9.4 Warm boot Test Cases

TBD

# 10 Configuration Example

# 11 Appendix

