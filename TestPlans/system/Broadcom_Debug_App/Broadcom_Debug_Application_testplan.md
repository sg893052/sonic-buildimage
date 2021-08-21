#  SQA Test Plan
# Broadcom Debug Application
#  SONiC 4.0.0 Project and Cyrus Release
[TOC]
# Test Plan Revision History
| Rev | Date | Author | Change Description |
|:---:|:-----------:|:------------------:|-----------------------------|
| 0.1  | 08/17/2019 | prudviraj kristipati | initial version    |

# List of Reviewers
|  Function | Name |
|:---:|:-----------:|
| Dev | Sharad Agarwal |
| Dev | Bandaru Viswanath  |
| QA  | Kalyan vadlamani |

# List of Approvers
|  Function | Name | Date Approved|
|:---:|:-----------:|:------------------:|
| Dev | sharad Agarwal |
| Dev | Bandaru Viswanath  |
| QA  | kalyan vadlamani |

# Definition/Abbreviation
| **Term** | **Meaning**                     |
| -------- | ------------------------------- |
|   BDBG         |	  Broadcom SONIC Debug App|
|   Data Source  |   	A specific source for the BDBG to collect the data from. It could be one of the Database Tables, Syslog, Kernel (fs) etc |
|   Observation Point  |   	Locations on the switch where metrics are being observed e.g. on-chip Buffers at Ports, Queues etc |

# Feature Overview
BDBG attempts a use case oriented approach to debugging issues on a Broadcom SONIC Switch
  - It aims to solve select (speific use case) problems
  - Presents a unified, normalized and focused interface for the data to the user
  - Correlates data from different sources on the Switch – software and hardware
  - Presents a historical perspective along with current view
  - Allows exporting of aggregated data to ease burden on external collectors

There are existing individual features in Broadcom SONIC that aim to solve specific problems. BDBG does not compete / replace existing features, rather uses the data gathered by them.

The BDBG infrastructure implements common aspects so that individual debug tools can share the infrastructure without having to re-implement from scratch. More details on provisioning are available in subsequent sections.

Along with the basic infrastructure, two specific tools for monitoring congestions and drops are built. The tools are named `congestion` and `drops` respectively.

# 1 Test Focus Areas
## 1.1 CLI Testing
 - All CLI config commands related to Watermark counters
## 1.2 Functional Testing 
verifying below counter values for user watermark and persistent watermark
  - verifying for shared PG counters
  - verifying for queue unicast counters
  - verifying for queue multicast counters
  - verifying for buffer pool counters
  - verifying for drop counters

# 2 Topologies
## 2.1 Topology 1
![watermakrs_using_snapshot](watermark_using_snapshot.png)
### 2.2 Switch configuration
- create one vlan and add vlan memebers and keep sending 3:1 congestion traffic.

# 3 Test  Case and Objectives
## 3.1 CLI
### 3.1.1 Verify the  collection and max-retention interval configurations and default values.

| **Test ID** | **ft_collection_max-retention |
|--------|:----------------|
| **Test Name** | **Verify the  collection and max-retention interval configurations and default values.** |
| **Test Setup** | **Topology1** |
| **Type** | **CLI** |
| **Steps** | **1) configure the bdbg config collection-interval  and max-retention interval CLI command. <br/>2) The command should not through any error. <br/>3) verify the default interval values are reflecting correctly |

### 3.1.2 Verify the congestion-threshold configuration and default values.

| **Test ID** | **ft_congestion-threshold** |
|--------|:----------------|
| **Test Name** | **Verify the congestion-threshold configuration and default values.** |
| **Test Setup** | **Topology1** |
| **Type** | **CLI** |
| **Steps** | **1) configure the bdbg config congestion congestion-threshold CLI command. <br/>2) The command should not through any error. <br/>3) verify the default interval values are reflecting correctly** |

### 3.1.3 Verify  different show commands "bdbg show"  and "bdbg show" drops" and validate the contents.

| **Test ID** | **ft_show** |
|--------|:----------------|
| **Test Name** | **Verify  different show commands "bdbg show"  and "bdbg show" drops" and validate the contents.** |
| **Test Setup** | **Topology1** |
| **Type** | **CLI** |
| **Steps** | **1) configure the bdbg config congestion congestion-threshold CLI command. <br/>2) The command should not through any error. <br/>3) verify the default values observed** |

### 3.1.4 Verify  different congestion show commands "bdbg show congestion"  and "bdbg show congestion active" "bdbg show congestion top" and validate the contents.

| **Test ID** | **ft_show_congestion** |
|--------|:----------------|
| **Test Name** | **Verify  different congestion show commands "bdbg show congestion"  and "bdbg show congestion active" "bdbg show congestion top" and validate the contents.** |
| **Test Setup** | **Topology1** |
| **Type** | **CLI** |
| **Steps** | **1) configure the bdbg config congestion congestion-threshold CLI command. <br/>2) The command should not through any error. <br/>3) verify the default values observed** |

### 3.1.5 Verify  different clear commands.

| **Test ID** | **ft_clear** |
|--------|:----------------|
| **Test Name** | **Verify  different clear commands** |
| **Test Setup** | **Topology1** |
| **Type** | **CLI** |
| **Steps** | **1) verify command bdbg clear collection-interval. <br/> 2) verify command bdbg clear max-retention-interval. <br/> 3) verify command bdbg clear history.<br/> 4) verify command bdbg clear congestion history.<br/> 5) The command should not through any error. <br/> 6) verify the default values observed** |

## 3.2 Functional
### 3.2.1 Verify that congestion for shared PG can be seen in show congestion output.

| **Test ID**    | **ft_shared_PG**                                             |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that congestion for shared PG can be seen in show congestion output.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1)  Bring up the SONiC switch with default configuration.<br/>2) configure the congestion threshold.<br/>3) Send the traffic in from 3 TG ports to the 4th TG port (i.e. send 3:1 congested traffic) continuously.<br/>4) show congestion output .<br/>5) stop the traffic.** |

### 3.2.2 Verify that congestion for port queue can be seen in show congestion output.

| **Test ID**    | **ft_port_queue**                                            |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that congestion for port queue can be seen in show congestion output.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1)  Bring up the SONiC switch with default configuration.<br/>2) configure the congestion threshold.<br/>3) Send the traffic in from 3 TG ports to the 4th TG port (i.e. send 3:1 congested traffic) continuously.<br/>4) Verify show congestion output..<br/>5) stop the traffic.** |

### 3.2.3 Verify that  top congestion sources recorded behavior and the historical data behavior when max-retention-interval is exceeded.

| **Test ID**    | **ft_tp_congestion**                                         |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that  top congestion sources recorded behavior and the historical data behavior when max-retention-interval is exceeded.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1)  Bring up the SONiC switch with default configuration.<br/>2) configure the collector and max-retention-interval.<br/>3) Send the traffic in from 3 TG ports to the 4th TG port (i.e. send 3:1 congested traffic) continuously.<br/>4)Record the congestion traffic.<br/>5) clear the congestion history.<br/>6)Try to see the historical records exceeds the configured retention value the traffic.<br/> 7)After the retention interval the records purged and and will not be counted as historical data.<br/> 8)stop the traffic.** |

### 3.2.4 Verify congestion history for a particular source.

| **Test ID**    | **ft_congestion_source**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** Verify congestion history for a particular source.**      |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1)  Bring up the SONiC switch with default configuration.<br/>2) configure the collector and max-retention-interval.<br/>3) Send the multicast traffic in from 3 TG ports to the 4th TG port (i.e. send 3:1 congested traffic) continuously.<br/>4)  Verify  congestion history by "bdbg show congestion history" command.<br/>5)stop the traffic.** |


### 3.2.5 Verify the dropped  'TTL_ERR' packets can be verified from show drops.

| **Test ID**    | **ft_pkt_drop**                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that 'TTL_ERR' packets are dropped and dropped events are sent to the configured collector.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Setup:<br/>i. DUT with 4 TG ports and reachable through management interface.<br/>ii. L3 - IXIA stream having 'TTL_ERR' error. Set IPv4 L3 packet with TTL = '0' to introduce 'TTL_ERR' in the packet.<br/>iii. Place the ACL rule and ACL table in the 'config_db.json' file such that configured ACL should have rule i.e. MONITOR_DROP.<br/>iv. Routing configured and ARP resolved for TG ports.<br/>Procedure:<br/>1) Bring up the DUT with default configuration.<br/>2) Enable 'Drop Monitor' feature, configure sampling rate, aging interval and a collector.<br/>3) Configure a flow using 5-Tuple info to look for a specific pattern in the incoming flow of traffic.<br/>4) Send 'Martian address' packets and check the behavior on collector after capturing the packets.5) Verify that 'Martian address' packets are dropped and can be verified from show drops active flows.** |

### 3.2.6 Verify the packet drop counts for a interface which can be observed with 'show drops active interface Ethernet0'

| **Test ID**    | **ft_pkt_interface**                             |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the packet drop counts for a interface which can be observed with 'show drops active interface Ethernet0'.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Setup:<br/>i. DUT with 4 TG ports and reachable through management interface<br/>ii. L3 IXIA stream having 'L3_DEST_MISS'. Do not install L3 route/L3 host entry.<br/>iii. Place the ACL rule and ACL table in the 'config_db.json' file such that configured ACL should have rule i.e. MONITOR_DROP. Routing configured and ARP resolved for TG ports.<br/>Procedure:<br/>1) Bring up the DUT with default configuration.<br/>2) Enable 'Drop Monitor' feature, configure sampling rate, aging interval and a collector.<br/>3) Configure a flow using 5-Tuple info to look for a specific pattern in the incoming flow of traffic.<br/>4) Send 'L3_DEST_MISS' packets and check the behavior on collector after capturing the packets.<br/>5) Verify that 'L3_DEST_MISS' packets are dropped and the drop event with the help of show drops active interface Ethernet0 command.**

# 4 Reference Links

https://github.com/BRCM-SONIC/sonic_doc_private/blob/master/devops/tam/bdbg-hld.md

