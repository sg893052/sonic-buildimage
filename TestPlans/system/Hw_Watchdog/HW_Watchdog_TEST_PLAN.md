# HW Watchdog Test Plan

# SONiC 3.1.1 Project and Buzznik_plus_MR Release

[TOC]

# Test Plan Revision History

| Rev  |    Date    |        Author        | Change Description |
| :--: | :--------: | :------------------: | :----------------: |
| 0.1  | 10/06/2020 | Praveen Kumar Kota   |  Initial version   |

# List of Reviewers

| Function |         Name         |
| :------: | :------------------: |
|   Dev    |      Kalimuthu       |
| QA | Babu Rajaram |
| QA | Anil Kumar Kolkaleti |

# List of Approvers

| Function |         Name         | Date Approved |
| :------: | :------------------: | :-----------: |
|   Dev    |      Kalimuthu       |               |
| QA | Babu Rajaram | |



# 1 Topologies

## 1.1 Topology

#### <span style="color:red"> </span>

![Topology:1](HW_Watchdog_TOPOLOGY.png "Figure: HW_Watchdog ")


# 2 Functional

### 2.1.1 Verify the watchdog reset by killing the watchdog daemon process/hardware hung simulation.

| **Test ID**    | **Tescase1**                                                 |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the watchdog reset by killing the watchdog daemon process/hardware hung simulation.**   |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **<br/>Procedure:<br/>1)  Pause the watchdog util timer using "watchdogutil pause".<br/>2) Check the status of of watchdogutil using "watchdogutil status".<br/>3) Wait untill the expiry of watchdog timer.<br/>4) Check whether systems get reset after WDT expiry.<br/>Expected Result:<br/>1) Verify that command got executed properly. <br/>2) Verify the status is paused.<br/>3) Verify that WDT got expired.<br/>4)  Verify that watchdog reset by killing the watchdog daemon process/hardware hung simulation.** |


### 2.1.2 Verify the reboot reason when system reboots because of watchdog timeout.

| **Test ID**    | **Tesctase2**                                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the reboot reason when system reboots because of watchdog timeout.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **<br/>Procedure:<br/>1)  Pause the watchdog util timer using "watchdogutil pause".<br/>2) Check the status of of watchdogutil using "watchdogutil status".<br/>3) Wait untill the expiry of watchdog timer.<br/>4) Check whether systems get reset after WDT expiry.<br/>5) Check the reboot reason.<br/>Expected Result:<br/>1) Verify that command got executed properly. <br/>2) Verify the status is disarmed.<br/>3) Verify that WDT got expired.<br/>4)  Verify that watchdog reset by killing the watchdog daemon process/hardware hung simulation.<br/>5) Verify the reboot reason is shown as exact reason.** |


### 2.1.3   Verify the watchdog enable/disable support.

| **Test ID**    | **Tesctase3**                                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the watchdog enable/disable support.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **<br/>Procedure:<br/>1) Check whether watchdog can be disabled or not using "watchdogutil disarm"<br/>2) Check whether the status is shown as disarmed or not.<br/>3) Revert back the watchdog config by enabling back using "watchdogutil arm"<br/>4) Check wherther the status is shown as armed or not.<br/>Expected Result:<br/>1) Verify that command got executed properly.<br/>2) Verify the status is disarmed.<br/>3) Verify the command got executed properly.<br/>4) Verify the watchdog can be enable/disable.** |


### 2.1.4 Verify the watchdog disable during kdump collection.
| **Test ID**    | **Tesctase4**                                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the watchdog disable during kdump collection.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **<br/>Procedure:<br/>1)  Try to get kdump collection using " echo c>/proc/sysrq-trigger".<br/>2) Check watchdog get disable.<br/>3) Check whether system get reset and get reboot reason.<br/>4Collect kdump status.<br/>Expected Result:<br/>1) Verify the step executed successfully.<br/>2,3) Verify that system gets reset and reboot cause is shown appropriate message.<br/>4) Verify the watchdog disable during kdump collection.** |


### 2.1.5 Verify the watchdog reading of watchdog timeout value.
| **Test ID**    | **Tesctase5**                                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the watchdog reading of watchdog timeout value.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **<br/>Procedure:<br/>1)  Check the watchdog reading of watchdog timeout value using "watchdogutil timeout".<br/>Expected Result:<br/>1) Verify the watchdog reading of watchdog timeout value.** |


### 2.1.6 Verify the 2.0 API interface.
| **Test ID**    | **Tesctase6**                                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the 2.0 API interface.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **<br/>Procedure:<br/>1)  Check the Watchdogtutil and systemctl status watchdog-control should show running status.<br/>Expected Result:<br/>1) Verify that Watchdogtutil and systemctl status watchdog-control are shown in show  running status.** |


# 3.Reference
http://gerrit-lvn-07.lvn.broadcom.net:8083/plugins/gitiles/sonic/documents/+/refs/changes/06/23706/3/platforms/watchdog/watchdog.md



