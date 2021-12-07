SQA Test Plan

###### #SONiC_Reserved_VLAN

#SONiC 4.0.0 Release

## Test Plan Revision History

| Rev  | Date       | Author         | Change Description |
| ---- | ---------- | -------------- | ------------------ |
| 1    | 11/21/2021 | M A Raheem Ali | Initial Version    |
|      |            |                |                    |
|      |            |                |                    |
|      |            |                |                    |
|      |            |                |                    |

## List of Reviewers

| Function |      Name       |
| :------: | :-------------: |
|   Dev    | Kamlesh Agarwal |
|    QA    | Chandra Bhushan |
|    QA    |    Chandra V    |

## List of Approvers

| Function |      Name       | Date Approved |
| :------: | :-------------: | :-----------: |
|   Dev    | Kamlesh Agarwal |               |
|    QA    | Chandra Bhushan |               |
|    QA    |    Chandra V    |               |

# Definition/Abbreviation

| **Term** | **Meaning**            |
| -------- | ---------------------- |
| CLI      | Command Line Interface |
| DUT      | Device under test      |
|          |                        |
|          |                        |

## Introduction

### Objective 

The main objective of this document is to cover the test cases that will be executed to verify the Reserved VLAN.  Topologies and test cases  for testing the feature will be discussed as part of this document.

### Scope

This document describes the Testcases which covers Reserved VLAN.

## 1 Test Focus Areas

### 1.1 Functional Requirements

- Bring up the topology mentioned below.
- Verify default and Verify Non-default Range.
- Verify whether the system vlan <VLAN_ID> reserve the VLAN as expected.

## 2 Topologies

D1

## 3 Test Case and objectives

1. Verify the Default Reserved VLAN is 3967-4094.
2. Change the Default Reserved VLAN range and Verify it's updated.
3. Revert the Non-Default Reserved VLAN range and Verify Default Range is Reverted Back.
4. Verify Running Config is reflecting the Default reserved VLAN range.
5. Migration test from 3.x.x release to Cyrus.

### **3.1 Functional**

### 3.1.1 Verifying CLI works fine.

| **Test ID**    | reserved_vlan_range_cli                                      |
| :------------- | :----------------------------------------------------------- |
| **Test Name**  | reserved_vlan_range_func_001                                 |
| **Test Setup** | **reserved_vlan_topo**                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify Default Range of Reserved Vlans is 3967-4094.<br/>2) Verify running config is getting reflected with the default VLAN range. <br/>3) Try to Change the Default Range using the range command and verify range gets updated as per the config and also the running Config. <br/>4) Try to Change the reserved VLAN range as out of range and DUT should throw a suitable error. <br/> |

### 3.1.2 Verifying Default Reserved VLAN.

| **Test ID**    | reserved_vlan_range_001                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | reserved_vlan_range_func_002                                 |
| **Test Setup** | **reserved_vlan_topo**                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure a Vlan which is in the Reserved Vlan Range. <br/>2) Verify System throws an error and verify that VLAN isn't configured.<br/> |

### 3.1.3 Verifying Non-Default Reserved VLAN by changing the default range.

| **Test ID**    | reserved_vlan_range_002                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | reserved_vlan_range_func_003                                 |
| **Test Setup** | **reserved_vlan_topo**                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Change the Default reserved Vlan range to Non-Default Vlan range(1000-1128) and Verify it gets updated.<br/>2. Try to configure a VLAN which was is in the default reserved VLAN range(3967-4094) and System shouldn't throws an error message.<br/>3. Again Change the range to some new value(3000-3128) and Verify Range gets reflected using the show command.<br/>4. Try to Configure a VLAN which was in the previously configured reserved VLAN range and System shouldn't throws a error message.<br/>5. Unconfig Non-default range and verify default reserved VLAN range gets reverted back.<br/> |

### 3.1.4 Verify that Default and Non-default Reserved VLAN are retained across Config reload, cold reboot, fast reboot and Warm reboot.

| **Test ID**    | reserved_vlan_range_reboots                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **reserved_vlan_range_func_004**                             |
| **Test Setup** | **reserved_vlan_topo**                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure reserved VLAN and verify prior and post to config reload, cold reboot, fast reboot and Warm Reboot.<br/>2. Configure Non-default reserved VLAN and verify prior and post to config reload, cold reboot, fast reboot and Warm Reboot.<br/> |

### 3.1.5 Verifying Boundary VLAN by Changing the default range.

| **Test ID**    | reserved_vlan_range_003                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **reserved_vlan_range_func_005**                             |
| **Test Setup** | **reserved_vlan_topo**                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Change the Default Vlan range to Non-Default Range(2000-2127) and Verify it gets updated.<br />2. Configure a 1999, 2000, 2127 and 2128 VLAN. 1999 and 2128 should be successful.<br /> |

### 3.1.6 Verify reserved VLAN is retained across migration.

| **Test ID**    | reserved_vlan_upgrade_downgrade                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **reserved_vlan_range_func_005**                             |
| **Test Setup** | **reserved_vlan_topo**                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure a range on VLANs from3967-4094.<br />2. Add couple of Members to Each VLAN.<br />3. Upgrade to Cyrus Image and post Upgrade verify user is able to delete , add members to this VLAN, add IP address to this VLAN and configure new VLAN in that range. |

Reference: https://github.com/BRCM-SONIC/sonic_doc_private/pull/305/files#

