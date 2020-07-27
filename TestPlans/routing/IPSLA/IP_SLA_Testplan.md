#  SQA Test Plan
# IP SLA
#  SONiC 3.0.1 Project and Buzznik Plus Release
[TOC]
# Test Plan Revision History
| Rev | Date | Author | Change Description |
|:---:|:-----------:|:------------------:|-----------------------------|
| 0.1 | 06/22/2019 | Nagappa Chincholi | Initial version |

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
| ICMP     | Internet Control Message Protocol       |
| SLA     | Service Level Agreement |
| TCP      | Transmission Control Protocol     |
| PBR      | Policy Based Routing     |


# Feature Overview
Services (e.g. TCP optimizer, Firewall, etc.) in the network are provisioned with some level of redundancy. And routers are required to conditionally forward traffic towards those services based on availability of the services. ICMP and TCP SLA track features provide periodic reachability check for the tracked services and conditionally install or uninstall static route(s) toward the service based on the health and liveliness of the service being tracked.

# 1 Test Focus Areas
## 1.1 Functional Testing 
  - All CLI and debug commands
  - Scaling with max supported IP SLA of 50 ICMP-Echo target or 50 TCP-Connect target 
  - Test on default and user VRFs
## 1.2 Negative Testing 
  -	Link flaps
  -	Reload/kill daemon
  -	Withdraw/advertise routes
  -	Clear bgp neighbors/clear ipv4/v6 route table/clear arp/clear mac
  -	Warm reboot
  -	Fast reboot
  -	Config save and reload
  -	Stress test

## 1.3 Scale and Performance Testing

# 2 Topologies
## 2.1 Topology 1
#### 
![IP SLA](Topology1.PNG "Figure 1: IP SLA")
## 2.2 Topology 2

![IP SLA](Topology2.PNG "Figure 2: IP SLA with MLAG")

# 3 Test  Case and Objectives
## 3.1 CLI
### 3.1.1 Verify cli command to configure ip sla for icmp-echo and sub options

| **Test ID** | **FtOpSoRoIpSlaCli001** |
|--------|:----------------|
| **Test Name** | **Verify cli command to configure ip sla for icmp-echo and sub options.** |
| **Test Setup** | **Topology1** |
| **Type** | **CLI** |
| **Steps** | **1) Verify help strings are displayed while enabling ip sla mode in cli mode<br/>2) Verify sub options source-address, source-interface, request-data-size are availble and verify running-config** |

### 3.1.2 Verify cli command to configure ip sla for tcp-connect and sub options

| **Test ID** | **FtOpSoRoIpSlaCli002** |
|--------|:----------------|
| **Test Name** | **Verify cli command to configure ip sla for tcp-connect and sub options.** |
| **Test Setup** | **Topology1** |
| **Type** | **CLI** |
| **Steps** | **1) Verify help strings are displayed for ip sla sub options <br/>2) Verify sub options source-address, source-port are availble and verify running-config** |

### 3.1.3 Verify cli command to configure ip sla  options - frequency, threshold and timeout

| **Test ID** | **FtOpSoRoIpSlaCli003** |
|--------|:----------------|
| **Test Name** | **Verify cli command to configure ip sla for tcp-connect and sub options.** |
| **Test Setup** | **Topology1** |
| **Type** | **CLI** |
| **Steps** | **1) Verify help strings are displayed.<br/>2) Verify options frequency, threshold ad timeout are availble and verify running-config.<br/>3) Verify boundary values.** |

## 3.2 Functional
### 3.2.1 Track ICMP-Echo target

| **Test ID**    | **FtOpSoRoIpSlaFt001**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify ip sla to track icmp-echo target.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip sla to track icmp-echo target<br/>2) Map ip sla to a static route <br/>3) Verify ip sla state <br/>4) Verify configured/default ip sla parameters using  'show ip sla #' command.<br/>5) Verify static route is installed if the target is reachable<br/>6) Send L3 data traffic and verify <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts. |

### 3.2.2 Track TCP-Connect target

| **Test ID**    | **FtOpSoRoIpSlaFt002**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify ip sla to track tcp-connect target.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip sla to track tcp-connect target<br/>2) Map ip sla to a static route <br/>3) Verify ip sla state <br/>4) Verify configured/default ip sla parameters using  'show ip sla #' command.<br/>5) Verify static route is installed if the target is reachable<br/>6) Send L3 data traffic and verify <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts. <br/>8) Bring back the target and verify  after configured/default threshold route is reinstalled.|

### 3.2.3 Track TCP-Connect target

| **Test ID**    | **FtOpSoRoIpSlaFt003**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify ip sla to track tcp-connect target.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip sla to track tcp-connect target. Test for both ssh tcp port 22 and bgp port 179<br/>2) Map ip sla to a static route <br/>3) Verify ip sla state <br/>4) Verify configured/default ip sla parameters using  'show ip sla #' command.<br/>5) Verify static route is installed if the target is reachable<br/>6) Send L3 data traffic and verify <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>8) Bring back the target and verify  after configured/default threshold route is reinstalled. |

### 3.2.4 Switch between SLAs 

| **Test ID**    | **FtOpSoRoIpSlaFt004**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Switch between SLAs**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure icmp-echo and tcp-connect IP SLAs <br/>2) Map ip sla icmp-echo to a static route <br/>3) Verify ip sla state <br/>4) Verify static route is installed if the target is reachable.<br/>5) Unconfigure and reconfigure tcp-connect ip sla.<br/>6) Verify static route is installed if the target is reachable <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts. <br/>8) Bring back the target and verify  after configured/default threshold route is reinstalled.|

### 3.2.5  ICMP-Echo target in user vrf

| **Test ID**    | **FtOpSoRoIpSlaFt005**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify icmp-echo target in user vrf.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip sla to track icmp-echo target in user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Verify ip sla state <br/>4) Verify configured/default ip sla parameters using  'show ip sla #' command.<br/>5) Verify static route is installed if the target is reachable<br/>6) Send L3 data traffic and verify <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>8) Bring back the target and verify  after configured/default threshold route is reinstalled. |

### 3.2.6  TCP-Connect target in user vrf

| **Test ID**    | **FtOpSoRoIpSlaFt006**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify icmp-echo target in user vrf.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip sla to track tcp-connect target in user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Verify ip sla state <br/>4) Verify configured/default ip sla parameters using  'show ip sla #' command.<br/>5) Verify static route is installed if the target is able to bring up tcp connection.<br/>6) Send L3 data traffic and verify <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>8) Bring back the target and verify  after configured/default threshold route is reinstalled. |


### 3.2.7  ICMP-Echo and tcp-connect target with different frequency and threshold values

| **Test ID**    | **FtOpSoRoIpSlaFt007**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** ICMP-Echo and tcp-connect target with different frequency and threshold values.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Verify ip sla state <br/>4) configure max frequency and minimum threshold value.<br/>5) Verify icmp and tcp connect are sent at configured frequency <br/>6)  When the target is not reachable. <br/>7) Bring down the target and verify static route is uninstalled after configured threshold number of unsuccessful attempts.<br/>8) Bring back the target and verify  after configured threshold route is reinstalled. |

### 3.2.8  ICMP-Echo target with source interface

| **Test ID**    | **FtOpSoRoIpSlaFt008**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify icmp-echo target with source interface configured.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip slas to track icmp-echo target in default and user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Verify ip sla state <br/>4) Verify with different source interfaces - Vlan, Portchannel, physical port.<br/>5) Verify static route is installed if the target is reachable<br/>6) Send L3 data traffic and verify <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>8) Bring back the target and verify  after configured/default threshold route is reinstalled. |

### 3.2.9  ICMP-Echo and TCP-Connect target with source address

| **Test ID**    | **FtOpSoRoIpSlaFt009**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify icmp-echo target with source interface configured.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip slas to track icmp-echo and tcp-connect target in default and user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Verify ip sla state <br/>4) Configure a source address from which target is not reachable .<br/>5) Verify static route is not installed <br/>6) Reconfigured valid source address and verify static route is installed. <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>8) Bring back the target and verify  after configured/default threshold route is reinstalled. |

### 3.2.10  ICMP-Echo target with different request-data-size

| **Test ID**    | **FtOpSoRoIpSlaFt010**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify icmp-echo target with source interface configured.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip slas to track icmp-echo target in default and user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Verify ip sla state <br/>4) Configure a request-data-size as 28.<br/>5) Verify static route is installed <br/>6) Reconfigure max request-data-size and verify static route is installed. <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>8) Bring back the target and verify  after configured/default threshold route is reinstalled. |

### 3.2.11  ICMP-Echo and tcp-connect target with different ttl and tos values

| **Test ID**    | **FtOpSoRoIpSlaFt011**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** ICMP-Echo and tcp-connect target with different ttl and tos values.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Verify ip sla state <br/>4) configure ttl and tos values.<br/>5) Verify static route is installed <br/>6) Reconfigure ttl and tos and verify configured values. <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>8) Bring back the target and verify  after configured/default threshold route is reinstalled. |

### 3.2.12  Verify IP SLA with target reachability via default IPv6 route

| **Test ID**    | **FtOpSoRoIpSlaFt012**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** ICMP-Echo and tcp-connect target with different ttl and tos values.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Enable target reachability via default ipv6 route  <br/>4) verify ip sla state.<br/>5) Verify static route is installed <br/>6) Reconfigure reachability via IPv6 link-local and verify static route is installed. <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>8) Bring back the target and verify  after configured/default threshold route is reinstalled. |

### 3.2.13  Verify IP SLA with target reachability via connected and static routes

| **Test ID**    | **FtOpSoRoIpSlaFt013**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** ICMP-Echo and tcp-connect target with different ttl and tos values.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Enable target reachability via connected  route  <br/>4) verify ip sla state.<br/>5) Verify static route is installed <br/>6) Reconfigure reachability via static route and verify static route is installed. <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>8) Bring back the target and verify  after configured/default threshold route is reinstalled. |

### 3.2.14  Verify IP SLA with target reachability via bgp  and ospf routes

| **Test ID**    | **FtOpSoRoIpSlaFt014**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** ICMP-Echo and tcp-connect target with different ttl and tos values.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Enable target reachability via bgp  route  <br/>4) verify ip sla state.<br/>5) Verify static route is installed <br/>6) Reconfigure reachability via ospf route and verify static route is installed. <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>8) Bring back the target and verify  after configured/default threshold route is reinstalled. |

### 3.2.15  Verify IP SLA with target reachability via leaked routes

| **Test ID**    | **FtOpSoRoIpSlaFt015**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** ICMP-Echo and tcp-connect target with different ttl and tos values.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Enable target reachability via vrf leaked routes  <br/>4) verify ip sla state.<br/>5) Verify static route is installed <br/>/>6) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>7) Bring back the target and verify  after configured/default threshold route is reinstalled. |

### 3.2.16 Verify ip sla history

| **Test ID**    | **FtOpSoRoIpSlaFt016**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify ip sla history**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure icmp-echo and tcp-connect IP SLAs <br/>2) Map ip slas to static routes <br/>3) Verify ip sla states <br/>4) Verify static routes installed if the targets are reachable.<br/>5) Configure user vrf routes and map to slas .<br/>6) Flap the routes. <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>8) Verify ip sla history is updated.|

### 3.2.17  Verify IP SLA with MLAG

| **Test ID**    | **FtOpSoRoIpSlaFt017**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** ICMP-Echo and tcp-connect target with different ttl and tos values.**                 |
| **Test Setup** | **Topology2**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure MLAG as per topology.<br/>2)Configure ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf via mlag nodes.<br/>2) Map ip sla to a static route under a vrf<br/>3) Enable target reachability via default ipv6 route  <br/>4) verify ip sla state.<br/>5) Verify static route is installed <br/>6) Reconfigure reachability via IPv6 link-local and verify static route is installed. <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>8) Bring back the target and verify  after configured/default threshold route is reinstalled. |

### 3.2.18  Verify IP SLA with ACL Config for assigning CIR/PIR 

| **Test ID**    | **FtOpSoRoIpSlaFt018**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** Verify IP SLA with ACL Config for assigning CIR/PIR.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Configure acls, classifier and policy with CIR 1000.  <br/>4) verify ip sla state.<br/>5) Verify static route is installed <br/>6) Send line rate data traffic. <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>8) Bring back the target and verify  after configured/default threshold route is reinstalled. <br/>|

### 3.2.19  Verify IP SLA with PBR

| **Test ID**    | **FtOpSoRoIpSlaFt019**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** Verify IP SLA with PBR.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Configure PBR.  <br/>4) verify ip sla state.<br/>5) Verify static route is installed <br/>6) Send line rate data traffic. <br/>7) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>8) Bring back the target and verify  after configured/default threshold route is reinstalled. <br/>|

### 3.2.20  IP SLA to configure default routes 

| **Test ID**    | **FtOpSoRoIpSlaFt020**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** Verify IP SLA to install default ipv4 and ipv6 routes**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf<br/>2) Map ip slas to default static routes under a vrf<br/>3) verify ip sla states.  <br/>4) verify ipv4 and ipv6 static routes are installed <br/>5)  Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>6) Bring back the target and verify  after configured/default threshold route is reinstalled. |

### 3.2.21  Clear IP SLA statistics

| **Test ID**    | **FtOpSoRoIpSlaFt021**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** Clear IP SLA statistcs.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Verify ip sla counters and history.  <br/>4) verify ip sla state.<br/>5) Verify static route is installed <br/>6) Use clear ip sla commands to clear statistics and history. <br/>7) Verify counters are reset to zero and history is erased. |

### 3.2.22  Verify debug commands for IP SLA 

| **Test ID**    | **FtOpSoRoIpSlaFt022**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** Verify debug commands for IP SLA.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Enable IP SLA debug commands "debug iptrack" and "debug iptrack sla #"  <br/>4) verify debug logs. |

## 3.3 Scale

### 3.3.1 Verify max ICMP-Echo IP SLA in same vrf 

| **Test ID**    | **FtOpSoRoIpSlaScale001**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify max ICMP-Echo IP SLA in same vrf **                                    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure max 50 icmp-echo IP SLAs <br/>2) Map ip slas to static routes <br/>3) Verify ip sla states <br/>4) Verify static routes installed if the targets are reachable.<br/>5) Flap the routes. <br/>6) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>7) Verify ip sla history is updated. |

### 3.3.2 Verify max ICMP-Echo IP SLA in different vrfs

| **Test ID**    | **FtOpSoRoIpSlaScale002**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify max ICMP-Echo IP SLA in different vrfs**                                    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure max 50 icmp-echo IP SLAs <br/>2) Map ip slas to static routes <br/>3) Verify ip sla states <br/>4) Verify static routes installed if the targets are reachable.<br/>5) Flap the routes. <br/>6) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>7) Verify ip sla history is updated. |

### 3.3.3 Verify max TCP-Connect IP SLA in same vrf 

| **Test ID**    | **FtOpSoRoIpSlaScale003**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify max TCP-Connect IP SLA in same vrf **                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure max 50 tcp-connect with ssh,bgp IP SLAs <br/>2) Map ip slas to static routes <br/>3) Verify ip sla states <br/>4) Verify static routes installed if the targets are reachable.<br/>5) Flap the routes. <br/>6) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>7) Verify ip sla history is updated.|

### 3.3.4 Verify max TCP-Connect IP SLA in different vrfs

| **Test ID**    | **FtOpSoRoIpSlaScale004**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify max TCP-Connect IP SLA in different vrfs**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure max 50 tcp-connect IP SLAs <br/>2) Map ip slas to static routes <br/>3) Verify ip sla states <br/>4) Verify static routes installed if the targets are reachable.<br/>5) Flap the routes. <br/>6) Bring down the target and verify static route is uninstalled after configured/default threshold number of unsuccessful attempts.<br/>7) Verify ip sla history is updated.|

## 3.4 Reboot/Reload/Upgrade Test Cases
#### 3.4.1 Warm reboot

| **Test ID**    | **FtOpSoRoIpSlaTrig001**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** Warm reboot with IP sla configs.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure max ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Enable target reachability via default ipv6 route  <br/>4) verify ip sla state.<br/>5) Verify static route is installed <br/>6) Send traffic over the static route . <br/>7) Initiate warm reboot. <br/>8) Verify there is no traffic drop  |

#### 3.4.2  Cold reboot
| **Test ID**    | **FtOpSoRoIpSlaTrig002**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** Fast reboot with ip sla configs.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure max ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf<br/>2) Map ip sla to a static route under a vrf<br/>3) Enable target reachability via default ipv6 route  <br/>4) verify ip sla state.<br/>5) Verify static route is installed <br/>6) Send traffic over the static route . <br/>7) Initiate warm reboot. <br/>8) Verify there is no traffic drop  |

#### 3.4.3 Config reload

| **Test ID**    | **FtOpSoRoIpSlaTrig003**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** Config reload with ip sla configs.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure max ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf<br/>2) Map ip sla to a default vrf and user vrf static route. <br/>3)verify ip sla state  <br/>4) Save config and initiate config reload.<br/>5) Verify ip sla state and static route is installed after bgp daemon is up.<br/>6) Send traffic over the static route . |

#### 3.4.4  Docker restart

| **Test ID**    | **FtOpSoRoIpSlaTrig004**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** BGP docker restart with IP SLA configs.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure max ip slas to track icmp-echo and tcp-connect targets in default and user defined vrf<br/>2) Map ip sla to a default vrf and user vrf static route. <br/>3)verify ip sla state  <br/>4) Initiate bgp docker restart.<br/>5) Verify ip sla state and static route is installed after bgp daemon is up.<br/>6) Send traffic over the static route . |

## 3.5 Management

#### 3.5.1 SNMP

#### 3.5.2 gNMI

#### 3.5.3 REST API
##### 3.5.3.1  POST operation

| **Test ID**    | **FtOpSoRoIpSlaRest001**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** REST API POST to configure IP SLA.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Use REST POST method to configure IP SLA for ICMP-Echo and TCP-Connect. |

##### 3.5.3.2  PATCH operation

| **Test ID**    | **FtOpSoRoIpSlaRest002**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** REST API PATCH to configure IP SLA.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Use REST PATCH method to  configure IP SLA paramters like frequency, threshold, source-address etc.. |

##### 3.5.3.3  GET operation

| **Test ID**    | **FtOpSoRoIpSlaRest003**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** REST API GET to verify all configured IP SLA paramters.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Use REST GET method to  verify all configured IP SLA paramters. |

##### 3.5.3.4  DELETE operation

| **Test ID**    | **FtOpSoRoIpSlaRest004**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** REST API DELETE to unconfigure IP SLA.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Use REST DELETE method to unconfigure IP SLA for ICMP-Echo and TCP-Connect. |
## 3.6 Stress,Scale and Performance
#### 3.6.1 Stress test IP SLA with link flap
| **Test ID**    | **FtOpSoRoIpSlaStress001**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | ** Stress test IP SLA with link flap.**                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure IP SLA ICMP-Echo and TCP-Connect.  <br/>2) Flap route reachability tiggering static route install and unstall. Run the test for 24 hrs. <br/>3) Verify memory and cpu utilizations before and after the test run. |

# 4 Reference Links

<https://github.com/BRCM-SONIC/sonic_doc_private/blob/eab70f10cba0ab4a585472a62673faa3afca4e0c/L3/ipsla/ip_sla.md>

