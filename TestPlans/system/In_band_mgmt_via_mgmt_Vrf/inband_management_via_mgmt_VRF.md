SQA Test Plan

###### #SONiC_in_band_mgmt_via_mgmt_Vrf

#SONiC 3.2.0 Release

## Test Plan Revision History

| Rev  | Date       | Author      | Change Description |
| ---- | ---------- | ----------- | ------------------ |
| 1    | 12/28/2020 | Pavan Kumar | Initial Version    |
|      |            |             |                    |
|      |            |             |                    |
|      |            |             |                    |
|      |            |             |                    |

## List of Reviewers

| Function |       Name       |
| :------: | :--------------: |
|   Dev    | venkatmahalingam |
|    QA    | Chandra Bhushan  |
|    QA    |      Hafis       |

## List of Approvers

| Function |       Name       | Date Approved |
| :------: | :--------------: | :-----------: |
|   Dev    | venkatmahalingam |               |
|    QA    | Chandra Bhushan  |               |
|    QA    |      Hafis       |               |

# Definition/Abbreviation
| **Term** | **Meaning**                    |
| -------- | ------------------------------ |
| VRF      | Virtual routing and forwarding |
| FEP      | Front End Port                 |
|          |                                |
|          |                                |

## Introduction

### Objective 

The main objective of this document is to cover the test cases that will be executed for Inband mgmt via mgmt Vrf.  Topologies and test cases  for testing the feature will be discussed as part of this document.

### Scope

Only Inband management via management VRF is covered

### Out of scope

* This test plan does not covers Outband management.

## Feature Overview

VRF is a technology that allows network paths to be segmented without using multiple devices by enabling multiple instances of a routing table to exist in a router and work simultaneously. Management VRF is a subset of VRF (virtual routing tables and forwarding) and provides a separation between the out-of-band management network and the in-band data plane network. For all VRFs, the main routing table is the default table for all of the data plane switch ports. With management VRF a second table mgmt is used for routing through the Ethernet ports of the switch.

## 1 Test Focus Areas

### 1.1 Functional Requirements

* Provide management framework capabilities to handle:

  - Bind/Unbind L3 interface (e.g Phy/Port-channel/VLAN..etc) into mgmt VRF
  - Create/Delete mgmt VRF in HW to trap in-band management traffic to CPU
  - Add/Delete IP configured on the L3 interface into HW for ip2Me action even if the L3 interface is part of mgmt VRF.

## 2 Topologies

!(Inband_topo)

## 3 Test Case and objectives

### **3.1 Functional**

### 3.1.1 Verify that front end port can be assigned to a Management VRF

| **Test ID**    | **FtInbandMgmtFun001**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that front end port can be assigned to a Management VRF** |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Bind it to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Verify the FEP successfully bind to the mgmt vrf <br/> |

### 3.1.2 Verify that SSH works over Management VRF

| **Test ID**    | **FtInbandMgmtFun002**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that SSH works over Management VRF**                |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Bind it to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Verify that SSH to DUT is successful <br/>4)Verify that SSH from DUT is successful <br/> |

### 3.1.3 Verify that Ping works over Management VRF

| **Test ID**    | **FtInbandMgmtFun003**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that Ping works over Management VRF**               |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Bind it to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Verify that Ping works over Management VRF <br/>4)Verify that Ping from DUT is successful <br/> |

### 3.1.4 Remove/add the ipaddress and verify the updated addresses

| **Test ID**    | **FtInbandMgmtFun004**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Remove/add the ipaddress and verify the updated addresses** |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Bind it to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Remove/add the new ipaddress <br/>4)Verify the updated ip addresses <br/> |

### 3.1.5 Unbind and Rebind the interfaces to the vrf and verify traffic.

| **Test ID**    | **FtInbandMgmtFun005**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Unbind and Rebind the interfaces to the vrf and verify traffic** |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Bind it to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Start the traffic <br/>4) Unbind and Rebind the interfaces to the vrf <br/>5) Verify the traffic recovered <br/> |

### 3.1.6 Add the same ip in mgmt and default vrf, make sure ping works fine.

| **Test ID**    | **FtInbandMgmtFun006**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Add the same ip in mgmt and default vrf, make sure ping works fine** |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Bind it to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Configure interface (Physical,vlan,portchannel) <br/>4) Bind it to default Vrf and Configure same IP and IPv6 addresses <br/>5) Verify the ping works <br/> |

### 3.1.7 Test case to delete and add mgmt vrf & interface as part of mgmt vrf multiple times and make sure it is added back and ping works fine after each iteration.

| **Test ID**    | **FtInbandMgmtFun007**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify delete and add Management VRF and its interface multiple times** |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Bind it to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Verify that Ping works over Management VRF <br/>4)Delete the Mgmt vrf and its interfaces and add them back <br/>5) Verify that Ping works over Management VRF <br/> |

### 3.1.8 Adding multiple ip address on the mgmt vrf interface, verify if they are accepted

| **Test ID**    | **FtInbandMgmtFun008**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Adding multiple ip address on the mgmt vrf interface, verify if they are accepted** |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Bind it to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) add multiple ip address on the mgmt vrf interface <br/>4)Verify the updated ip addresses <br/> |

### 3.1.9 Enable protocols such as STP,OSPF on the vlan or portchannel interface which are part of mgmt vrf, it should be rejected

| **Test ID**    | **FtInbandMgmtFun009**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Enable STP,OSPF on the vlan or portchannel interface which are part of mgmt vrf, it should be rejected** |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Bind it to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Enable STP,OSPF on the vlan or portchannel interface which are part of mgmt vrf, it should be rejected <br/> |

### 3.1.10 Verify that sub interface can be assigned to a Management VRF

| **Test ID**    | **FtInbandMgmtFun010**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that sub interface can be assigned to a Management VRF** |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure the sub interface <br/>2) Bind it to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Verify the sub interface successfully bind to the mgmt vrf <br/> |

### 3.1.11  Verify sub interfaces of different physical ports can be assigned to same vrf
| **Test ID**    | **FtRoSubIntfFunc011**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify sub interfaces of different physical ports can be assigned to same vrf** |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure the sub interfaces of two different physical interfaces <br/>2) Bind them to Vrf and Configure IP and IPv6 addresses <br/>3) Verify the ping to the sub interfaces viceversa works fine<br/> |

### 3.1.12  Verify the sub interfaces of same physical ports can be assigned to different vrf
| **Test ID**    | **FtRoSubIntfFunc012**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the sub interfaces of same physical ports can be assigned to different vrf** |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure two sub interfaces of same physical interfaces <br/>2) Bind them to different Vrf and Configure IP and IPv6 addresses <br/>3) verify the sub interfaces has been bind to the appropriate vrfs<br/> |

### 3.1.13  Verify that ntp sync is successful over Management VRF
| **Test ID**    | **FtRoSubIntfFunc013**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ntp sync is successful over Management VRF**   |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure mgmt vrf <br/>2) Bind FEP to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Verify the FEP successfully bind to the mgmt vrf <br/>4) Verify that ntp sync is successful over Management VRF |

### 3.1.14  Verify that SNMP successful over Management VRF
| **Test ID**    | **FtRoSubIntfFunc014**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that SNMP successful over Management VRF**          |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure mgmt vrf <br/>2) Bind FEP to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Verify the FEP successfully bind to the mgmt vrf <br/>4) Verify that snmp requests is successful over Management VRF |

### 3.1.15  Verify that TACACS+ authentication is successful with Management VRF
| **Test ID**    | **FtRoSubIntfFunc015**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that TACACS+ authentication is successful with Management VRF** |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure mgmt vrf <br/>2) Bind FEP to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Add tacacs server authentication <br/>4)  Verify that TACACS authentication is successful with Management VRF |

### 3.1.16  Verify that Radius authentication is successful with Management VRF
| **Test ID**    | **FtRoSubIntfFunc016**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that Radius authentication is successful with Management VRF** |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure mgmt vrf <br/>2) Bind FEP to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Add Radius server authentication <br/>4)  Verify that Radius authentication is successful with Management VRF |

### 3.1.17  Verify that ZTP is successful over Management VRF
| **Test ID**    | **FtRoSubIntfFunc017**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ZTP is successful over Management VRF**        |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure mgmt vrf <br/>2) Bind FEP to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Add Radius server authentication <br/>4)  Verify that Radius authentication is successful with Management VRF |

### 3.1.18  Verify that sFlow server reachablity over Management VRF
| **Test ID**    | **FtRoSubIntfFunc018**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that sFlow server reachablity over Management VRF** |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure mgmt vrf <br/>2) Bind FEP to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Configure a sFlow server and generate samples <br/>4)  Verify that sFlow samples are recived at server successfully over Management VRF |

### 3.2 Reboot Test Cases**

### 3.2.1 Warm reboot with Management VRF

| **Test ID**    | **FtInbandMgmtReboot001**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Warm reboot with Management VRF**                          |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Bind it to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Warmboot the DUT with ping to fep in progress <br/>4) Verify that warm boot is successful and Management VRF config sustains the warmboot <br/> |

### 3.2.2 Cold reboot with Management VRF

| **Test ID**    | **FtInbandMgmtReboot002**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Cold reboot with Management VRF**                          |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Bind it to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Cold boot the DUT with ping to fep in progress <br/>4) Verify that cold boot is successful and Management VRF config sustains the coldboot <br/> |

### 3.2.3 Config reload with Management VRF

| **Test ID**    | **FtInbandMgmtReboot003**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Config reload with Management VRF**                        |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Bind it to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Verify that config reload is successful and Management VRF config sustains the config reload <br/> |

### 3.2.4 Fast reboot with Management VRF.

| **Test ID**    | **FtInbandMgmt004**                                          |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Fast reboot with Management VRF**                          |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Bind it to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Fast reboot the DUT <br/>4) Verify that cold boot is successful and Management VRF config sustains the coldboot <br/> |

### **3.3 Negative Test Cases**

### 3.3.1 To verify the traffic with the port in default vrf.

| **Test ID**    | **FtInbandMgmtNeg001**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **verify the traffic with the ports in default vrf**         |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Bind it to default Vrf and Configure IP and IPv6 addresses <br/>3) Verify the traffic flowing <br/>4) Rebind the interface from default to the mgmt vrf and re-verify the traffic drop <br/>5) Rebind the interface to the default vrf and verify traffic recovery |

### 3.3.2 Try deleting the mgmt vrf and verify.

| **Test ID**    | **FtInbandMgmtNeg002**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Try deleting the mgmt vrf and verify**                     |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Bind it to mgmt Vrf and Configure IP and IPv6 addresses <br/>3) Verify the mgmt vrf cannot be deleted. |

### 3.3.3 Deleting vlan or portchannel shouldn't be allowed if it is part of any vrf.

| **Test ID**    | **FtInbandMgmtNeg003**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Deleting vlan or portchannel shouldn't be allowed if it is part of any vrf** |
| **Test Setup** | **Inband_topo**                                              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (vlan,portchannel) <br/>2) Bind it to default Vrf and Configure IP and IPv6 addresses <br/>3) Verify the interface bind with vrf <br/>4) Deleting vlan/portchannel should fail as long aas long as the interface is a part of vrf. <br/>5) Repeat the test with mgmt vrf as well |

## Reference Links**

https://github.com/project-arlo/SONiC/blob/d2ab17dd9db986d715e64718c2f1f0be17008311/doc/in-band-mgmt/SONiC_in_band_mgmt_via_mgmt_Vrf_HLD.md

