# EVPN Multi-Site DCI

# High Level Design Document

Rev 0.2

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
- [9 Upgrade and Downgrade](#9-Upgrade-and-Downgrade)
- [10 Unit Test](#9-Unit-Test)
  - [10.1 Functional Test Cases](#9_1-Functional-Test-Cases)
  - [10.2 Negative Test Cases](#9_2-Negative-Test-Cases)
  - [10.3 Warm boot Test Cases](#9_3-Warm-boot-Test-Cases)
- [11 Configuration Example](#10-Configuration-Example)


# List of Tables

[Table 1: Abbreviations](#Table-1-abbreviations)

# Revision

| Rev  | Date      | Author                                           | Change Description |
| ---- | --------- | ------------------------------------------------ | ------------------ |
| 0.1  | 4/30/2021 | Syed Hasan Naqvi, Rajesh Sankaran, Kishore Kunal | Initial version    |
| 0.2  | 6/21/2021 | Syed Hasan Naqvi                                 | Review comments, show outputs, etc.|
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
4. Ability to support termination of traffic for (single-homed and multi-homed) services locally attached to BL.
5. Co-existence with advertise-PIP feature on BL, while advertise-PIP not being mandatory for multi-site.
6. Compliance with EVPN VxLAN interconnect procedure as per **RFC 9014** (wherever applicable.)
7. Support for downstream assigned VNI for connecting to remote sites.
8. Ability to support this feature on ToR for terminating VxLAN tunnels originating from server, and re-originating tunnel towards BL.
9. Support all above on all available platforms on which EVPN VxLAN is supported.
10. No impact to intra-site tunnels and traffic in case of failure of inter-site BGP peering or tunnel(s).
11. No impact to existing EVPN functionality if this feature is not configured.



Following are non-goals for the EVPN Multi-site DCI feature:

1. Support for Type-1 and Type4 routes. These routes will not be crossing the BL.
2. BUM replication method other than Ingress Replication.
3. ESI based multi-homing for BL
4. Anycast Border Gateway. Only MCLAG Border Gateway is supported.
5. Provider Backbone Bridging
6. Conversational MAC
7. Conversational ARP



### 1.1.2 Configuration and Management Requirements

Following are configuration and management requirements for EVPN Multi-site DCI:

1. Ability to enable or disable this feature dynamically without impact to existing VxLAN configuration.
2. Support for REST/KLISH for all of the configuration and show.
3. OC-yang based REST URIs.



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

1. Connecting multiple DC/sites.
2. Connecting multiple PoDs within the DC/site.
3. Scaling VxLAN on server use-case by allowing VxLAN tunnels from server to be terminated on ToRs instead of BL.



Figure below attempts to describe the VxLAN on server/VM and DCI use-cases of EVPN multi-site.

<figure><img src="evpn_multisite_images/multisite_tunnel_types.png" align="center"><figcaption align="center">Figure: VxLAN internal, external, and pass-through tunnels.</figcaption></img></figure>

**VxLAN on VM use-case**

In this use-case, hosts/VMs attached to the ToR/Leaf nodes are capable of termination and origination of VxLAN. The green VxLAN tunnels are originated and terminated on the hosts. The Leaf-Spine network in this case acts as underlay network.

However, internet or inter-site traffic originated at the hosts has to exit via BL nodes in a given site. Typically, hosts/VM form direct VxLAN tunnel to the BL to reach the external network. Since the number of VMs may reach thousands in number, the BL will have severe scaling challenge maintaining VxLAN tunnel to each and every VM.

Using the multisite feature, administrator can configure BGP EVPN neighbors to VMs as fabric-external such that routes originated by the BL are terminated and re-originated to VM with Leaf's next-hop. This will allow tunnels originated at VM to be terminated at the Leaf nodes.



**DCI use-case**

In this use-case, BL in a given site connects to multiple remote sites. BGP EVPN neighbors to the remote BLs are configured as fabric-external. Traffic originated from within the site is VxLAN terminated at the BL and re-originated to the remote sites. However, the routes between the remote BLs are exchanged without changing the next-hop allowing remote BLs to create passthrough tunnel in order to communicate directly.



## 2.2 Functional Description

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

Once a neighbor is configured as *fabric-external*, it will belong to separate rib-out group isolated from non-fabric-internal group of neighbors. For explaining purposes, let's use ***fabric-internal*** to refer to non-fabric-external neighbors.



**Re-origination of Type-2 and Type-5 routes**

Following will be the behavior for Type-2 routes received/advertised between fabric-internal and fabric-external neighbors:

| Route Type                                                   | Advertised to<br />Fabric-Internal Neighbor | Advertised to<br />Fabric-External Neighbor |
| :----------------------------------------------------------- | ------------------------------------------- | ------------------------------------------- |
| Locally originated (SHD/MHD)<br />(Advertise-PIP not configured) | NH=VIP, RMAC=local (Virtual-MAC)            | NH=External-IP, RMAC=local (Virtual-MAC)    |
| Locally originated (MHD)<br />(Advertise-PIP configured)     | NH=VIP, RMAC=local (Virtual-MAC)            | NH=External-IP, RMAC=local (Virtual-MAC)            |
| Locally originated (SHD)<br />(Advertise-PIP configured)     | NH=PIP, RMAC=local (System-MAC)             | NH=PIP, RMAC=local (System-MAC)             |
| Received from fabric-internal neighbor<br />(Advertise-PIP not configured) | next-hop unchanged                          | NH=External-IP, RMAC=local (Virtual-MAC)    |
| Received from fabric-internal neighbor<br />(Advertise-PIP configured) | next-hop unchanged                          | NH=External-IP, RMAC=local (Virtual-MAC)    |
| Received from Fabric-external neighbor<br />(Advertise-PIP not configured) | NH=VIP, RMAC=local (Virtual-MAC)            | next-hop unchanged                          |
| Received from Fabric-external neighbor<br />(Advertise-PIP configured) | NH=VIP, RMAC=local (Virtual-MAC)            | next-hop unchanged                          |

Following will be the behavior for Type-5 routes received/advertised between fabric-internal and fabric-external neighbors:

| Route Type                                                   | Advertised to<br />Fabric-Internal Neighbor | Advertised to<br />Fabric-External Neighbor |
| :----------------------------------------------------------- | ------------------------------------------- | ------------------------------------------- |
| Locally originated <br />(Advertise-PIP not configured) | NH=VIP, RMAC=local (Virtual-MAC)            | NH=External-IP, RMAC=local (Virtual-MAC)    |
| Locally originated <br />(Advertise-PIP configured)     | NH=PIP, RMAC=local (System-MAC)            | NH=PIP, RMAC=local (System-MAC)            |
| Received from fabric-internal neighbor<br />(Advertise-PIP not configured) | next-hop unchanged                          | NH=External-IP, RMAC=local (Virtual-MAC)    |
| Received from fabric-internal neighbor<br />(Advertise-PIP configured) | next-hop unchanged                          | NH=PIP, RMAC=local (System-MAC)    |
| Received from Fabric-external neighbor<br />(Advertise-PIP not configured) | NH=VIP, RMAC=local (Virtual-MAC)            | next-hop unchanged                          |
| Received from Fabric-external neighbor<br />(Advertise-PIP configured) | NH=PIP, RMAC=local (System-MAC)            | next-hop unchanged                          |

When one or more fabric-external neighbors are configured, Type-2/5 routes received from fabric-internal or fabric-external neighbors will be re-originated with local route-distinguisher (RD) and route-targets (RT) configured for the MAC-VRF or IP-VRF. The original route-targets in the route are stripped in the re-originated route. If the route is received from fabric-external neighbor, the re-originated route is advertised only to fabric-internal neighbor(s), and vice-versa.

The Type-2 routes converted to (/32 or /128) host-routes will not be re-originated as Type-5 routes. 



**Behavior of Type-3 routes**

The Type-3 (IMET) routes received from fabric-external or fabric-internal neighbors are not re-originated. Instead, these are consumed locally. Only the local IMET routes are advertised to fabric-external and fabric-internal neighbors.



**Behavior of Type-1 and Type-4 routes**

The Type-1 and Type-4 routes are not advertised by BL as these route types are currently not supported. Any such route received from fabric-internal or fabric-external neighbor will be locally consumed and will not be re-originated.



Note that user can apply inbound and outbound policies on the fabric-external/fabric-internal neighbors to send special communities or extended-communities in order to have custom control on the routes crossing the BL.



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

#### 2.2.4.1 BGP configuration

In the example below, administrator is importing routes matching 1000:1 and 2000:1 RTs, where RT 2000:1 is configured on the remote site.

```
vni 1000
    rd 1000:1
    route-target export 1000:1
    route-target import 1000:1
    route-target import 2000:1
```



Similarly, Type-5 routes matching remote RT 20010:1 are imported into Vrf-red:

```
router bgp 10 vrf Vrf-red
    address-family l2vpn evpn
        rd 10010:1
        route-target export 10010:1
        route-target import 10010:1
        route-target import 20010:1
```



The VNI in the incoming BGP update will be carried in the control plane and remote MAC/ARP and IMET entries will be programmed in the Linux kernel and hw with the remote VNI.

```
00:09:01:0b:00:01 dev vtep1-1000 vlan 1000 offload master Bridge
00:09:01:0b:00:01 dev vtep1-1000 dst 1.0.3.1 vni 2000 self offload
```

Assuming L2VNI in the route with RT 2000:1 is 2000, the corresponding MAC entry is programmed in Linux against vxlan netdevice of VNI 1000 with remote VNI 2000. Same will be achieved in the hw. The VXLAN_FDB_TABLE and VXLAN_REMOTE_VNI tables have VNI field that is effectively the downstream VNI.



#### 2.2.4.2 VxLAN configuration

Extra VxLAN configuration is required to pre-allocate the hw resources required for maintaining per tunnel VNI mapping. Maintaining downstream VNI per tunnel doesn't use the hw resource optimally, and therefore downstream VNI feature is disabled by default.

At the Border-leaf, the downstream VNI feature can be enabled for external or specific VTEPs.

```
sonic(config)# interface vxlan vtep-1
sonic(conf-if-vxlan-vtep-1)# vni-downstream ?
    external          External VTEPs
    <A.B.C.D>         Specific VTEP IP address
```

Typically, within the DC, the VTEPs will have the same VLAN-VNI mapping. However, the remote site(s) to which the BL is connecting to may be  in a separate administrative control and may have different VNI assignment. In such a case, it will be easier to configure downstream VNI assignment for all of the external VTEPs as shown below:

```
sonic(config)# interface vxlan vtep-1
sonic(conf-if-vxlan-vtep-1)# vni-downstream external
```



However, if there are only specific external VTEP(s) which have different VLAN-VNI assignment, it might be better to specify such remote VTEP IP address(es), instead of configuring downstream VNI for all of the external VTEPs.

```
sonic(config)# interface vxlan vtep-1
sonic(conf-if-vxlan-vtep-1)# vni-downstream 10.10.10.10
sonic(conf-if-vxlan-vtep-1)# vni-downstream 10.10.10.11
```

Even though very unlikely, but in case an internal VTEP has a distinct VLAN-VNI assignment, downstream-VNI can be configured for that VTEP using the above configuration.

It does not make sense to specify external VTEP IP address(es) if *"vni-downstream external"* is already configured.

Note that all of the above downstream VNI configurations have to be performed before configuring VNI-VLAN mapping.



### 2.2.5 Co-existence of Primary IP on Border Leaf

VxLAN Primary IP configuration is not mandatory for supporting Multisite. However, in order to avoid usage of peer-link of MCLAG Border Leaf, VxLAN Primary IP address can be configured if there are services/hosts locally attached to orphan port(s). 

If VxLAN Primary IP is configured on the BL, the Primary IP as next-hop will be applicable only for the local routes advertised by the MCLAG Border Leaf. The Type-2 routes for orphan hosts and Type-5 routes will be advertised to both internal and external BGP peers with PIP as the next-hop.

The routes re-advertised between external and internal peers will continue to use External IP and Virtual IP, respectively, as described in previous subsections.



### 2.2.6 Neighbor suppression on Border Leaf

Neighbor suppression is an optional configuration that avoids unnecessary flooding of ARP requests and ND solicit messages over VxLAN tunnels. Neighbor suppression can be enabled on Border Leaf in order to allow BL to respond to ARP/ND request messages locally. ARP/ND requests arriving from local edge ports on the BorderLeaf will be suppressed. Neighbor suppression will not work for the ARP/ND requests transiting over VxLAN between internal and external VTEPs.




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



**VXLAN_DOWNSTREAM_VNI_TUNNEL_TABLE**

```;
;New table
;Specifies VTEPs for which remote VNI assignment will be different from local mapping.
;
; Status: stable
key = VXLAN_DOWNSTREAM_VNI_TUNNEL:remote_vtep_ip  ; Remote VTEP IPv4 address
                                    ; 0.0.0.0 VTEP IP address stands for all of the external VTEPs
```



### 3.2.2 APP DB

**VXLAN_TUNNEL_TABLE**

Producer:  VxlanMgr

Consumer: VxlanOrch

Description: Updated existing table to store external IP.

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
; Updated group to identify split horizon group the mac belongs to.

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
; Updated group to identify split horizon group the IMET route belongs to.

key = VXLAN_REMOTE_VNI_TABLE:"Vlan"vlanid:remote_vtep_ip
                  ; Vlan ID and Remote VTEP IP 
; field = value
group = "internal" / "external" ; split-horizon group IMET route belongs to. If this field is absent, it means "internal".
```



**VXLAN_DOWNSTREAM_VNI_TUNNEL_TABLE**

Producer: VxlanMgr

Consumer: VxlanOrch

Description: New table to maintain VxLAN tunnels which will have VNI assignment different from local mapping.

Schema:

```
; New table
; Specifies VTEPs for which remote VNI assignment will be different from local mapping.

key = VXLAN_DOWNSTREAM_VNI_TUNNEL_TABLE:remote_vtep_ip ; Remote VTEP IP
                                          ; 0.0.0.0 VTEP IP stands for all of the external VTEPs.
```

 

**ROUTE_TABLE**

Producer:  Fpmsyncd

Consumer: RouteOrch

Description: Updated existing table to store split-horizon group.

Schema:

```
; Existing table
; Updated group to identify split horizon group the Type-5 route belongs to.

key = ROUTE_TABLE:vrfname:ip_prefix
                  ; Vrf-name and IPv4/IPv6 prefix
; field = value
group = "internal" / "external" ; split-horizon group Type-5 route's nexthop belongs to. If this field is absent, it means "internal".
```



**NHID_TABLE**

Producer:  Fpmsyncd

Consumer: NhgOrch

Description: Updated existing table to store split-horizon group.

Schema:

```
; Existing table
; Updated group to identify split horizon group the Next-hop group member belongs to.

key = NHID_TABLE:nhid
                  ; Nexthop group id
; field = value
group = "internal" / "external" ; split-horizon group Next-hop group memeber belongs to. If this field is absent, it means "internal".
```



### 3.2.3 STATE DB

**VXLAN_TUNNEL_TABLE**

Producer:  VxlanOrch

Consumer: VxlanOrch

Description: Updated existing table to store VTEP's internal/external split-horizon group and downstream-vni state.

Schema:

```
;Existing table
;defines VTEP. Updated to store tunnel's external/internal classification and downstream-vni assignment state.
;
;Status: stable

key = VXLAN_TUNNEL:VTEP_NAME ; VTEP name as a string
"external" = boolean ; true if VTEP is external. Absense of this field indicates VTEP is internal.
"downstream_vni" : boolean ; true if VTEP has remote assignment of vni-vlan. Absense of this field means VNI assignment is global.
```



### 3.2.4 ASIC DB

No Changes

### 3.2.5 COUNTER DB

No Changes



## 3.3 FRR, Fpmsyncd, and Fdbsyncd

Following FRR components will undergo changes to support EVPN Multi-site and downstream assigned VNI:

- BGP
- Zebra

Fpmsyncd and Fdbsyncd are also enhanced to bring awareness of external EVPN routes.

There is no change required in neighsyncd as remote neighbor entries are installed against SVI interfaces and are agnostic to external vs internal VTEPs.



### 3.3.1 BGP changes

#### 3.3.1.1 Fabric external peers

BGP peering with external DCI needs to be done as fabric-external peering. It is recommended that fabric-external command is only used in multi-site deployment. Fabric-external enables the next-hop rewrite for multi-site. By default peers will be fabric-internal peers.



#### 3.3.1.2 BGP route re-advertisement between fabric-external and internal peers

EVPN routes on the Border gateways between fabric-external and fabric-internal peers will never be reflected but it will be consumed and re-advertised. The route-reflection (or re-advertisement) between fabric-external peers or  between fabric-internal peers will continue to be done without changing the next-hop and path attributes.

Type-2 and Type-5 routes received from fabric-external will be re-advertised to fabric-internal (and vice-versa) if the route is imported and they become active routes in MAC-VRF. The route will be re-advertised as new routes with the following fields:

\-     RD as BGW’s RD. 

\-     RT as BGW’s RT. 

\-     Next-hop as BGW's VTEP IP

+ Virtual IP (VIP) when re-advertising route from fabric-external to fabric-internal peer.
+ External IP (EIP) when sending route from fabric-internal to fabric-external peer.

\-     Router-Mac as BGW’s router-mac (common to both of the LVTEP nodes.)

 

<figure><img src="evpn_multisite_images/bgp_design.png" align="center"><figcaption align="center">Figure: FRR BGP design</figcaption></img></figure>



**Ribout flow during re-advertisement**

All the evpn peers are marked as fabric-external or fabric-internal peers. 

- EVPN routes learned from fabric-internal will be reflected to all the fabric-internal neighbors RIB-out. And imported to the MAC_VRF / IP_VRF tables.
- EVPN routes learned from fabric-external will be reflected to all the fabric-external neighbors RIB-out. And imported to the MAC_VRF / IP_VRF tables.
- If the route imported from fabric-external (/fabric-internal) neighbor becomes active then the same route with local next-hop, RD, and attributes is exported to RIB-in as a local route.
- These locally re-originated routes are then added to rib-out of  fabric-external neighbor if the original route was received from fabric-internal, and vice-versa.



### 3.3.2 Zebra changes

#### 3.3.2.1 Handling of external and internal VxLAN netdevices

Zebra is enhanced to maintain two vxlan netdevice references for each VNI:

- vxlan_if

  The is the existing vxlan interface and routes received from internal peers are installed on this interface.

- vxlan_ext_if

  This is the new vxlan interface and routes received from external peers are installed on this interface.

#### 3.3.2.2 Handling of downstream assigned VNI

BGP is enhanced to extract VNI (label) information from the Type-2/Type-5 routes and from PMSI tunnel attribute for Type-3 routes and send to it zebra along with route updates.

Zebra are enhanced to maintain downstream assigned vni (referred as label) in zebra_mac_t, zebra_neigh_t, zebra_rmac_t, and zebra_vtep_t data-structures.

#### 3.3.2.3 Handling of EVPN routes received from fabric-external peers

BGP is enhanced to send 'external' flag to zebra for the routes which are selected best and are received from fabric-external peers.

Zebra is enhanced to maintain external flag in zebra_mac_t, zebra_neigh_t, zebra_rmac_t, and zebra_vtep_t data-structures and installed them into the kernel against vxlan_if or vxlan_ext_if accordingly. Similarly, the external flag is sent to route and NHID updates sent to fpmsyncd.

### 3.3.3 Fpmsyncd changes

Fpmsyncd is enhanced to extract external flag in the EVPN route updates received from zebra and populate group field in populated in ROUTE_TABLE and NHID_TABLE entries.



### 3.3.4 Fdbsyncd changes

Fdbsyncd is enhanced to maintain the internal/external vxlan netdevice information when receiving netlink mac events from kernel. If the mac update was received with external vxlan netdevice interface, group field is populated in VXLAN_FDB_TABLE and REMOTE_VNI_TABLE entries.



### 3.3.5 VxlanOrch and VxlanMgr changes

Support for external tunnels.

- External IP handling in VxlanMgr and population of the APP DB. 
- External IP kernel programming changes in VxlanMgr. 
- Maintain a separate P2MP tunnel for external tunnels and also associated external P2P tunnels.
- Interface with IsolationGroup Orch to create an Isolation group for the first external tunnel and delete the isolation group for the last  tunnel. 
- Set the Isolation group as part of the tunnel bridge port creation. 

Support for downstream assigned VNI

- Downstream VNI configuration handling in VxlanMgr.
- Create Separate encap-mapper based on the VNI downstream configuration. 
- Maintain a refcnt of the number of routes per VNI per remote IP.
- Add/remove encap mapping entries based on when the refcnt becomes non-zero/zero. 



## 3. 4 SAI

**Support for external tunnels ** 

- A new P2MP Tunnel object with the source IP as the external IP will be created.
- A new Isolation Group will be created for external tunnels. 
- The bridge-port created for external tunnels will carry the above isolation group id as an attribute.
- No changes to the SAI interface for internal tunnel programming.

**Support for Downstream Assigned VNI **

- Encap Mappers of type VLAN-VNI and VRF-VNI will be created for each P2P Tunnel object.
- Encap mapping entry against the encap mapper created above will be created for every downstream assigned VNI. 
- No change to encap and decap mapper/mapping entry for internal tunnels. 
- Decap mappers and mapping entries for external tunnels shall be the same as for internal tunnels.



## 3.5 CLI

### 3.5.1 IS-CLI Compliance


### 3.5.2 Data Models
### 3.5.3 Configuration Commands

#### 3.5.3.1 KLISH commands

The below configuration command is used to configure external IP address on the Border Leaf node:

```
sonic(config)# interface vxlan vtep-1
sonic(conf-if-vxlan-vtep-1)# source-ip 192.168.10.1
sonic(conf-if-vxlan-vtep-1)# external-ip 10.1.1.1
```



The following command will be used to configure BGP neighbor as fabric-external:

```
sonic(config)# router bgp 10
sonic(config-router-bgp)# neighbor 10.1.10.1
sonic(config-router-bgp-neighbor)# remote-as external
sonic(config-router-bgp-neighbor)# address-family l2vpn evpn
sonic(config-router-bgp-neighbor-af)# activate
sonic(config-router-bgp-neighbor-af)# fabric-external     ! External peer
```



The following command is available to specify which of the VTEPs will have VNI assignments different from the local configuration.

```
sonic(config)# interface vxlan vtep-1
sonic(config-if-vxlan-vtep-1)# vni-downstream {external|<A.B.C.D>}
```

The `external` option enables capability for external VTEPs to have VNI assignment separate from local configuration. The specific VTEP IP address can also be specified to enable this capability. 

Note that source-ip, external-ip, and vni-downstream configurations are to be applied before creating VLAN-VNI mappings.

The Route-Target configuration commands for L2 VNI and L3 VNI under BGP L2VPN EVPN address-family configuration modes are enhanced to ease up the RT configuration when using downstream assigned VNI.



Typically, BL supporting Multisite will be required to import routes from internal VTEPs using auto-RT, and import routes from external VTEPs using manually specified RT values. Before this release, auto-RT and manual RT cannot co-exist. That is, by default auto-RT is enabled, but when user configures manual RT, the manual RT takes precedence and route import/export using auto-RT is disabled.

Below RT configuration is newly introduced in this release and allows user to explicitly configure import/export of routes in the given VNI or VRF using auto-generated RT values. When manual RT is not configured, auto-RT is enabled by default. However, when manual RT values are configured, the import/export of routes using auto-RT has to be configured explicitly (if required). 

Under VNI configuration mode:

```
sonic(config)# router bgp 10
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# vni 1010
sonic(config-router-bgp-af-vni)# route-target both 1:1010
sonic(config-router-bgp-af-vni)# [no] route-target {import|export|both} auto
```

Under BGP VRF L2VPN EVPN configuration mode:

```
sonic(config)# router bgp 10 vrf Vrf-red
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# route-target both 1:10010
sonic(config-router-bgp-af)# [no] route-target {import|export|both} auto
```



Below RT configuration can be used to import routes in the given VNI or VRF ignoring the Global Administrator field in the RTs of the incoming routes.

Under VNI configuration mode:

```
sonic(config-router-bgp-af)# vni 1010
sonic(config-router-bgp-af-vni)# [no] route-target import *:<value>
```

Under BGP VRF L2VPN EVPN configuration mode:

```
sonic(config-router-bgp-af)# [no] route-target import *:<value>
```



**Note:** The user may choose to use auto-RT for both local and remote sites. In that case, there will not be a need to configure manual RTs or explicitly configure auto-RT.



Below route-map option is introduced in KLISH:

```
sonic(config)# router bgp 10 vrf Vrf-red
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# [no] advertise ipv4|ipv6 unicast route-map <rmap>
```

The above command can be used to filter IPv4 and IPv6 routes while exporting into EVPN from BGP VRF address-families.



#### 3.5.3.2 Click commands

All of the BGP commands mentioned in this document will be available in FRR Vtysh as well.



Below are corresponding VxLAN config commands in CLICK:

```
config vxlan external_ip {add|del} <vtep-name> <external-ip>
```

```
config vxlan vni-downstream {add|del} <vtep-name> {external | <remote-vtep-ip>}
```



### 3.5.4 Show Commands

BGP show neighbor command output is updated to show Fabric-external status:

```
BGP neighbor on Ethernet0: fe80::5054:ff:fece:4c39, remote AS 10, local AS 20, external link
Hostname: sonic
  BGP version 4, remote router ID 100.0.0.1, local router ID 2.2.2.2
  BGP state = Established, up for 00:00:45
..

 For address family: IPv4 Unicast
  Update group 3, subgroup 3
  Packet Queue length 0
  Community attribute sent to this neighbor(all)
  0 accepted prefixes

 For address family: L2VPN EVPN
  Update group 4, subgroup 4
  Packet Queue length 0
  Fabric-external
  NEXT_HOP is propagated unchanged to this neighbor
  Community attribute sent to this neighbor(all)
  1 accepted prefixes

  Connections established 3; dropped 2
  ..

```



The below KLISH command will now show the External VTEP IP address and downstream VNI configuration(s):

```
sonic# show vxlan interface

VTEP Name        :  vtep1
VTEP Source IP   :  1.1.1.1
VTEP Primary IP  :  2.2.2.2
VTEP External IP :  10.10.10.10
EVPN NVO Name    :  nvo1
EVPN VTEP        :  vtep1
Source Interface :  Loopback10
Primary IP interface : Loopback20
External IP interface: Loopback30
sonic#
```



Below CLICK command will now show the  External VTEP IP and downstream VNI configuration(s):

```
admin@sonic:~$ show vxlan interface
VTEP Information:

        VTEP Name : vtep1, SIP  : 1.1.1.1
        Primary IP  : 2.2.2.2
        External IP : 10.10.10.10
        NVO Name  : nvo1,  VTEP : vtep1
        Source interface  : Loopback10
        Primary IP interface : Loopback20
        External IP interface: Loopback30
admin@sonic:~$
```





Below show output is enhanced to show:

- Group ("external" or "external") a given remote VTEP belongs to.
- DVNI (Downstream-assigned VNI) enabled or disabled for the given VTEP.

```
leaf2# show vxlan tunnel
Name                SIP               DIP                 source     Group      DVNI   operstatus
=======             ======            ======              ======     ========   ====   ======  
EVPN_1.0.1.1        1.0.1.255         1.0.1.1             EVPN       external   yes    oper_up 
EVPN_1.0.3.1        1.0.1.255         1.0.3.1             EVPN       external   yes    oper_up   
EVPN_1.0.3.255      1.0.1.255         1.0.3.255           EVPN       internal   no     oper_up   
EVPN_1.0.4.1        1.0.1.255         1.0.4.1             EVPN       external   yes    oper_up   
EVPN_1.0.5.1        1.0.1.255         1.0.5.1             EVPN       external   yes    oper_up   
```



Below show output is enhanced to show group ("external" / "internal") from which the IMET route is received from.

```
leaf2# show vxlan remote vni
Vlan       Tunnel      Group     VNI
======   =========     ========  =====
Vlan1001  1.0.3.255    internal  1001
Vlan1001  1.0.5.1      external  100001
Vlan1002  1.0.3.255    internal  1002
Vlan1002  1.0.5.1      external  100002
...
```





Below show output is enhanced to show the group ("external"/"internal") from which given MAC is received from.

```
leaf2# show vxlan remote mac
Vlan      Mac                  Type     Tunnel     Group      VNI
======    ==============       =======  ========   ======     ======
Vlan1001  3c:2c:99:2d:7d:38    dynamic  1.0.3.255  internal  1001
Vlan1001  3c:2c:99:6d:da:4c    dynamic  1.0.3.255  internal  1001
Vlan1002  3c:2c:99:2d:7d:38    dynamic  1.0.3.255  internal  1002
Vlan1002  3c:2c:99:6d:da:4c    dynamic  1.0.3.255  internal  1002
Vlan1003  00:c0:05:31:00:01    dynamic  1.0.5.1    external  10003
Vlan1003  00:c0:05:31:00:02    dynamic  1.0.5.1    external  10003
...
```





### 3.5.5 Debug Commands

Existing debug commands available for BGP/EVPN to be used for multisite as well.


### 3.5.6 REST API Support

The following are the REST URIs for configuring VxLAN external-IP and Downstream-assigned VNI:
    
VxLAN External IP:

curl -v -u admin:broadcom -H "Content-type: application/yang-data+json" -X PATCH https://10.59.135.245/restconf/data/openconfig-interfaces:interfaces/interface=vtep1/openconfig-vxlan:vxlan-if/config/external-ip -k -d "{ \"external-ip\": \"12.12.12.13\" }" -k

curl -v -u admin:broadcom -X DELETE -H "Content-type: application/yang-data+json" https://10.59.135.245/restconf/data/openconfig-interfaces:interfaces/interface=vtep1/openconfig-vxlan:vxlan-if/config/external-ip -k

curl -v -u admin:broadcom -H "Content-type: application/yang-data+json" -X GET https://10.175.126.29:2000/restconf/data/openconfig-interfaces:interfaces/interface=vtep1/openconfig-vxlan:vxlan-if/config/external-ip -k


For Downstream VNI, only SONiC yangs are implemented. 

curl -X POST "https://10.59.135.247/restconf/data/sonic-vxlan:sonic-vxlan/VXLAN_DOWNSTREAM_VNI" -H "accept: */*" -H "Authorization: Basic YWRtaW46YnJvYWRjb20=" -H "Content-Type: application/yang-data+json" -d "{\"sonic-vxlan:VXLAN_DOWNSTREAM_VNI_LIST\":[{\"name\":\"vtep14\",\"ipadd\":\"1.1.1.1\"}]}"     (Use 0.0.0.0 for vni-downstream external)

curl -X DELETE "https://10.59.135.247/restconf/data/sonic-vxlan:sonic-vxlan/VXLAN_DOWNSTREAM_VNI/VXLAN_DOWNSTREAM_VNI_LIST=vtep14,2.3.4.5" -H "accept: */*" -H "Authorization: Basic YWRtaW46YnJvYWRjb20="

curl -X GET "https://10.59.135.247/restconf/data/sonic-vxlan:sonic-vxlan/VXLAN_DOWNSTREAM_VNI/VXLAN_DOWNSTREAM_VNI_LIST" -H "accept: application/yang-data+json" -H "Authorization: Basic YWRtaW46YnJvYWRjb20="




# 4 Flow Diagrams




# 5 Error Handling
No explicit error handling requirement introduced due to this feature.




# 6 Serviceability and Debug
Existing serviceability and debug applicable to this feature. No new debugging commands are added.



# 7 Warm Boot Support
Warm reboot will be supported. No specific changes required for supporting warm-reboot.



# 8 Scalability

No impact to scalability. Existing scale numbers will be supported with this feature.



# 9 Upgrade and Downgrade

Upgrade from previous SONiC release not supporting EVPN Multisite to this release will be supported. The Multisite feature will have to be configured afresh after upgrade.



# 10 Unit Test

## 10.1 Functional Test Cases

**Fabric-external peering and advertisement**

1. Configure 2 internal and 2 fabric-external peers and verify peer is established over BGP unnumbered, IPv4, and IPv6 links.

2. Verify Type-5 routes received from internal peers are advertised to external (and vice-versa) with local nexthop and rmac.

3. Verify Type-2 routes received from internal peers are advertised to external (and vice-versa) with local nexthop and rmac and both of the VNIs.

4. Verify Type-3 routes received from internal peers are not advertised to external (and vice-versa). Only locally originated IMET routes are advertised to external and internal peers.

5. Clear fabric-external and internal peers and verify all of the above routes are still present with corresponding parameters.

6. Remove fabric-external peers and verify routes are removed from internal peers.

7. Re-add fabric-external peers and verify routes are re-advertised to internal peers with correct parameters.

   

**Fabric-external peers route installation **

1. Verify Type-2 routes received from external peers are installed in kernel on external vxlan netdevice and VXLAN_FDB_TABLE has group as external.

2. Verify Type-2 host routes received from external peers are installed in ROUTE_TABLE with group as external.

3. Verify Type-3 routes received from external peers are installed in kernel on external vxlan netdevice and REMOTE_VNI_TABLE has group as external.

4. Verify Type-5 routes received from external peers are installed in ROUTE_TABLE with group as external.

5. Move the MAC from external to internal peer and verify that MAC is moved from external to internal vxlan netdevice and group field is updated in VXLAN_FDB_TABLE.

6. Verify moving the MAC from external to local, internal to local, local to external, and local to internal.

7. Verify Type-2 host routes after the MAC move are updated in ROUTE_TABLE with correct internal group.

8. Advertise Type-5 route from internal peer as well, and verify ECMP is formed in ROUTE_TABLE with external and internal groups

9. Remove Type-5 route from external peer and verify ECMP nexthop is updated in ROUTE_TABLE.
   Reconfigure the same VTEP IP on internal network and verify IMET route is updated.

   

**Downstream assigned VNI**

1. Configure two VTEPs with same VLANs and L3VNI IRB VLANs but different VNI mappings. Configure RTs on the VTEPs to import each other's routes in correct MAC-VRF and IP-VRFs. Verify routes are imported into correct VRFs.
2. Verify Type-2 routes are installed with correct remote VNI.
3. Verify Type-3 routes are installed with correct remote VNI.
4. Verify Type-2 converted host routes are installed with correct remote VNI
5. Verify Type-5 routes are installed with correct remote VNI.
6. Move the Type-2 route to another VTEP with symmetric VNI mapping. And verify Type-2 routes are installed with correct VNI.
7. Verify above with fabric-external peers.
8. Verify forming ECMP for Type-5 with external and internal peers with separate VNI mapping.
9. Move the Type-2 route to another VTEP and verify MAC is updated with correct remote VNI.
10. Move the MAC from remote to local and local to remote and verify MAC is updated with correct VNI.



## 10.2 Negative Test Cases

1. Configure same VTEP IP address on internal and external networks and remove one of them. Verify that Type-2,3,and5 routes from the remaining VTEP are correctly installed.

2. Configure VTEPs with asymmetric VNI mapping without configuring downstream VxLAN configuration. Verify traffic is impacted. Correct the configuration and verify traffic is forwarded correctly.

3. Clear internal and external BGP peers one after another and verify traffic is resumed after sessions are re-established.

   

## 10.3 Scale Test Cases

1. Verify moving 40K MAC from internal to external peers and vice-versa.
2. Verify downstream assigned VNI For 1K VLAN and 40K MACs




## 10.4 Warm boot Test Cases

1. Configure fabric-external peers with internal and external peers with bidirectional traffic. Verify that traffic continues to flow after warm-reboot and fast-reboot.

2. Configure different VLAN to VNI mappings on internal and external peers. Verify that traffic continues to flow after warm-reboot and fast-reboot.

   

# 11 Configuration Example



```
BL node1:
sonic(config)# interface vxlan vtep-1
sonic(conf-if-vxlan-vtep-1)# source-ip 192.168.1.1                 ! Virtual IP
sonic(conf-if-vxlan-vtep-1)# external-ip 10.10.10.10               ! External IP
sonic(conf-if-vxlan-vtep-1)# primary-ip 192.168.1.2                   ! Primary IP
sonic(conf-if-vxlan-vtep-1)# vni-downstream external       ! External VTEPs will have remotely assigned VNI

!! Configure local VLAN-VNI assignemts here !!
..

!! Configure BGP !!
sonic(config)# router bgp 10
sonic(config-router-bgp)# router-id 192.168.1.2
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-af)# advertise-all-vni
sonic(config-router-af)# advertise-pip peer-ip 192.168.1.3
sonic(config-router)# neighbor 192.168.10.1          ! Internal peer
sonic(config-router-bgp-neighbor)# remote-as external
sonic(config-router-bgp-neighbor-af)# address-family l2vpn evpn
sonic(config-router-bgp-neighbor-af)# activate
sonic(config-router)# neighbor 10.1.10.1
sonic(config-router-bgp-neighbor)# remote-as external
sonic(config-router-bgp-neighbor)# address-family l2vpn evpn
sonic(config-router-bgp-neighbor-af)# activate
sonic(config-router-bgp-neighbor-af)# fabric-external     ! External peer

BL node2:
sonic(config)# interface vxlan vtep-2
sonic(conf-if-vxlan-vtep-1)# source-ip 192.168.1.1              ! Virtual IP
sonic(conf-if-vxlan-vtep-1)# external-ip 10.10.10.10            ! External IP
sonic(conf-if-vxlan-vtep-1)# primary-ip 192.168.1.3             ! Primary IP
sonic(conf-if-vxlan-vtep-1)# vni-downstream external      ! External VTEPs will have remotely assigned VNI

!! Configure local VLAN-VNI assignemts here!!
..

!! Configure BGP !!
sonic(config)# router bgp 10
sonic(config-router-bgp)# router-id 192.168.1.3
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# advertise-all-vni
sonic(config-router-bgp-af)# advertise-pip peer-ip 192.168.1.2
!! Add the neighbors here as configured on BL node1
```
