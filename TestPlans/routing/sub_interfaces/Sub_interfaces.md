SQA Test Plan

# Routed Sub-Interfaces
#  SONiC 3.2.0 Release

Rev 1.9

[TOC]

## Test Plan Revision History

| Rev  | Date       | Author                        | Change Description                                    |
| ---- | ---------- | ----------------------------- | ----------------------------------------------------- |
| 1    | 11/22/2020 | Sooriya Gajendrababu | Initial Version                                       |
|     |            |            |                    |
|      |            |            |                    |
|      |            |            |                    |
|      |            |            |                    |
|      |            |            |                    |

## List of Reviewers

| Function | Name |
| :------: | :--: |
|          |      |

## List of Approvers

| Function | Name | Date Approved |
| :------: | :--: | :-----------: |
|          |      |               |

## Definition/Abbreviation

| **Term** | **Meaning**                    |
| -------- | ------------------------------ |
| VRF      | Virtual routing and forwarding |
| RIF      | Router interface               |
|          |                                |
## Introduction

### Objective 

The main objective of this document is to cover the test cases that will be executed for Sub port Interface.  Topologies and test cases  for testing the feature will be discussed as part of this document.

### Scope

Only Sub port interface serving as an interface to VRF is covered

### Out of scope

* This test plan does not cover creating and using sub port as a bridge port to a .1D bridge


## Feature Overview

A sub port interface is a logical interface that can be created on a physical port or a port channel. A sub port interface serves as an interface to either a .1D bridge or a VRF, but not both.

Multiple L3 sub port interfaces, each characterized by a VLAN id in the 802.1q tag, can be created on a physical port or a port channel. Sub port interfaces attaching to the same physical port or port channel can interface to different VRFs, though they share the same VLAN id space and must have different VLAN ids. Sub port interfaces attaching to different physical ports or port channels can use the same VLAN id, even when they interface to the same VRF. However, there is no L2 bridging between these sub port interfaces; each sub port interface is considered to stay in a separate bridge domain.



## 1 Test Focus Areas

### 1.1 Functional Requirements

* Manage the life cycle of a sub port interface created on a physical port or a port channel and used as a router interface to a VRF:

  - Creation with the specified dot1q vlan id encapsulation
  - Runtime admin status change
  - Removal

  A sub port interface shall support the following features:

  - L3 forwarding
  - BGP
  - ARP and NDP
  - VRF
  - RIF counters
  - QoS setting inherited from parent physical port or port channel
  - mtu inherited from parent physical port or port channel
  - Per sub port interface admin status config

## 2 Topologies

![sub-interface](sub_interface.png "Figure 1: Topology 1")



## 3 Test Case and objectives

### **3.1 CLI Test Cases**

### 3.1.1 Verify all the config/show CLIs for sub interface

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify all the config/show CLIs for sub interface**        |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **CLI**                                                      |
| **Steps**      | Verify config/delete cli for sub interface with all options both in click and klish<br/>Verify show output returns sub interface operational states <br/>Verify out of range encapsulation id throws suitable error <br/> Suitable error should be thrown <br/>Verify adding parent interface to Portchannel throws error<br/>Try adding physical sub interfaces to Portchannel and verify config throws error<br/>Verify enabling L2 protocol on parent interface throws error <br/> |

### 3.1.2 Verify all the Sub interface CLIs using OCI Yang/Rest api's.

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify all the Sub interface CLIs using OCI Yang/Rest api's.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **CLI**                                                      |
| **Steps**      | Verify all the below config CLIs for sub interfaces using Rest ocyang URIs. <br/>Verify sub interface operational configs via OCYANG GET URIs<br />Verify sub interface config removal using DELETE URIs<br/> |



### 3.1.3 Verify running-config for sub interface configs.

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify running-config for sub interface configs**          |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **CLI**                                                      |
| **Steps**      | 1)Configure multiple sub interfaces over both physical and portchannel interfaces<br/>2)Verify "show running config" displays all the sub interface configs<br/>3)Delete the configs and verify it is removed from running config |

### 3.2 Functional Test Cases**



### 3.2.1 To verify basic sub interface routing created over physical port

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | To verify basic sub interface routing created over physical port |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure 2 hosts connecting to D2(L2 switch) one on Vlan 100(100.1.1.2 and 1001::2) and other on Vlan200(200.1.1.2 and 2001::2)<br/>2) Configure D2 ports towards Router D1 as trunk member for Vlan 100 and Vlan200<br/>3)On D1(Router), Create two sub interfaces for the physical port connecting to D2 with encapsulation id 100,200 respectively<br/>4) Configure ip/ipv6 addresses addresses for these sub interfaces created over physical port 100.1.1.1 , 1001::1 and 200.1.1.1,2001::1 respectively<br/>5)Send L3 ipv4 and ipv6  between Host1 and Host2 and Verify D1 routes the traffic over sub interfaces |

### 3.2.2  To verify basic sub interface routing created over Lag port



| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **To verify basic sub interface routing created over Lag port** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure 2 hosts connecting to D2(L2 switch) one on Vlan 300(300.1.1.2 and 3001::2) and other on Vlan400(400.1.1.2 and 4001::2)<br/>2) Configure D2 ports towards Router D1 as trunk member for Vlan 100 and Vlan2003
3)On D1(Router), Create two sub interfaces for the portchannel port connecting to D2 with encapsulation id 100,200 respectively
4) Configure ip/ipv6 addresses addresses for these sub interfaces created over lag port 300.1.1.1 , 3001::1 and 300.1.1.1,3001::1 respectively
5)Send L3 ipv4 and ipv6  between Host1 and Host2 and Verify D1 routes the traffic over sub interfaces<br/>6) Delete Parent PortChannel interface with sub interfaces configured and verify all parent and sub interfaces gets deleted |





### 3.2.3  To verify sub port from different physical/lag ports can map to same vrf

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **To verify sub port from different physical/lag ports can map to same vrf** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure a sub interface on physical interface with encap id 100 and a sub interface on lag interface with encap id 200<br/>2) Assign both sub interfaces to same user-vrf Vrf1.<br/>3) Verify D1 routes traffic between sub interfaces belonging to different parent interfaces<br/> |

### 3.2.4  To verify sub port from same parent port map to different VRFs

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To verify sub port from same parent port map to different VRFs** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure two sub interfaces on single parent port with encap id as 100 and 200 respectively  and on lag port create 2 sub interfaces<br/>2) Assign first sub interface to Vrf1 and second sub interface to Vrf2 on both physical and lag ports<br/>3) Verify sub interfaces are assigned to corresponding VRFs and verify traffic gets forwarded within VRFs |



### 3.2.5  To verify same sub port from different parent ports map to different VRFs

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To verify same sub port from different parent ports map to different VRFs** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interfaces with same id on two different parent ports<br/> 2) Assign first sub interface on first parent interfaces  to Vrf1 and sub interface on second parent interface  to Vrf2 <br/>3) Verify sub interfaces are assigned to corresponding VRFs and verify traffic gets forwarded within VRFs<br/> |



### 3.2.6  To Verify sub ports on different parent ports can have same encapsulation id with BUM traffic

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify sub ports on different phy/lag ports can have same encapsulation id** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interface with encap id 100 on both physical and Portchannel parent ports <br/>2)Verify sub interface creation is successful<br/>3)Send L2 traffic with Vlanid 100 same as sub interface id and verify L2 bridging does not happen between the sub interfaces with same encap id<br/>4) Configure Router Tgen port as L2 port on Vlan 100 and send BUM traffic from router Tgen and verify traffic not flooded to sub interfaces with encap id 100<br/>5) Configure SVI interface over Vlan 100 with ip and ipv6 address and verify BUM flooding did not happen<br/>6)Delete the Vlan id 100 which is same as encap-id and verify sub interface configs are intact |



### 3.2.7  To Verify creating sub interface with port id  and encapsulation id different

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify creating sub interface with port id  and encapsulation id different** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure Sub interface on Ethernet100 with port-id as 20 and encapsulation id as 10<br/>2) Configure one more sub interface on Eth100 with port-id as 10 and encapsulation id as 20<br/>3)Bind it to Vrf and assign ip/ipv6 addressess<br/>4)Verify L3 traffic routing between the sub interfaces<br/>5) Delete the  Ethernet100.10 and verify the other sub interface is intact<br/>6)Recreate Ethernet100.20 encap-id 10 nd verify Traffic forwarding resumes |



### 3.2.8  To Verify port admin status flap for sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify port admin status flap for sub interfaces**      |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure multiple sub interfaces on both physical and Portchannel parent ports <br/>2)Verify admin down of parent port brings down all sub interfaces<br/>3)Verify admin up of parent interfaces brings all sub interfaces to admin up<br/>4)Verify sub interface state can be administratively controlled separately and it does not affect parent port state and other sub interfaces on the same parent port<br/>5)Verify sub interfaces goes oper down when parent port goes oper down<br/>6)Bring back all parent and sub interfaces  and verify all ports are Oper-up<br/>7)Bring down the parent interface oper-down by shutting the link on remote DUT<br/>8)Verify  parent and sub interface state goes oper-down<br/>9) Configure Portchannel minimum link to  3 for the parent interface and verify both parent and sub interfaces goes oper down |



### 3.2.9  To Verify parent physical/lag port shows aggregate stats of sub ports

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify parent physical/lag port shows aggregate stats of sub ports** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure multiple sub interfaces on both physical and Port channel parent ports <br/>2) Send L3 traffic between sub interfaces<br/>3) Verify parent interface statistics shows aggregate count from all of its sub interfaces <br/> |



### 3.2.10  To Verify interface stats at sub interface level

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify interface stats at sub interface level**         |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure multiple sub interfaces on both physical and Port channel parent ports <br/>2) Send L3 traffic between sub interfaces<br/>3) Verify Sub interfaces counter statistics are shown correctly for each sub interface <br/>4) Verify counters on other sub interface belonging to same parent interface do not increment |

### 3.2.11  To Verify primary and secondary  ip and ipv6 address configs on sub interfaces 

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify primary and secondary  ip and ipv6 address configs on sub interfaces** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure multiple sub interfaces on both physical and Port channel parent ports <br/>2) Configure primary and secondary ipv4 addressed on sub interfaces and verify L3 routing <br/>3)Verify "show ip/ipv6 interfaces" displays the IP, VRF and oper-state for the sub interfaces<br/>4)Delete and re-add configured IP addresses and verify configs are getting deleted and re-applied<br/>5)Configure multiple Ipv6 addresses on Sub interfaces and verify L3 routing works <br/>6)Delete and reconfigure ipv6 addresses and verify configs are getting deleted and re-applied<br/>7)Configure ipv6 link local address on sub interface and verify it gets assigned after sub interface link flap<br/>8)Configure overlapping/duplicate IP/IPv6 addresses and subnets over multiple parent and sub interfaces and verify it gets rejected |

### 3.2.12  To Verify ipv6 disable/enable and verify link-local assignment on sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify ipv6 disable/enable and verify link-local assignment on sub interfaces** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interfaces on phy/lag ports<br/>2)Disable ipv6 globally and verify it is disabled for all sub interfaces configured<br/>3)Re-enable ipv6 globally and verify sub interfaces will be assigned with auto-generated ipv6 link-local address<br/>4)Configure link-local address explicitly on sub interfaces and verify it is getting assigned<br/> |



### 3.2.13  To Verify functionality of switching from user-vrf to default for sub interfaces and parent interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify Verify functionality of switching from user-vrf to default for sub interfaces** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interfaces on phy/lag ports<br/>2)Bind the sub interfaces to user-vrf with all possible L3 configs on sub interfaces<br/>3)Do L3 configs on parent interface also on Vrf2<br/>3) Verify L3 routing between hosts in different sub interfaces<br/>4)Unbind Vrf1 from sub interface and verify all L3 configurations gets deleted under the sub interface<br/>5) Rebind Vrf1 to sub interface and configure IP/Ipv6 configurations<br/>6)Verify L3 traffic gets routed on Vrf1 between sub interfaces<br/>7) Unbind VRF configs from parent interface and verify sub interface configs are intact and routing between sub interfaces works |



### 3.2.14  To Verify IP unnumbered on sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify IP unnumbered on sub interfaces**                |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure two loopback interfaces with ip address 100.1.1.1/32 and 200.1.1.1/32 on user vrf Vrf1<br/>2) Create 2 sub interfaces on Physical interfaces with encap id as 100 and 200<br/>3)Configure the sub interfaces as ip unnumbered interface with loopback interface as donor<br/>4)Verify sub interface gets the ip addresses from the donor interface<br/>5)Verify L3 routing works between ip unnumbered sub interfaces |



### 3.2.15  To Verify MTU configs on parent and sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify MTU configs on sub interfaces**                  |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure multiple sub interfaces on physical and Lag interfaces<br/>2)Verify default MTU size on sub interfaces gets inherited from parent interface<br/>3)Configure non default MTU sizes on parent interfaces and verify MTU sizes gets inherited to sub interfaces<br/>4)Send L3 packets for different packet sizes and verify traffic gets fragmented as per MTU sizes on sub interfaces<br/>5)Configure different MTU sizes for each sub interfaces on same parent interface and verify each sub interface has its configured MTU value as 1200,1900 bytes<br/>6)Send L3 stream exceeding MTU size of sub interface and verify fragmentation happens at sub interface level |

### 3.2.16  To Verify static and dynamic  ARP/ND on sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify static and dynamic ARP/ND on sub interfaces**    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interfaces on phy/lag port <br/>2)Assign Ipv4 and Ipv6 addresses on sub interfaces<br/>3) Resolve ARP and ND from hosts connected to L2 switch<br/>4)Verify arp and ND table show resolved ip and ipv6 entries for the sub interfaces<br/>5)Configure static ARP and ND on sub interfaces and verify the table<br/>6)Perform "Clear arp" and "clear ndp" and verify dynamic entries are relearnt |



### 3.2.17  To Verify ping and traceroute towards from and to IP/IPv6s configured on Sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify ping and traceroute towards from and to IP/IPv6s configured on Sub interfaces** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interfaces on phy/lag port<br/>2) Bind the sub interfaces to user vrf<br/>3) From DUT, ping to the hosts configured on Tgen on Vlan 100 and 200 with -I arg<br/>4)Verify ping works<br/>5)Do traceroute to Tgen hosts and verify it works on sub ports<br/>6)Perform ping from Tgen to IP and IPv6 configured on Sub interfaces and ve rify DUT respond with icmp reply on the sub interfaces |



### 3.2.18 To Verify multiple BGP sessions on sub interfaces and parent  interfaces with ECMP

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify multiple BGP sessions on sub interfaces and parent interfaces with ECMP** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure 3 sub interfaces on phy interfaces with encap id 100,200,300<br/>2)Configure 3 sub interfaces on Lag interfaces with encap id 400,500,600<br/>3)Bind the sub interfaces to user-vrf<br/>4) Configure ipv4 and ipv6 addresses on all sub interfaces<br/>5)Configure ip address on parent interface and establish BGP session on parent interface<br/>6)Configure BGP neighbors on all sub interfaces and enable ECMP paths<br/>7)Advertise prefixes from bgp neighbor and verify traffic<br/>8)Verify ECMP traffic forwarding with mix of parent interface , sub interfaces |



### 3.2.19 To Verify BGP 5549 unnumbered over sub interfaces



| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify BGP 5549 unnumbered over sub interfaces**        |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interfaces on physical and lag interfaces<br/>2)Configure encap_id on each sub interfaces and bind it to user-vrf<br/>3)Configure BGP 5549 unnumbered neighbor over sub interfaces<br/>4)Verify ipv4 prefixes are advertised over bgp 5549 session established via sub interfaces |





### 3.2.20  To Verify OSPFv2 /OSPFv3 neighbors over sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify OSPFv2 /OSPFv3 neighbors over sub interfaces**   |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interfaces on physical and lag interfaces<br/> 2)Configure encap_id on each sub interfaces and bind it to user-vrf <br/>3) Enable ospfv2 and ospv3 on all sub interfaces<br/>4)Verify OSPF neighbors are up for all the sub interfaces<br/> |



### 3.2.21  To Verify ICMP SLAs with sub interface as source-interface

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify ICMP SLAs with sub interface as source-interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interfaces on physical and lag interfaces<br/>  2)Configure encap_id on each sub interfaces and bind it to user-vrf  <br/>3)COnfigure ICMP SLA for the subnet ip configured on D3<br/>4)Verify ICMP SLA comes up fine<br/>5)Configure src-intf as sub interfaces and verify SLAs are UP<br/>6)Bring down the destination subnet and verify SLAs are timed out<br/> |

### 3.2.22  To Verify TCP SLAs with sub interface as source-interface

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify ICMP SLAs with sub interface as source-interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interfaces on physical and lag interfaces  <br/> 2)Configure encap_id on each sub interfaces and bind it to user-vrf <br/>  3)Configure TCP SLA for the subnet ip configured on D3  <br/>4)Verify ICMP SLA comes up fine<br/> 5)Configure src-intf as sub interfaces and verify SLAs are UP 6)Bring down the destination subnet and verify SLAs are timed out |



### 3.2.23  To Verify DHCP relay over sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify DHCP relay over sub interfaces**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interfaces on physical and lag interfaces    <br/>2) Configure dhcp v4 and dhcpv6 relay agent over sub interfaces<br/>3)Verify dhcp messages from client gets relayed to server and vice-versa over sub interfaces<br/>4)Verify option-82 added for each sub interface<br/>5) Configure src-intf suboption on sub interfaces with loopback address<br/>6)Verify DHCP packets are relayed to server with loopback address as source<br/> |



### 3.2.24 To Verify VRRP functionality over sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify Verify VRRP functionality over sub interfaces**  |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interfaces on physical and lag interfaces on D1 and D2   <br/>2)Enable VRRP on sub interfaces<br/>3)Verify VRRP master /Backup election on sub interfaces for both ipv4 and ipv6<br/>4) Verify traffic forwarding<br/>5)Configure sub interface as VRRP owner and verify master/backup elections<br/>6)Configure different priories and verifymaster/Backup election happens as per the configured priority |



### 3.2.25 To Verify Static routes with sub interface as next hop interface

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify Static routes with sub interface as next hop interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interfaces on with ip and ipv6 interfaces<br/>2)Configure a static route for network 150.1.1.0/24 and 1501::/64  with sub interface as nexthop interfaces<br/>3)Verify ip route table shows static entry<br/>4) Send L3 traffic for both ip and ipv6 destination subnets and verify traffic gets forwarded<br/>5)Delete static route and verify static route is removed<br/>6)Re add static route and verify traffic gets forwarded on sub interfaces<br/> |

### 3.2.26 To Verify Static route leak reachable via sub interface on default,user and management vrf

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify Static route leak reachable via sub interface default,user and management vrf** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure one sub interface on Vrf1 and second sub interface on Vrf2<br/> 2) Configure ipv4 and ipv6 addresses on both sub interfaces<br/>3) Configure static route leak on Vrf2 for a destination network reachable via sub interface (Vrf1) as next hop interface<br/>4)Verify routes installed on Vrf2 routing table and traffic gets forwarded<br/>5)Change the Vrf config on sub interface1 from Vrf1 to default-vrf and change the static route nexthop vrf to default <br/>6)Verify route leak from default-vrf to Vrf2 works and vice-versa<br/>7) Configure a static route-leak on mgmt-vrf for the subnet configured on sub-interface2(Vrf2) and verify route lea works for Vrf2 to mgmt-vrf |



### 3.2.27 To Verify BFD peers over sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify BFD peers over sub interfaces**                  |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interfaces on with ip and ipv6 addresses<br/>2)Bind the sub interfaces on user-vrf<br/>3) Configure static bfd peers between sub interfaces and D3<br/>4)Verify BFD over sub interfaces comes up<br/>5)Trigger link failure on remote side and verify BFD sessions timed out<br/>6) Configure non default BFD timers and verify <br/> |



### 3.2.28 To Verify Sub interfaces as MCLAG client interfaces and ICL keepalive

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify Sub interfaces as MCLAG client interfaces and ICL keepalive** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure MLAG peer nodes  with mlag client<br/>2)Configure sub interface as ICL peer link for Keepalive<br/>2) Configure sub interfaces over Portchannel interface with encap id 100 & 200<br/>3) Configure the client dut with Portchannel member of Vlans 100 & 200<br/>4)Send L3 traffic from hosts connected to Client on Vlan 100,200<br/>5)Verify Traffic forwarding with sub interfaces as client interfaces<br/>6)Verify MAC/ARP sync on mlag nodes learnt on sub interfaces |



### 3.2.29 To Verify upstream link tracking brings down mlag client sub interface

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify upstream link tracking brings down mlag client sub interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure MLAG peer nodes  with mlag client<br/>2)Configure sub interface as ICL peer link for Keepalive<br/>2) Configure sub interfaces over Portchannel interface with encap id 100 & 200<br/>3) Configure the client dut with Portchannel member of Vlans 100 & 200<br/>4)Send L3 traffic from hosts connected to Client on Vlan 100,200<br/>5)Verify Traffic forwarding with sub interfaces as client interfaces<br/>6)COnfigure upstream link tracking and verify bringing down upstream link brings down the mlag sub interface client interface <br/>7)Bring back the upstream link and verify sub interfce comes up after hold timer expiry |



### 3.2.30 To Verify SAG on sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify SAG on sub interfaces**                          |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure MLAG peer nodes  with mlag client<br/> 2)Configure sub interface as ICL peer link for Keepalive <br/>3) Configure sub interfaces over Portchannel interface<br/>4)Configure  static ipv4 and ipv6 anycast address on the sub interface on Mlag peers<br/> 6)Verify Traffic forwarding with from lag client to orphan port using static anycast addresses configured on sub interfaces as gateway<br/> |



### 3.2.31 To Verify sub interfaces  as underlay in Vxlan topology

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify sub interfaces as underlay in Vxlan topology**   |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Bring up EVPN topology with 2 Spine and 3 Leaf nodes<br/>2) In the underlay, configure 3 sub interfaces between each Spine and Leaf node over physical and Lag interfaces<br/>3) With Vxlan tunnel established and remote VTEP is reachable via all 3 sub interfaces on Leaf1<br/>4)Send L2 BUM traffic and L3 traffic from Leaf1<br/>4)Verify Vxlan uses only one sub interface for forwarding the traffic to remote VTEP<br/>5) Bring down the sub interface and verify next sub interface used for traffic forwarding<br/>6)Flap the vxlan tunnel and verify traffic forwarding over sub interfaces<br/> |

### 3.2.32  To Verify qos service-policy on sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify qos service-policy  on sub interfaces**          |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure a qos policy-map to match source ip address,source ipv6 address and define set actions like dscp,traffic-class<br>2)Attach the service policy to parent interface<br/>3)Verify service-policy gets inherited to sub interfaces<br/>4)Send traffic matching the classifier fields and verify sub interface applies the qos service-policy at ingress as well as egress directions<br/>5)Verify mapping a different policy-map directly to sub interface and verify<br/> |



### 3.2.33  To Verify PBR service-policy on sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify PBR service-policy on sub interfaces**           |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure SUb interfaces over phy and lag interfaces <br/>2)Bind sub interfaces over phy interface on Vrf1 and sub interfaces over lag interface on Vrf2<br/>3)Configure policy map and set the nexthop pointing to lag sub interface on Vrf2<br/>4)Bind the service policy  on  phy interface and verify it gets inherited to sub interfaces<br/>5)Upon receiving the L3 traffic on Vrf1 , traffic should be routed to sub interface over lag interface on Vrf2 <br/>6)Verify null interface on policy map drops the traffic<br/> |



### 3.2.34  To Verify port mirroring on parent and sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify port mirroring on parent and sub interfaces**    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure Sub interfaces over phy and lag interfaces <br/>2)Configure parent interface as mirroring destination port <br/>3)Verify port ingressing on source port gets mirrored to parent interface<br/>4) Configure sub interface as destination port and verify port mirroring works<br/>5)Repeat the test steps with parent and sub interface as source port and verify packet ingressing on sub interfaces gets mirrored |

### 3.2.35  To Verify L3 acl functionality applied on sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify L3 acl functionality applied on sub interfaces** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure Sub interfaces over phy and lag interfaces <br/>2) Configure IP and IPv6 address and bind it to user-vrf<br/>3)Configure ipv4 and ipv6 access-lists with src-ip/dst-ip and src-ipv6/dst-ipv6 specified with permit and deny rules<br/>4)Apply v4 and v6 ACls to sub interfaces<br/>5)Verify Flows matching the acl rules will be forwarded and discarded as per permit/deny action<br/>6)Verify ACL counters gets incrmented |

### 3.2.36  To Verify delete and recreate sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify delete and recreate sub interfaces**             |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure 2 hosts connecting to D2(L2 switch) one on Vlan 300(300.1.1.2 and 3001::2) and other on Vlan400(400.1.1.2 and 4001::2)<br/>2) Configure D2 ports towards Router D1 as trunk member for Vlan 100 and Vlan2003
3)On D1(Router), Create two sub interfaces for the physical port connecting to D2 with encapsulation id 100,200 respectively
4) Configure ip/ipv6 addresses addresses for these sub interfaces created over lag port 300.1.1.1 , 3001::1 and 300.1.1.1,3001::1 respectively
5)Send L3 ipv4 and ipv6  between Host1 and Host2 and Verify D1 routes the traffic over sub interfaces
6)Delete sub interface and verify all configs are gone
7)Recreate sub interfaces and verify routing between subinterfaces |






### 3.2.37 To Verify Inband management on sub interface 

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify InBand management on sub interface**             |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interface on a parent interface<br/>2)Bind it to Vrf1 and Configure IP and IPv6 addresses <br/>3)Verify ssh and telnet to the IP and Ipv6 addresses configured on sub interfaces works from peer node<br/>4)Repeat the test steps by binding sub interfaces to default and management VRFs |



###  3.2.38 To Verify dynamic breakout on parent interface



| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify Verify dynamic breakout on parent interface**    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interface on a parent interface<br/> 2)Bind sub interface to user vrf and do L3 ipv4 and ipv6 configs <br/>3) Do dynamic breakout on parent interface and verify all the sub interfaces created on that parent interface gets deleted<br/>4)Disable breakout config on parent interface and configure back the sub interfaces<br/>5)Verify L3 routing happens between Sub interfaces after breakout<br/> |

### 3.2.39 To Verify NTP server functionality on sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify NTP over sub interfaces**                        |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interface on a parent interface both physical and Port channel   <br/>2)Configure NTP client on eth0 management interface .Create loopback interface and leak it to Vrf1<br/>3)Configure sub interfaces on Vrf1 and configure NTP server as leaked Loopback address<br/>4)Verify NTP client connected to sub interfaces gets time synced |

### 3.2.40 To Verify SNMP walk retrieves sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify snmpwalk retrieves sub interfaces**              |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interface on a parent interface both physical and Portchannel<br/>  2)Bind sub interface to user vrf and do L3 ipv4 and ipv6 configs <br/>3)Perform SNMP mib walk and verify Sub interface gets retrieved<br/> |



### 3.2.41 To Verify sub interfaces logs gets updated on Syslog

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify snmpwalk retrieves sub interfaces**              |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interface on a parent interface both physical and Port channel   <br/>2)Bind sub interface to user vrf and do L3 ipv4 and ipv6 configs<br/>  3)Verify logs associated with sub interfaces gets written to Syslog <br/> |

### 3.2.42 To Verify handling of untagged packets on sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify handling of untagged packets on sub interfaces** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interface on a parent interface both physical and Port channel <br/>2)Configure encapsulation IDs on the sub interfaces<br/>3) Configure ip/ipv6 configuration on parent interface  <br/>4) Configure ip/ipv6 addresses on  sub interfaces Eth100.10 and Eth100.20<br/>5) Verify untagged ping request towards subnet configured on parent interface works<br/>6)Send L3 untagged data packet and verify parent interface routes the traffic,since it does not match nay encap_id configured on sub interfaces |



### 3.2.43 To Verify handling of QinQ packets on sub interfaces

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify handling of QinQ packets on sub interfaces**     |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure sub interface on Ethenet10.100 with 100 as encap id and Ethernet10.200 with 200 as encap id<br/>2) Bind it to user vrf and assign ip/ipv6 addresses<br/>3)Send QinQ L3 traffic with outer vlan id as 100 and verify Ethernet10.100 routes the traffic to Ethernet10.200<br/> |



### 3.2.44 To Verify creation of sub interface in port aliasing mode

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To Verify creation of sub interface in port aliasing mode** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)With aliasing mode enabled, configure sub interfaces over parent interface in aliasing mode<br/>2)Verify sub interfaces are created as expected with encap ids<br/>3) Verify L3 routing between sub interfaces |

### 3.2.45 To Verify creation of sub interface over Portchannel with long interface name

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To To Verify creation of sub interface over Portchannel with long interface name** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure Portchannel on DUT1<br/>2) Configure sub interface over Portchennel using long-interface name as PortChannelXXXX and verify sub interface gets created |

### **3.3 Reboot Test Cases**

### 3.3.1 To verify routing over sub interfaces with config reload  ###

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **To verify routing over sub interfaces with config reload** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure multiple sub interfaces on physical interface as well as L3 Portchannel<br/>2)Send L3 traffic from Tgen hosts and verify inter vlan routing happens on DUT between sub interfaces<br/>3)Save config and Perform config reload<br/>4)Verify all sub interface configs are retained and all ports comes up<br/>5)Verify Traffic forwarding |


### 3.3.2 To verify routing over sub interfaces with Coldboot

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **To verify routing over sub interfaces with Coldboot**      |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure multiple sub interfaces on physical interface as well as L3 Portchennel<br/>2)Send L3 traffic from Tgen hosts and verify inter vlan routing happens on DUT between sub interfaces
3)Save config and Perform coldboot
4)Verify all sub interface configs are retained and all ports comes up
5)Verify Traffic forwarding |

### 3.3.3 To verify routing over sub interfaces with Fastboot 

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **To verify routing over sub interfaces with Fastboot**      |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure multiple sub interfaces on physical interface as well as L3 Portchennel<br/>2)Send L3 traffic from Tgen hosts and verify inter vlan routing happens on DUT between sub interfaces
3)Save config and Perform Fastboot
4)Verify all sub interface configs are retained and all ports comes up
5)Verify Traffic forwarding |

### 3.3.4 To verify routing over sub interfaces with Warmboot

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **To verify routing over sub interfaces with Warmboot**      |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure multiple sub interfaces on physical interface as well as L3 Portchennel<br/>2)Send L3 traffic from Tgen hosts and verify inter vlan routing happens on DUT between sub interfaces
3)Save config and Perform warmboot
4)Verify all sub interface configs are retained and all ports comes up
5)Verify Traffic forwarding and check there is no traffic loss during warmboot |

  

### **3.4 Scale Test Cases**

### 3.4.1 To verify maximum sub interfaces per parent interface  ###


| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **To verify maximum sub interfaces per parent interface**    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure maximum supported 250 sub interfaces on a physical port<br/>2) Assign IP and IPv6 addresses on each sub interfaces<br/>3) Assign encapsulation ID in the range 1-4090 for each sub interface randomly<br/>4) Verify all Sub interfaces comes up without any issues<br/>5)Verify all directly connected subnets on sub interfaces gets installed in routing table<br/>6)Repeat the test on Port channel interface |

### 3.4.2 To verify maximum sub interfaces system level ###


| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **To verify maximum sub interfaces system level**            |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure maximum supported 750 sub interfaces system level with 3 parnt ports having 250 sub interfaces each<br/>2) Assign IP and IPv6 addresses on each sub interfaces
3) Assign encapsulation ID in the range 1-4090 for each sub interface randomly
4) Verify all 750 Sub interfaces comes up without any issues
5)Verify all directly connected subnets on sub interfaces gets installed in routing table |





## **Reference Links**


https://docs.google.com/document/d/1RY3No-V8EjicA5FVqyFGNxWpMbvOWw3STbpvOyFmtx0/edit#heading=h.b4jtujka6b5e

