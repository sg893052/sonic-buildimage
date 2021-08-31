#  SQA Test Plan
#  gNMI Subscription TestPlan.
#  SONiC 4.0.0 Release 

[TOC]


# Test Plan Revision History
| Rev  |    Date     |        Author           |      Change Description      |
| :--: | :--------:  | :---------------------: | :--------------------------: |
| 0.1  |  8/11/2021  | Ramprakash Reddy Kanala |   Initial version            |

# List of Reviewers
| Function  |         Name         |
| :------:  | :------------------: |
|   DEV     |  Anand Kumar         |
|   DEV     |  Sachin Holla        |
|   QA      |  Kalyan Vadlamani    |

# List of Approvers
| Function  |           Name         | Date Approved |
| :-------: | :--------------------: | :-----------: |
|   DEV     |  Anand Kumar           |               |
|   DEV     |  Sachin Holla          |  8/23/2021    |
|   QA      |  Kalyan Vadlamani      |               |


# Definition/Abbreviation
| **Term** | **Meaning**                                   |
| -------- | --------------------------------------------- |
| CLI      | Command-Line Interface                        |
| REST     | Representational State Transfer               |
| HLD      | High Level Design Document                    |
| URI      | Uniform Resource Identifier                   |

### Objective 
The main objective of this document is to cover the test cases that will be executed to verify the ON_CHANGE
 Subscription. Topologies and test cases  for testing the feature will be discussed as part of this document.

For below yang modules, ON_CHANGE support going to be supported in 4.0.0 release
```
        OC-transceiver-diag.yang
        OC-vlan.yang
        OC-vlan-interface.yang
        OC-threshold.yang
        OC-system-ntp.yang
        OC-sflow.yang
        OC-queue.yang
        OC-qos-pfc.yang
        OC-qos-clear.yang
        OC-portchannel.yang
        OC-portchannel-interface.yang
        OC-port.yang
        OC-port-group.yang
        OC-port-breakout.yang
        OC-interface.yang
        OC-device-metadata.yang
        OC-counters.yang
```
Subscription Paths are avaliable in [Telementry Subscription Path]

### Scope
This test plan will cover following aspects of ON_CHANGE Subscription.
1. All Subscription Paths avaliable in [Telementry Subscription Path]
2. Subscribe each Subscription Path to ON_CHANGE and Validating the ON_CHANGE notification.
3. Validating with and without wildcards.

# Feature Overview
**ON CHANGE Subscription:**
Server sends data updates to the client when the value of subscribed path is changed. 
This mode is not suitable for frequently changing values like counters. 
Server can reject the ON_CHANGE subscribe request if it cannot support ON_CHANGE updates to the requested paths. 
Once accepted, server first sends current data for the subscribed paths to client (initial sync). 
Thereafter server sends only the delta updates to client when the values are changed on the server.

# 1 Test Focus Areas
## 1.1 Functional Testing 
   - Subscribe URI to ON_CHANGE and Validating the current data first time(initial sync).
   - Subscribe URI to ON_CHANGE and Validating the delta data.

## 1.2 Scale and Performance Testing
  - Subscribe Multiple/MAX URIs to ON_CHANGE and Validating the ON_CHANGE notification.
 
## 1.3 Negative Testing  
 - Server should reject the ON_CHANGE subscribe request if it cannot support ON_CHANGE updates to the requested paths
 
## 1.4  Reboot/Reload/Upgrade Testing
  - Verify ON_CHANGE Subscription across 
   ```
    - Warm reboot
    - Fast reboot
    - System reboot
    - Config reload
    - Telemetry docker restart
   ```
Note: If management interface or telemetry service goes down, Re-Subscription required from client when service becomes
 up.
 
# 2 Topologies
## 2.1 Topology
![Topology](images/topology_1.PNG "Figure 1: Topology")

# 3 Test  Case and Objectives
## 3.1 Functional
### 3.1.1 Verify that server first sends current data for the subscribed paths to client (initial sync). Thereafter server sends only the delta updates to client when the values are changed on the server
| **Test ID**    | **onchange001**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that server first sends current data for the subscribed paths to client (initial sync). Thereafter server sends only the delta updates to client when the values are changed on the server** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Now change the Subscribe path values by using any UI <br/> 4) Verify that server sends only the delta updates to client when the values are changed on the server. <br/>|

### 3.1.2 Verify that change the value for multiple times make sure ON_CHANGE notifies it correctly.
| **Test ID**    | **onchange002**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that change the value for multiple times make sure ON_CHANGE notifies it correctly** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Now change the Subscribe path values multiple time <br/> 4) Verify that server sends only the delta updates to client for each time values get change. <br/>|

### 3.1.3 Verify that ON_CHANGE config and state calls are working fine for all(Mentioned in HLD)URI paths.
| **Test ID**    | **onchange003**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ON_CHANGE config and state calls are working fine for all(Mentioned in HLD)URI paths** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe each URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> |

### 3.1.4 Verify that ON_CHANGE subscribed send notification when value change by using CLICK UI.
| **Test ID**    | **onchange004**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ON_CHANGE subscribed send notification when value change by using CLICK UI** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Now change the Subscribe path values by using CLICK UI <br/> 4) Verify that server sends only the delta updates to client when the values are changed on the server. <br/>|

### 3.1.5 Verify that ON_CHANGE subscribed send notification when value change by using Klish UI.
| **Test ID**    | **onchange005**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ON_CHANGE subscribed send notification when value change by using Klish UI** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Now change the Subscribe path values by using KLISH UI <br/> 4) Verify that server sends only the delta updates to client when the values are changed on the server. <br/>|

### 3.1.6 Verify that ON_CHANGE subscribed send notification when value change by using REST UI.
| **Test ID**    | **onchange006**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ON_CHANGE subscribed send notification when value change by using REST UI** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Now change the Subscribe path values by using REST UI <br/> 4) Verify that server sends only the delta updates to client when the values are changed on the server. <br/>|

### 3.1.7 Verify that yang feature Functionality work fine with ON_CHANGE config.
| **Test ID**    | **onchange007**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that yang feature Functionality work fine with ON_CHANGE config** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Configure yang feature on DUT<br/> 4) Make sure Snooping entries are getting populated during ON_CHANGE is subscribed <br/>|

### 3.1.8 Verify that Delete the value for multiple times make sure ON_CHANGE notifies it correctly.
| **Test ID**    | **onchange008**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that Delete the value for multiple times make sure ON_CHANGE notifies it correctly** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Now delete the Subscribe path values multiple time <br/> 4) Verify that server sends only the delta updates to client for each time values get change. <br/>|

### 3.1.9 Verify that server first sends current data for the subscribed paths with wild card to client (initial sync). Thereafter server sends only the delta updates to client when the values are changed on the server
| **Test ID**    | **onchange009**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that server first sends current data for the subscribed paths to client (initial sync). Thereafter server sends only the delta updates to client when the values are changed on the server** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Now change the Subscribe path values by using any UI <br/> 4) Verify that server sends only the delta updates to client when the values are changed on the server. <br/>|

### 3.1.10 Verify that ON_CHANGE functionality with wild card subscription in more than one place.
| **Test ID**    | **onchange010**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ON_CHANGE functionality with wild card subscription in more than one place** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI with wild card in more than one place to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Now change the Subscribe path values by using any UI<br/> 4) Verify that server sends only the delta updates to client when the values are changed on the server.<br/>|

## 3.2 Scale and Performance Test Cases
### 3.2.1 Verify that ON_CHANGE notification with Multiple/MAX URIs to ON_CHANGE subscription.
| **Test ID**    | **onchange011**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ON_CHANGE notification with Multiple/MAX URIs to ON_CHANGE subscription** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe MAX/Available URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/>3) Change the values of all subscription paths<br/>4) Make sure delta updated should trigger for all changes<br/> |

### 3.2.2 Verify that CHANGE gets notified to corresponding client in multiple client environment.
| **Test ID**    | **onchange012**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that CHANGE gets notified to corresponding client in multiple client environment.** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) client1 can subscribe for paths A, B, C and client2 can subscribe to B, C, D<br/> 2) Verify that initial sync of A gets notified to only client1 whereas B should be notified to both <br/> 3) Now change the Subscribe path values <br/> 4) Verify that delta updates of A gets notified to only client1 whereas B should be notified to both. <br/>|

### 3.2.3 Verify that CHANGE gets notified to rest of clients, when other client are killed.
| **Test ID**    | **onchange013**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that CHANGE gets notified to rest of clients, when other client are killed.** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) client1 can subscribe for paths A, B, C and client2 can subscribe to B, C, D<br/> 2) Verify that initial sync of A gets notified to only client1 whereas B should be notified to both <br/> 3) Kill client2.<br/> 4) Now change the Subscribe path values <br/> 5) Verify that delta updates of A gets notified to only client1 whereas B should be notified to client1 as client2 is killed. <br/>|


## 3.3 Negative Test Cases
### 3.3.1 Verify that Server should reject the ON_CHANGE subscribe request for URIs not support ON_CHANGE.
| **Test ID**    | **onchange014**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that Server should reject the ON_CHANGE subscribe request for URIs not support ON_CHANGE.** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the unsupported URI to the ON_CHANGE notification, make sure Server should reject the subscribe request <br/> 2) Verify that server should not send data related to the path to client.<br/>|

### 3.3.2 Verify that Server should not send ON_CHANGE notification once delete the ON_CHANGE Subscription.
| **Test ID**    | **onchange015**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that Server should not send ON_CHANGE notification once delete the ON_CHANGE Subscription.** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Un-Subscribe the URI and change the Subscribe path values multiple time <br/> 4) Verify that server should not send updates to client.<br/>|


## 3.4 Reboot/Reload/Upgrade Test Cases
### 3.4.1 Verify ON_CHANGE Subscription across warm reboot.
| **Test ID**    | **onchange016**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify ON_CHANGE Subscription across warm reboot** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify ON_CHANGE Subscription across warm reboot <br/> |

###  3.4.2  Verify ON_CHANGE Subscription across fast reboot.
| **Test ID**    | **onchange017**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify ON_CHANGE Subscription across fast reboot** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify ON_CHANGE Subscription across fast reboot <br/>|

### 3.4.3 Verify ON_CHANGE Subscription across System reload.
| **Test ID**    | **onchange018**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify ON_CHANGE Subscription across System reload** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify ON_CHANGE Subscription across System reload <br/> |

### 3.4.4 Verify ON_CHANGE Subscription across config reload.
| **Test ID**    | **onchange019**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify ON_CHANGE Subscription across config reload** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify ON_CHANGE Subscription across config reload <br/>|

### 3.4.5 Verify ON_CHANGE Subscription across telemetry docker restart.
| **Test ID**    | **onchange020**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify ON_CHANGE Subscription across telemetry docker restart** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify ON_CHANGE Subscription across telemetry docker restart <br/> |


## 4 Reference Links
[Telemetry Subscribe RPC] <br/>
[Telementry Subscription Path]

[Telementry Subscription Path]: https://github.com/project-arlo/SONiC/blob/e255b1726ad5a7845397478b435fb253f275ea4d/doc/system-telemetry/telemetry_subscribe_paths.md
[Telemetry Subscribe RPC]: https://github.com/BRCM-SONIC/sonic_doc_private/blob/master/manageability/mgmt-framework/Telemetry_Subscribe_RPC.md
