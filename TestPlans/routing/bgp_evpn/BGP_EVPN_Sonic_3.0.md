# SQA Test Plan

# IP Fabric using BGP EVPN
#  SONiC 3.0 Project and Buzznik Release

Rev 1.9

[TOC]

## Test Plan Revision History

| Rev  | Date       | Author                        | Change Description                                    |
| ---- | ---------- | ----------------------------- | ----------------------------------------------------- |
| 1    | 10/01/2019 | Gangadhara Sahu               | Initial Version                                       |
| 2    | 10/07/2019 | Julius                        | Added cases for L2VNI                                 |
| 3    | 10/07/2019 | Gangadhara Sahu               | Added cases for L3 VNI, SAG, ARP Suppression          |
| 4    | 10/11/2019 | Meenal                        | Added cases for LVTEP section                         |
| 5    | 10/18/2019 | Gangadhara, Meenal and Julius | Incorporated L2 VNI, L3 VNI and LVTEP review comments |

## List of Reviewers

| Function | Name |
| :------: | :--: |
|          |      |

## List of Approvers

| Function | Name | Date Approved |
| :------: | :--: | :-----------: |
|          |      |               |

## Definition/Abbreviation

| **Term** | **Meaning**                               |
| -------- | ----------------------------------------- |
| EVPN     | Ethernet VPN                              |
| EVI      | EVPN Instance                             |
| ESI      | Ethernet Segment Identifier               |
| RT       | Route-target                              |
| RD       | Route-distinguisher                       |
| VXLAN    | Virtual Extended LAN                      |
| VTEP     | VXLAN Tunnel End point                    |
| VRF      | Virtual Routing and Forwarding            |
| VNI      | VXLAN Network Identifier                  |
| NVO      | Network Virtualization Overlay            |
| L3GW     | Layer 3 Gateway                           |
| IRB      | Integrated Routing and Bridging           |
| IMET     | Inclusive Multicast Ethernet Tag          |
| BUM      | Broadcast, Unknown unicast, and Multicast |
| BGP      | Border Gateway Protocol                   |
| SHD      | Single-Homed Device                       |
| MHD      | Multi-Homed Device                        |
| ICCP     | Inter-Chassis Communication Protocol      |
## Introduction

### Objective 

The main objective of this document is to cover the test cases that will be executed for IP Fabric based in BGP EVPN Signaling feature in SONIC 3.0 Buzznik release.  Topologies and test cases  for testing the feature will be discussed as part of this document.

### Scope

BGP EVPN Signaling test cases will be covered in this test plan .

### Out of scope

* This test plan does not cover the legacy BGP test cases. 
* This test plan uses the MLAG scenarios and covers EVPN test case including LVTEP having MC-LAG configured. MC-LAG as a feature will be covered in separate MLAG test plan

## Feature Overview

Virtual Extensible LAN (VXLAN) is an overlay technology for network virtualization. It provides Layer 2 extension over a shared Layer 3 underlay infrastructure network by using MAC address in IP User Datagram Protocol (MAC in IP/UDP) tunneling encapsulation. The purpose of obtaining Layer 2 extension in the overlay network is to overcome the limitations of physical server racks and geographical location boundaries and achieve flexibility for workload placement within a data center or between different data centers.

The initial IETF VXLAN standards (RFC 7348) defined a multicast-based flood-and-learn VXLAN without a control plane. It relies on data-based flood-and-learn behavior for remote VXLAN tunnel endpoint (VTEP) peer discovery and remote end-host learning. The overlay broadcast, unknown unicast, and multicast traffic is encapsulated into multicast VXLAN packets and transported to remote VTEP switches through the underlay multicast forwarding. Flooding in such a deployment can present a challenge for the scalability of the solution. The requirement to enable multicast capabilities in the underlay network also presents a challenge because some organizations do not want to enable multicast in their data centers or WAN networks.

To overcome the limitations of the flood-and-learn VXLAN as defined in RFC 7348, organizations can use Multiprotocol Border Gateway Protocol (MP-BGP) Ethernet Virtual Private Network (EVPN) as the control plane for VXLAN. MP-BGP EVPN has been defined by IETF as the standards-based control plane for VXLAN overlays. 

The MP-BGP EVPN control plane provides protocol-based VTEP peer discovery and end-host reachability information distribution that allows more scalable VXLAN overlay network designs suitable for private and public clouds. The MP-BGP EVPN control plane introduces a set of features that reduces or eliminates traffic flooding in the overlay network and enables optimal forwarding for both east-west and north-south traffic.

## 1 Test Focus Areas

### 1.1 Underlay flavors

* IPv4 Unnumbered (using OSPF as routing protocol)
* RFC 5549 
* L3 port-channel - IPv4 numbered
* L3 port-channel - IPv6 numbered
* Physical interface (L3 router port or L3 Port Channel port) /31 subnet
* Untagged L3 VLAN interface (L2 switch port or LAG) with /31 subnet

## 2 Topologies

Topology 1

![bgp_evpn](BGP_EVPN.png "Figure 1: Topology 1")

## 3 Test Case and objectives

### **3.1 CLI Test Cases**

### 3.1.1 Verify all the CLICK based VxlAN CLIs

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify all the CLICK based VxlAN CLIs**                    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **CLI**                                                      |
| **Steps**      | **Verify all the below CLICK based config CLIs**. <br/>config vxlan add <vtepname> <src_ipv4><br/>config vxlan evpn_nvo add <nvoname> <vtepname><br/>config vxlan map add <vtepname> <vlanid> <vnid><br/>config vxlan map_range add <vtepname>  <vlanstart> <vlanend> <vnistart><br/> config vrf add_vrf_vni_map <vrf-name> <vni><br/>config vrf del_vrf_vni_map <vrf-name><br/>config neigh-suppress vlan <vlan-id> <"on"/"off"> <br>**Verify all the below CLICK based show CLIs.** <br/>1. show vxlan interface <br/>2. show vxlan vlanvnimap <br/>3. show vxlan vrfvnimap <br/>4. show vxlan tunnel <br/>5. show vxlan evpn_remote_mac <remoteip/all>  <vlanid/all> <br/>6. show vxlan evpn_remote_vni <remoteip/all> |

### 3.1.2 Verify all the KLISH based VxlAN CLIs

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify all the KLISH based VxlAN CLIs**                    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **CLI**                                                      |
| **Steps**      | Verify all the below config CLIs**. <br/>VTEP Source IP configuration <br/>switch(config) interface vxlan <vtepname> <br/>switch(config-if-vtep1) [no] vxlan source-ip  <src_ipv4>  <vtepname> is a string. <br/><src_ipv4> is an IPV4 address in dotted notation A.B.C.D <br/>EVPN NVO configuration <br/>switch(config) evpn <nvo_name> <br/>switch(config-evpn) nvo <vtepname> <br/><nvoname> and <vtepname> are strings. VLAN VNI Mapping configuration <br/>switch(config-if-vtep1) [no] vxlan map vlan <vidstart> vni <vnistart>  count <n> <br/><n> is the number of mappings being configured.  <br/><vidstart>, <vnistart> are the starting VID and VNID.  <br/>count is optional and when specified maps contigous sets of VIDs to contigous VNIDs<br/>switch(config-if-vtep1) [no] vxlan map vrf VRF-Blue vni 10001 <br/>Verify all the below show CLIs.** <br/>show vlan brief <br/>show mac |
### 3.1.3 Verify configuration replace feature for EVPN underlay and overlay configs

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify configuration replace feature for EVPN underlay and overlay configs** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **CLI**                                                      |
| **Steps**      | Configure EVPN underlay and overlay and save the config using "config save" and "copy running to startup config".  <br/>Say the saved with name as "evpn_config_1". <br/>Make sure traffic is running fine with this EVPN config. <br/>Now change the underlay to different type (from RFC 5549 to IPv4 unnumbered) and change the overlay parameters as well like VTEP address, NVO names and different L2 VNI and L3 VNI config parameters. And save this config as "evpn_config_2". <br/>Make sure all L2 VTEP and L3 VTEP traffic are running fine now. <br/>Move "evpn_config_2" to "evpn_config_1" by using the configration replace feature.<br/>Verify that configuration replace feature is working correctly by checking all the underlay and overlay config and traffic running fine with new configuration in place. |

### **3.2 Functional Test Cases**

### **3.2.1 EVPN Control Plane Test case** ###

### 3.2.1.1 Test BGP EVPN session by removing a neighbor from EVPN address family 

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test BGP EVPN session by removing a neighbor from EVPN address family** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Remove a neighbor from EVPN address family<br/>3) Verify BGP EVPN session comes up for the neighbors configured under l2vpn evpn address family<br/>4) Verify BGP EVPN session doesn’t come up for the neighbors not configured under l2vpn evpn address family<br/>5) Add back the removed neighbor in step2 and verify BGP EVPN session comes up |

### 3.2.1.2 Test VxLAN by removing  the loopback interface bound to overlay gateway

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by removing  the loopback interface bound to overlay gateway** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Remove loopback interface which is bound to overlay gateway on leaf1<br/>3) Check tunnel operational status going down but EVPN routes should not be withdrawn even if tunnel is down<br/>4) Configure same loopback interface to the overlay gateway<br/>5) Check tunnel comes online.<br/>6) Repeat the same case by removing the loopback IP and add back<br/>7) Repeat the same case by removing and adding back different loopback interface to overlay gateway |

### 3.2.1.3 Test VxLAN by removing and adding back NVO name

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by removing and adding back NVO name**          |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration**<br/>**2) Remove NVO name <br/>3) now tunnel should be deleted and overlay route should removed. <br/>4)Now add back the NVO (same or different name) <br/>5) Verify tunnel come up and traffic forwarding works. |

### 3.2.1.4 Test VxLAN by creating vlan after VxLAN map for that VLAN

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by creating vlan after VxLAN map for that VLAN** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)  switch(config-if-vtep1) [no] vxlan map VLAN <vidstart> vni <vnistart>  count <n> <br/> 2) config vlan add VLAN |

###  **3.2.2 L2 VNI Test Cases**

### 3.2.2.1  Test VxLAN by removing and adding back VLAN to VNI mapping while vxlan tunnel is up

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by removing and adding back VLAN to VNI mapping while vxlan tunnel is up** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration**<br/>**2) Remove all the VLAN to VNI mapping on leaf1**<br/>**3) Verify that tunnel is still UP due to availability of other routes like type 5 route b/w leaf nodes**<br/>**4) Configure new VLAN to VNI mapping**<br/>**5) Check tunnel is still up and member of the newly extended VLAN with its VNI id and IMR routes are advertised for the new VLANs. |

### 3.2.2.2  Test VxLAN by removing and adding back RD

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by removing and adding back RD**                |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration**<br/>**2) Remove the RD configuration from leaf1**<br/>**3) Check tunnel gets deleted and routes are withdrawn**<br/>**4) Add back same RD in leaf1**<br/>**5) Check tunnel comes online and routes are advertised again** |

### 3.2.2.3  Test VxLAN by modifying RD

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by modifying RD**                               |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration**<br/>**2) modify the RD configuration in leaf1**<br/>**3) Check tunnel momentarily flaps**<br/>**4) check routes are advertised again with modified RD** |

### 3.2.2.4  Test VxLAN by removing and adding back import RT

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by removing and adding back import RT**         |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration**<br/>**2) Remove the import RT configuration from leaf1**<br/>**3) Check tunnel gets deleted and routes are not imported in leaf1**<br/>**4) Check tunnel status(Up) and routes are imported on remote nodes**<br/>**5) Add back the removed import RT configuration in leaf1**<br/>**6) Check tunnel comes online and routes are imported properly in leaf1** |

### 3.2.2.5  Test VxLAN by configuring wrong import RT

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by configuring wrong import RT**                |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration**<br/>**2) Configure import RT on leaf1 which is different than export RT configured in remote switches**<br/>**3) Check the tunnel is down and no routes imported in leaf1**<br/>**4) Correct the import RT value on leaf1 which is same as export RT configured in remote switches**<br/>**5) Check tunnel comes online and routes are imported properly in leaf1** |

### 3.2.2.6  Test VxLAN by removing and adding back export RT

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by removing and adding back export RT**         |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration**<br/>**2) Remove the export RT configuration from leaf1**<br/>**3) Check tunnel gets deleted and routes are not imported in remote switches**<br/>**4) Check tunnel status(Up) and routes are imported in leaf1**<br/>**5) Add back the removed export RT configuration in leaf1**<br/>**6) Check tunnel comes online and routes are imported properly in remote switches** |

### 3.2.2.7  Test VxLAN by configuring wrong export RT

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by configuring wrong export RT**                |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration**<br/>**2) Configure export RT in leaf1 which is different than import RT configured in remote switches**<br/>**3) Check the tunnel is down and no routes are imported in remote switches**<br/>**4) Correct the export RT value on leaf1 which is same as import RT configured in remote switches**<br/>**5) Check tunnel comes online and routes are imported properly in remote switches** |

### 3.2.2.8  Test VxLAN by configuring mis-match VNI ID

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by configuring mis-match VNI ID**               |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration**<br/>**2) On leaf1, map a VNI id (to VLAN) which is not configured in remote switches under EVPN instance**<br/>**3) Verify IMET routes are generated and advertised to all the switches from leaf1**<br/>**4) Verify no VxLAN tunnel created in this case**<br/>**5) In remote leaf or leaf 2, add the same step 2 configuration having same VNI mapping. <br/>6) Verify tunnel is UP and routes are installed in remote switches |

### 3.2.2.9  Test VxLAN by clearing EVPN neighbor

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by clearing EVPN neighbor**                     |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with required configuration<br/>Clear BGP EVPN neighbors<br/>verify tunnel momentarily flaps<br/>Verify routes are learned again once tunnel comes UP<br/> |

### 3.2.2.10  Test VxLAN by clearing EVPN table

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by clearing EVPN table**                        |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with required configuration<br/>Clear BGP EVPN table (soft-in)<br/>verify tunnel stays UP<br/>Verify routes are refreshed<br/> |

### 3.2.2.11  Test VxLAN by removing redistribute connected from BGP

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by removing redistribute connected from BGP**   |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with required configuration<br/>Remove redistribute connected from BGP in Leaf1<br/>Verify tunnel goes down as VTEP IP is unreachable<br/>Add back redistribute connected to make VTEP reachable<br/>Verify tunnel comes UP and routes are learned properly<br/> |

### 3.2.2.12  Test VxLAN by clearing BGP session with max path 1

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by clearing BGP session with max path 1**       |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with required configuration<br/>Set max path value to 1 for BGP and verify only one path exists to remote VTEP<br/>Start the traffic<br/>Clear the BGP session while the traffic is ON<br/>Verify traffic loss is there until the tunnel comes back online<br/> |

### 3.2.2.13  Test VxLAN by clearing BGP session with max path more than one

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by clearing BGP session with max path more than one** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with required configuration<br/>Set BGP max path value to more than one<br/>verify more than one entry exists to remote VTEP<br/>start the traffic<br/>Clear the BGP session maintained with spine1 while the traffic is ON<br/>Verify traffic switches towards spine2 and tunnel stays operational<br/>Verify there is no traffic loss as alternate path exists<br/> |

### 3.2.2.14  Test VxLAN by deleting and adding back BGP EVPN address family configuration

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by deleting and adding back BGP EVPN address family configuration** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with required configuration<br/>Delete EVPN address family configuration from leaf1<br/>Verify tunnel gets deleted and all routes withdrawn<br/>Add back EVPN address family configuration in leaf1<br/>Verify tunnel comes online and routes are advertised<br/> |

### 3.2.2.15  Test VxLAN by deleting and adding back BGP configuration

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by deleting and adding back BGP configuration** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with required configuration<br/>Delete BGP configuration from leaf1<br/>Verify BGP session not exists and no tunnel<br/>Add back BGP configuration<br/>Verify tunnel comes online and routes are advertised<br/> |

### 3.2.2.16  Test VxLAN for broadcast traffic

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN for broadcast traffic**                         |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with required configuration<br/>Initiate broadcast traffic for one of the VLAN in leaf1<br/>Verify that broadcast traffic is flooded correctly to all remote VTEPs(part of that VLAN) and thereafter to local edge ports<br/>Remove that VLAN from one of the remote VTEP<br/>Verify that broadcast traffic is not flooded to that remote VTEP(VLAN removed)<br/>Add back the removed VLAN on that remote VTEP<br/>Verify that broadcast traffic is flooded now to this remote VTEP along with other remote switches<br/> |

### 3.2.2.17  Test VxLAN for MAC aging and clear MAC

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN for MAC aging and clear MAC**                   |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with required configuration<br/>Start traffic from 10 different VLANs and learn them locally in leaf1<br/>Verify all 10 MACs are advertised to remote VTEPs<br/>Stop the traffic & wait for MAC aging time to expire in leaf1<br/>Verify all 10 MACs are removed from BGP EVPN routing table in leaf1<br/>Start the step2 traffic again & relearn all those 10 MACs<br/>Verify all 10 MACs are advertised to remote VTEPs<br/>Stop the traffic & clear the MAC table in leaf1<br/>Verify all MACs are withdrawn from remote VTEPs.<br/>Use the debug or internal command "bcmcmd l2 show" and ensure that both the local and remote macs are installed in the hardware. <br/>Also we will have unicast bidirectional traffic to ensure that the traffic is not flooded (This will indicate that the mac is programmed correctly in the hw). |

### 3.2.2.18  Test VxLAN by shutting down the links towards downstream

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by shutting down the links towards downstream** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with required configuration<br/>Start traffic from 10 different VLANs and learn them locally on leaf1<br/>Verify all 10 MACs are advertised to remote VTEPs<br/>Shutdown the link towards client from where the MACs are learnt<br/>Verify the MAC’s are withdrawn from all the remote VTEPs<br/>Enable back the shutdown links<br/>Verify all MACs are learned again and advertised to remote VTEPs<br/> |

### 3.2.2.19  Test VxLAN for static MAC

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN for static MAC**                                |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with required configuration<br/>Configure static MAC in leaf1<br/>Verify static MAC is advertised to remote VTEPs<br/>Remove the static MAC<br/>Verify static MAC is withdrawn from remote VTEPs<br/>Verify that traffic is not flooded once static entry is created. <br/>Verify the above steps by shutting down the static MAC pointing interface. |

### 3.2.2.20  Test VxLAN for dynamic MAC movement

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN for dynamic MAC movement**                      |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with required configuration<br/>Learn some MACs locally in leaf1<br/>Stop the traffic towards leaf1, learn the same MACs from another leaf(leaf2)<br/>Verify that leaf2 updates those MACs as local<br/>Verify that leaf1 updates those MACs as remote learned from leaf2<br/>Verify remote to remote MAC move as well. |

### 3.2.2.21  Test VxLAN for static MAC behavior across leaf nodes

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN for static MAC behavior across leaf nodes**     |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with required configuration<br/>configure some static MACs in leaf1<br/>Verify those MAC routes are advertised to all remote VTEPs<br/>Receive traffic with same MAC as SMAC on any one of the remote VTEP(leaf2)<br/>Verify that leaf2 do not learn the same MAC as dynamic entry<br/> |

### 3.2.2.22  Test VxLAN for MAC route dampening

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN for MAC route dampening**                       |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with required configuration<br/>Configure duplicate MAC timer & count as 10 under EVPN instance in leaf1<br/>Learn some MACs locally in leaf1 by sending traffic<br/>Start the same traffic(same MACs) from another leaf(leaf2)<br/>Both leafs 1 & 2 keep sending this MAC move updates 10 times with in the interval specified in mac dampening<br/>Verify that after mac dampening timer, none of leaf1 and leaf2 learns these set of MACs and error log is generated<br/>Traffic forwarding will be stopped for the dampened mac. <br/>Verify that if MAC moves less than 10 times within the timers value configured OR mac moves 10 or more time but after the timer value specified, only duplicate mac event should be triggered without MAC being dampened. |

### 3.2.2.23  Test VxLAN by shutting down link between leaf and spine

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by shutting down link between leaf and spine**  |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with required configuration<br/>Send traffic from leaf1<br/>Verify traffic is load balanced across the 2 spines and reaches all remote VTEPs<br/>Shutdown links between anyone of the spine and leaf<br/>Verify traffic still forwarded through another spine<br/>Bring back all the shutdown ports<br/>Verify traffic is now load balanced across the 2 spines<br/> |

### 3.2.2.24 Verify ping from SVTEP to LVTEP node IP with in extended VLAN boundary 

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify ping from SVTEP to LVTEP node IP with in extended VLAN boundary** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify ping from SVTEP to LVTEP node IP with in extended VLAN boundary <br/>Here source ip and destination ip are with in the same subnet. |

### 3.2.2.25 Verify ping from host 1 connected to LVTEP to host 2 IP present in the extended VLAN

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify ping from host 1 connected to LVTEP to host 2 IP present in the extended VLAN** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify ping from host 1 connected to LVTEP to host 2 IP present in the extended VLAN. <br/>This ping should trigger ARP resolution by ingress leaf node to destination IP leaf node.  <br/> After ARP is resolved, ping traffic should be forwarded <br/> ARP suppression should be kicked in if static anycast gateway is configured on ingress VLAN interface. <br/>Here ping source ip and dest ip are with in same subnet. |

### 3.2.2.26 Test remote macs are not aged out

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test Remote macs are not aged out**                        |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify that the remote macs are not aging out. <br/>check the "bcmcmd l2 show" and ensure that both the local and remote macs are installed in the hw. <br/>Have unicast bidir traffic and ensure that the traffic is not flooded. |

### 3.2.2.27  Test VxLAN by removing and adding back VLAN to VNI mapping which causes tunnel flap

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN by removing and adding back VLAN to VNI mapping which causes tunnel flap** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration**<br/>**2) Remove all the VLAN to VNI mapping on leaf1**<br/>**3) Check tunnel goes down & deleted because there is no other EVPN routes exchanged b/w the leaf nodes and routes are withdrawn**<br/>**4) Configure new VLAN to VNI mapping**<br/>**5) Check tunnel comes online and routes are advertised for the new VLANs. |

### **3.2.3 LVTEP Test Cases**

### 3.2.3.1 LVTEP discovery with Loopback as VTEP

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **LVTEP discovery with Loopback as VTEP**                    |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure loopback ip as VTEP. <br/>3)Verify that the remote leaf node has discovered the LVTEP and the tunnel is up. |

### 3.2.3.2 L2 Traffic from single node leaf node to MLAG leaf node

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **L2 Traffic from single node leaf node to MLAG leaf node**  |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Generate mac routes from MLAG peers on various vlans.<br/>3) Verify mac tables in the MLAG peers and the remote leaf nodes. <br/>4) Generate mac routes from Leaf2 and verify routes in MLAG peers.<br/>5) Send bidirectional L2 traffic between Leaf2 and MLAG leaf node.<br/>6) Verify traffic |

### 3.2.3.3 L2 Traffic from MLAG leaf node to MLAG leaf node

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **L2 Traffic from MLAG leaf node to MLAG leaf node**         |
| **Test Setup** | **Topology 2**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology 2 with required configuration. Leaf1 consists of 2 MLAG peers and Leaf2 also consists of 2 MLAG peers.<br/>2) Generate mac routes from MLAG peers on Leaf1 and MLAG peers on Leaf2 on various vlans ie. MLAG leaf nodes learn the MAC addresses and advertise them to the other Leaf nodes.<br/>3) Verify mac tables in  all the MLAG peers of both leaf nodes.<br/>4) Send bidirectional L2 traffic between Leaf1 and Leaf2. (Both are MLAG leaf nodes)<br/>5) Verify traffic<br />6) Shutdown the uplink of one of the MLAG peers and verify  minimal traffic loss.<br/>7) Unshut the interface and verify traffic <br />8) Shutdown the MLAG member port and verify minimal traffic loss. |

### 3.2.3.4 Asymmetric routing from Single node leaf node to MLAG leaf node

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Asymmetric routing from Single node leaf node to MLAG leaf node** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Create vlans 30 and 40 on MLAG leaf and leaf2 respectively and extend them through evpn.<br/>3) Verify MLAG peers of leaf1 have learnt mac on vlan 40 for the end host connected to leaf2.<br/>3) Send traffic on vlan 40 from host connected to leaf2, destined to host connected to vlan 30 on MLAG leaf node.<br/>4) Verify traffic.<br/>5) Shutdown the uplink of one of the MLAG peers and verify  minimal traffic loss.<br/>6) Unshut the interface and verify traffic <br />7) Shutdown the MLAG member port and verify minimal traffic loss.<br/>8) Clear bgp and verify<br />9) Perform this testcase for both ipv4 and ipv6. |

### 3.2.3.5 Asymmetric routing from MLAG leaf node to single node leaf node

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **L2 Traffic from MLAG leaf node to single node leaf node**  |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Create vlans 30 and 40 on MLAG leaf and leaf2 respectively and extend them through evpn.<br/>3) Verify MLAG peers of leaf1 have learnt mac on vlan 40 for the end host connected to leaf2.<br/>4) Send traffic on vlan 30 from host connected to leaf1 destined to host connected to vlan 40 on leaf2.<br/>5) Verify traffic.<br/>6) Shutdown the uplink of one of the MLAG peers and verify  minimal traffic loss.<br/>7) Unshut the interface and verify traffic <br />8) Shutdown the MLAG member port and verify minimal traffic loss<br />9) Perform this testcase for both ipv4 and ipv6. |

### 3.2.3.6 Asymmetric routing from MLAG leaf node to MLAG leaf node

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Asymmetric routing from MLAG leaf node to MLAG leaf node** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Create vlans 30 and 40 on MLAG leaf and leaf2 respectively and extend them through evpn.<br/>3) Verify MLAG peers of leaf1 have learnt mac on vlan 40 for the end host connected to leaf2.<br/>4) Send traffic on vlan 30 from host connected to leaf1.<br/>5) Verify traffic.<br/>6) Shutdown the uplink of one of the MLAG peers and verify  minimal traffic loss.<br/>7) Unshut the interface and verify traffic <br />8) Shutdown the MLAG member port and verify minimal traffic loss<br />9)  Perform this testcase for both ipv4 and ipv6. |

### 3.2.3.7 Symmetric routing from MLAG leaf node to single node leaf node

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Symmetric routing from MLAG leaf node to single node leaf node** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Learn L3 prefix routes on MLAG peers of Leaf1 and different L3 prefix routes on Leaf2<br/>3) Verify vrf routing table on the MLAG peers of leaf1 has the L3 Prefix routes with SVTEP of Leaf2 as NH.(ie. to reach these prefixes, traffic has to be sent over the tunnel to Leaf2).<br/>4) Verify vrf routing table on Leaf2 has the L3 Prefix routes with LVTEP of Leaf1 as NH.<br/>5) Send traffic from MLAG leaf to Leaf2 prefixes and verify traffic.<br/>6) Shutdown the uplink of one of the MLAG peers and verify  minimal traffic loss.<br/>7) Unshut the interface and verify traffic <br />8) Shutdown the MLAG member port and verify minimal traffic loss<br />9) Perform this testcase for both ipv4 and ipv6 |

### 3.2.3.8 Symmetric routing from single node leaf node to MLAG node leaf node

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Symmetric routing from single node leaf node to MLAG node leaf node** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Learn L3 prefix routes on MLAG peers of Leaf1 and different L3 prefix routes on Leaf2 in VRFs.<br/>3) Verify vrf routing table on the MLAG peers of leaf1 has the L3 Prefix routes with SVTEP of Leaf2 as NH.(ie. to reach these prefixes, traffic has to be sent over the tunnel to Leaf2).<br/>4) Verify vrf routing table on Leaf2 has the L3 Prefix routes with LVTEP of Leaf1 as NH.<br/>5) Send traffic from MLAG leaf to Leaf2 prefixes and verify traffic.<br/>6) Shutdown the uplink of one of the MLAG peers and verify  minimal traffic loss.<br/>7) Unshut the interface and verify traffic <br />8) Shutdown the MLAG member port and verify minimal traffic loss <br />9) Perform this testcase for both ipv4 and ipv6 |

### 3.2.3.9 Symmetric routing from MLAG node leaf node to single node leaf node

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Symmetric routing from MLAG node leaf node to single node leaf node** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Learn L3 prefix routes on MLAG peers of Leaf1 and different L3 prefix routes on Leaf2 in VRFs.<br/>3) Verify vrf routing table on the MLAG peers of leaf1 has the L3 Prefix routes with SVTEP of Leaf2 as NH.(ie. to reach these prefixes, traffic has to be sent over the tunnel to Leaf2).<br/>4) Verify vrf routing table on Leaf2 has the L3 Prefix routes with LVTEP of Leaf1 as NH.<br/>5) Send traffic from MLAG prefixes to leaf2 prefixes and verify traffic.<br/>6) Shutdown the uplink of one of the MLAG peers and verify  minimal traffic loss.<br/>7) Unshut the interface and verify traffic <br />8) Shutdown the MLAG member port and verify minimal traffic loss <br />9) Perform this testcase for both ipv4 and ipv6 |

### 3.2.3.10 Symmetric routing from MLAG node leaf node to MLAG node leaf node

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Symmetric routing from MLAG node leaf node to MLAG node leaf node** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Learn L3 prefix routes on MLAG peers of Leaf1 and different L3 prefix routes on MLAG peers of Leaf2 in VRFs.<br/>3) Verify vrf routing table on the MLAG peers of leaf1 has the L3 Prefix routes with SVTEP of Leaf2 as NH.(ie. to reach these prefixes, traffic has to be sent over the tunnel to Leaf2).<br/>4) Verify vrf routing table on Leaf2 has the L3 Prefix routes with LVTEP of Leaf1 as NH.<br/>5) Send traffic from  leaf1 to leaf2 prefixes and verify traffic.<br/>6) Shutdown the uplink of one of the MLAG peers and verify  minimal traffic loss.<br/>7) Unshut the interface and verify traffic <br />8) Shutdown the MLAG member port and verify minimal traffic loss<br />9) Perform this testcase for both ipv4 and ipv6 |

### 3.2.3.11 L2 Traffic to orphan port from remote leaf nodes

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **L2 Traffic to orphan port from remote leaf nodes **        |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration.<br/>2) Generate mac routes from MLAG peers on various vlans. <br/>3) Generate mac route from orphan port.<br/>4) Verify mac tables in the MLAG peers and the remote leaf nodes.<br/>5) Generate mac routes from Leaf2 and verify routes in MLAG peers.<br/>6) Send bidirectional L2 traffic between Leaf2 and MLAG leaf node.<br/>7) Verify traffic. <br />8) Send multiple streams and verify spine load-balancing is happening -<br />i. Traffic is received on the MLAG leaf where orphan port is connected.<br/>ii. Traffic is received on MLAG peer node which will send traffic over peer-link to its peer to orphan port. |

###  3.2.3.12 Asymmetric Traffic to orphan port from remote leaf nodes

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Asymmetric Traffic to orphan port from remote leaf nodes** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure ve 30 on the orphan port and extend vlan 30 through evpn.<br/>3) Configure vrf and ip configs on ve 30 and configure 'redistribute connected' under bgp vrf.<br/>4) Verify vrf routing tables in the MLAG peers and the remote leaf nodes.<br/>5) Send  traffic from vlan40 of Leaf2 to the host ip on ve30 connected to the orphan port in MLAG leaf node.<br/>6) Verify traffic. |

### 3.2.3.13 Symmetric Traffic to orphan port from remote leaf nodes

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Symmetric Traffic to orphan port from remote leaf nodes**  |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Learn prefix A from the orphan port on vrf red. <br/>3) Verify vrf routing tables in the MLAG peers and the remote leaf nodes. Remote leaf node must show LVTEP as NH.MLAG leaf node must show prefix A with the ip on  the orphan port as NH in the vrf table. <br/>5) Send  traffic from Leaf2 to prefixA.<br/>6) Verify traffic. |

### 3.2.3.14 Traffic through Anycast Gateway within the MLAG

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Traffic through SAG within the MLAG**                      |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure SAG in MLAG peers of Leaf1. <br />3) Create vlans 30 and 40 on MLAG leaf and leaf2 respectively and extend them through evpn.<br />4) Verify MLAG peers of leaf1 have learnt mac on vlan 40 for the end host connected to leaf2.<br />5) Send traffic on vlan 40 from host connected to leaf2 to host on vlan 30 connected to MLAG leaf.<br />6) Verify traffic<br />7) Delete/Modify the SAG ip and verify behavior<br />8) Delete/Modify SAG MAC and verify behavior<br />9) Change the SAG ip to regular ip and vice versa.  Verify behavior. |

### 3.2.3.15 Traffic through Anycast Gateway across the MLAG clusters

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Traffic through Anycast Gateway across the MLAG clusters** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure SAG on MLAG peers of Leaf1 and MLAG peers of Leaf2 . <br />3) Create vlans 30 and 40 on MLAG leaf and leaf2 respectively and extend them through evpn.<br/>4) Verify MLAG peers of leaf1 have learnt mac on vlan 40 for the end host connected to leaf2.<br/>5) Send traffic to host on vlan 40 from host connected to MLAG peers of leaf2.<br/>6) Verify traffic. |

### 3.2.3.16 Traffic forwarding to silent hosts connected to  MLAG leaf node on an MLAG leaf port

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Traffic forwarding to silent hosts connected to  MLAG leaf node on an MLAG leaf port** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Generate mac routes from MLAG peers on various vlans.<br/>3) Create a host on vlan 10 connected to mlag port. This host is yet to start sending any traffic.<br/>4) Verify mac tables in the MLAG peers and the remote leaf nodes.<br/>5) Send L2 traffic between Leaf2 and MLAG leaf node.<br/>6) Verify traffic. |

### 3.2.3.17 Traffic forwarding to silent hosts connected to  MLAG leaf node on an orphan port

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Traffic forwarding to silent hosts connected to  MLAG leaf node on an orphan port** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Generate mac routes from MLAG peers on various vlans.<br/>3) Create a host on vlan 10 connected to mlag port. This host is yet to start sending any traffic.<br/>4) Verify mac tables in the MLAG peers and the remote leaf nodes.<br/>5) Send L2 traffic between Leaf2 and MLAG leaf node.<br/>6) Verify traffic. Verify that flooding happens to resolve the arp and then traffic flows fine. |

### 3.2.3.18 Flapping of the keepalive link when the traffic is on

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Flapping of the keepalive link when the traffic is on**    |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Generate different types of routes from MLAG peers and remote leaf node.<br/>3) Send L2 traffic, asymmetric traffic, symmetric traffic.<br/>4) Verify traffic.<br/>5) Flap the keepalive link within the session timeout period without causing the ICCP session down<br />6) Verify no traffic loss.<br />7) Flap the keepalive link to create ICCP session down scenario.<br />8) Verify session is back up within the expected time and traffic resumes. |

### 3.2.3.19 Track the MLAG port and verify after flapping the port

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Track the MLAG port and verify after flapping the port**   |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Generate different types of routes from MLAG peers and remote leaf node.<br/>3)Track the uplink port of the MLAG.<br/>4) Send L2 traffic, asymmetric traffic, symmetric traffic.<br/>5) Verify traffic.<br/>6) Shutdown all the uplinks to the spine. Verify system brings down all the MLAG interfaces.<br/>7) Verify traffic from MHD to the remote leaves will be sent to MLAG peer node who will directly forward the traffic to the Spines without going through the peer link.<br/>8) Send traffic from remote leaf node destined to SHD attached to the MLAG node that has all the uplink ports down. Verify traffic goes through the MLAG peer node and peer link to reach the SHD<br /> 8) Unshut the uplink port and verify traffic. |

### 3.2.3.20 Verify traffic flows through peer-link when all MLAG ports are down and uplink tracking not enabled

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | Verify traffic flows through peer-link when all MLAG ports are down and uplink tracking not enabled |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Generate different types of routes from MLAG peers and remote leaf node.<br/>3) Send L2 traffic, asymmetric traffic, symmetric traffic.<br/>4) Verify traffic.<br/>5) Shutdown all MLAG interfaces<br/>6) Verify traffic from MHDs are sent to peer MLAG leaf only and traffic flows through the peer link and reaches the right MLAG peer node.<br/>7) Unshut the uplink port and verify traffic. |

### 3.2.3.21 Flapping the ICCP protocol

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Flapping the ICCP protocol **                              |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Generate different types of routes from MLAG peers and remote leaf node. Learn  macs from hosts on different vlans connected to the MLAG peers.<br/>3)Verify mac tables on both MLAG peers. Some macs are learnt locally, some learnt from remote leaf node and some from the peer over ICCP protocol.<br/>4) Send L2 traffic, asymmetric traffic, symmetric traffic.<br/>5) Verify traffic.<br/>6) Flap the ICCP protocol.<br/>7) Verify traffic. Verify MAC table on both the peer nodes. |

### 3.2.3.22 Change the keepalive interface from vlan to physical and vice versa

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Change the keepalive interface from vlan to physical and vice versa** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Generate different types of routes from MLAG peers and remote leaf node.<br/>3) Send L2 traffic, asymmetric traffic, symmetric traffic.<br/>4) Verify traffic.<br/>5) Change the keepalive link from vlan to router port.<br/>6) Verify traffic.<br/>7) Change it back to ve and verify traffic |

### 3.2.3.23 Failure of keepalive link

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Failure of keepalive link**                                |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Generate different types of routes from MLAG peers and remote leaf node.<br/>3) Send L2 traffic, asymmetric traffic, symmetric traffic.<br/>4) Verify traffic.<br/>5) Shutdown the keepalive link.<br/>6) Verify known unicast traffic from remote VTEP to active MLAG node to MHD/SHD is not disrupted.<br/>7) Verify traffic to standby MLAG node to MHD will be dropped since MLAG interface will be down on standby MLAG node.<br />8) Bring up the keepalive link and verify traffic to standby MLAG  node. |

### 3.2.3.24 Verify Peer link failure scenario

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify Peer link failure scenario**                        |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Generate different types of routes from MLAG peers and remote leaf node.<br/>3) Send L2 traffic, asymmetric traffic, symmetric traffic.<br/>4) Verify traffic.<br/>5) Shutdown the peer link.<br/>6) Verify -<br/>i. Known unicast and BUM traffic toward MHD1 whose MLAG interface is up will be fine<br/>ii. Known unicast and BUM traffic toward MHD2 whose MLAG interface down will be dropped (i.e can't use peer link)<br/>iii. Known unicast traffic toward local SHD will be fine<br/>iv. Known unicast traffic toward remote SHD will be dropped7) Shutdown the MLAG interface. Verify traffic drops .<br/>8) Unshut the MLAG interface and verify all traffic flows fine. |

### 3.2.3.25 Changing the control link to carry data traffic as well

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Changing the control link to carry data traffic as well**  |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Change the control link to carry data traffic as well.<br/>3) verify all traffic flows fine (known unicast and BUM towards MHDs, local/remote SHDs)<br/>4) Swap the links and verify all traffic  flows fine. |

### 3.2.3.26 MLAG Error recovery

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **MLAG Error recovery**                                      |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Verify LVTEP is up on the remote leaf node. <br/> 3) Shutdown the keepalive link <br />4) After the MLAG is down, Unshut the keepalive interface.<br />5) Verify LVTEP is up<br />6) Restart ICCP protocol. Verify LVTEP is up. |

### 3.2.3.27 BUM traffic forwarder in an MLAG leaf node

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **BUM traffic forwarder in an MLAG leaf node**               |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Verify system uses local-bias scheme where MLAG node will always floods traffic to MHDs if the MLAG interface is up. <br />3) Shutdown the MLAG interface, verify peer MLAG node floods to the MHD.<br/>3) Verify spine  load-balances BUM traffic received from remote leafa to either MLAG node whose will use local-bias scheme to flood to MHDs<br/>4) Verify BUM traffic is not looping between the nodes <br />5) Verify that BUM traffic received on local VTEP for a VLAN is replicated only to those remote sites/leaf nodes which have the corresponding  vlan of the traffic extended. |

### 3.2.3.28 Duplicate MAC detection should not be supported with in MLAG cluster nodes

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Duplicate MAC detection not be supported with in MLAG cluster nodes** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Learn mac address of a host on vlan 100 connected to 1 of the MLAG peers<br />3) Continuously move the host between the 2 MLAG nodes who will continuously send MAC update to remote leaf nodes. <br />4) Verify mac table. Duplicate mac should NOT be detected <br />5) Also verify mac table in the remote leaf node. <br />6) Remove the host connected to the remote leaf node now<br />7) Verify mac table on all the leaf nodes. |

### 3.2.3.29 MAC Learning from peer node in the MLAG cluster

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **MAC Learning from peer node in the MLAG cluster**          |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Learn mac address of a host on vlan 100 connected to 1 of the MLAG peers<br />3) Verify mac table. It should distinguish mac learnt from client connected to it, mac learnt from orphan ports , macs learnt from peer node and macs learnt from remote leaf<br />4) Move the host to the other MLAG peer<br />5) Verify mac table. Mac should now be learnt through the other peer and not local<br /> |

### 3.2.3.30 Verify Same RD on MLAG peer nodes

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify Same RD on MLAG peer nodes**                        |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Configure same RD under evpn on both nodes of the MLAG and verify behavior.<br/>3) Configure different RD under evpn on both nodes of the MLAG and verify behavior |

### 3.2.3.31 MAC Move detection in an MLAG cluster

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **MAC Move detection in an MLAG cluster**                    |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Learn mac address of a host on vlan 100 connected to 1 of the MLAG peers<br />3) Move the host to the other MLAG peer<br />4) Verify mac table. Mac should now be learnt through the other peer and not local<br />5) Also verify mac table in the remote leaf node. <br />6) Move the host to the remote leaf node now<br />7) Verify mac table on all the leaf nodes. |

### 3.2.3.32 MAC Move detection between MLAG leaf node and a remote leaf node

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **MAC Move detection between MLAG leaf node and a remote leaf node** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Learn mac address of a host on vlan 100 connected to 1 of the MLAG peers<br />3) Move the host to a remote leaf node .<br />4) Verify mac table on the remote leaf node and the 2 MLAG peer nodes<br />5) Move the host back to the MLAG peer node <br />6) Verify mac table on all the leaf nodes. |

### 3.2.3.33 MAC flapping

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **MAC flapping**                                             |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Learn mac address of a host on vlan 100 connected to 1 of the MLAG peers<br />3) Withdraw the mac and relearn the mac for a few times.<br />4) Verify mac table. <br />5) Verify mac table on all the leaf nodes. |

### 3.2.3.34 MAC Dampening

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **MAC Dampening**                                            |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration.<br/>2) Learn mac address of a host on vlan 100 connected to 1 of the MLAG peers.<br />3) Move the host to the other MLAG peer.<br />4) Verify mac table. Mac should now be learnt through the other peer and not local<br />5) Move the host from 1 leaf to another a few more times in quick successions <br />6) Verify that BGP has dampened this route, with other details like number of mac moves with in a given tolerance time.<br />7) Verify mac table on all the leaf nodes.<br />8) Verify after sometime, when the host move has stopped. The route should have been undampened. |

### 3.2.3.35 BGP neighborship flap  in an MLAG cluster

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **BGP neighborship flap  in an MLAG cluster**                |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Learn mac address of a host on vlan 100 connected to 1 of the MLAG peers<br />3) Flap the bgp neighborship between the spine and 1 of the nodes in the MLAG leaf<br />4) Verify all traffic flows through the peer link for a while, until bgp neighborship comes up (or flows from the other spine directly to this node) <br /> 5) Verify mac table on all the leaf nodes.<br /> |

### 3.2.3.36 Vxlan tunnel flapping in an MLAG cluster

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Vxlan tunnel flapping in an MLAG cluster**                 |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Learn mac address of a host on vlan 100 connected to 1 of the MLAG peers<br />3) Flap the vxlan tunnel by removing the vni mapping or by flapping the uplink port<br />4) Verify all routes learnt from this leaf node is marked as stale.<br />5) Verify mac table on all the leaf nodes. |

### 3.2.3.37 Verify BUM traffic received from VxLAN tunnel T1 

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify BUM traffic received from VxLAN tunnel T1           |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)  Verify BUM traffic received from VxLAN tunnel T1 is not flooded back to another VxLAN tunnel T2 <br />2) Verify BUM traffic received from VxLAN tunnel T1 is flooded over peer link <br />3) Verify BUM traffic received from VxLAN tunnel T1 is flooded to SHD and MHD connected to MLAG leaf1 |

### 3.2.3.38 Verify BUM traffic received from peer-link

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify BUM traffic received from peer-link is not flooded back to VxLAN tunnel T1** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify BUM traffic received from peer-link is flooded to SHD <br />2) Verify BUM traffic received from peer-link is not flooded to MHD when MCLAG interface is up on MLAG peer<br />3) Verify BUM traffic received from peer-link is flooded to MHD when MCLAG interface is down on MLAG peer <br />4) Verify BUM traffic received from peer-link is not flooded back to peer-link |

### 3.2.3.39 Verify BUM traffic from SHD

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify BUM traffic from SHD is flooded to SHD, MHD, all VxLAN tunnels and peer-link** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Step**       | 1) Bring up EVPN topology with required configuration<br/>2) Send BUM traffic <br />3) Verify BUM trafifc from SHD is flooded to SHD, MHD, all VxLAN tunnels and peer-link |

### 3.2.3.40 Verify BUM traffic received from MHD is flooded to SHD, other MHD, all VxLAN tunnels and peer-link

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify BUM traffic received from MHD is flooded to SHD, other MHD, all VxLAN tunnels and peer-link** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Send BUM traffic <br />3) Verify BUM traffic from MHD is flooded to SHD, other MHD, all VxLAN tunnels and peer-link |

### 3.2.3.41 Session flap with Single peer-link for both control and data traffic 

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Session flap with Single peer-link for both control and data traffic |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration <br />2) Have a single peer-link for both control and data traffic<br />3) Shutdown this interface.<br />4) Verify ICCP session goes down. Verify traffic to MHD still flows fine, as long as the MLAG interface is up. Note that for SHD, some of it will be drop. It is for the case if remote traffic is received on MLAG2 node and SHD is connected to MLAG1 node.<br />5) Bring up this interface and verify ICCP session is back up and all traffic flows fine<br />6) Bring the ICCP session down and verify the same as above<br />7) Bring the remote MLAG node down.<br />8) Verify traffic to MHD flows completely through the active MLAG node and no traffic loss observed for the MHDs. |

### 3.2.3.42 Traffic when one of the MLAG nodes down

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Traffic when one of the MLAG nodes down **                 |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br />2) Bring the standby MLAG node down. <br />3) Verify no traffic drop on the active node for all the MHDs<br />4) Bring the standby node up and verify ICCP session is up<br />5) Bring the active node down<br />6) There is some traffic drop due to MLAG flap on the standby until it resumes. Verify traffic to MHDs resumes to flow fine without drop after this.<br /> |

### 3.2.3.43 Verify BUM path selection per tunnel for multiple VLANs carrying BUM traffic 

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify BUM path selection per tunnel for multiple VLANs carrying BUM traffic** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Send 10 no of  L2 BUM traffic streams using the 10 extended VLANs . <br/>Check that the same vxlan tunnel is member of all the 10 extended VLAN b/w leaf 1 and leaf2. <br/>Check the BUM patch selection will choose only one path per tunnel out of all the underlay ECMP path. <br/>Verify that when a new vlan is extended or old vlan is deleted or extended back, the same BUM path (as used by other extended VLANs) is used for carrying the BUM traffic. <br/>Shut down the BUM path used by the tunnel and check that all the BUM traffic stream will go through a new BUM path selected by the tunnel. <br/>Bring up the old BUM path which was shut down before. Verify that all the BUM traffic stream will switch over to old BUM patch after old BUM path comes up and current BUM path is made down.<br/>Configure the ECMP path such that BUM path selected as L3 LAG interface. <br/>Verify the BUM replicated packets on LAG interface are load balanced across LAG member ports due to LAG hashing (only criteria used for hashing here is source UDP port because all other fields in inner and outer header will remain same except UDP source port). |

### **3.2.4 L3 VNI Test Cases**

### 3.2.4.1 Verify redistribute of connected and static routes via EVPN

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify redistribute of connected and static routes via EVPN** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Extend L3 VNI across Leaf nodes. <br/>2. Configure redistribution of connected routes into BGP. <br/>3. Verify the connected route advertised to other leaf nodes using EVPN AF.<br/> 4. Verify all the configuration is displayed correctly in the running-config.<br/>5. Verify connected routes are redistributed into BGP correctly for VRF/Address-families.<br/>6. Verify these routes are exported into the BGP EVPN routing table with the correct route-<br/>target community attributes and correct RD value. Use overlapping prefixes as well.<br/>7. Verify these routes are advertised to all EVPN neighbors correctly.<br/>8. Verify that the L3VNI advertised with these routes is correct.<br/>9. Verify that the gateway mac advertised with these routes is correct.<br/>10. Disable redistribution of connected routes into BGP for all the VRF/Address-families.<br/>11. Verify that routes are not present in the BGP VRF routing table.<br/>12. Verify that exported routes are deleted from the BGP EVPN routing table.<br/>13. Verify that these routes are withdrawn from the EVPN neighbors.<br/>14. Re-enable redistribution of connected routes into BGP VRF/AF.<br/>15. Verify that routes are not present in the BGP VRF routing table.<br/>16. Verify that exported routes are deleted from the BGP EVPN routing table.<br/>17. Verify that these routes are withdrawn from the EVPN neighbors.<br/>18. Configure static routes in each of the VRF/AF.<br/>19. Configure redistribution of static routes into BGP for the VRF/AF.<br/>20. Verify that static routes are redistributed correctly into BGP VRF routing table.<br/>21. Verify that these routes are exported into the EVPN routing table correctly.<br/>22. Verify that these routes are advertised to the EVPN neighbors correctly.<br/>23. Disable redistribution of static routes into BGP for the VRF/AF.<br/>24. Verify that routes are not present in the BGP VRF routing table.<br/>25. Verify that exported routes are deleted from the BGP EVPN routing table.<br/>26. Verify that these routes are withdrawn from the EVPN neighbors.<br/>27. Clear the VRF/AF IP routing table.<br/>28. Verify that routes are deleted and re-exported/advertised to all the EVPN neighbors.<br/>29. Clear the BGP VRF/AF routing table.<br/>30. Verify that routes are deleted and re-exported/advertised to all the EVPN neighbors.<br/> |


### 3.2.4.2 Verify redistribute of IPv4 prefix routes via EVPN

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify redistribute IPv4 prefix routes via EVPN**          |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Extend L3 VNI across Leaf nodes. <br/>2. Configure redistribution of IPv4 prefix routes into BGP<br/>.3. Verify the IPv4 prefix route advertised to other leaf nodes using EVPN AF.<br/> 4. Verify all the configuration is displayed correctly in the running-config.<br/>5. Verify IPv4 prefix routes are redistributed into BGP correctly for VRF/Address-families.<br/>6. Verify these routes are exported into the BGP EVPN routing table with the correct route-target community attributes and correct RD value. Use overlapping prefixes as well.<br/>7. Verify these routes are advertised to all EVPN neighbors correctly.<br/>8. Verify that the L3VNI advertised with these routes is correct.<br/>9. Verify that the gateway mac advertised with these routes is correct.<br/>10. Disable redistribution of IPv4 prefix routes into BGP for all the VRF/Address-families.<br/>11. Verify that routes are not present in the BGP VRF routing table.<br/>12. Verify that exported routes are deleted from the BGP EVPN routing table.<br/>13. Verify that these routes are withdrawn from the EVPN neighbors.<br/>14. Re-enable redistribution of IPv4 prefix routes into BGP VRF/AF.<br/>15. Verify that routes are not present in the BGP VRF routing table.<br/>16. Verify that exported routes are deleted from the BGP EVPN routing table.<br/>17. Verify that these routes are withdrawn from the EVPN neighbors.<br/> |
### 3.2.4.3 Verify redistribute of IPv6 prefix routes via EVPN

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify redistribute IPv6 prefix routes via EVPN**          |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Extend L3 VNI across Leaf nodes. <br/>2. Configure redistribution of IPv6 prefix routes into BGP.<br/>3. Verify the IPv6 prefix route advertised to other leaf nodes using EVPN AF. <br/>4. Verify all the configuration is displayed correctly in the running-config. <br/>5. Verify IPv6 prefix routes are redistributed into BGP correctly for VRF/Address-families.<br/>6. Verify these routes are exported into the BGP EVPN routing table with the correct route-target community attributes and correct RD value. Use overlapping prefixes as well.<br/>7. Verify these routes are advertised to all EVPN neighbors correctly.<br/>8. Verify that the L3VNI advertised with these routes is correct.<br/>9. Verify that the gateway mac advertised with these routes is correct.<br/>10. Disable redistribution of IPv6 prefix routes into BGP for all the VRF/Address-families.<br/>11. Verify that routes are not present in the BGP VRF routing table.<br/>12. Verify that exported routes are deleted from the BGP EVPN routing table.<br/>13. Verify that these routes are withdrawn from the EVPN neighbors.<br/>14. Re-enable redistribution of IPv6 prefix routes into BGP VRF/AF.<br/>15. Verify that routes are not present in the BGP VRF routing table.<br/>16. Verify that exported routes are deleted from the BGP EVPN routing table.<br/>17. Verify that these routes are withdrawn from the EVPN neighbors.<br/> |
### 3.2.4.4 Verify deletion of RD configuration for the VRF

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify deletion of RD configuration for the VRF**          |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Verify that exported routes are removed the EVPN routing table<br />2. Verify that routes are withdrawn from the EVPN neighbors.<br/> |

### 3.2.4.5 Verify change of RD configuration for the VRF

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify change of RD configuration for the VRF**            |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Verify that routes advertised with the previous RD are deleted from the EVPN routing table. <br/>2. Verify that routes are withdrawn from the EVPN neighbors. <br/>3. Verify that VRF routes are exported to the EVPN routing table with the new RD value. <br/>4. Verify that routes are advertised to the EVPN neighbors with the new RD value.<br/> |
### 3.2.4.6 Verify change in export route-target for the VRF

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify change in export route-target for the VRF**         |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Verify that routes are NOT deleted from the EVPN routing table. <br />2. Verify that routes are re-advertised to the EVPN neighbors with the new route-target community attributes.<br/> |

### 3.2.4.7 Verify deletion of export route-target for the VRF

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify deletion of export route-target for the VRF**       |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Verify that exported routes are removed the EVPN routing table.<br/>2. Verify that routes are withdrawn from the EVPN neighbors. <br/> |
### 3.2.4.8 Verify change in L3 VNI associated with VRF/AF

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify change in L3 VNI associated with VRF/AF**           |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Verify that the new VLAN is extended correctly.<br/>2. Verify that routes are re-advertised correctly with the new L3VNI and GW mac-address.<br/> |
### 3.2.4.9 Verify deletion of L3 VNI configuration for the VRF

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify deletion of L3 VNI configuration for the VRF**      |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Verify that exported routes are removed the EVPN routing table.<br/>2. Verify that routes are withdrawn from the EVPN<br/>neighbors. |
### 3.2.4.10 Verify best route export to EVPN routing table

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify best route export to EVPN routing table**           |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure two BGP sessions in the VRF AF.<br/>2.Learn same prefixes from these BGP neighbors.<br/>3. Verify that only the best route is exported into the EVPN routing table.<br/>4.Change the best route for these prefixes.<br/>5. Verify that the new best route is exported into the EVPN routing table and the old best route is withdrawn from the EVPN routing table.<br/>6.Verify that the new best route is advertised to the EVPN neighbors.<br/> |
### 3.2.4.11 Verify flap of BGP VRF routes

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify flap of BGP VRF routes**                            |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1.	Verify that  flap of the BGP VRF routes and verify EVPN Prefix advertisements are generated correctly. |

### 3.2.4.12 Verify importing Routes via EVPN

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify importing Routes via EVPN**                         |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Verify all EVPN prefix routes are received and installed in the EVPN routing table.<br/>2. Verify that prefix routes are imported in the appropriate VRF/AF routing table. correctly<br/>3. Verify that imported routes are installed correctly in the IP VRF routing table. <br/>4. Verify that imported routes installed in the IP routing table have the L3 VNI as the nexthop interface. <br/>5. Verify that the imported routes installed in the IP routing table have the GW Mac address as the nexthop mac-address. <br/>6. Delete the import route-target for the VRF.<br/>7. Verify that received EVPN prefix routes are still present in the EVPN routing table.<br/>8. Verify that imported routes are removed from the BGP VRF/AF routing table.<br/>9. Configure a new import route-target for the VRF.<br/>10. Verify that no routes are imported in to the BGP VRF/AF routing table.<br/>11. Configure the original import route-target.<br/>12. Verify that EVPN prefix routes are imported into the BGP VRF/AF routing table.<br/>13. Verify that imported routes are correctly installed in t4e IP VRF/AF routing table.<br/>14. Configure the same import route-target on multiple VRFs154. Verify that routes are imported into each of the BGP VRF/AF routing tables.<br/>15. Verify that routes are installed in each of IP VRF/AF routing tables correctly.<br/> |
### 3.2.4.13 Verify local VRF route is preferred over the imported route

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify local VRF route is preferred over the imported route** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure BGP neighbors in the VRF/AF.<br/>2. Learn the same prefixes that were imported<br/>3. Verify that local VRF route is preferred over the imported route. |
### 3.2.4.14 Verify the new best route is exported via EVPN

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify the new best route is exported via EVPN**           |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure BGP neighbors in the VRF/AF. <br/>2. Learn the same prefixes that were imported. <br/>3.Verify that local VRF route is preferred over the imported route<br/>4.Verify that the new best route is exported via EVPN<br/> |
### 3.2.4.15 Verify clearing of EVPN routing table

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify clearing of EVPN routing table**                    |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Clear the EVPN routing table. Verify that routes are re-imported into the BGP VRF AF routing table.<br/>2. Verify that routes are re-installed in the IP VRF/AF routing table.<br/>3. Learn MAC-IP Routes from remote leaf switches in the EVPN routing table.<br/> |
### 3.2.4.16 Verify symmetric IRB Forwarding

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify symmetric IRB Forwarding**                          |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure traffic devices on Spirent as per the following tables. <br/>2. Configure full mesh of L2 traffic streams for each VLAN. <br/>3. Configure full mesh of IPv4 L3 traffic streams for each VRF. <br/>4. Verify all streams reach the destination. <br/>5. Verify traffic is load balanced across all available links. <br/>6. Toggle Leaf-Spine links and verify traffic forwarding continues with available links. <br/>7. Modify underlay ECMP max-path configuration and verify traffic forwarding continues. <br/>8. Clear the underlay routing table. <br/>9. Verify VxLAN tunnels are operational after routing table is repopulated.  <br/>9. Verify forwarding continues. <br/>10. Verify there is no flooding for L2 streams. <br/>11. Clear the mac-address tables for all vlans. <br/>12. Verify forwarding of L2 streams continues with flooding. <br/>13. Verify flooding stops and unicast switching starts within a reasonable amount of time. <br/>14. Reboot one of the spine switches. <br/>15. Verify that forwarding continues with the remaining spine switches. <br/>16. Verify that load balancing of traffic resumes once the spine switch is up. <br/>17. Verify that streams are forwarded as per symmetric routing. <br/>18. NOTE: Destination VLAN is not part of extended vlan list. <br/>19. Verify bi-direction traffic b/w two leaf nodes.<br/> |


### 3.2.4.17 Verify asymmetric IRB Forwarding

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify asymmetric IRB Forwarding**                         |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure traffic devices on Spirent as per the following tables. <br/>2. Configure full mesh of L2 traffic streams for each VLAN. <br/>3. Configure full mesh of IPv4 L3 traffic streams for each VRF.<br/>4. Verify all streams reach the destination.<br/>5. Verify traffic is load balanced across all available links.<br/>6. Toggle Leaf-Spine links and verify traffic forwarding continues with available links.<br/>7. Modify underlay ECMP max-path configuration and verify traffic forwarding continues.<br/>8. Clear the underlay routing table.<br/>9. Verify VxLAN tunnels are operational after routing table is repopulated.<br/>10. Verify forwarding continues.<br/>11. Verify there is no flooding for L2 streams.<br/>12. Clear the mac-address tables for all vlans.<br/>13. Verify forwarding of L2 streams continues with flooding.<br/>14. Verify flooding stops and unicast switching starts within a reasonable amount of time.<br/>15. Reboot one of the spine switches.<br/>16. Verify that forwarding continues with the remaining spine switches.<br/>17. Verify that load balancing of traffic resumes once the spine switch is up.<br/>18. Verify that streams are forwarded as per asymmetric routing. <br/>19. NOTE: Destination VLAN is part of extended vlan list.<br/>20. Verify bi-direction traffic b/w two leaf nodes. |
### 3.2.4.18 Verify jumbo frames forwarding through IP Fabric

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify jumbo frames forwarding through IP Fabric**         |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VTEPs and discover remote VTEPs via BGP. <br/>2. Send traffic through the VLANs mapped to the EVPN instance and verify end to end traffic is established.<br/>3. Send unidictional and bidirectional traffic.<br/>4. Send traffic with L2, L3 and L4 headers through the L2 path.<br/>5. Perform various triggers and verify that end to end traffic is restored.<br/> |
### 3.2.4.19 Verify MTU value should be supported for Vxlan encapsulation for 9k byte inner packets 

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify MTU value should be supported for Vxlan encapsulation for 9k byte inner packets** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Send 9k byte data packets generated from host end over VxLAN tunnel |

### 3.2.4.20 Verify IP fragmentation for VxLAN encapsulated packets IP Fabric 

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify IP fragmentation for VxLAN encapsulated packets<br/>IP Fabric** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Send traffic with ip header having don’t fragment bit as 0 so that we can verify IP fragmentation when egress L3 MTU configured is less than packet frame size. <br/>2. Send traffic with ip header having don’t fragment bit as 1 so that we can verify IP fragmentation do not kick in even if egress L3 MTU configured is less than packet frame size.<br/> 3. The packets should be dropped and show counters should show drop reason being MTU violation.<br/> |
### 3.2.4.21 Verify IMR generation and Tunnel Membership with IPv4 Prefix route

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify IMR generation and Tunnel Membership with IPv4 Prefix route** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Make sure there is no L2 VNI extended b/w Leaf nodes.  <br/>2. Now extend L3 VNI b/w leaf nodes. <br/>3. Verify that IMR route is generated at source leaf node for ipv4 prefix routes and same is sent to remote leaf node and verify that vxlan tunnel comes up after same IMR is received. <br/> |
### 3.2.4.22 Verify IMR generation and Tunnel Membership with IPv6 Prefix route

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify IMR generation and Tunnel Membership with IPv6 Prefix route** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Make sure there is no L2 VNI extended b/w Leaf nodes.<br />2. Now extend L3 VNI b/w leaf nodes. <br /> 3. Verify that IMR route is generated at source leaf node for ipv6 prefix routes and same is sent to remote leaf node and verify that vxlan tunnel comes up after same IMR is received. <br /> |
### 3.2.4.23 Verify IMR generation and Tunnel VLAN membership with RT modification

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify IMR generation and Tunnel VLAN membership with RT modification** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify IMR generation and Tunnel VLAN membership after RT modification so that tunnel  flaps but comes up successfully. |

### 3.2.4.24 Verify Fast External Failover with EVPN L3 Traffic

| **Test ID**    | **iTAS Test Case Manager ID to be updated**            |
| -------------- | ------------------------------------------------------ |
| **Test Name**  | **Verify Fast External Failover with EVPN L3 Traffic** |
| **Test Setup** | **Topology 1**                                         |
| **Type**       | **Functional**                                         |
| **Steps**      | Verify Fast External Failover with EVPN L3 Traffic     |

### 3.2.4.25 Verify traffic tromboning

| **Test ID**    | **iTAS Test Case Manager ID to be updated** |
| -------------- | ------------------------------------------- |
| **Test Name**  | **Verify Traffic tromboning**               |
| **Test Setup** | **Topology 1**                              |
| **Type**       | **Functional**                              |
| **Steps**      | Verify Traffic tromboning                   |

### 3.2.4.26 Verify slow path Forwarding Scenarios over IP Fabric  

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify slow path Forwarding Scenarios over IP Fabric**     |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | PING from local leaf to remote host across network/vlan.<br />Verify slow path Forwarding Scenarios over IP Fabric. |

### 3.2.4.27 Verify traffic with duplicate ip across across VRF

| **Test ID**    | **iTAS Test Case Manager ID to be updated**            |
| -------------- | ------------------------------------------------------ |
| **Test Name**  | **Verify traffic with duplicate ip across across VRF** |
| **Test Setup** | **Topology 1**                                         |
| **Type**       | **Functional**                                         |
| **Steps**      | Verify traffic with duplicate ip across across VRF     |

### 3.2.4.28 Verify traffic forwarding to Silent Hosts connected to single node leaf node

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify traffic forwarding to Silent Hosts connected to single node leaf node** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify Traffic forwarding to Silent Hosts connected to single node leaf node. |

### 3.2.4.29 Verify traffic forwarding to Silent Hosts connected remote MC-LAG Leaf node port-channel interface

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify traffic forwarding to Silent Hosts connected remote MC-LAG Leaf node port-channel interface** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify traffic forwarding to Silent Hosts connected remote MC-LAG Leaf node port-channel interface. |

### 3.2.4.30 Verify traffic forwarding to Silent Hosts connected to remote MCT leaf node Orphon port

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify traffic forwarding to Silent Hosts connected to remote MC-LAG leaf node Orphon port** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify traffic forwarding to Silent Hosts connected to remote MCT leaf node Orphon port. |

### 3.2.4.31 Verify ping from host 1 connected to LVTEP to host 2 IP connected to SVTEP

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify ping from host 1 connected to LVTEP to host 2 IP connected to SVTEP** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify ping from host 1 connected to LVTEP to host 2 IP connected to SVTEP. <br/>This ping should trigger ARP resolution by ingress leaf node to destination IP leaf node.  <br/> After ARP is resolved, ping traffic should be forwarded over the tunnel with VxLAN encapsulation. <br/> ARP suppression should be kicked in if static anycast gateway is configured on ingress L3 VLAN interface. <br/>Here ping source ip and dest ip are across subnet. |

### **3.2.5 Static Anycast Gateway Test Cases**

### 3.2.5.1 Verify traffic using SAG IP gateway using default mac address 

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify traffic using SAG IP gateway using default mac address** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify traffic using SAG IP gateway using default mac address |

### 3.2.5.2 Verify traffic using SAG IP gateway using user defined mac address

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify traffic using SAG IP gateway using user defined mac address** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify traffic using SAG IP gateway using user defined mac address |

### 3.2.5.3 Verify L3 traffic from LVTEP to SVTEP using IPv4 SAG and vice versa

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify L3 traffic from LVTEP to SVTEP using IPv4 SAG and vice versa** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify L3 traffic from LVTEP to SVTEP using IPv4 SAG and vice versa |

### 3.2.5.4 Verify L3 traffic from LVTEP to SVTEP using IPv6 SAG and vice versa

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify L3 traffic from LVTEP to SVTEP using IPv6 SAG and vice versa** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify L3 traffic from LVTEP to SVTEP using IPv6 SAG and vice versa |

### **3.2.6 ARP/ND suppression Test Cases**

### 3.2.6.1 Verify ARP/ND packet forwarding over VxLAN tunnel 

| **Test ID**    | **iTAS Test Case Manager ID to be updated**           |
| -------------- | ----------------------------------------------------- |
| **Test Name**  | **Verify ARP/ND packet forwarding over VxLAN tunnel** |
| **Test Setup** | **Topology 1**                                        |
| **Type**       | **Functional**                                        |
| **Steps**      | Verify ARP/ND packet forwarding over VxLAN tunnel     |

### 3.2.6.2 Verify local VTEP proxy ARP and ND response when it is learnt on remote VT

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify local VTEP proxy ARP and ND response when it is learnt on remote VT ** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify local VTEP proxy ARP and ND response when it is learnt on remote VT |

### 3.2.6.3 Verify VTEP will not do a proxy reply when request comes from local host for another local host learnt locally

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify VTEP will not do a proxy reply when request comes from local host for another local host learnt locally  ** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify VTEP will not do a proxy reply when request comes from local host for another local host learnt locally. |

### **3.2.7 Unit D Scale Testing** 

### 3.2.7.1  Test VxLAN for max number of 4K VLANs

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN for max number of 4K VLANs**                    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with 4K VLANs<br />Send traffic randomly for few VLANs<br />Do some triggers and verify traffic<br />Triggers like clearing the neighbors, shutting the links b/w leaf & spine, clearing routes |

### 3.2.7.2  Test VxLAN for max number of 40K MACs

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN for max number of 40K MACs**                    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with 40K MACs<br />Send traffic randomly for few MACs<br />Do some triggers and verify traffic<br />Triggers like clearing the neighbors, shutting the links b/w leaf & spine, clearing MAC/routes |

### 3.2.7.3  Verify scale no of VRFs used for L3 VNI is 1000

| **Test ID**    | **iTAS Test Case Manager ID to be updated**         |
| -------------- | --------------------------------------------------- |
| **Test Name**  | **Verify scale no of VRFs used for L3 VNI is 1000** |
| **Test Setup** | **Topology 1**                                      |
| **Type**       | **Functional**                                      |
| **Steps**      | Verify number of VRFs supported for L3 VNI is 1000  |

### 3.2.7.4  Verify scale test for SAG IPv6 addresses on leaf node with and without user defined GW mac

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify scale test for SAG IPv6 addresses on leaf node with and without user defined GW mac** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify scale test for SAG IPv6 addresses on leaf node with and without user defined GW mac. |

### 3.2.7.5  Verify scale test for SAG IPv4 addresses on leaf node with and without user defined GW mac 

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify scale test for SAG IPv4 addresses on leaf node with and without user defined GW mac** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify scale test for SAG IPv4 addresses on leaf node with and without user defined GW mac. |

### 3.2.7.6  Verify max Ipv4 route supported in TD3 is 81k

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify max Ipv4 route supported in TD3 is 81k**            |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify max Ipv4 route supported in TD3 is 81k. <br/>This scale no of route applicable for underlay and overlay network together. |

### 3.2.7.7  Verify max Ipv6 route supported in TD3

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify max Ipv6 route supported in TD3**                   |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify max Ipv6 route having mask less than and equal to /64 (or ipv6 address size <=64b) is 32K in TD3. <br/>Verify max Ipv6 route having mask more than /64 (or ipv6 address size <=64b) is 25K in TD3.<br/>This scale no of route applicable for underlay and overlay network together. |

### 3.2.7.8  Verify max no of vxlan tunnels supported in TD3 is 1024

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify max no of vxlan tunnel supported in TD3 box is 1024** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify max no of vxlan tunnels supported in TD-3 box is 1024. |

### **3.2.8 RFC 5549 Underlay Test cases**

### 3.2.8.1 Verify RFC 5549 underlay with BFD enabled running traffic across IPv6 link specific trigger

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify RFC 5549 underlay with BFD enabled running traffic across IPv6 link specific trigger** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up the RFC 5549 underlay using the ipv6 link local. <br/>Send multiple L2 and L3 traffic stream so that we can verify all the underlay paths are used for carrying ipv6 traffic. <br/>Apply the IPv6 link specific triggers like clear ND table and re-learn. <br/>Flap one or more underlay paths or add one more underlay path so that we can verify the traffic is re-routed through other available path.  <br/>Verify RFC 5549 underlay by running IPv4 and IPv6 overlay traffic.<br/>Verify the link failure triggers BFD event and route is converged to next best path fast enough due to BFD event. |

### **3.2.9 IPv4 unnumbered with OSPF enabled Test case**

### 3.2.9.1 Verify IPv4 unnumbered underlay running OSPF and BFD with traffic across generic trigger

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify IPv4 unnumbered underlay running OSPF and BFD with traffic across generic trigger** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up the IPv4 Unnumbered underlay using OSPF protocol as IGP and enabled BFD on top of it. <br/>Send multiple L3 and L3 traffic stream so that we can verify all the underlay paths are used for carrying ipv6 traffic. <br/>Apply the IPv4 link specific triggers like clear ARP table and re-learn ARP. <br/>Flap one or more underlay paths or add one more underlay path so that we can verify the traffic is re-routed through other available path.<br/>Verify IPv4 unnumbered underlay by running IPv4 and IPv6 overlay traffic.<br/>Verify the link failure triggers BFD event and route is converged to next best path fast enough due to BFD event. |

### **3.2.10 All other underlay test case (L3 Port-Channel, Router Port & L3 VLAN Interfaces)**

### 3.2.10.1 Verify all other underlay with traffic across generic trigger

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify underlay ECMP with IPv4 and IPv6 traffic**          |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up the underlay using L3 port-channel link and /31 ipv4 address and mix of other underlays like router port and L3 VLAN interface having either /31 or /127 mask or regular mask /24 ipv4 mask and /64 ipv6 mask. <br/>Send multiple L3 and L3 traffic stream so that we can verify all the underlay paths are used for carrying ipv6 traffic. <br/>Apply the link specific triggers like clear ARP table and re-learn ARP. Similarly clear ND table and re-learn ND entries. <br/>Flap one or more underlay paths or add one more underlay path so that we can verify the traffic is re-routed through other available path. <br/>Verify underlay by running IPv4 and IPv6 overlay traffic. |

### **3.2.11 ECMP Test cases**

### 3.2.11.1 Verify underlay ECMP with IPv4 and IPv6 traffic and triggers

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify underlay ECMP with IPv4 and IPv6 traffic**          |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Send multiple ipv4 traffic stream so that we can verify all the underlay paths are used for carrying ipv4 traffic. <br/>Send multiple ipv6 traffic stream so that we can verify all the underlay paths are used for carrying ipv6 traffic.<br/>Flap one or more underlay paths so that we can verify the traffic is re-routed through other available path. <br/>Change the ECMP path number or BGP multi path number to see if it is taking effect for IPv4 traffic and IPv6 traffic stream separately.<br/>Here we are changing the ECMP path value from multiple to multiple.<br/>Verify underlay ECMP by running IPv4 traffic only <br/>Verify underlay ECMP by running IPv6 traffic only. <br/>Apply the triggers like underlay link flap, bgp session clear, underlay routing flap, spine/leaf reboot. <Br/>Apply each one trigger 10 times and than go to next trigger and cover all the trigger so as to stress the Leaf or Spine node. |

### 3.2.11.2 Verify overlay ECMP path with IPv4 and IPv6 traffic 

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify overlay ECMP path with IPv4 and IPv6 traffic**      |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Extend the L3 VNI from LVTEP node to two SVTEP nodes SVTEP 1 and SVTEP 2. <br/>Now have the same IPv4 subnet (say L3 interface with15.1.1.1/24 in Vrf1) advertised by LVTEP  and  SVTEP 1(say L3 interface with15.1.1.2/24 in Vrf1) to SVTEP 2 in the same VRF which is extended through the L3 VNI. <br/> Now SVTEP 2 will show multiple overlay ECMP paths for the same prefix 15.1.1.0/24 one ECMP path pointing VTEP in LVTEP node 1 and second path pointing to VTEP in LVTEP node 2 and third ECMP path pointing to LVTEP in SVTEP 1. |

### 3.2.11.3 Verify underlay ECMP for BUM traffic and triggers

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify underlay ECMP for BUM traffic**                     |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Send multiple L2 BUM traffic stream so that we can verify all the underlay paths are used for carrying the traffic. <br/>Flap one or more underlay paths so that we can verify the traffic is re-routed through other available path. <br/>Change the ECMP path number or BGP multi path number to see if it is taking effect for IPv4 traffic and IPv6 traffic stream separately.<br/>Here we are changing the ECMP path value from multiple to multiple.<br/>Verify underlay ECMP by running BUM IPv4 and IPv6 traffic separately. <br/>Apply the triggers like underlay link flap, bgp session clear, underlay routing flap, spine/leaf reboot. <br/>Apply each one trigger 10 times and than go to next trigger and cover all the trigger so as to stress the Leaf or Spine node. |

### 3.2.11.4 Verify change in underlay ECMP path to single path and vice versa

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify change in underlay ECMP path to single path and vice versa** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Send multiple unicast IPv4 or IPv6 traffic stream. <br/>Verify that all the underlay paths are used for carrying the sent traffic <br/>Change the ECMP path number to 1 or single path now to see if it is taking effect for IPv4 traffic and IPv6 traffic stream separately.<br/>Verify that only one underlay path is used for carrying the sent traffic even if there are multiple physical underlay paths available <br/>Here we are changing the ECMP path value from multiple to single and vive versa.<br/>Now we can repeat the test by change the single path to multiple equal cost paths <br/>Verify above steps for IPv4 and IPv6 traffic separately. |

### **3.3 Negative**

### 3.3.1 Negative Test Cases

### 3.4 Reboot/Reload/Upgrade Test Cases

### **3.4.1 Warm reboot and fast reboot**

### 3.4.1.1 Verify L2VPN EVPN configuration across warm reboot

| **Test ID**    | **iTAS Test Case Manager ID to be updated**            |
| -------------- | ------------------------------------------------------ |
| **Test Name**  | **Verify L2VPN EVPN configuration across warm reboot** |
| **Test Setup** | **Topology 1**                                         |
| **Type**       | **Functional**                                         |
| **Steps**      | Verify L2 VPN EVPN configuration across warm reboot    |

### 3.4.1.2 Test VxLAN with warm reboot

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN with warm reboot**                              |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with 40K MAC and 4K VLANs<br />Send traffic for few VLANs<br />Trigger warm reboot<br />After reboot, verify routes are learned and no loss in traffic. |

### 3.4.1.3 Warm reboot of the MLAG peer

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Warm reboot of the MLAG peer**                             |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Generate different types of routes from MLAG peers and remote leaf node.<br/>3) Send L2 traffic, asymmetric traffic, symmetric traffic.<br/>4) Verify traffic.<br/>5) Warm Reboot 1 of the MLAG peers.<br/>6) Verify tunnel is still up on the remote leaf.<br/>7) Verify traffic. |
### 3.4.1.4 Test L2 VTEP, L3 VTEP traffic across fast reboot

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN with warm reboot**                              |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with L2 VTEP and L3 VTEP.<br />Have SVTEP and LVTEP in the topology. <br/>Send traffic for few VLANs<br />Apply fast reboot command<br />After reboot, verify routes are learned and no loss or minimal in traffic. |

### **3.4.2  Cold reboot**

### 3.4.2.1 Verify EVPN configuration across cold reboot after saving the configuration.

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify EVPN configuration across cold reboot after saving the configuration** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify EVPN configuration across cold reboot after saving the configuration |

### 3.4.2.2 EVPN tunnel when 1 node reboots out of two MLAG leaf nodes

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **EVPN tunnel when 1 node reboots out of two MLAG leaf nodes** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Generate different types of routes from MLAG peers and remote leaf node.<br/>3) Send L2 traffic, asymmetric traffic, symmetric traffic.<br/>4) Verify traffic.<br/>5) Reboot 1 of the MLAG peers.<br/>6) Verify tunnel is still up on the remote leaf.<br/>7) Verify traffic. Verify that traffic is load-balancing from Spine to the rebooted MLAG node after it comes back up. |

### **3.4.3 Config reload**

### 3.4.3.1 Verify EVPN configuration across cold reboot after saving the configuration.

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify EVPN configuration across config reload after saving the configuration** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify EVPN configuration across config reload after saving the configuration |

### **3.4.4  Docker restart**

### 3.4.4.1  Test VxLAN with SwSS docker restart

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN with SwSS docker restart**                      |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with 40K MAC and 4K VLANs<br />Send traffic for few VLANs<br />Trigger SwSS docker restart<br />After reboot, verify routes are learned and no loss in traffic |

### 3.4.4.2  Test VxLAN with BGP docker restart

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN with BGP docker restart**                       |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with 40K MAC and 4K VLANs<br />Send traffic for few VLANs<br />Trigger BGP docker restart<br />After reboot, verify routes are learned and no loss in traffic |

### 3.4.4.3  Test VxLAN with teamd docker restart

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test VxLAN with teamd docker restart**                     |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Bring up EVPN topology with 40K MAC and 4K VLANs<br />Send traffic for few VLANs<br />Trigger teamd docker restart<br />After reboot, verify routes are learned and no loss in traffic |

### 3.4.4.4 Verify L2 VPN EVPN configuration across FRR/BGP docker restart

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify LVPN EVPN configuration across cold reboot after saving the configuration** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify L2VPN EVPN configuration across cold reboot after saving the configuration. |

### 3.4.4.5 ICCP docker restart and start/stop protocol

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **ICCP docker restart and start/stop protocol**              |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with required configuration<br/>2) Generate different types of routes from MLAG peers and remote leaf node.<br/>3) Send L2 traffic, asymmetric traffic, symmetric traffic.<br/>4) Verify traffic.<br/>5) ICCP docker restart on 1 of the peers.<br/>6) Verify tunnel is still up on the remote leaf.<br/>7) Verify traffic. |

### 3.4.4.6 Verify orchagent docker restart

| **Test ID**    | **iTAS Test Case Manager ID** to be updated                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify orchagent docker restart**                          |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify orchagent docker restart <br/> Traffic should be resumed after the operation. |

### **3.4.5  Upgrade Test cases**

### 3.4.5.1 Verify EVPN configuration across image upgrade after saving the configuration

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify EVPN configuration across image upgrade after saving the configuration** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify EVPN configuration across image upgrade after saving the configuration. |

### 3.4.5.2 Verify BGP docker upgrade

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify BGP docker upgrade**                                |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify EVPN configuration across BGP docker upgrade after saving the configuration. <br/>Verify the traffic should be resumed. |

### 3.4.5.3 Verify orchagent docker upgrade

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify orchagent docker upgrade**                          |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify EVPN configuration across orchagent upgrade after saving the configuration. <br/>Verify the traffic should be resumed. |

### 3.4.5.4 Verify teamd docker upgrade 

| **Test ID**    | **iTAS Test Case Manager ID to be updated**                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify teamd docker upgrade**                              |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify EVPN configuration across teamd upgrade after saving the configuration. <br/>Verify the traffic should be resumed. |

### 3.5 Management

### 3.5.1 SNMP

### 3.5.2 gNMI

### 3.5.3 REST APIs

### 3.6 Stress,Scale and Performance 

### 3.5.1 Add Stress Scale and Performance test cases here

## **Reference Links**

[SONiC EVPN L2 VxLAN HLD link]::https://docs.google.com/document/d/1yikKYBkx19UVG84UxrJoBhvbjI1Q6hOhLiTnc1XeXv8/edit
[Nephos MLAG HLD]: https://github.com/Azure/SONiC/blob/f478fe7cbc03c144f3b147e9638f460f764ce4b7/doc/Sonic-mclag-hld.md
[Broadcom MLAG Enhancement HLD]:http://10.59.132.240:9009/projects/csg_sonic/ln406662/HLD/MLAG/MCLAG_HLD.md(https://www.google.com/url?q=http%3A%2F%2F10.59.132.240%3A9009%2Fprojects%2Fcsg_sonic%2Fln406662%2FHLD%2FMLAG%2FMCLAG_HLD.md&sa=D&ust=1568696065534000&usg=AFQjCNGK0_yRDRF9ZeMH1YGXQhz31tOAsg)
[LVTEP HLD]:http://gerrit-lvn-07.lvn.broadcom.net:8083/plugins/gitiles/sonic/documents/+/refs/changes/47/11347/1/EVPN/LVTEP/EVPN_LVTEP_HLD.md

