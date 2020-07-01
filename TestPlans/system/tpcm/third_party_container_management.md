# SQA Test Plan

# Third Party Container Management
Rev 1

[TOC]

## Test Plan Revision History

| Rev  | Date        | Author | Change Description |
| ---- | ----------- | ------ | ------------------ |
| 1    | 06/09/20202 | Julius | Initial version    |
|      |             |        |                    |
|      |             |        |                    |

## List of Reviewers

|   Reviewer Name    | Date Reviewed |
| :----------------: | :-----------: |
| Kalimuthu Velappan |               |

## List of Approvers

|   Approver Name    | Date Approved |
| :----------------: | :-----------: |
| Kalimuthu Velappan |               |

## Definition/Abbreviation

| **Term** | **Meaning**                           |
| -------- | ------------------------------------- |
| TPC      | Third Party Container                 |
| TPCM     | Third Party Container Management      |
| ONIE     | Open Network Installation Environment |
## Introduction

### Objective 

Objective of this document is to cover the test cases planned for SONIC 3.1 feature "Third Party Container Management". All the testcases will be tested with default vrf 

## Feature Overview

TPCM allows the user to install/upgrade/uninstall third party container into the SONIC OS environment which acts independently. Zero or More TPC can be installed into the SONIC system. 

## 1 Functional Testcases

### 1.1 Test TPC installation using HTTP

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test TPC installation using HTTP**                         |
| **Test Setup** | **single node DUT**                                          |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) bring up DUT with SONIC image<br/>2)  install TPC using HTTP "tpcm install mydocker http://myserver/path/mydocker.tar.gz -y"<br/>3) Verify tpc using "tpcm list"<br/>4) verify the presence of TP docker using "docker ps"<br/>5) uninstall TPC using "tpcm uninstall \<tpc-name\>  -y"<br/>6) Verify TPC service and image file gets removed; check the folders /host/tcp/images and /host/tpc/service<br/>7) verify "docker ps" for the uninstalled TPC |

### 1.2 Test TPC installation using SCP

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test TPC installation using SCP**                          |
| **Test Setup** | **single node DUT**                                          |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) bring up DUT with SONIC image<br/>2) start TPC install using SCP<br/>3) Verify tpc using "tpcm list"<br/>4) verify the presence of TP docker using "docker ps"<br/>5) do the uninstallation using "tpcm uninstall \<tpc-name\>  -y"<br/>6) Verify TPC service and image file gets removed; check the folders /host/tcp/images and /host/tpc/service<br/>7) verify "docker ps" for the uninstalled TPC<br/> |

### 1.3 Test TPC installation using SFTP

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test TPC installation using SFTP**                         |
| **Test Setup** | **single node DUT**                                          |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) bring up DUT with SONIC image<br/>2) install TPC using SFTP<br/>3) Verify tpc using "tpcm list"<br/>4) verify the presence of TP docker using "docker ps"<br/>5) uninstall using "tpcm uninstall \<tpc-name\>  -y"<br/>6) Verify TPC service and image file gets removed; check the folders /host/tcp/images and /host/tpc/service<br/>7)  verify "docker ps" for the uninstalled TPC<br/>|

### 1.4 Test TPC installation using media path

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test TPC installation using media path**                   |
| **Test Setup** | **single node DUT**                                          |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) bring up DUT with SONIC image<br/>2) install TPC using media path "tpcm install mydocker /media/usb/path/mydocker.tar.gz -y"<br/>3) Verify tpc using "tpcm list"<br/>4) verify the presence of TP docker using "docker ps"<br/>5) uninstall TPC using "tpcm uninstall \<tpc-name\>  -y"<br/>6) Verify TPC service and image file gets removed; check the folders /host/tcp/images and /host/tpc/service<br/>7)  verify "docker ps" for the uninstalled TPC<br/> |

### 1.5 Test TPC installation using docker hub

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test TPC installation using docker hub**                   |
| **Test Setup** | **single node DUT**                                          |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) bring up DUT with SONIC image<br/>2) install TPC using docker hub "tpcm install \<tpc-container-name\> \<Image name\>[:\<Tagname\>\]\[--args \] \[-y\]"<br/>3) Verify tpc using "tpcm list"<br/>4) verify the presence of TP docker using "docker ps"<br/>5) uninstall TPC using "tpcm uninstall \<tpc-name\>  -y"<br/>Verify TPC service and image file gets removed; check the folders /host/tcp/images and /host/tpc/service<br/>7)  verify "docker ps" for the uninstalled TPC<br/> |

### 1.6 Test TPC upgrade without skip data option

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test TPC upgrade without skip data option**                |
| **Test Setup** | **single node DUT**                                          |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) bring up DUT with SONIC image<br/>2) add the route "sudo ip rule add pref 32768 from all lookup mgmt"<br/>3) install two TPC(TPC1,TPC2) using HTTPS "tpcm install mydocker https://myserver/path/mydocker.tar.gz -y"<br/>4) upgrade TPC1 alone using "tpcm upgrade  \<TPC1\> \<tpc-image source\> -y "<br/>5) verify container data backup and restore steps are NOT skipped<br/>6) verify TPC container created from newly installed TPC1 image<br/>7) verify old TPC1 container and image got removed<br/>8) Verify TPC2 container remains same<br/>9) uninstall TPC1 and TPC2 using "tpcm uninstall \<tpc-name\>  -y"<br/>10) Verify TPC service and image file gets removed; check the folders /host/tcp/images and /host/tpc/service<br/>11)  verify "docker ps" for the uninstalled TPC's |

### 1.7 Test TPC upgrade with skip data option

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test TPC upgrade with skip data option**                   |
| **Test Setup** | **single node DUT**                                          |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) bring up DUT with SONIC image<br/>2) install TPC using HTTPS "tpcm install mydocker https://myserver/path/mydocker.tar.gz -y"<br/>3) upgrade TPC with skip_data option "tpcm upgrade  \<tpc-name\> \<tpc-image source\> -y --skip_data"<br/>4) verify container data backup and restore steps skipped<br/>5) verify TPC container created from newly installed TPC image<br/>6) verify old TPC container and image got removed<br/>7) uninstall TPC using "tpcm uninstall \<tpc-name\>  -y"<br/>8) Verify TPC service and image file gets removed; check the folders /host/tcp/images and /host/tpc/service<br/>9)  verify "docker ps" for the uninstalled TPC |

### 1.8 Verify TPC not restored with SONIC image netinstall

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test TPC not restored with SONIC image netinstall**        |
| **Test Setup** | **single node DUT**                                          |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) bring up DUT with SONIC image<br/>2) install 2 TPC and verify service started for each TPC's(TPC1 and TPC2)<br/>3) Netinstall SONIC image <br/>4) verify no TPC exists once the DUT comes up |

### 1.9 Test TPC restoration with SONIC image upgrade

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test TPC restoration with SONIC image upgrade**            |
| **Test Setup** | **single node DUT**                                          |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) bring up DUT with SONIC image<br/>2) install four TPC and  verify service started for each TPC's(TPC1, TPC2, TPC3 and TPC4)<br/>3) Uninstall TPC1 and verify that particular TPC's service and image file got removed<br/>4) stop TPC2 service and verify that particular TPC's service stopped<br/>5) upgrade SONIC image and wait for it to be complete<br/>6) verify TPC2,TPC3 and TPC4 are restored when DUT comes up with new SONIC image<br/>7) Verify TPC2's service not started but started for TPC3, TPC4<br/>8) uninstall TPC2, TPC3 and TPC4<br/>9) Verify TPC service and image file gets removed; check the folders /host/tcp/images and /host/tpc/service<br/>10)  verify "docker ps" for the uninstalled TPC's |

### 1.10 Test CPU usage for TPC

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test CPU usage for TPC**                                   |
| **Test Setup** | **single node DUT**                                          |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) bring up DUT with SONIC image<br/>2) install four TPC (TPC1, TPC2, TPC3 and TPC4) one by one<br/>3) verify CPU usage for each TPC is restricted to 20%<br/>4) uninstall TPC's one by one<br/>5) verify CPU usage is freed up for each TPC |

### **2 Reboot testcases**

### 2.1 Test TPC restoration with cold reboot

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test TPC restoration with system reboot**                  |
| **Test Setup** | **single node DUT**                                          |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) bring up DUT with SONIC image<br/>2) install four TPC and verify service started for each TPC's(TPC1, TPC2, TPC3 and TPC4)<br/>3) uninstall TPC1 and verify that particular TPC's service file and image got removed<br/>4) stop TPC2 service and verify that particular TPC's service stopped<br/>5) Trigger cold reboot<br/>6) verify TPC2,TPC3 and TPC4 are restored when DUT comes up after reboot<br/>7) Verify TPC2's service not started but started for TPC3, TPC4<br/>8) uninstall TPC2, TPC3 and TPC4<br/>9) Verify TPC service and image file gets removed; check the folders /host/tcp/images and /host/tpc/service<br/>10)  verify "docker ps" for the uninstalled TPC's<br/> |

### 2.2 Test TPC restoration with fast reboot

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test TPC restoration with fast reboot**                    |
| **Test Setup** | **single node DUT**                                          |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) bring up DUT with SONIC image<br/>2) install four TPC and verify service started for each TPC's(TPC1, TPC2, TPC3 and TPC4)<br/>3) uninstall TPC1 and verify that particular TPC's service file and image got removed<br/>4) stop TPC2 service and verify that particular TPC's service stopped<br/>5) Trigger fast reboot<br/>6) verify TPC2,TPC3 and TPC4 are restored when DUT comes up after reboot<br/>7) Verify TPC2's service not started but started for TPC3, TPC4<br/>8) uninstall TPC2, TPC3 and TPC4<br/>9) Verify TPC service and image file gets removed; check the folders /host/tcp/images and /host/tpc/service<br/>10)  verify "docker ps" for the uninstalled TPC's<br/> |

### 2.3 Test TPC restoration with warm reboot

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test TPC restoration with warm reboot** |
| **Test Setup** | **single node DUT**                                          |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) bring up DUT with SONIC image<br/>2) install four TPC and verify service started for each TPC's(TPC1, TPC2, TPC3 and TPC4)<br/>3) uninstall TPC1 and verify that particular TPC's service file and image got removed<br/>4) stop TPC2 service and verify that particular TPC's service stopped<br/>5) Trigger warm reboot<br/>6) verify TPC2,TPC3 and TPC4 are restored when DUT comes up after reboot<br/>7) Verify TPC2's service not started but started for TPC3, TPC4<br/>8) uninstall TPC2, TPC3 and TPC4<br/>9) Verify TPC service and image file gets removed; check the folders /host/tcp/images and /host/tpc/service<br/>10)  verify "docker ps" for the uninstalled TPC's<br/> |

### **3 Negative Testcases**

### 3.1 Test TPC upgrade for non existing container

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Test TPC upgrade for non existing container**              |
| **Test Setup** | **single node DUT**                                          |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) bring up DUT with SONIC image<br/>2) upgrade TPCM for the non existing container "tpcm upgrade  \<non_existing_container\> \<tpc-image source\> -y<br/>3) verify error message is generated<br/> |

## **Reference Links**

[TPCM HLD Gerrit link]:http://gerrit-lvn-07.lvn.broadcom.net:8083/plugins/gitiles/sonic/documents/+/refs/changes/62/21762/2/platforms/tpcm/tpcm.md

