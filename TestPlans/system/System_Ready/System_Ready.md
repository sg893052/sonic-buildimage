SQA Test Plan

###### #SONiC_System_Ready_Status_Enhancement

#SONiC 4.0 Release

## Test Plan Revision History

| Rev  | Date       | Author      | Change Description |
| ---- | ---------- | ----------- | ------------------ |
| 1    | 06/14/2021 | Pavan Kumar | Initial Version    |
|      |            |             |                    |

## List of Reviewers

| Function |          Name           |
| :------: | :---------------------: |
|   Dev    | Senthil Kumar Guruswamy |
|    QA    |     Chandra Bhushan     |
|    QA    |        Chandra V        |

## List of Approvers

| Function |          Name           | Date Approved |
| :------: | :---------------------: | :-----------: |
|   Dev    | Senthil Kumar Guruswamy |               |
|    QA    |     Chandra Bhushan     |               |
|    QA    |        Chandra V        |               |

# Definition/Abbreviation

| **Term** | **Meaning**            |
| -------- | ---------------------- |
| CLI      | Command Line Interface |
| HLD      | High Level Design      |
|          |                        |

## Introduction

### Objective 

The main objective of this document is to cover the test cases that will be executed for System Ready.  Topologies and test cases  for testing the feature will be discussed as part of this document.

### Scope

System ready will be enhanced to consider the overall active systemd services consisting of all docker services, vital host services and portready status along with portinit.

### Out of scope

-  Only Click commands are supported and not KLISH commands as KLISH will not work if Mgmt-framework service is down.

## Feature Overview

SONiC is an open-source network operating system based on Debian Linux, integrated with various systemd services. At present, system ready is based on only core docker services(swss ,bgp,teamd,pmon,syncd,database,mgmt-framework) and the portinitdone status.
System ready will be enhanced to consider the overall active systemd services consisting of all docker services, vital host services and portready status along with portinit.

## 1 Test Focus Areas

### 1.1 Functional Requirements

System Readiness:
- Identifying the vital host systemd and docker services 
- Enabling the services and analyze the system status output to see if all the services were up.
- Validating the syslog messages for (up/down) events of the services.
- Verify if show system status output generated a message "All services are ok" and "One or more services have failed" depending on the services status.

## 2 Topologies

D1

## 3 Test Case and objectives

### **3.1 Functional**

### 3.1.1 Verify that system is ready with base line commands.

| **Test ID**    | **SystemReady_Fun_001**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_system_check_base**                                   |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Bring up the device <br/>2)Verify all the system base line commands to check the system status and validate accordingly <br/>- Check show system status all output<br/>- Check show system status core output<br/>- Check show system status all summary output<br/>- Check show system status all detail output<br/>- Check show interface status output<br/> |

### 3.1.2 Make any of the docker apps down and check failed apps details are shown

| **Test ID**    | **SystemReady_Fun_002**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_docker_app_events**                                   |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Stop one of the docker service <br/>2) Verify if that particular service is down using show system status all output <br/>3) Start the service again and reverify the system status <br/> |

### 3.1.3 Make any of the host apps down and check failed apps details are shown

| **Test ID**    | **SystemReady_Fun_003**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_host_app_events**                                     |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Stop one of the host service <br/>2) Verify if that particular service is down using show system status all output <br/>3) Start the service again and reverify the system status <br/> |

### 3.1.4 Capture and verify syslog to be generated for the services that changes event (went down/up)

| **Test ID**    | **SystemReady_Fun_004**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_syslog_events**                                       |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Stop one of the host or docker service <br/>2)Capture the syslogs and verify if the log is generated for the particular event<br/> |

### 3.1.5 Enable a host service and verify the show system status all output.

| **Test ID**    | **SystemReady_Fun_005**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_host_service_updated**                                |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Enable one of the host or docker service <br/>2)Verify if the service is shown up in show system status all output |

### 3.1.6 Check show system status all summary output and verify syslog to be generated for "All services are OK" .

| **Test ID**    | **SystemReady_Fun_006**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_system_all_services_log**                             |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Bring up the dut <br/>2) Execute show system status all, if all the services were up <br/>3) verify syslog to be generated for "All services are OK" <br/> |

### 3.1.7 Check show system status all summary output and verify syslog to be generated for "One or more services have Failed" .

| **Test ID**    | *SystemReady_Fun_007**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_system_one_more_services_log**                        |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Bring up the dut <br/>2) Execute show system status all, if all services were up bring one of more the service down 
3) verify syslog to be generated for "One or more services have Failed" |
### 3.1.8 Capture and verify syslog to be generated for any Sonic systemd services that changes event (went down/up)

| **Test ID**    | **SystemReady_Fun_008**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_systemd_service_log**                                 |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Bring up the dut <br/>2) Execute show system status all, change the systemd services events. 
3) verify syslog to be generated for for the systemd service events. |


### 3.1.9 Kill one of the critical processes of a docker and see if the docker is exiting.

| **Test ID**    | **SystemReady_Fun_009**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_docker_kill**                                         |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Bring up the dut <br/>2) Kill one of the docker service (pmon) and verify if the docker service is exiting. 
3) verify syslog to be generated for for the service events. |
### 3.1.10 Verify the system status is up only after all the port initialization is done.

| **Test ID**    | **SystemReady_Fun_010**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_portinitialization**                                  |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Bring up the dut <br/>2) verify system status ready is declared only after the portinit is done from syslog message |


### 3.2 Reboot Test Cases**

### 3.2.1 Warm reboot and verify if the services were as expected using the system status CLIs

| **Test ID**    | **SystemReady_Fun_011**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_system_status_warm_reboot**                           |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Warm reboot the device <br/>2) once the dut is up and running, verify the below system status clis using base line test function. <br/>- Check show system status all output<br/>- Check show system status core output<br/>- Check show system status all summary output<br/>- Check show system status all detail output<br/>- Check show interface status output<br/> |

### 3.2.2 Fast reboot and verify if the services were as expected using the system status CLIs

| **Test ID**    | **SystemReady_Fun_012**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_system_status_fast_reboot**                           |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Fast reboot the device <br/>2) once the dut is up and running, verify the below system status clis using base line test function. <br/>- Check show system status all output<br/>- Check show system status core output<br/>- Check show system status all summary output<br/>- Check show system status all detail output<br/>- Check show interface status output<br/> |

### 3.2.3 Config reload and verify if the services were as expected using the system status CLIs

| **Test ID**    | **SystemReady_Fun_013**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_system_status_config_reload**                         |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)COnfig reload the device <br/>2) once the dut is up and running, verify the below system status clis using base line test function. <br/>- Check show system status all output<br/>- Check show system status core output<br/>- Check show system status all summary output<br/>- Check show system status all detail output<br/>- Check show interface status output<br/> |

### 3.2.4 Power cycle and verify if the services were as expected using the system status CLIs

| **Test ID**    | **SystemReady_Fun_014**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_system_status_power_cycle**                           |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Power cycle the device <br/>2) once the dut is up and running, verify the below system status clis using base line test function. <br/>- Check show system status all output<br/>- Check show system status core output<br/>- Check show system status all summary output<br/>- Check show system status all detail output<br/>- Check show interface status output<br/> |

### 3.2.5 Docker restart and verify if the services were as expected using the system status CLIs

| **Test ID**    | **SystemReady_Fun_015**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_system_status_docker_restart**                        |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Restart the docker on the device  <br/>2) once the docker is up and running, verify the below system status clis using base line test function. <br/>- Check show system status all output<br/>- Check show system status core output<br/>- Check show system status all summary output<br/>- Check show system status all detail output<br/>- Check show interface status output<br/> |

### 3.2.6 Save reboot and verify if the services were as expected using the system status CLIs

| **Test ID**    | **SystemReady_Fun016**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_system_status_Save_reboot**                           |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Save reboot the device <br/>2) once the dut is up and running, verify the below system status clis using base line test function. <br/>- Check show system status all output<br/>- Check show system status core output<br/>- Check show system status all summary output<br/>- Check show system status all detail output<br/>- Check show interface status output<br/> |



### **3.3 Negative Test Cases**

### 3.3.1 To verify if the existing "show system status" click command is deprecated .

| **Test ID**    | **SystemReady_Fun_016**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **test_system_status_negative**                              |
| **Test Setup** | **D1**                                                       |
| **Type**       | **Negative**                                                 |
| **Steps**      | 1)Bring up the device <br/>2) verify if the existing "show system status" click command is deprecated or not. |



**Reference Links**

HLD: https://github.com/BRCM-SONIC/sonic_doc_private/pull/171/files