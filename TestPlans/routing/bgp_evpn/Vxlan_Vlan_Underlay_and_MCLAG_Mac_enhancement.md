# SQA Test Plan

# VXLAN Vlan Underlay support and MCLAG MAC Enhancements
#  SONiC 3.1.0 Project and Buzznik Plus Release
[TOC]

## Test Plan Revision History

| Rev  | Date       | Author            | Change Description |
| ---- | ---------- | ----------------- | ------------------ |
| 1    | 05/07/2020 | Nagappa Chincholi | Initial Version    |
|      |            |                   |                    |
|      |            |                   |                    |

## List of Reviewers

| Function | Name |
| :------: | :--: |
|          |      |

## List of Approvers

| Function | Name | Date Approved |
| :------: | :--: | :-----------: |
|          |      |               |

## Definition/Abbreviation

| **Term** | **Meaning**                 |
| -------- | --------------------------- |
| EVPN     | Ethernet VPN                |
| EVI      | EVPN Instance               |
| ESI      | Ethernet Segment Identifier |
| RT       | Route-target                |
| RD       | Route-distinguisher         |
## Introduction

### Objective 

The main objective of this document is to cover the test cases that will be executed for BGP EVPN with Vlan interface as an underlay in SONIC 3.1 Buzznik Plus release.  Topologies and test cases  for testing the feature will be discussed as part of this document.

### Scope

BGP EVPN Signaling test cases will be covered in this test plan .

### Out of scope

* This test plan does not cover the legacy BGP EVPN test cases. 
* This test plan uses the MLAG scenarios and covers EVPN test case including LVTEP having MLAG configured. MLAG as a feature will be covered in separate MLAG test plan

## Feature Overview

The MP-BGP EVPN control plane provides protocol-based VTEP peer discovery and end-host reachability information distribution that allows more scalable VXLAN overlay network designs suitable for private and public clouds.  Support for VLAN interface as underlay for Vxlan is added in this release.VXLAN traffic is forwarded only on one VLAN interface per physical port or LAG. If the Leaf loses connectivity to its spine group, Leaf node will form the VXLAN tunnel using Vlan interface through the peer Leaf node to remote VTEP.

MCLAG system MAC support - As part of MCLAG enhancement user is allowed to configure MCLAG system MAC address which overrides the auto generated MCLAG system MAC. In the absence user configuration, auto generated MCLAG system MAC will be used in LACP PDU's of MCLAG PortChannels. User configured MCLAG system MAC will take precedence over the auto generated system MAC. 

## 1 Test Focus Areas

### 1.1 Underlay flavors

* IPv4 Unnumbered
* RFC 5549
* L3 port-channel - Ipv4 numbered
* L3 port-channel - Ipv6 numbered
* Physical interface /31 subnet
* Ve interface /31 subnet

## 2 Topologies





![BGP_EVPN](BGP_EVPN.PNG "Figure 1: BGP_EVPN")
* Configure between Spine and Leaf - 3 types of ports 
*  - Physical router port
*  - Vlan as tagged member of  physcial port
*  - Vlan as tagged member of PortChannel
## 3 Test Case and objectives

### 3.1 Functional Test Cases

#### 3.1.1 EVPN Control Plane Test case

###### 3.1.1.1 Tagged Vlan interface as underlay between lvtep nodes 

| **Test ID**    | **FtOpSoRoEvpnVlanFt001**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Tagged Vlan interface as underlay between lvtep nodes**    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Bring down links towards spine on Leaf1 and verify vxlan tunnel stays up with next hop pointing to Vlan interface V1. |

###### 3.1.1.2 Untagged Vlan interface as underlay between lvtep nodes

| **Test ID**    | **FtOpSoRoEvpnVlanFt002**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Untagged Vlan interface as underlay between lvtep nodes**  |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as untagged. <br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Bring down links towards spine on Leaf1 and verify vxlan tunnel stays up with next hop pointing to Vlan interface V1. |

###### 3.1.1.3 iBGP EVPN with Vlan interface between LVTEP.

| **Test ID**    | **FtOpSoRoEvpnVlanFt003**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** iBGP EVPN with Vlan interface between LVTEP**             |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up iBGP EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as untagged. <br/>3) Configure iBGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Bring down links towards spine on Leaf1 and verify vxlan tunnel stays up with next hop pointing to Vlan interface V1. |

###### 3.1.1.4 L2 VNI traffic over Vlan interface between LVTEP.

| **Test ID**    | **FtOpSoRoEvpnVlanFt004*                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **L2 VNI traffic over Vlan interface between LVTEP**         |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Bring down links towards spine on Leaf1 and verify vxlan tunnel stays up with next hop pointing to Vlan interface V1.<br/>6) Add L2 VNIs and verify traffic over Vlan interface V1. |

###### 3.1.1.5 L3 VNI traffic over Vlan interface between LVTEP.

| **Test ID**    | **FtOpSoRoEvpnVlanFt005**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **L3 VNI traffic over Vlan interface between LVTEP**         |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Bring down links towards spine on Leaf1 and verify vxlan tunnel stays up with next hop pointing to Vlan interface V1.<br/>6) Add L3 VNIs and verify routes and traffic over Vlan interface V1. |

###### 3.1.1.6 Multiple Vlan interface between Lvtep nodes.

| **Test ID**    | **FtOpSoRoEvpnVlanFt006**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Multiple Vlan interface between Lvtep nodes.**             |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1,V2,V3 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Bring down links towards spine on Leaf1 and verify vxlan tunnel stays up with next hop pointing to Vlan interface.<br/>6) Switch between V1 to v2 by removing vlan membership both ends and verify tunnel stays up. <br/>7) Switch between V2 to v3 by removing ip of vlan interface v2 on both ends. <br/>8) Verify with L2,L3 traffic. |

###### 3.1.1.7 Verify Asymmetric IRB forwarding with Vlan interface.

| **Test ID**    | **FtOpSoRoEvpnVlanFt007**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** Verify Asymmetric IRB forwarding with Vlan interface**    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Configure for asymmetric routing and verify with L3 traffic.<br/>6) Bring down links towards spine on Leaf1 and verify vxlan tunnel stays up with next hop pointing to Vlan interface V1. |

###### 3.1.1.8 Verify Symmetric IRB forwarding with Vlan interface.

| **Test ID**    | **FtOpSoRoEvpnVlanFt008**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify Symmetric IRB forwarding with Vlan interface**      |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Configure for symmetric routing and verify with L3 traffic.<br/>6)Bring down links towards spine on Leaf1 and verify vxlan tunnel stays up with next hop pointing to Vlan interface V1. |

###### 3.1.1.9 Enable MCLAG Unique IP with Vlan interface between lvtep nodes 

| **Test ID**    | **FtOpSoRoEvpnVlanFt009**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Enable MCLAG Unique IP with Vlan interface between lvtep nodes** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Configure Vlans as mclag members and enable unique ip.<br/>6)Bring down links towards spine on Leaf1 and verify vxlan tunnel stays up with next hop pointing to Vlan interface V1. |

###### 3.1.1.10 Enable  SAG and Vlan interface  between lvtep nodes 

| **Test ID**    | **FtOpSoRoEvpnVlanFt010**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Enable  SAG and Vlan interface  between lvtep nodes**      |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Configure Vlans as mclag members and enable SAG.<br/>6)Bring down links towards spine on Leaf1 and verify vxlan tunnel stays up with next hop pointing to Vlan interface V1. |

###### 3.1.1.11 Verify  BUM traffic with Vlan interface between lvtep nodes

| **Test ID**    | **FtOpSoRoEvpnVlanFt011**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify  BUM traffic with Vlan interface between lvtep nodes** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Bring down links towards spine on Leaf1 with BUM traffic between lvtep and svtep. |

###### 3.1.1.12 Verify IPv6 Asymmetric IRB forwarding with Vlan interface.

| **Test ID**    | **FtOpSoRoEvpnVlanFt012**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify IPv6 Asymmetric IRB forwarding with Vlan interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Configure IPv6 asymmetric routing and send L3 traffic from tgen. <br/>6) Bring down links towards spine on Leaf1 with IPv6 traffic between lvtep and svtep. |

###### 3.1.1.13 Verify IPv6 Symmetric IRB forwarding with Vlan interface.

| **Test ID**    | **FtOpSoRoEvpnVlanFt013**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify IPv6 Symmetric IRB forwarding with Vlan interface.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Configure IPv6 symmetric routing and send L3 traffic from tgen. <br/>6) Bring down links towards spine on Leaf1 and verify vxlan tunnel stays up with next hop pointing to Vlan interface V1. |

###### 3.1.1.14 BGP docker restart

| **Test ID**    | **FtOpSoRoEvpnVlanFt014**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **BGP docker restart**                                       |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5)  Bring down links towards spine on Leaf1 and verify vxlan tunnel stays up with next hop pointing to Vlan interface V1.<br/>6) Initiate BGP docker restart and verify there is no traffic drop. |

###### 3.1.1.15 Enable link tracking   

| **Test ID**    | **FtOpSoRoEvpnVlanFt015**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Enable link tracking for all mclag ports **                |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Enable link tracking for all mclag ports on Leaf1. <br/>6) Send traffic between lvtep mclag and svtep host .<br/>7)Bring down links towards spine on Leaf1 <br/>8) verify orphan ports are brought down but the traffic between lvtep and svtep is not affected. |

###### 3.1.1.16 Clear BGP session with vlan interface  enabled 

| **Test ID**    | **FtOpSoRoEvpnVlanFt016**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Clear BGP session with vlan interface  enabled**           |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Bring down links towards spine on Leaf1 and verify vxlan tunnel stays up with next hop pointing to Vlan interface V1.<br/>6) clear bgp session and verify bgp reconvergence. |

###### 3.1.1.17 Config save and reboot 

| **Test ID**    | **FtOpSoRoEvpnVlanFt017**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Config save and reboot **                                  |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5) Bring down links towards spine on Leaf1 and verify vxlan tunnel stays up with next hop pointing to Vlan interface V1.<br/>6) Config save and reboot and verify the tunnel status and traffic. |

###### 3.1.1.18 IPv6 link local as underlay 

| **Test ID**    | **FtOpSoRoEvpnVlanFt018**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **User configured MCLAG system mac**                         |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with IPv6 link local as underlay <br/>2) Configure Vlan V1 and add to peer interface between Lvtep nodes as tagged.<br/>3) Configure BGP neighobrship between lvtep nodes on Vlan V1. <br/>4) Verify BGP session comes up between lvtep nodes.<br/>5)Bring down links towards spine on Leaf1 and verify vxlan tunnel stays up with next hop pointing to Vlan interface V1. |


#### 3.1.2 MCLAG System MAC 

###### 3.1.2.1 User defined MCLAG System MAC 

| **Test ID**    | **FtOpSoRoEvpnSysMacFt001**                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **User defined MCLAG System MAC**                            |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up MCLAG setup <br/>2) Configure MCLAG portchannels towards client.<br/>3) Configure system MAC address. <br/>4) Verify configured system MAC is updated in MCLAG LACP PDUs. <br/>5) Unconfigure system MAC and verify LACP PDUs are updated with Active MCLAG node system MAC.<br/> 6) Bring down a MCLAG portchannel. <br/> 7) Configure a different system MAC and verify LACP PDUs are updated with configured system MAC.<br/> 8) Bring up the MCLAG portchannel.<br/> 9) Verify LACP PDUS use the configured system mac. |

###### 3.1.2.2 Verify Orphan Portchannels are not affected by User defined MCLAG system mac

| **Test ID**    | ***FtOpSoRoEvpnSysMacFt002**                                 |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** Verify Orphan Portchannels are not affected by User defined MCLAG system mac** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up MCLAG setup <br/>2) Configure MCLAG portchannels towards client.<br/>3) Configure system MAC address. <br/>4) Verify configured system MAC is updated in MCLAG LACP PDUs. <br/>5) Verify orphan portchannels and peer link portchannel use system mac and not user defined MCLAG system mac. |


###### 3.1.2.3 User defined MCLAG System MAC with L3 Portchannel

| **Test ID**    | **FtOpSoRoEvpnSysMacFt003**                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **User defined MCLAG System MAC with L3 Portchannel**        |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up MCLAG setup <br/>2) Configure MCLAG L3 portchannels towards client.<br/>3) Configure system MAC address. <br/>4) Verify configured system MAC is updated in MCLAG LACP PDUs. <br/>5) Unconfigure system MAC and verify LACP PDUs are updated with Active MCLAG node system MAC.<br/> 6) Configure a different system MAC and verify LACP PDUs are updated with configured system MAC. |

###### 3.1.2.4 Reboot with user defined MCLAG System MAC config

| **Test ID**    | **FtOpSoRoEvpnSysMacFt004**                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Reboot with user defined MCLAG System MAC config**         |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up MCLAG setup <br/>2) Configure MCLAG portchannels towards client.<br/>3) Configure system MAC address. <br/>4) Verify configured system MAC is updated in MCLAG LACP PDUs. <br/>5) Unconfigure system MAC and verify LACP PDUs are updated with Active MCLAG node system MAC.<br/> 6) Save config and reboot both active and standby MCLAG nodes and verify mclag system macs. |

###### 3.1.2.5 MCLAG toggle  with user defined  System MAC config

| **Test ID**    | **FtOpSoRoEvpnSysMacFt005**                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **MCLAG toggle  with user defined  System MAC config**       |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up MCLAG setup <br/>2) Configure MCLAG portchannels towards client.<br/>3) Configure system MAC address. <br/>4) Verify configured system MAC is updated in MCLAG LACP PDUs. <br/>5) Unconfigure system MAC and verify LACP PDUs are updated with Active MCLAG node system MAC.<br/> 6) shut/no shut MCLAG and verify mclag system macs. <br/> 7) shut/no shut PEER Link and verify mclag system macs |

###### 3.1.2.6 Config Reload with user defined MCLAG System MAC 

| **Test ID**    | **FtOpSoRoEvpnSysMacFt006**                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Reboot with user defined MCLAG System MAC config**         |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up MCLAG setup <br/>2) Configure MCLAG portchannels towards client.<br/>3) Configure system MAC address. <br/>4) Verify configured system MAC is updated in MCLAG LACP PDUs. <br/>5) Unconfigure system MAC and verify LACP PDUs are updated with Active MCLAG node system MAC.<br/> 6) Save config and config reload and verify mclag system macs. |

#### 3.1.3 Peer MCLAG node reboot 

###### 3.1.3.1 Active node reboot 

| **Test ID**    | **FtOpSoRoEvpnRebootFt001**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Active node reboot**                                       |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up MCLAG setup <br/>2) Configure MCLAG portchannels towards client.<br/>3) Verify Active node system MAC is used in MCLAG LACP PDUs. <br/>4) Send traffic between Lvtep and Svtep.<br/> 5) Reboot Active node.<br/> 6) Verify MCLAG portchannels does not flap and LACP sytem mac is still Active node system mac. <br/> 7) Verify MAC and ARP info is retained and there is no traffic disruption . |

###### 3.1.3.2 Ungraceful shutdown of Active node  

| **Test ID**    | **FtOpSoRoEvpnRebootFt002**                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Active node reboot**                                       |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up MCLAG setup <br/>2) Configure MCLAG portchannels towards client.<br/>3) Bring down Acitve node ungracefully either by power cycling or killing a process. <br/>4) Send traffic between Lvtep and Svtep.<br/> 5) Reboot Active node.<br/> 6) Verify MCLAG portchannels flap and no core files observed. |

###### 3.1.3.3 Active node shutdown 

| **Test ID**    | **FtOpSoRoEvpnRebootFt003**                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Active node shutdown**                                     |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up MCLAG setup <br/>2) Configure MCLAG portchannels towards client.<br/>3) Verify Active node system MAC is used in MCLAG LACP PDUs. <br/>4) Send traffic between Lvtep and Svtep.<br/> 5) Shutdown Active node.<br/> 6) Verify Standby node behavoir when Active node does not come back online in 5 minutes. <br/> 7) Verify no core files or unexpected behavior is observed on standby node. |

###### 3.1.3.4 Active node reboot with Maintenance mode 

| **Test ID**    | **FtOpSoRoEvpnRebootFt004**                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Active node shutdown**                                     |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up MCLAG setup <br/>2) Configure MCLAG portchannels towards client and enable maintenance mode and configure graceful shutdown under BGP.<br/>3) Configure MCLAG system mac, MCLAG gateway mac. <br/>4) Send traffic between Lvtep and Svtep.<br/> 5) Reboot Active node.<br/> 6) Verify Standby node behavoir when Active node is rebooted. <br/> 7) Verify no core files or unexpected behavior is observed on standby node. |

#### 3.1.4 MCLAG Gateway MAC 

###### 3.1.4.1 Configure MCLAG Gateway MAC 

| **Test ID**    | **FtOpSoRoEvpnMclagGwFt001**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **User configured Gateway MAC for vlan interface**           |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure MCLAG clients.<br/> 3) Configure MCLAG gateway mac address. <br/> 4) Verify configured gateway mac is advertised in Type-5 evpn routes. <br/> 5) Verify with MCLAG member failover with IPv4 and IPv6 traffic between hosts connected to LVTEP and SVTEP.Verify there is no traffic drop. <br/> 6) Unconfigure gateway MAC and verify default behavior is restored.<br/> 7) Modify Gateway MAC and verify type-5 evpn routes are updated . |

###### 3.1.4.2 Configure  MCLAG Gateway MAC and reboot  

| **Test ID**    | **FtOpSoRoEvpnMclagGwFt002**                                 |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **User configured Gateway MAC for vlan interface**           |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure MCLAG clients.<br/> 3) Configure MCLAG gateway mac address. <br/> 4) Verify configured gateway mac is advertised in Type-5 evpn routes. <br/> 5) Verify with MCLAG member failover with IPv4 and IPv6 traffic between hosts connected to LVTEP and SVTEP.Verify there is no traffic drop. <br/> 6) Config save and reboot Active node .<br/> 7) Verify  configured gateway mac once the node is up. |

###### 3.1.4.3 Configure  MCLAG Gateway MAC and SAG

| **Test ID**    | **FtOpSoRoEvpnMclagGwFt003**                                 |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Configure MCLAG gateway and SAG **                         |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure MCLAG clients.<br/> 3) Configure MCLAG gateway mac address and SAG over same VLAN. <br/> 4) Verify configured gateway mac is advertised in Type-5 evpn routes. <br/> 5) Verify with MCLAG member failover with IPv4 and IPv6 traffic between hosts connected to LVTEP and SVTEP.Verify there is no traffic drop. <br/> 6) Config save and reboot Active node .<br/> 7) Verify  configured gateway mac once the node is up. |



###### 

### 3.3  Upgrade

##### 3.3.1 Verify EVPN configuration across image upgrade after saving the configuration

| **Test ID**    | **FtOpSoRoEvpnVlanFt300 to be updated**                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify EVPN configuration across image upgrade after saving the configuration** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify EVPN configuration across image upgrade after saving the configuration. |

## 3.4 Management

#### 3.4.1 SNMP

#### 3.4.2 gNMI

#### 3.4.3 REST API

###### 3.4.3.1 User defined MCLAG System MAC 

| **Test ID**    | **FtOpSoRoEvpnVlanRestFt001**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **User defined MCLAG System MAC**                            |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up MCLAG setup <br/>2) Configure MCLAG portchannels towards client.<br/>3) Use POST,GET, DELETE and verify MCLAG system mac. |

###### 3.4.3.2 User defined MCLAG gateway  MAC 

| **Test ID**    | **FtOpSoRoEvpnGwMACRestFt001**                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **User defined MCLAG gateway MAC **                          |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up MCLAG setup <br/>2) Configure MCLAG portchannels towards client.<br/>3) Use POST,GET, DELETE and verify gateway mac. |

## 3.6 Stress,Scale and Performance

#### 3.5.1 Add Stress Scale and Performance test cases here

## Reference Links
[SONiC VLAN underlay support HLD link]::http://gerrit-lvn-07.lvn.broadcom.net:8083/c/sonic/documents/+/20526/7/L3/EVPN/EVPN+VXLAN+HLD.md
[SONiC MCLAG_SYSTEM_MAC HLD link]::http://gerrit-lvn-07.lvn.broadcom.net:8083/c/sonic/documents/+/20429/5/MCLAG/mclag_system_mac/MCLAG_SYSTEM_MAC_L3_GW_MAC_HLD.md
[SONiC EVPN L2 VxLAN HLD link]::https://docs.google.com/document/d/1yikKhttp://gerrit-lvn-07.lvn.broadcom.net:8083/c/sonic/documents/+/20526/7/L3/EVPN/EVPN+VXLAN+HLD.md
YBkx19UVG84UxrJoBhvbjI1Q6hOhLiTnc1XeXv8/edit
[Nephos MLAG HLD]: https://github.com/Azure/SONiC/blob/f478fe7cbc03c144f3b147e9638f460f764ce4b7/doc/Sonic-mclag-hld.md
[Broadcom MLAG Enhancement HLD]:http://10.59.132.240:9009/projects/csg_sonic/ln406662/HLD/MLAG/MCLAG_HLD.md(https://www.google.com/url?q=http%3A%2F%2F10.59.132.240%3A9009%2Fprojects%2Fcsg_sonic%2Fln406662%2FHLD%2FMLAG%2FMCLAG_HLD.md&sa=D&ust=1568696065534000&usg=AFQjCNGK0_yRDRF9ZeMH1YGXQhz31tOAsg)
[LVTEP HLD]:http://gerrit-lvn-07.lvn.broadcom.net:8083/plugins/gitiles/sonic/documents/+/refs/changes/47/11347/1/EVPN/LVTEP/EVPN_LVTEP_HLD.md(https://www.google.com/url?q=http%3A%2F%2Fgerrit-lvn-07.lvn.broadcom.net%3A8083%2Fplugins%2Fgitiles%2Fsonic%2Fdocuments%2F%2B%2Frefs%2Fchanges%2F47%2F11347%2F1%2FEVPN%2FLVTEP%2FEVPN_LVTEP_HLD.md&sa=D&ust=1568696065534000&usg=AFQjCNExWCQN1vuvpedN2LsVJY-wFPjPug)

