#  SQA Test Plan
# SONiC -  Threshold Feature Enhancements
[TOC]
# Test Plan Revision History
| Rev | Date | Author | Change Description |
|:---:|:-----------:|:------------------:|-----------------------------|
| 0.2  | 08/17/2021 | prudviraj kristipati | Enhanced version    |

# List of Reviewers
|  Function | Name |
|:---:|:-----------:|
| Dev | Sharad Agarwal |
| Dev | Bandaru Viswanath  |
| Dev | yelamandeswara rao |
| QA  | Kalyan vadlamani |

# List of Approvers
|  Function | Name | Date Approved|
|:---:|:-----------:|:------------------:|
| Dev | sharad Agarwal |
| Dev | Bandaru Viswanath  |
| Dev | yelamandeswara rao |
| QA  | kalyan vadlamani |

# Definition/Abbreviation
| **Term** | **Meaning**                     |
| -------- | ------------------------------- |
|   BDBG         |	  Broadcom SONIC Debug App|
|   Data Source  |   	A specific source for the BDBG to collect the data from. It could be one of the Database Tables, Syslog, Kernel (fs) etc |
|   Observation Point  |   	Locations on the switch where metrics are being observed e.g. on-chip Buffers at Ports, Queues etc |

# Feature Overview
The threshold feature allows configuration of a threshold on supported buffers in ingress and egress. A threshold breach notification (entry update in COUNTERS_DB) is generated on breach of the configured buffer threshold in ASIC.

# 1 Test Focus Areas
## 1.1 CLI Testing
 - All CLI config commands related to Watermark counters
## 1.2 Functional Testing 
verifying below counter values for user watermark and persistent watermark
  - verifying Ingress port buffer pool
  - verifying Egress port buffer pool
  - verifying Egress service pool
  - verifying Device buffer pool

# 2 Topologies
## 2.1 Topology 1
SONiC switch with 4 TG ports
### 2.2 Switch configuration
- create one vlan and add vlan members and keep sending 3:1 congestion traffic.

# 3 Test  Case and Objectives
## 3.1 Functional
### 3.1.1 verify threshold device value by configuring the incorrect value and seen valid warning message is seen in the console or not.

| **Test ID**    | **ft_device_negative**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **verify threshold device value by configuring the incorrect value and seen valid warning message is seen in the console or not.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Bring up the SONiC switch with default configuration.<br/>2) Configure threshold device value by configuring the incorrect value.<br/>3) Verify that SONiC switch is UP with default configuration.<br/>4) Verify valid warning message is seen in the console or not.** |

### 3.1.2 verify device buffer counters after configuring a threshold limit and verify the peak traffic is more than the configured threshold

| **Test ID**    | **ft_tf_device_buffer_pool**                                 |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **verify device buffer counters after configuring a threshold limit and verify the peak traffic is more than the configured threshold** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1)  Bring up the SONiC switch with default configuration.<br/>2) Configure device buffer threshold  with a valid range 1-100.<br/>3) Issue valid show command 'show device threshold to check the configured threshold.<br/>4) Start traffic <br/>5) Check the threshold breach event table using command 'show threshold breaches'.<br/>6) Configure device threshold and check the threshold breach event table.<br/>7) Verify that device buffer threshold configuration is successful.<br/>8) Verify that command output shows configured values.<br/>9) Verify that traffic gets forwarded and command output shows the device buffer values.<br/>10) Verify that a threshold is breached  and verify the device buffer globally.<br/>11)stop the traffic and wait for twice the configured interval and verify the respective counter value should be 0** |

### 3.1.3 verify global Egress service pool multicast buffer counters after configuring a threshold limit and verify the peak traffic is more than the configured threshold

| **Test ID**    | **ft_tf_global_egress_buffer_pool_multicast**                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **verify global Egress service pool multicast buffer counters after configuring a threshold limit and verify the peak traffic is more than the configured threshold.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1)  Bring up the SONiC switch with default configuration.<br/>2) Configure global Egress service pool Multicast buffer threshold  with a valid range 1-100.<br/>3) Issue valid show command Show threshold egress multicast to check the configured threshold.<br/>4) Start traffic.<br/>5) Check the threshold breach event table using command 'show threshold breaches'.<br/>6) Verify that SONiC switch is UP with default configuration.<br/>7) Verify that  global Egress Multicast threshold configuration is successful.<br/>8) Verify that command output shows configured values.<br/>9) Verify that traffic gets forwarded and command output shows the egress-multicast peak values.<br/>10) Verify that a threshold is breached.<br/>11)stop the traffic and wait for twice the configured interval and verify the respective counter value should be 0.** |

### 3.1.4 verify  Egress port buffer pool unicast buffer counters, after configuring a threshold limit and verify the peak traffic is more than the configured threshold

| **Test ID**    | **ft_tf_egress_port_pool_unicast**                           |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** verify  Egress port buffer pool unicast buffer counters, after configuring a threshold limit and verify the peak traffic is more than the configured threshold.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1)  Bring up the SONiC switch with default configuration.<br/>2) Configure Egress port pool buffer threshold  with a valid range 1-100.<br/>3) Issue valid show command Show threshold  to check the configured threshold.<br/>4) Start traffic.<br/>5) Check the threshold breach event table using command 'show threshold breaches'.<br/>6) Configure multiple thresholds on multiple ports and check the threshold breach event table.<br/>7) Verify that SONiC switch is UP with default configuration.<br/>8)  Verify that egress port buffer pool  threshold configuration is successful.<br/>9) Verify that command output shows configured values.<br/>10) Verify that traffic gets forwarded and command output shows the egress-unicast peak values.<br/>11) Verify that a threshold is breached.<br/>12)stop the traffic and wait for twice the configured interval and verify the respective counter value should be 0.** |


### 3.1.5 verify  Egress port buffer pool shared(uc+mc) buffer counters, after configuring a threshold limit and verify the peak traffic is more than the configured threshold

| **Test ID**    | **ft_tf_egress_port_pool_shared**                            |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **verify  Egress port buffer pool shared(uc+mc) buffer counters, after configuring a threshold limit and verify the peak traffic is more than the configured threshold.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **1)  Bring up the SONiC switch with default configuration.<br/>2) Configure Egress port buffer pool threshold  with a valid range 1-100.<br/>3) Issue valid show command Show threshold  to check the configured threshold.<br/>4) Start traffic.<br/>5) Check the threshold breach event table using command 'show threshold breaches'.<br/>6) Configure multiple thresholds on multiple ports and check the threshold breach event table.<br/>7) Verify that SONiC switch is UP with default configuration.<br/>8)  Verify that egress port buffer pool  threshold configuration is successful.<br/>9) Verify that command output shows configured values.<br/>10) Verify that traffic gets forwarded and command output shows the egress port shared peak values.<br/>11) Verify that a threshold is breached .<br/>12)stop the traffic and wait for twice the configured interval and verify the respective counter value should be 0.** |

### 3.1.6 verify port Ingress  buffer pool shared(uc+mc) buffer counters after configuring a threshold limit and verify the peak traffic is more than the configured threshold

| **Test ID**    | **ft_tf_ingress_port_pool_shared**                           |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **verify port Ingress  buffer pool shared(uc+mc) buffer counters after configuring a threshold limit and verify the peak traffic is more than the configured threshold.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **1)  Bring up the SONiC switch with default configuration.<br/>2) Configure Ingress  buffer pool shared threshold  with a valid range 1-100.<br/>3) Issue valid show command Show threshold  to check the configured threshold.<br/>4) Start traffic.<br/>5) Check the threshold breach event table using command 'show threshold breaches'.<br/>6) Configure multiple thresholds on multiple ports and check the threshold breach event table.<br/>7) Verify that SONiC switch is UP with default configuration.<br/>8)  Verify that ingress port buffer pool  threshold configuration is successful.<br/>9) Verify that command output shows configured values.<br/>10) Verify that traffic gets forwarded and command output shows the ingress port shared peak values.<br/>11) Verify that a threshold is breached.<br/>12)stop the traffic and wait for twice the configured interval and verify the respective counter value should be 0.** |

# 4 Reference Links

https://github.com/BRCM-SONIC/sonic_doc_private/blob/master/devops/telemetry/SONiC%20Threshold%20feature%20spec.md

