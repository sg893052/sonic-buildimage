#  SQA Test Plan

#  BFD Profile
#  SONiC 4.0 Project and Cyrus Release
[TOC]

# Test Plan Revision History
| Rev  |    Date    |      Author       | Change Description |
| :--: | :--------: | :---------------: | ------------------ |
| 0.1  | 12/07/2021 | Lakshminarayana D | Initial version    |

# List of Reviewers
| Function |       Name       |
| :------: | :--------------: |
|   Dev    |    Narendra B    |
|   Dev    |  Sumit Agarwal   |
|    QA    | Kalyan Vadlamani |

# List of Approvers
| Function |       Name       | Date Approved |
| :------: | :--------------: | :-----------: |
|   Dev    |    Narendra B    |               |
|   Dev    |  Sumit Agarwal   |               |
|    QA    | Kalyan Vadlamani |               |

# Definition/Abbreviation
| **Term** | **Meaning**                        |
| -------- | ---------------------------------- |
| BFD      | Bidirectional forwarding detection |
| VRF      | virtual route forwarding           |
| eBGP     | External border gateway protocol   |
| iBGP     | Interior border gateway protocol   |
| LAG      | Link aggregation                   |
| OSPF     | Open shortest path first           |
| PIM      | Protocol Independent Multicast     |

# Feature Overview
Bidirectional Forwarding Detection, (BFD) is a protocol defined by the BFD working group at IETF. The protocol defines a method of rapid detection of the failure of a forwarding path by checking that the next hop router is alive. The protocol will be able to detect the forwarding path failure in milliseconds depending on the actual configuration.  

BFD profile is a template which can be applied to multiple BFD peers, making it easier to change default configuration without configuring each of the peer separately. Profile also enables changing BFD timers of dynamic session without configuring static BFD peers. All BFD configurations are supported in Klish only.

# 1 Test Focus Areas
## 1.1 Functional Testing 
  - All CLI related to BFD profile
  - BFD Profile scaling with max supported of BFD sessions
  - Test on default and user VRFs
  - Test BFD profile on BGP, OSPF and PIM peer mode, static BFD peers

# 2 Topologies
![BFD profile](Topology2.png)

### 2.1 TEST APPROACH

### 2.1.1 Functionality Test

As part of module configuration, bringing up the BGP, OSPF PIM neighbourship with the Port based, LAG, VLAN interfaces between DUT1 and DUT2. Configure static routes, static ARP and NDP on TG connected ports to traffic verification. The above said configuration would be performed on both the Default and User vrfs. The BFP profile functionality verification can be done over both ipv4 and ipv6. With this common module configuration in place, individual functions may configure/unconfigure the required configuration as per the test procedure.

# 3 Test  Case and Objectives

## 3.1 CLI

### 3.1.1 Verify cli commands for BFD Profile configure and show commands

| **Test Name**  | **Verify CLI commands for BFD Profile configure and show commands** |
| -------------- | :----------------------------------------------------------- |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **CLI**                                                      |
| **Test ID**    | **bfd_profile_cli_001**                                      |
| **Steps**      | **1) Verify CLI to configure/unconfigure fields under BFD profile command.<br/> 2) Verify CLI to configure/unconfigure BFD profile in BGP, OSPF, PIM peers and static BFD peer.<br/> 3) Verify CLI to configure/unconfigure passive-mode in BFD peers.<br/> 4) Verify CLI to configure/unconfigure minimum-ttl in BFD peer .<br/> 5) Verify CLI to below show commands.<br/>show bfd profile.<br/>show bfd profile \<profile-name\>.<br/>show bfd peer <br/>show bfd peer \<peer params\>** |

## 3.2 Functional

### 3.2.1 Verify BFD profile for protocol establishment with single-hop configuration.

| **Test ID**    | **bfd_profile_func_001**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BFD profile for protocol establishment with single-hop configuration.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure BFD enabled protocols BGP, OSPF, PIM on dut1 and dut2..<br/>2) Establish BGP, OSPF and PIM sessions between dut1 and dut2.<br/>3) Create different BFD profiles and apply on each protocol interface.<br/>4) Verify BFD session establishment properly on each protocol interface.<br/>5) Configure BFD sessions between dut1 and dut2 on this BGP, OSPF and PIM neighbors.<br/>6) Verify BFD session establishment properly on each protocol interface.<br/>7) Delete applied profiles on from few interface and verify BFD sessions status.<br/>8) Delete profiles from all protocol interfaces. verify BFD sessions status and come up with default values.<br/>9) Verify BFD packet transmission as per configured interval.** |

### 3.2.2 Verify BFD profile functionality with single-hop protocol configuration

| **Test ID**    | **bfd_profile_func_002**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BFD profile functionality with single-hop protocol configuration** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure BFD enabled protocols BGP, OSPF, PIM on dut1 and dut2..<br/>2) Establish BGP, OSPF and PIM sessions between dut1 and dut2.<br/>3) Create different BFD profiles and apply on each protocol interface. Verify BFD session status.<br/>4) Modify the params in applied BFD profiles and verify the BFD session should come up with modified values.,<br/>5) Delete all applied profiles. Verify profile configuration has been removed from profile applied interfaces.<br/>6) Apply same profile in all protocol interfaces. Verify all BFD session come up properly.<br/>7) Modify the params in profile. Verify same configuration reflects in all profile applied peers.<br/>8) Apply new profile on protocol interface without deleting the previous profile. Verify BFD session should come with new profile configuration.<br/>9) Apply profile in BGP peer-group command. Configure peer group in BGP neighbor interfaces. Verify the BFD sessions came up with configure profile in peer-group mode.<br/>10) Delete the profile from peer-group. Verify the BFD configuration has been removed from all peer-group applied neighbors.<br/>11) Apply/Remove same profile into different VRF peers. Verify BFD sessions status.<br/>12) Configure few alternate paths to the same destination and Establish traffic across two end points through this peers. <br/>13) Simulate link down event between the dut1 and dut2.<br/>14) On dut1 BFD kicks in after configured receive-interval \* detect multiplier and BGP to teardown the session immediately.<br/>15) Retrieve the routes learned via the above neighbors and verify that the above next hop is removed and alternate next hop is shown .<br/>16) Monitor the traffic, observe that traffic is redirected through alternate path and based on the packet loss check convergence time to verify.** |

### 3.2.3 Verify BFD profile functionality with single-hop static BFD peers configuration

| **Test ID**    | **bfd_profile_func_003**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BFD profile functionality with single-hop static BFD peers with local address & interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure BFD enabled protocols BGP, OSPF, PIM on dut1 and dut2..<br/>2) Establish BGP, OSPF and PIM sessions between dut1 and dut2.<br/>3) Configure static BFD sessions with non default params through local-address and interface on configured neighbors between dut1 and dut2.<br/>4) Apply non exist profile into configured static peers. Verify the BFD sessions came up with static peer values.<br/>5) Create BFD profile with above applied profile name. Verify the BFD sessions, params configured in static peer takes precedence over BFD profile.<br/>6) Delete the applied profile and removed from all static BFD peers.<br/>7) Delete static BFD peers and configure without changing any parameters within the static peer.<br/>8) Apply profile into above created static peers. Verify static BFD sessions came up with params configured on the profile.** |

### 3.2.4 Verify BFD profile with session timeout functionality on single-hop peers

| **Test ID**    | **bfd_profile_func_004**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BFD profile with single-hop with session timeout functionality on single-hop peers** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Establish BGP, OSPF and PIM sessions between dut1 and dut2.<br/>2) Configure BFD enabled protocols BGP, OSPF, PIM on dut1 and dut2.<br/>3) Create different BFD profiles and apply on each protocol interface. Verify BFD session status.<br/>4) Modify the params in applied BFD profiles and verify the BFD session should come up with modified values.,<br/>5) Verify the BFD sessions status with flapping the protocol configured physical interfaces.<br/>6) Verify the BFD sessions status with remove and add IP addresses from the protocol configured interfaces.<br />7) Verify the BFD sessions status with flapping the port channel interface.<br/>8) Verify the BFD sessions status with remove and add port channel from the protocol configured physical interface.<br/>9) Verify the BFD sessions status with remove and add VLAN from the protocol configured interfaces.<br/>10) Verify the BFD sessions status with remove and add peer-group the protocol configured interfaces.** |

### 3.2.5 Verify BFD profile with sharing same BFD session on multiple protocols interfaces

| **Test ID**    | **bfd_profile_func_005**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BFD profile with sharing same BFD session on multiple protocols interfaces** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Establish BGP, OSPF and PIM sessions between dut1 and dut2.<br/> 2) Configure BFD enabled protocols BGP, OSPF, PIM on dut1 and dut2.<br/> 3) Create different BFD profiles and apply on each protocol interface. Verify BFD session status.<br/> 4) Remove the all protocols configured on the device and verify BFD profile  cleared from the DUT.<br/> 5) Configure BGP, OSPF and PIM on same interface. Verify the protocols came up properly.<br/> 6) Configure different BFD profiles on each of the routing protocol interface. Verify last configured profile should be configured on all protocols.<br/> 7) Modify the params from any of the BFD profile configured, all protocol peers should come up modified profile.<br/> 8) Remove configured protocols. Establish peers with different destinations. Apply profiles all protocol interfaces. Verify BFD session status.** |

### 3.2.6 Verify BFD passive-mode functionality on single-hop peer

| **Test ID**    | **bfd_profile_func_006**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BFD passive-mode functionality on single-hop peer** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Establish BGP, OSPF and PIM sessions between dut1 and dut2.<br/> 2) Configure BFD enabled protocols BGP, OSPF, PIM on dut1 and dut2.<br/> 3) Configure passive-mode on the peer device and verify the BFD session status.<br/> 4) Perform the clear BFD counters on all interfaces. Verify peer counters.<br/> 5) Create BFD profile with passive-mode enable. Apply on static peers and verify BFD session status, should not change previous behavior configured on the static peer.<br/> 6) Remove configured static peers and enable BFD on protocol neighbor interfaces. Verify BFD session should come up properly.<br/> 7) Verify the BFD session shutdown/no shutdown on BFD peer mode.<br/> 8) Apply passive-mode enable profile on protocol enabled interfaces.<br /> 9) Perform the clear bfd counters and verify the counters interface.<br/> 10) Disable passive-mode on applied BFD profile. Verify the BFD session status.** |

### 3.2.7 Verify BFD profile with clear BGP, OSPF and PIM protocols

| **Test ID**    | **bfd_profile_func_007**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BFD profile with clear BGP, OSPF and PIM protocols** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Establish BGP, OSPF and PIM sessions between dut1 and dut2.<br/> 2) Configure BFD enabled protocols BGP, OSPF, PIM on dut1 and dut2.<br/> 3) Create a profile with name as maximum length support(63 characters).<br/> 4) Apply profile on each protocol interface. Verify BFD session status.<br/> 7) Issue clear BGP, OSPF and PIM.<br/> 8) Verify BFD sessions established after clear protocols** |

### 3.2.8 Verify BFD profile functionality with multi-hop protocol configuration

| **Test ID**    | **bfd_profile_func_008**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BFD profile functionality with multi-hop protocol configuration** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure BFD enabled protocols eBGP, OSPF, PIM on dut1 and dut2..<br/>2) Establish eBGP, OSPF and PIM sessions between dut1 and dut2.<br/>3) Create different BFD profiles and apply on each protocol interface. Verify BFD session status.<br/>4) Modify the params in applied BFD profiles and verify the BFD session should come up with modified values.,<br/>5) Delete all applied profiles. Verify profile configuration has been removed from profile applied interfaces.<br/>6) Apply same profile in all protocol interfaces. Verify all BFD session come up properly.<br/>7) Modify the params in profile. Verify same configuration reflects in all profile applied peers.<br/>8) Apply new profile on protocol interface without deleting the previous profile. Verify BFD session should come with new profile configuration.<br/>9) Apply profile in eBGP peer-group command. Configure peer group in BGP neighbor interfaces. Verify the BFD sessions came up with configure profile in peer-group mode.<br/>10) Delete the profile from peer-group. Verify the BFD configuration has been removed from all peer-group applied neighbors.<br/>11) Apply/Remove same profile into different VRF peers. Verify BFD sessions status.<br/>12) Configure few alternate paths to the same destination and Establish traffic across two end points through this peers. <br/>13) Simulate link down event between the dut1 and dut2.<br/>14) On dut1 BFD kicks in after configured receive-interval \* detect multiplier and BGP to teardown the session immediately.<br/>15) Retrieve the routes learned via the above neighbors and verify that the above next hop is removed and alternate next hop is shown .<br/>16) Monitor the traffic, observe that traffic is redirected through alternate path and based on the packet loss check convergence time to verify.** |

### 3.2.9 Verify BFD profile functionality with multi-hop static BFD peers configuration

| **Test ID**    | **bfd_profile_func_009**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BFD profile functionality with multi-hop static BFD peers with local address & interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure BFD enabled protocols eBGP, OSPF, PIM on dut1 and dut2..<br/>2) Establish eBGP, OSPF and PIM sessions between dut1 and dut2.<br/>3) Configure static BFD sessions with non default params through local-address and interface on configured neighbors between dut1 and dut2.<br/>4) Apply non exist profile into configured static peers. Verify the BFD sessions came up with static peer values.<br/>5) Create BFD profile with above applied profile name. Verify the BFD sessions, params configured in static peer takes precedence over BFD profile.<br/>6) Delete the applied profile and removed from all static BFD peers.<br/>7) Delete static BFD peers and configure without changing any parameters within the static peer.<br/>8) Apply profile into above created static peers. Verify static BFD sessions came up with params configured on the profile.** |

### 3.2.10 Verify BFD profile with session timeout functionality on multi-hop peers

| **Test ID**    | **bfd_profile_func_010**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BFD profile with session timeout functionality on multi-hop peers** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**e                                              |
| **Steps**      | **1) Establish eBGP, OSPF and PIM sessions between dut1 and dut2.<br/>2) Configure BFD enabled protocols eBGP, OSPF, PIM on dut1 and dut2.<br/>3) Create different BFD profiles and apply on each protocol interface. Verify BFD session status.<br/>4) Modify the params in applied BFD profiles and verify the BFD session should come up with modified values.,<br/>5) Verify the BFD sessions status with flapping the protocol configured physical interfaces.<br/>6) Verify the BFD sessions status with remove and add IP addresses from the protocol configured interfaces.<br />7) Verify the BFD sessions status with flapping the port channel interface.<br/>8) Verify the BFD sessions status with remove and add port channel from the protocol configured physical interface.<br/>9) Verify the BFD sessions status with remove and add VLAN from the protocol configured interfaces.<br/>10) Verify the BFD sessions status with remove and add peer-group the protocol configured interfaces.** |

### 3.2.11 Verify BFD minimum-ttl functionality on multi-hop peer

| **Test ID**    | **bfd_profile_func_011**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BFD minimum-ttl functionality on multi-hop peer.**  |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Establish eBGP sessions between dut1 and dut2.<br/> 2) Configure BFD enabled protocols eBGP on dut1 and dut2.<br/> 3) Configure non default minimum-ttl value on the peer device and verify the BFD session status.<br/> 4) Configure minimum-ttl value to as packet not reach to destination hop. Verify the BFD sessions should not come up on peer device.<br/> 5) Delete the minimum-ttl value and change it to more than the reachable hop count. Verify the BFD session should come up with configure value.<br/> 6) Create a profile with minimum-ttl value configured. Apply on protocol interface and verify the BFD session status.<br/> 7) Change the minimum-ttl value to not reachable to next hop. Verify the BFD session should goes down.<br/> 8) Delete the applied template and verify the BFD sessions.** |

### 3.2.12 Verify BFD profile behavior configured on maximum BFD sessions supported 

| **Test ID**    | **bfd_profile_func_012**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BFD profile behavior configured on maximum BFD sessions supported** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Establish BGP, OSPF and PIM sessions between dut1 and dut2 based on maximum BFD sessions.<br/> 2) Create BFD profiles based on maximum supported BFD sessions on the device.<br/> 3) Configure BFD profiles between dut1 and dut2 on this BGP, OSPF and PIM neighbors.<br/> 4) Verify BFD session establishment in default and user VRF. <br/> 5) Configure maximum static BFD sessions with non-default aggressive timers (100ms*5).<br/> 6) Verify BFD session establishment in default and user VRF. <br /> 7) Delete the created BFD profiles from configured peers. Verify the BFD session status.** |

## 3.3 Reboot/Reload/Upgrade Test Cases

### 3.3.1 Verify warm reboot with BFD profile applied on peers

| **Test ID**    | **bfd_profile_func_013**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify warm reboot with BFD profile applied on peers.**    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Establish BGP, OSPF and PIM sessions between dut1 and dut2.<br/> 2) Configure BFD enabled protocols BGP, OSPF, PIM on dut1 and dut2.<br/> 3) Create different BFD profiles and apply on each protocol interface. Verify BFD session status.<br/> 4) Issue warm Reboot and verify BFD sessions establishment.** |

### 3.3.2  Verify save and reload with BFD profile applied on peers

| **Test ID**    | **bfd_profile_func_014**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify save and reload with BFD profile applied on peers** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) C1) Establish BGP, OSPF and PIM sessions between dut1 and dut2.<br/> 2) Configure BFD enabled protocols BGP, OSPF, PIM on dut1 and dut2.<br/> 3) Create different BFD profiles and apply on each protocol interface. Verify BFD session status.<br/> 4) Issue reboot and verify BFD sessions establishment.** |

### 3.3.3 Verify Config reload with BFD profile applied on peers
| **Test ID**    | **bfd_profile_func_015**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify Config reload with BFD profile applied on peers**   |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Establish BGP, OSPF and PIM sessions between dut1 and dut2.<br/> 2) Configure BFD enabled protocols BGP, OSPF, PIM on dut1 and dut2.<br/> 3) Create different BFD profiles and apply on each protocol interface. Verify BFD session status.<br/> 4) Issue config reload and verify BFD sessions establishment.** |

### 3.3.4  Verify BGP docker restart with BFD profile applied on peers
| **Test ID**    | **bfd_profile_func_016**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BGP docker restart with BFD profile applied on peers** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Establish BGP, OSPF and PIM sessions between dut1 and dut2.<br/> 2) Configure BFD enabled protocols BGP, OSPF, PIM on dut1 and dut2.<br/> 3) Create different BFD profiles and apply on each protocol interface. Verify BFD session status.<br/> 7) Issue BGP docker restart and verify BFD sessions establishment.** |

### 3.3.5  Verify Fast reboot restart with BFD profile applied on peers
| **Test ID**    | **bfd_profile_func_017**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify Fast reboot restart with BFD profile applied on peers** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Establish BGP, OSPF and PIM sessions between dut1 and dut2.<br/> 2) Configure BFD enabled protocols BGP, OSPF, PIM on dut1 and dut2.<br/> 3) Create different BFD profiles and apply on each protocol interface. Verify BFD session status.<br/> 4) Issue fast reboot and verify BFD sessions establishment.** |
# 4 Reference Links

https://github.com/BRCM-SONIC/sonic_doc_private/pull/188/files/fdbec2611afd4118610706aef95fed4784211945#diff-303565d4f7382ca4563603c5bafccf9c1c86e1b535a2c188be1d274b97d2bd60

