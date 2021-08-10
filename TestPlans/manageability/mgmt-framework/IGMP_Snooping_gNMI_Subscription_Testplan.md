#  SQA Test Plan
#  IGMP Snooping gNMI Subscription TestPlan
#  SONiC 3.2.0 Release 

[TOC]

# Test Plan Revision History
| Rev  |    Date     |        Author        |      Change Description      |
| :--: | :--------:  | :------------------: | :--------------------------: |
| 0.1  | 05/01/2021  | Prudvi Mangadu       |   Initial version            |
| 0.2  | 13/01/2021  | Prudvi Mangadu       |   Addressing review comments |
| 0.3  | 03/08/2021  | Prudvi Mangadu       |   Adding SAMPLE Wildcard with path keys test cases |

# List of Reviewers
| Function  |         Name         |
| :------:  | :------------------: |
|   DEV     |  Sachin Holla        |
|   DEV     |  Balachandra Mani    |
|   QA      |  Kalyan Vadlamani    |

# List of Approvers
| Function  |           Name         | Date Approved |
| :-------: | :--------------------: | :-----------: |
|   DEV     |  Sachin Holla          |               |
|   DEV     |  Balachandra Mani      |               |
|   QA      |  Kalyan Vadlamani      |               |

# Definition/Abbreviation
| **Term** | **Meaning**                                   |
| -------- | --------------------------------------------- |
| CLI      | Command-Line Interface                        |
| REST     | Representational State Transfer               |
| HLD      | High Level Design Document                    |
| URI      | Uniform Resource Identifier                   |
| WCKV     | Wildcard Path key value                           |

### Objective 
The main objective of this document is to cover the test cases that will be executed to verify the ON_CHANGE
and Wildcard SAMPLE Subscription. Topologies and test cases  for testing the feature will be discussed as part of this
 document.

ON_CHANGE support going to be supported in 3.2 release.

SAMPLE with wildcard path key support going to be supported in 4.0.0 release.

gNMI Server should support wild cards in path key for all subscription modes. 
Example: /openconfig-interfaces:interfaces/interface[name=*]/config. Path can contain any number of wildcard key values.

### Scope
This test plan will cover following aspects of ON_CHANGE Subscription.
1. ON_CHANGE Supported only for igmp-snooping URI mentioned under 10.4 - Onchange supported URI Paths of the [Telemetry
 Subscribe RPC]
2. Subscribe each URI to ON_CHANGE and Validating the ON_CHANGE notification.
3. Subscribe each URI to SAMPLE with WCKV and Validating the Update and Delete notification w.r.t sample_interval.
4. Subscribe each URI to SAMPLE with WCKV and Validating the Update notification w.r.t suppress_redundant.
5. SAMPLE WCKV supports only for paths avaliable in [Telementry Subscription Path]


# Feature Overview
**ON CHANGE Subscription:**
Server sends data updates to the client when the value of subscribed path is changed. 
This mode is not suitable for frequently changing values like counters. 
Server can reject the ON_CHANGE subscribe request if it cannot support ON_CHANGE updates to the requested paths. 
Once accepted, server first sends current data for the subscribed paths to client (initial sync). 
Thereafter server sends only the delta updates to client when the values are changed on the server.

**SAMPLE Subscription:**
Server samples the data values periodically and streams the data to the client. 
Client would specify the paths and sample interval through the Subscribe request. 
Server would reject the request if it cannot support the requested sample interval.

# 1 Test Focus Areas
## 1.1 Functional Testing 
   - Subscribe URI to ON_CHANGE and Validating the current data first time(initial sync).
   - Subscribe URI to ON_CHANGE and Validating the delta data.
   - Subscribe path value wildcard URI to SAMPLE and Validating the current data first time(initial sync).
   - Subscribe path value wildcard URI to SAMPLE and Validating the sample update and delete w.r.t sample interval.
   
## 1.2 Scale and Performance Testing
  - Subscribe Multiple/MAX URIs to ON_CHANGE and Validating the ON_CHANGE notification.
  - Subscribe Multiple/MAX URIs to SAMPLE and Validating the Update and delete notification.
 
## 1.3 Negative Testing  
 - Server should reject the ON_CHANGE subscribe request if it cannot support ON_CHANGE updates to the requested paths
 - Server should reject the SAMPLE wildcard subscribe request if it cannot support.
 
## 1.4  Reboot/Reload/Upgrade Testing
  - Verify ON_CHANGE and SAMPLE wildcard path key Subscription across 
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
| **Test ID**    | **OnChange311**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that server first sends current data for the subscribed paths to client (initial sync). Thereafter server sends only the delta updates to client when the values are changed on the server** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Now change the Subscribe path values by using any UI <br/> 4) Verify that server sends only the delta updates to client when the values are changed on the server. <br/>|

### 3.1.2 Verify that change the value for multiple times make sure ON_CHANGE notifies it correctly.
| **Test ID**    | **OnChange312**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that change the value for multiple times make sure ON_CHANGE notifies it correctly** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Now change the Subscribe path values multiple time <br/> 4) Verify that server sends only the delta updates to client for each time values get change. <br/>|

### 3.1.3 Verify that ON_CHANGE config and state calls are working fine for all(Mentioned in HLD)URI paths.
| **Test ID**    | **OnChange313**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ON_CHANGE config and state calls are working fine for all(Mentioned in HLD)URI paths** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe each URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> |

### 3.1.4 Verify that ON_CHANGE subscribed send notification when value change by using CLICK UI.
| **Test ID**    | **OnChange314**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ON_CHANGE subscribed send notification when value change by using CLICK UI** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Now change the Subscribe path values by using CLICK UI <br/> 4) Verify that server sends only the delta updates to client when the values are changed on the server. <br/>|

### 3.1.5 Verify that ON_CHANGE subscribed send notification when value change by using Klish UI.
| **Test ID**    | **OnChange315**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ON_CHANGE subscribed send notification when value change by using Klish UI** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Now change the Subscribe path values by using KLISH UI <br/> 4) Verify that server sends only the delta updates to client when the values are changed on the server. <br/>|

### 3.1.6 Verify that ON_CHANGE subscribed send notification when value change by using REST UI.
| **Test ID**    | **OnChange316**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ON_CHANGE subscribed send notification when value change by using REST UI** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Now change the Subscribe path values by using REST UI <br/> 4) Verify that server sends only the delta updates to client when the values are changed on the server. <br/>|

### 3.1.7 Verify that IGMP snooping Functionality work fine with ON_CHANGE config.
| **Test ID**    | **OnChange317**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that IGMP snooping Functionality work fine with ON_CHANGE config** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Configure IGMP snooping on DUT<br/> 4) Make sure Snooping entries are getting populated during ON_CHANGE is subscribed <br/>|

### 3.1.8 Verify that Delete the value for multiple times make sure ON_CHANGE notifies it correctly.
| **Test ID**    | **OnChange318**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that Delete the value for multiple times make sure ON_CHANGE notifies it correctly** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Now delete the Subscribe path values multiple time <br/> 4) Verify that server sends only the delta updates to client for each time values get change. <br/>|

### 3.1.9 Verify that SAMPLE WCKV subscription will send initial sync and then only send updates to client w.r.t to SAMPLE_INTERVAL. 
| **Test ID**    | **IGMP_SNOOPING_SAMPLE_WC_001**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that SAMPLE subscription will send initial sync and then only send updates to client w.r.t to SAMPLE_INTERVAL. ** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe Path with SAMPLE and SAMPLE_INTERVAL, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Verify that server sends updates to client for every SAMPLE_INTERVAL.<br/>|

### 3.1.10 Verify that SAMPLE WCKV subscription with SUPPRESS_REDUNDANT will send initial sync and then only send non-redundant updates to client.
| **Test ID**    | **IGMP_SNOOPING_SAMPLE_WC_002**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that SAMPLE subscription with SUPPRESS_REDUNDANT will send initial sync and then only send non-redundant updates to client ** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe Path with SAMPLE and SUPPRESS_REDUNDANT, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Verify that server should not sends updates to client for every SAMPLE_INTERVAL.<br/>|4) Verify that server sends updates to client for every SAMPLE_INTERVAL if update is not redundant..<br/>|

### 3.1.11 Verify that SAMPLE WCKV subscription will send initial sync and send the modified data in update w.r.t user change.
| **Test ID**    | **IGMP_SNOOPING_SAMPLE_WC_003**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that SAMPLE subscription will send initial sync and send the modified data in update w.r.t user change ** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe Path with SAMPLE and SAMPLE_INTERVAL, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Verify that server sends updates to client for every SAMPLE_INTERVAL.<br/>|4) Now change the value of the DB using any UI type. <br/>|5) Verify that server sends updates modified values to client for every SAMPLE_INTERVAL.<br/>|

### 3.1.12 Verify that Server should not send SAMPLE notification once delete notification received to client.
| **Test ID**    | **IGMP_SNOOPING_SAMPLE_WC_004**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that Server should not send SAMPLE notification once delete notification received to client.** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe Paths with SAMPLE and different SAMPLE_INTERVAL, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Verify that server sends updates of both paths to client with corresponding SAMPLE_INTERVAL.<br/>|4) Now un-config the value to remove from DB.<br/>|5) Verify that server sends delete notification to client and stop sending the update notification.<br/>|


## 3.2 Scale and Performance Test Cases
### 3.2.1 Verify that ON_CHANGE notification with Multiple/MAX URIs to ON_CHANGE subscription.
| **Test ID**    | **OnChange321**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ON_CHANGE notification with Multiple/MAX URIs to ON_CHANGE subscription** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe MAX/Available URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/>3) Change the values of all subscription paths<br/>4) Make sure delta updated should trigger for all changes<br/> |

### 3.2.2 Verify that CHANGE gets notified to corresponding client in multiple client environment.
| **Test ID**    | **OnChange322**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that CHANGE gets notified to corresponding client in multiple client environment.** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) client1 can subscribe for paths A, B, C and client2 can subscribe to B, C, D<br/> 2) Verify that initial sync of A gets notified to only client1 whereas B should be notified to both <br/> 3) Now change the Subscribe path values <br/> 4) Verify that delta updates of A gets notified to only client1 whereas B should be notified to both. <br/>|

### 3.2.3 Verify that CHANGE gets notified to rest of clients, when other client are killed.
| **Test ID**    | **OnChange323**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that CHANGE gets notified to rest of clients, when other client are killed.** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) client1 can subscribe for paths A, B, C and client2 can subscribe to B, C, D<br/> 2) Verify that initial sync of A gets notified to only client1 whereas B should be notified to both <br/> 3) Kill client2.<br/> 4) Now change the Subscribe path values <br/> 5) Verify that delta updates of A gets notified to only client1 whereas B should be notified to client1 as client2 is killed. <br/>|

### 3.2.4 Verify that SAMPLE WCKV subscription with Multi paths with same SAMPLE_INTERVAL.
| **Test ID**    | **IGMP_SNOOPING_SAMPLE_WC_005**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that SAMPLE subscription with Multi paths with same SAMPLE_INTERVAL** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe Paths with SAMPLE and same SAMPLE_INTERVAL, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Verify that server sends updates of both paths to client for every SAMPLE_INTERVAL.<br/>|

### 3.2.5 Verify that SAMPLE WCKV subscription with Multi paths with different SAMPLE_INTERVAL.
| **Test ID**    | **IGMP_SNOOPING_SAMPLE_WC_006**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that SAMPLE subscription with Multi paths with different SAMPLE_INTERVAL.** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe Paths with SAMPLE and different SAMPLE_INTERVAL, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Verify that server sends updates of both paths to client with corresponding SAMPLE_INTERVAL.<br/>|


## 3.3 Negative Test Cases
### 3.3.1 Verify that Server should reject the ON_CHANGE subscribe request for URIs not support ON_CHANGE.
| **Test ID**    | **OnChange331**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that Server should reject the ON_CHANGE subscribe request for URIs not support ON_CHANGE.** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the unsupported URI to the ON_CHANGE notification, make sure Server should reject the subscribe request <br/> 2) Verify that server should not send data related to the path to client.<br/>|

### 3.3.2 Verify that Server should not send ON_CHANGE notification once delete the ON_CHANGE Subscription.
| **Test ID**    | **OnChange332**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that Server should not send ON_CHANGE notification once delete the ON_CHANGE Subscription.** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Subscribe the URI to the ON_CHANGE notification, make sure Server accepts the subscribe request <br/> 2) Verify that server first sends current data for the subscribed paths to client (initial sync) <br/> 3) Un-Subscribe the URI and change the Subscribe path values multiple time <br/> 4) Verify that server should not send updates to client.<br/>|


## 3.4 Reboot/Reload/Upgrade Test Cases
### 3.4.1 Verify ON_CHANGE Subscription across warm reboot.
| **Test ID**    | **OnChange341**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify ON_CHANGE Subscription across warm reboot** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify ON_CHANGE Subscription across warm reboot <br/> |

###  3.4.2  Verify ON_CHANGE Subscription across fast reboot.
| **Test ID**    | **OnChange342**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify ON_CHANGE Subscription across fast reboot** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify ON_CHANGE Subscription across fast reboot <br/>|

### 3.4.3 Verify ON_CHANGE Subscription across System reload.
| **Test ID**    | **OnChange343**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify ON_CHANGE Subscription across System reload** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify ON_CHANGE Subscription across System reload <br/> |

### 3.4.4 Verify ON_CHANGE Subscription across config reload.
| **Test ID**    | **OnChange344**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify ON_CHANGE Subscription across config reload** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify ON_CHANGE Subscription across config reload <br/>|

### 3.4.5 Verify ON_CHANGE Subscription across telemetry docker restart.
| **Test ID**    | **OnChange345**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify ON_CHANGE Subscription across telemetry docker restart** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify ON_CHANGE Subscription across telemetry docker restart <br/> |

### 3.4.6 Verify SAMPLE WCKV Subscription across warm reboot
| **Test ID**    | **IGMP_SNOOPING_SAMPLE_WC_007**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify SAMPLE WCKV Subscription across warm reboot** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify SAMPLE WCKV Subscription across warm reboot <br/> |

### 3.4.7 Verify SAMPLE WCKV Subscription across fast reboot
| **Test ID**    | **IGMP_SNOOPING_SAMPLE_WC_008**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify SAMPLE WCKV Subscription across warm reboot** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify SAMPLE WCKV Subscription across warm reboot <br/> |

### 3.4.8 Verify SAMPLE WCKV Subscription across System reload
| **Test ID**    | **IGMP_SNOOPING_SAMPLE_WC_009**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify SAMPLE WCKV Subscription across warm reboot** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify SAMPLE WCKV Subscription across warm reboot <br/> |

### 3.4.9 Verify SAMPLE WCKV Subscription across config reload
| **Test ID**    | **IGMP_SNOOPING_SAMPLE_WC_010**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify SAMPLE WCKV Subscription across warm reboot** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify SAMPLE WCKV Subscription across warm reboot <br/> |

### 3.4.10 Verify SAMPLE WCKV Subscription across telemetry docker restart
| **Test ID**    | **IGMP_SNOOPING_SAMPLE_WC_011**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify SAMPLE WCKV Subscription across warm reboot** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify SAMPLE WCKV Subscription across warm reboot <br/> |


## 4 Reference Links
HLD: [Telemetry Subscribe RPC] <br/>
Supported Path: [Telementry Subscription Path]

[Telemetry Subscribe RPC]: https://github.com/BRCM-SONIC/sonic_doc_private/blob/master/manageability/mgmt-framework/Telemetry_Subscribe_RPC.md
[Telementry Subscription Path]:https://github.com/BRCM-SONIC/sonic_doc_private/blob/master/manageability/mgmt-framework/Telemetry_Subscribe_Paths.md

