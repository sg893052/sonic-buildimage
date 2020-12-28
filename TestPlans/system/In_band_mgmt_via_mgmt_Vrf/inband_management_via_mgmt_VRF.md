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

### **3.2 Reboot Test Cases**

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

## Reference Links**

https://github.com/project-arlo/SONiC/blob/d2ab17dd9db986d715e64718c2f1f0be17008311/doc/in-band-mgmt/SONiC_in_band_mgmt_via_mgmt_Vrf_HLD.md