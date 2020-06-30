#  SQA Test Plan
# BGP Dynamic Neighbor Discovery and 5549 Underlay Testplan
#  SONiC 3.0 Project and Buzznik Release
[TOC]
# Test Plan Revision History
| Rev | Date | Author | Change Description |
|:---:|:-----------:|:------------------:|-----------------------------|
| 0.1 | 10/11/2019 | Manisha Joshi | Initial version |

# List of Reviewers
|  Function | Name |
|:---:|:-----------:|
|  |   |

# List of Approvers
|  Function | Name | Date Approved|
|:---:|:-----------:|:------------------:|
|  |   |  |

# Definition/Abbreviation
| **Term** | **Meaning**                     |
| -------- | ------------------------------- |
|     |        |



# Feature Overview

Below is the link to high level design of above features

5549 unnumbered: <http://gerrit-lvn-07.lvn.broadcom.net:8083/plugins/gitiles/sonic/documents/+/refs/changes/47/9647/4/L3/ipv6_link_local/ipv6_link_local.md>

# 1 Test Focus Areas
This document covers test cases designed to verify BGP dynamic discovery and 5549 underlay functionalities on SONiC 3.0 release.


# 2 Topologies
## 2.1 Topology 1

![Dynamic](Dynamic Discovery.png "Topology 1")



# 3 Test  Case and Objectives
## 3.1 **CLI**
## 3.2 **Functional**
#### 3.2.1     Verify BGP unnumbered and listen range over physical interface

| Test    ID | FtOpSoRoDyndisFun001                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP unnumbered and listen range over physical interface |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 2) Configure normal IPv4 and IPv6 BGP sessions between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify 5) Flap the interface between DUT1 and DUT2 and verify traffic again. |

#### 3.2.2     Verify BGP unnumbered and listen range over vlan

| Test    ID | FtOpSoRoDyndisFun002                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP unnumbered and listen range over vlan             |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 over a virtual interface 2) Configure normal IPv4 and IPv6 BGP sessions between DUT2 and DUT3 over vlan 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify 5) Flap the interface between DUT1 and DUT2 and verify traffic again. |

#### 3.2.3     Verify BGP unnumbered and listen range over port channel

| Test    ID | FtOpSoRoDyndisFun003                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP unnumbered and listen range over port channel     |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 and IPv6 BGP session between DUT2 and DUT3 over a port channel 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify 5) Modify the listen limit and verify traffic again. 6) Remove the interfaces from the port channel, add them back and verify again |

#### 3.2.4     Verify BGP unnumbered along with listen limit over port channel

| Test    ID | FtOpSoRoDyndisFun004                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP unnumbered along with listen limit over port channel |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 and IPv6 BGP session between DUT2 and DUT3 over a port channel 3) On DUT2 use listen range and listen limit to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify 5) Modify the limit and verify again |

#### 3.2.5     Verify BGP unnumbered along with peer group

| Test    ID | FtOpSoRoDyndisFun005                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP unnumbered along with peer group over port channel |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered sessions between DUT1 and DUT2 over a port channel using peer group 2) Configure normal IPv4 and IPv6 BGP sessions between DUT2 and DUT3 over a port channel 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify |

#### 3.2.6     Verify BGP session with manually configured link local along with listen range

| Test    ID | FtOpSoRoDyndisFun006                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP session with manually configured link local along with listen range over port channel |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP session between DUT1 and DUT2 over a port channel with manually configured link local address 2) Configure normal IPv4 and IPv6 BGP sessions between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify 5) Modify the listen range and verify traffic again. 6) Modify the link local address and verify the session comes up |

#### 3.2.7     Verify BGP session with manually configured link local along with listen limit

| Test    ID | FtOpSoRoDyndisFun007                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP session with manually configured link local along with listen limit over port channel |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP session between DUT1 and DUT2 over a port channel with manually configured link local address 2) Configure normal IPv4 and IPv6 BGP session between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify 5) Modify the listen limit and verify traffic again. 6) Modify the link local address between DUT2 and DUT3 and verify the session comes up |

#### 3.2.8     Verify BGP unnumbered along with route-map and modify the next hop to take IPv6 address

| Test    ID | FtOpSoRoDyndisFun008                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP unnumbered along with route-map over port channel and modify the next hop to take IPv6 address |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 and IPv6 BGP session between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) On DUT1 add a route map to change the next hop to IPv6 global address. 5) Verify BGP sessions come up 6) Send traffic from DUT1 to DUT3 and verify 7) Modify DUT2 BGP neighbor to remove BGP unnumbered and configure global IPv6 address |

#### 3.2.9     Verify BGP unnumbered along with route dampening configuration

| Test    ID | FtOpSoRoDyndisFun009                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP unnumbered along with route dampening configuration |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 and IPv6 BGP session between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. Configure route dampening feature 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify 5) Modify the listen limit and verify traffic again. 6) Remove the interfaces from the port channel, add them back and verify again |

#### 3.2.10     Verify BGP unnumbered along with listen range and modify the session to static neighbor

| Test    ID | FtOpSoRoDyndisFun010                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP unnumbered along with listen range and modify the session to static neighbor |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 and IPv6 BGP session between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify 5) Remove listen range on DUT2 and modify it to a static neighbor 6) Remove the static configuration and add the neighbor back |

#### 3.2.11	Verify BGP unnumbered with manually configured link local on the other side

| Test    ID | FtOpSoRoDyndisFun011                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP unnumbered with manually configured link local on the other side |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP session between DUT1 and DUT2 over a port channel with manually configured link local address on one side and BGP unnumbered on the other side 2) Configure normal IPv4 and IPv6 BGP sessions between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify 5) Modify the listen range and verify traffic again. 6) Modify the link local address and verify the session comes up |

#### 3.2.12	Verify ECMP with BGP unnumbered

| Test    ID | FtOpSoRoDyndisFun012                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify ECMP with BGP unnumbered                              |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure two BGP unnumbered session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 and IPv6 BGP session between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify 5) Modify the listen limit and verify traffic again. 6) Remove the interfaces from the port channel, add them back and verify again |

#### 3.2.13	Verify BGP unnumbered after enabling and disabling IPv6 at the interface

| Test    ID | FtOpSoRoDyndisFun013                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP unnumbered after enabling and disabling IPv6 at the interface |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 and IPv6 BGP session between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify 5)  Disable and enable IPv6 at the interface and verify |

#### 3.2.14	Verify BGP unnumbered session after clear bgp neighbor 

| Test    ID | FtOpSoRoDyndisFun014                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP unnumbered session after clear bgp neighbor       |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 and IPv6 BGP sessions between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify 5) Clear bgp neighbors on DUT2 and verify the sessions |

#### 3.2.15	Verify BGP unnumbered session after clear ndp

| Test    ID | FtOpSoRoDyndisFun015                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP unnumbered session after clear ndp                |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 and IPv6 BGP session between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify 5) Clear ndp and verify the sessions |

#### 3.2.16	Verify BGP unnumbered session after adding and removing bgp configuration globally

| Test    ID | FtOpSoRoDyndisFun016                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP unnumbered session after adding and removing bgp configuration globally |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 and IPv6 BGP sessions between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify 5) Remove BGP configuration globally, add it back and verify |

#### 3.2.17    Configure global IPv6 address and modify BGP unnumbered session modify it to take it

| Test    ID | FtOpSoRoDyndisFun017                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Configure global IPv6 address and modify BGP unnumbered session modify it to take it |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 and IPv6 BGP sessions between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify 5) Remove the BGP unnumbered configuration, assign a global IPv6 address and verify it |

#### 3.2.18	Verify BGP unnumbered session with redistributed connected

| Test    ID | FtOpSoRoDyndisFun018                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Configure global IPv6 address and modify BGP unnumbered session modify it to take it |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 over a port channel and redistribute connected 2) Configure normal IPv4 and IPv6 BGP sessions between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify |

#### 3.2.19	Verify multihop BGP session along with listen range

| Test    ID | FtOpSoRoDyndisFun019                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify multihop BGP session along with listen range          |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure multihop BGP session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 and IPv6 BGP session between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify |

#### 3.2.20	Verify BGP session along with listen range after removing and adding loopback interface

| Test    ID | FtOpSoRoDyndisFun020                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify multihop BGP session along with listen range          |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure multihop BGP session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 and IPv6 BGP session between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify  6) Remove and add the loopback interface back and verify the session again |

#### 3.2.21	Verify BGP session along with listen range after modifying loopback address

| Test    ID | FtOpSoRoDyndisFun021                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP session along with listen range after modifying loopback address |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session  between DUT1 and DUT2 over a port channel 2) Configure multihop IPv4 and IPv6 BGP session between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify  6) Modify the loopback interface address and verify |

#### 3.2.22   Verify BGP session along with listen range having /127 and /31 subnets

| Test    ID | FtOpSoRoDyndisFun022                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP session along with listen range having /127 and /31 subnets |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 BGP session with /31 subnet and IPv6 BGP session with /127 subnet between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify  6) Remove the IP address configure the traffic and reverify 7) Repeat the test case for various subnet ranges |

#### 3.2.23	Verify BGP along with listen range after configuring multiple addresses on the interface

| Test    ID | FtOpSoRoDyndisFun023                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BGP along with listen range after configuring multiple global IPv6 addresses on the interface |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 and IPv6 BGP session between DUT2 and DUT3 and add more than than one IP address to the underlying interfaces 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify  6)  Remove one address and verify the next address is taken |

#### 3.2.24	Verify BFD on BGP unnumbered session

| Test    ID | FtOpSoRoDyndisFun024                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify BFD  on BGP unnumbered session                        |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure BGP unnumbered session between DUT1 and DUT2 over a port channel 2) Configure normal IPv4 and IPv6 BGP session between DUT2 and DUT3 3) On DUT2 use listen range to configure the sessions and verify that DUT1 and DUT3 neighbors fall in that range. 4) Verify BGP sessions come up 5) Send traffic from DUT1 to DUT3 and verify  6) Configure BFD on BGP unnumbered session and verify 7) Modify the BFD timers and verify |

## 

## 3.3 Negative

## 3.4 Reboot
#### 3.4.1    Verify BGP unnumbered and listen range on default vrf and non-default vrf after a reboot

| **Test  ID** | **FtOpSoRoDyndisFun025**                                     |
| ------------ | ------------------------------------------------------------ |
| Test Name    | Verify BGP unnumbered and listen range on default vrf and non-default vrf after a reboot |
| Test Setup   | Topology1                                                    |
| Type         | Functional                                                   |
| Steps        | 1) Configure BGP unnumbered and listen range and verify after a cold reboot |

#### 3.4.2    Verify BGP unnumbered and listen range on default vrf and non-default vrf after a warm reboot

| **Test  ID** | **FtOpSoRoDyndisFun026**                                     |
| ------------ | ------------------------------------------------------------ |
| Test Name    | Verify BGP unnumbered and listen range on default vrf and non-default vrf after a warm reboot |
| Test Setup   | Topology1                                                    |
| Type         | Functional                                                   |
| Steps        | 1) Configure BGP unnumbered and listen range and verify after a warm reboot |

#### 3.4.3    Verify BGP unnumbered and listen range on default vrf and non-default vrf after a warm reboot

| **Test  ID** | **FtOpSoRoDyndisFun027**                                     |
| ------------ | ------------------------------------------------------------ |
| Test Name    | Verify BGP unnumbered and listen range on default vrf and non-default vrf after a config reload |
| Test Setup   | Topology1                                                    |
| Type         | Functional                                                   |
| Steps        | 1) Configure BGP unnumbered and listen range and verify after a config reload |

## 3.5 Stress,Scale and Performance
### 3.5.1    Verify  maximum BGP unnumbered sessions 

| **Test ID** | **FtOpSoRoAutoSc001**                                |
| ----------- | ---------------------------------------------------- |
| Test Name   | Verify  maximum BGP unnumbered sessions              |
| Test Setup  | Topology                                             |
| Type        | Functional                                           |
| Steps       | Configure maximum BGP unnumbered sessions and verify |

### 3.5.2    Verify  maximum BGP unnumbered sessions 

| **Test ID** | **FtOpSoRoAutoSc002**                                        |
| ----------- | ------------------------------------------------------------ |
| Test Name   | Verify  maximum listen range                                 |
| Test Setup  | Topology                                                     |
| Type        | Functional                                                   |
| Steps       | Configure maximum listen range and BGP sessions. In a loop Decrease/restore limit value so that one session goes down and comes up |

## 3.6 Management

### 3.6.1    SNMP
### 3.6.2    gNMI
### 3.6.3     REST API

# 4 Reference Links

