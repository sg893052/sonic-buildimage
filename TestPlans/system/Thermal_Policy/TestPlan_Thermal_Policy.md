#  SQA Test Plan
# Thermal Policy
#  SONiC 3.1 Buzznik+ Release
[TOC]
# Test Plan Revision History
| Rev | Date | Author | Change Description |
|:---:|:-----------:|:------------------:|-----------------------------|
| 0.1 | 05/11/2020 | Kevin Nguyen | Initial version |
| 0.2 | 05/21/2020 | Kevin Nguyen | Incorporated review comments |

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
| ODM  | Original Design Manufacturer |
| OEM  | Original Equipment Manufacturer |
| PDDF  | Platform Driver Development Framework |
| PSU   | Power Supply Unit |
| I2C  | Inter-integrated Circuit communication protocol |
| SysFS | Virtual File System provided by the Linux Kernel |
| BMC  | Baseboard Management Controller |

# Feature Overview

Thermal policy for a platform dictates how the platform would react to temperature changes in its operating environment. Usually the policy is driven by the ambient temperature and operational status of the Fans. The ambient temperature is determined by the onboard temperature sensors which are accessible by CPU \(using I2C/PCI\) or Baseboard Management Controller \(BMC\).

In Buzznik+ release, thermal policy is supported on Accton platform AS4630-54PE. Following is the platform configuration

**_AS4630-54PE_**

| **Platform**                | **Number of Fans**          | **Number of temperature Sensors**      | **Sensor Type**    |
|-----------------------------|-----------------------------|----------------------------------------|--------------------|
| (Accton TD3-X5) AS4630-54PE |           3                 | 3 (Onboard-0x48, CPU-0x4b and FAN-0x4a)|   LM77 and LM75    |

# 1 Test Focus Areas
## 1.1 Functional Testing 
  - All CLI 'show' commands
  - Thermal Rules (Please see the Thermal Policy HLD, Section 3 for details)
  - Fan module hot-swap
  - Syslogs
  - Config reload
  - Warm reboot
  - Cold reboot

## 1.2 Negative Testing 
  -	Multiple Fans OIR
  -	Force system shutdown on high temperature

## 1.3 Scale and Performance Testing

# 2 Topologies
## 2.1 Topology 1
Standalone DUT

## 2.2 Topology 2
NA

# 3 Test  Case and Objectives
## 3.1 CLI
### 3.1.1 Verify CLI command for monitoring fans

| **Test ID** | **iTAS Test Case Manager ID** |
|--------|:----------------|
| **Test Name** | **Verify cli show command for monitoring fans** |
| **Test Setup** | **Topology1** |
| **Type** | **CLI** |
| **Steps** | **Verify all supported CLI commands and help strings are displayed<br/>1) show platform fanstatus<br/>2) show environment ** |

## 3.2 Functional
### 3.2.1 Verify Fans status under a normal operational condition

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify fans status under a normal operational condition** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Issue 'show platform fanstatus'. Verify number of fans, status, speed (RPM) and direction<br/>2) Issue 'show environment' to verify fans RPM and components temperature<br/>3) Verify syslogs** |

### 3.2.2 Hot-swap Fan Module

| **Test ID** | **iTAS Test Case Manager ID** |
|--------|:----------------|
| **Test Name** | **Remove and re-insert a fan module** |
| **Test Setup** | **Topology1** |
| **Type** | **Functional** |
| **Steps** | **Remove and reinsert each and every fan module, one at a time<br/>1) Remove a fan module<br/>2) Issue 'show platform fanstatus' to verify the remaining of the fans operated in max RPM cycle and the fan removed ID matched with the label on the box<br/>3) Issue 'show environment' to verify fans RPM and components temperature <br/>4) Verify logs captured the fan removal event and Fan ID matched<br/>5) Re-insert the fan module<br/>6) Repeat steps 1-5 for next fan module ** |

### 3.2.3 Force system temperature increase

| **Test ID** | **iTAS Test Case Manager ID** |
|--------|:----------------|
| **Test Name** | **Force system temperature increase** |
| **Test Setup** | **Topology1** |
| **Type** | **Functional** |
| **Steps** | **The following steps apply for each and every fan module, one at a time<br/>1) Remove a fan module<br/>2) Use a card board or paper to cover the fan housing<br/>3) Issue 'show platform fanstatus' and 'show environment' repeatedly and observe if system temperature increase<br/>4) Verify logs captured the fan removal and high temp threshold crossed events<br/>5) Re-insert the fan module<br/>6) Repeat steps 1-5 for next fan module** |

## 3.3 Negative
### 3.3.1 Force system reset/shutdown on high temperature

| **Test ID** | **iTAS Test Case Manager ID** |
|--------|:----------------|
| **Test Name** | **Force system reset/shutdown on high temperature** |
| **Test Setup** | **Topology1** |
| **Type** | **Functional** |
| **Steps** | **Remove all fan modules, one at a time<br/>to force system reset or shutdown (depends on Thermal policy implemented by the ODMs<br/>1) Remove a fan module<br/>2) Issue 'show platform fanstatus' to verify the remaining of the fans operated in max RPM cycle and the fan removed ID matched with the label on the box<br/>3) 'show environment' to verify fans RPM and components temperature<br/>4) Verify logs captured the fan removal event and Fan ID matched<br/>5) Repeat steps 1-4 for next fan module until no more fan module left<br/>6) Observed a message on console to alert system will be reset / shutdown in 120 seconds<br/>7) If shutdown action supported, let system shutdown completely<br/>8) Power off PDU outlet where system AC connected to<br/>9) Re-insert all fan modules back to theirs housing<br/>10) Power on PDU<br/>11) If only Reset action supported, then just re-insert all fan modules when system initiates reset<br/>12) Verify system boot up successfully and wait for system to be ready<br/>13) Verify logs for system reset/shutdown logs due to high temp<br/>14) Issue 'show platform fanstatus' to verify all the fans operate in normal range RPM cycle<br/>15) Issue 'show environment' to verify fans RPM and components temperature<br/>16) Document ODM supported action: system reset or system shutdown** |

## 3.4 Reboot/Reload/Upgrade
#### 3.4.1 Warm reboot

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Warm Reboot**                                              |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Issue 'warm-reboot -y' command<br/>2) Verify system boot up successfully and wait for system to be ready<br/>3) Issue 'show platform fanstatus' to verify all the fans operate in normal range RPM cycle<br/>4) Issue 'show environment' to verify fans RPM and components temperature<br/>5) Verify fan events in system logs** |

#### 3.4.2  Cold reboot

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Cold Reboot**                                              |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Issue 'reboot -y' command<br/>2) Verify system boot up successfully and wait for system to be ready<br/>3) Issue 'show platform fanstatus' to verify all the fans operate in normal range RPM cycle<br/>4) Issue 'show environment' to verify fans RPM and components temperature<br/>5) Verify fan events in system logs** |

#### 3.4.3 Config reload

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Config Reload**                                            |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Issue 'config reload -y' command<br/>2) Wait for system to be ready<br/>3) Issue 'show platform fanstatus' to verify all the fans operate in normal range RPM cycle<br/>4) Issue 'show environment' to verify fans RPM and components temperature<br/>5) Verify fan events in system logs** |

#### 3.4.4  Upgrade

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Upgrade**                                                  |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Issue 'sonic_installer install \<URL\>' command<br/>2) Wait for installer to complete new image installation<br/>3) Issue 'reboot -y'<br/>4) Verify system boot up successfully and wait for system to be ready<br/>5) Issue 'show platform fanstatus' to verify all the fans operate in normal range RPM cycle<br/>6) Issue 'show environment' to verify fans RPM and components temperature<br/>7) Verify fan events in system logs** |

## 3.5 Management

#### 3.5.1 SNMP

#### 3.5.2 gNMI

#### 3.5.3 REST API

## 3.6 Stress,Scale and Performance
#### 3.6.1 Cancel system reset/shutdown after re-insert one fan

| **Test ID** | **iTAS Test Case Manager ID** |
|--------|:----------------|
| **Test Name** | **Cancel system reset/shutdown after re-insert the last fan** |
| **Test Setup** | **Topology1** |
| **Type** | **Functional** |
| **Steps** | **1) Remove all fan modules, one at a time<br/>2) As soon as a message displayed on the console to alert system will be reset/shutdown in 120 seconds, re-insert one fan back into its housing to cancel system reset/shutdown<br/>3) Waited for 120 seconds passed and system should not be reset/shutdown<br/>4) Re-insert all fan modules back to theirs housing, one at a time for all fans<br/>5) Issue 'show platform fanstatus' to verify all the fans operate in normal range RPM cycle<br/>6) Issue 'show environment' to verify fans RPM and components temperature<br/>7) Verify system cancel reset/shutdown logs** |
# 4 Reference Links

Thermal monitoring HLD
<http://gerrit-lvn-07.lvn.broadcom.net:8083/c/sonic/documents/+/20810>

