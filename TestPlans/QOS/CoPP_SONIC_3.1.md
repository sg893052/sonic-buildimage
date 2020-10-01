#  SQA Test Plan
# SONIC CoPP Management
#  SONiC 3.1 Project and Buzznik+ Release
[TOC]

## Test Plan Revision History

| Rev  | Date       | Author          | Change Description                                          |
| ---- | ---------- | --------------- | ----------------------------------------------------------- |
| 0.1  | 11/05/2020 | Gangadhara Sahu | Initial Version                                             |
| 0.2  | 06/05/2020 | Gangadhara Sahu | updated with new CLI after change in CLI as per MSFT design |

## List of Reviewers

| Function | Name |
| :------: | :--: |
|          |      |

## List of Approvers

| Function | Name | Date Approved |
| :------: | :--: | :-----------: |
|          |      |               |

## Definition/Abbreviation

| **Term** | **Meaning**                |
| -------- | -------------------------- |
| QoS      | Quality of Service         |
| CoPP     | Control Plane Policing     |
| CIR      | Committed Information Rate |
| CBS      | Committed Burst Size       |
| PIR      | Peak Information Rate      |
| PBS      | Peak Burst Size            |
## Introduction

### Objective 

The main objective of this document is to cover the test cases that will be executed to verify the new command line introduced to configure CoPP Mgmt or Control Plane Policing Management feature in SONIC Topologies and test cases  for testing the feature will be discussed as part of this document.

### Scope

This test plan will cover CoPP related enhancements done in Buzznik+ release which are mostly in the area of new CoPP policy CLI and  manageability.

1. Support creation of multiple CoPP classifiers with protocol trap match types
2. Support applying multiple CoPP classifiers to the system CoPP policy.
3. Support configuration of CoPP classifier actions such as assignment of the protocol trap to a specific CPU queue.
4. Support addition and configuration of a policer to a CoPP classifier to rate limit the bandwidth of each protocol trap matched by the classifier.
5. The new CLI introduced for the CoPP management feature in this release is supporting only klish command.   

### Out of scope

* Multi-dimensional protocol test cases along with COPP feature is out of scope or will not be covered as that is specific to system level testing with different protocol specific features enabled at the same time. 

## Feature Overview

The Control Plane Policing (CoPP) feature allows management of the network traffic flow handled by the control plane CPU. CoPP is designed to protect the CPU from getting overwhelmed by a high rate of CPU bound traffic which can affect system performance. CoPP policies allow configuration of trap IDs (traffic punted to CPU), CPU queue assignments, and policers to rate limit the traffic.

## 1 Test Focus Areas

### 1.1 CLI Testing 

  - All commands to support configuration CoPP classifiers. Some of the CLIs will be restricted to configure as it may cause system to go to unstable state if user wrongly configure those parameter. Please refer the below link for more details.<BR/>https://docs.google.com/spreadsheets/d/1oj5X-jgHw3myZXCtltXq1IhukRA6RwaWu5tGdTD0LmY/edit#gid=1989870311
  - CoPP classifiers application to the system CoPP policy.

### 1.2 Functional Testing

  -	Support creation of multiple user defined CoPP classifiers with protocol trap match types.
  -	Support applying multiple user defined CoPP classifiers to the system CoPP policy.
  -	Support configuration of CoPP classifier actions such as assignment of the protocol trap to a specific CPU queue. System will allow user to configure protocol match types only if the same protocol match is not present already in the default copp policy. Meaning only non conflicting protocol match types will be allowed.
  -	Support addition and configuration of a policer to a CoPP classifier to rate limit the bandwidth of each protocol trap matched by the classifier.
  -	Config save and reload

### 1.3 Scalability Testing

* Support a CoPP classifier for each protocol trap supported by SONiC. This allows configuration of a separate policer with a unique rate limit value for each protocol trap.
* Multiple CoPP classifiers can be assigned to the same CPU queue. COPP management feature will allow max up to 32 CPU queues (internal design limitation).

## 2 Topologies

<span style="color:red"> </span>![CoPP Topology](CoPP_topology.png "Figure: Topology 1")

#### 								Topology 1 

  - Most of the test case will use only one to two links between DUT 1 and TGEN.
  - In case any control protocol which can not be simulated b/w DUT 1 and TGEN box, we will use DUT 1 and DUT 2.
  - First link b/w DUT 1 and TGEN will be used for pumping control traffic from TGEN towards DUT 1.
  - Second link b/w DUT 1 and TGEN will be used for mirroring the ingress packets from TG to DUT 1 in case any of the control traffic is not setup properly by TGEN.  

## 3 Test Case and objectives

### 3.1 CLI Test Cases

#### 3.1.1 Verify CoPP classifier global CLI

| **Test ID**    | **Copp311**                                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify CoPP classifier global CLI**                        |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Create a CoPP classifier. <BR />2. Adding match protocol traps to the user defined CoPP Classifers. <BR />sonic(config)# classifier copp-user-arp match-type copp <BR />sonic(config-classifier)# match protocol arp_req  <BR />sonic(config-classifier)# exit  <BR />3. Delete the copp classifier globally.<BR />Verify the CoPP classifier got deleted along with its match protocol CLI using the command show copp classfier. <BR/>NOTE: creation/deletion/modify supported for only user defined classifiers only.  copp-system classifiers cannot be deleted or modified. |

#### 3.1.2 Verify match protocol CLI in copp classifier

| **Test ID**    | **Copp312**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CLI for CoPP classifier match protocol change**     |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Create a copp class and add match protocol under it <BR />sonic(config)# classifier copp-system-new match-type copp<BR />sonic(config-classifier)# match protocol ptp <BR />sonic(config-classifier)# exit <BR />2. Verify the copp class and its associated match protocol updated using the show copp classifiers CLI or show classifier match-type copp. <BR />sonic# show classifier match-type copp <BR />Classifier copp-system-new match-type copp <BR />&nbsp;&nbsp;protocol ptp <BR />3. Now remove the match protocol <BR />sonic(config)# classifier copp-system-new match-type copp <BR />sonic(config-classifier)# no match protocol ptp <BR />sonic(config-classifier)# match protocol icmp <BR />sonic(config-classifier)# exit <BR />3. Verify the match protocol change for the copp class using the show policy type copp.<BR />sonic# show classifier match-type copp <BR />Classifier copp-system-new match-type copp <BR />&nbsp;&nbsp;protocol icmp |

#### 3.1.3 Verify trap action CLI in copp classifier  

| Test ID        | **Copp313**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CLI for CoPP classifier action as trap action**     |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configuring the copp action group with trap-action<br />sonic(config)# copp-action copp-user-mtu<br/>sonic(config-action)# set trap-action trap<br/>NOTE: Here trap action could be one of the value from "drop, forward, copy, copy_cancel, trap, log, deny, transit".<br/>2. Remove the trap action from the copp action group.<br />sonic(config)# copp-action copp-user-mtu<br/>sonic(config-action)# no set trap-action trap<br/>3. Apply new trap action for the copp action group.<br />sonic(config)# copp-action copp-user-mtu<br/>sonic(config-action)# set trap-action copy<br/>4. Verify the new trap action for the same copp action group using the command show copp actions. <BR/>NOTE: Each protocol can only be configured for a subset of these trap actions. These will show up as syslog errors in unsupported cases. |

#### 3.1.4 Verify trap queue CLI in CoPP classifier    

| Test ID        | **Copp314**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CLI for CoPP classifier action as CPU queue inside the CoPP policy** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configuring the copp action group with trap-queue<br />sonic(config)# copp-action copp-system-ip2me<br/>sonic(config-action)# set trap-queue 2<br/>2. Remove the trap queue from the copp action group.<br />sonic(config)# copp-action copp-system-ip2me<br/>sonic(config-action)# no set trap-queue 2<br/>NOTE: no trap-queue => will set it to Queue 0.<BR/>3. Apply new trap queue for the copp action group.<br />sonic(config)# copp-action copp-system-ip2me<br/>sonic(config-action)# set trap-queue 3<br/>4. Verify the new trap action for the same copp action group using the command show copp actions. |

#### 3.1.5 ~~Verify CoPP classifier policer meter type bps CLI (not supported CLI)~~

#### 3.1.6 ~~Verify CoPP classifier policer mode from sr_tcm to tr_tcm (not supported CLI)~~

| Test ID        | **Copp316**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | ~~**Verify CoPP classifier policer mode CLI (not supported)**~~ |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | ~~1. Configuring the copp action group with policer mode<br />sonic(config)# copp-action copp-user-ip2me<br/>sonic(config-action)# police mode sr_tcm red drop<br/>2. Remove the police mode from the copp action group.<br />sonic(config)# copp-action copp-user-ip2me<br/>sonic(config-action)# no police mode sr_tcm<br/>3. Apply new police meter-mode as tr_tcm for the copp action group.<br />sonic(config)# copp-action copp-system-ip2me<br/>sonic(config-action)# police mode tr_tcm red drop<br/>4. Verify the new trap action for the same copp action group using the command show copp actions.~~ |

#### 3.1.7 Verify binding of copp class and copp action group to copp policy

| Test ID        | **Copp317**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify binding of copp class and copp action group to copp policy** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Conigure the copp class <br/>sonic(config)# classifier copp-user-ospf match-type copp <br/>sonic(config-classifier)# match protocol ospf <br/>sonic(config-classifier)# exit <br/><br/>2. Conigure the copp action group <br/>sonic(config)# copp-action copp-user-ospf<br/>sonic(config-action)# set trap-action copy<br/>sonic(config-action)# set trap-priority 5 <br/>sonic(config-action)# set trap-queue 5 <br/>sonic(config-action)# police meter-type pps <br/>sonic(config-action)# police mode sr_tcm red drop<br/>sonic(config-action)# police cir 10000 cbs 10000<br/>sonic(config-action)# exit<br/><br/>3. Bind the copp class and action group to the copp policy. <br/>sonic(config)# policy copp-system-policy type copp<br/>sonic(config-policy)# class copp-user-ospf<br/>sonic(config-policy-flow)# set copp-action copp-user-ospf<br/>sonic(config-policy-flow)# exit<br/><br/>4. Verify the show policy type copp to see the copp class and action in binding. <BR/>NOTE:  With new changes, copp-system classifiers cannot be modified (test 1). As user classifier can be created and bound to the copp policy after the copp-system classifier is removed from the policy. |

#### 3.1.8 Verify CoPP classifier policer rate CLI

| Test ID        | **Copp318**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP classifier policer rate CLI**                  |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configuring the copp action group with policer rate<br />sonic(config)# copp-action copp-user-ip2me<br/>sonic(config-action)# police cir 6000 cbs 6000<br/>2. Remove the policer rate from the copp action group.<br />sonic(config)# copp-action copp-user-ip2me<br/>sonic(config-action)# no police cir 6000<br/>sonic(config-action)# no police cbs 6000<br/>3. Apply new policer rate for the copp action group.<br />sonic(config)# copp-action copp-user-ip2me<br/>sonic(config-action)# police cir 6300 cbs 6300<br/>4. Verify the new policer rate for the same copp action group using the command show copp actions. |

#### 3.1.9 Verify show classifier match-type copp

| Test ID        | **Copp319**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP classifier policer rate or show copp classifiers CLI** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify shows classifier match-type copp to confirm the configured and default copp classes present in the system.<br/>sonic# show classifier match-type copp <br/>Classifier copp-system-bgp match-type copp <br/>&nbsp;&nbsp;protocol bgp  <br/>&nbsp;&nbsp;protocol bgpv6  <br/>Classifier copp-system-arp match-type copp  <br/>&nbsp;&nbsp;protocol arp_req  <br/>&nbsp;&nbsp;protocol arp_resp  <br/>&nbsp;&nbsp;protocol neigh_discovery  <br/>... |

#### 3.1.10 Verify show copp protocols CLI

| Test ID        | **Copp3110**                                                 |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP classifier policer rate CLI**                  |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | shows the copp protocol trap ids configured or present in the system by default<br/>sonic# show copp protocols <br/>Classifier match-type copp protocols <br/>&nbsp;&nbsp;protocol bgp <br/>&nbsp;&nbsp;protocol bgpv6 <br/>&nbsp;&nbsp;protocol arp_req <br/>&nbsp;&nbsp;protocol arp_resp <br/>&nbsp;&nbsp;protocol neigh_discovery<br/>... |

#### 3.1.11 Verify show copp actions CLI

| Test ID        | **Copp3111**                                                 |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP classifier policer rate CLI**                  |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | shows configured CoPP action groups conigured or present in the system by default.<br/>sonic# show copp actions<br/>CoPP action group copp-system-arp<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    trap-action: copy<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    trap-priority: 3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    trap-queue: 3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    police cir 6000 cbs 6000<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/> |

#### 3.1.12 Verify show policy type copp CLI  

| Test ID        | **Copp312**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP classifier policer rate CLI**                  |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | show the CoPP policy copp-system-policy <br/>sonic# show policy type copp<br/>Policy copp-system-policy Type copp<br/>&nbsp;Flow copp-system-arp<br/>&nbsp;&nbsp;&nbsp;  Action copp-system-arp<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    trap-action: copy<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    trap-priority: 3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    trap-queue: 3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    police cir 6000 cbs 6000<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/>.... |

#### 3.1.13 Verify show copp classifiers CLI

| Test ID        | **Copp3113**                                                 |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify show copp classifiers CLI**                         |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify shows copp classifiers CLI to confirm configured and default copp classes present in the system. sonic# show classifier match-type copp  Classifier copp-system-bgp match-type copp   protocol bgp    protocol bgpv6   Classifier copp-system-arp match-type copp    protocol arp_req    protocol arp_resp    protocol neigh_discovery   ... |

#### 3.1.14 Verify show copp policy CLI

| Test ID        | **Copp3114**                                                 |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify copp policy CLI**                                   |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify show copp policy CLI <br/>sonic# show copp policy <br/>Policy copp-system-policy Type copp<br/>&nbsp;Flow copp-system-arp<br/>&nbsp;&nbsp;&nbsp;  Action copp-system-arp<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    trap-action: copy<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    trap-priority: 3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    trap-queue: 3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    police cir 6000 cbs 6000<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/>.... |

### 3.2 Functional Test Cases

##### 3.2.1 Verify single CoPP class with its copp action group applied to copp system policy 

| **Test ID**    | **Copp321**                                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify single CoPP class with its copp action group applied to copp system policy** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure single copp class and single copp action group. <BR/><br/>sonic(config)# classifier copp-user-arp match-type copp <BR/>sonic(config-classifier)# match protocol arp_req <BR/>sonic(config-classifier)# match protocol arp_resp <BR/>sonic(config-classifier)# match protocol neigh_discovery<BR/>sonic(config-classifier)# exit <BR/> <BR/>sonic(config)# copp-action copp-user-arp <BR/>sonic(config-action)# set trap-action copy <BR/>sonic(config-action)# set trap-priority 3  <BR/>sonic(config-action)# set trap-queue 3 <BR/>sonic(config-action)# police meter-type pps <BR/>sonic(config-action)# police mode sr_tcm red drop <BR/>sonic(config-action)# police cir 6000 cbs 6000 <BR/>sonic(config-action)# exit <BR/><BR/>2. Apply or bind the single copp class and the copp action group to the default copp system policy after unbinding the copp-system-arp class from the policy<BR/>sonic(config)# policy copp-system-policy type copp<br/>sonic(config-policy)# no class copp-system-arp<BR/>sonic(config-policy)# class copp-user-arp<BR/>sonic(config-policy-flow)# set copp-action copp-user-arp<br/>sonic(config-policy-flow)# exit<BR/>3. Verify the copp class and copp action for applied to copp system policy using show policy type copp. |
##### 3.2.2 Verify multiple copp classes applied to the copp system policy 

| **Test ID**    | **Copp322**                                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify multiple copp classes applied to the copp system policy** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1.Configure multiple copp classes and multiple copp action groups. <BR/>2.Bind all the classes and corresponding copp action group to copp system policy. <BR/>3. Verify the copp system policy will show only one class and action group applied.<BR/>Note that the copp-system classifiers cannot be modified. Intention here is to validate the protocols in the default copp system classifiers. |

- **Default CoPP policy config should show as** 

sonic(config)# classifier copp-system-arp match-type copp <br/>
sonic(config-classifier)# match protocol arp_req <br/>
sonic(config-classifier)# match protocol arp_resp <br/>
sonic(config-classifier)# match protocol neigh_discovery<br/>
sonic(config-classifier)# exit<br/>
sonic(config)# classifier copp-system-lldp match-type copp<br/>
sonic(config-classifier)# match protocol lldp<br/>
sonic(config-classifier)# exit<br/>
sonic(config)# classifier copp-system-bgp match-type copp<br/>
sonic(config-classifier)# match protocol bgp<br/>
sonic(config-classifier)# exit<br/>
sonic(config-classifier)# match protocol bgpv6<br/>
sonic(config-classifier)# exit<br/>
sonic(config)# classifier copp-system-icmp match-type copp<br/>
sonic(config-classifier)# match protocol icmp<br/>
sonic(config-classifier)# match protocol icmpv6<br/>
sonic(config-classifier)# match protocol dhcp<br/>
sonic(config-classifier)# match protocol dhcpv6<br/>
sonic(config-classifier)# exit<br/>
sonic(config)# classifier copp-system-igmp match-type copp<br/>
sonic(config-classifier)# match protocol igmp<br/>
sonic(config-classifier)# exit<br/>
sonic(config)# classifier copp-system-ip2me match-type copp<br/>
sonic(config-classifier)# match protocol ip2me<br/>
sonic(config-classifier)# exit<br/>
sonic(config)# classifier copp-system-lacp match-type copp<br/>
sonic(config-classifier)# match protocol lacp<br/>
sonic(config-classifier)# exit<br/>
sonic(config)# classifier copp-system-l3mtu match-type copp<br/>
sonic(config-classifier)# match protocol src_nat_miss<br/>
sonic(config-classifier)# match protocol dest_nat_miss<br/>
sonic(config-classifier)# match protocol l3_mtu_error<br/>
sonic(config-classifier)# exit<br/>
sonic(config)# classifier copp-system-ospf match-type copp<br/>
sonic(config-classifier)# match protocol ospf<br/>
sonic(config-classifier)# exit<br/>
sonic(config)# classifier copp-system-pim match-type copp<br/>
sonic(config-classifier)# match protocol pim<br/>
sonic(config-classifier)# exit<br/>
sonic(config)# classifier copp-system-ptp match-type copp<br/>
sonic(config-classifier)# match protocol ptp<br/>
sonic(config-classifier)# exit<br/>
sonic(config)# classifier copp-system-sflow match-type copp<br/>
sonic(config-classifier)# match protocol sample_packet<br/>
sonic(config-classifier)# exit<br/>
sonic(config)# classifier copp-system-pvrst match-type copp<br/>
sonic(config-classifier)# match protocol stp<br/>
sonic(config-classifier)# match protocol pvrst<br/>
sonic(config-classifier)# exit<br/>
sonic(config)# classifier copp-system-subnet match-type copp<br/>
sonic(config-classifier)# match protocol subnet<br/>
sonic(config-classifier)# exit<br/>
sonic(config)# classifier copp-system-suppress match-type copp<br/>
sonic(config-classifier)# match protocol arp_suppress<br/>
sonic(config-classifier)# match protocol nd_suppress<br/>
sonic(config-classifier)# exit<br/>
sonic(config)# classifier copp-system-iccp match-type copp<br/>
sonic(config-classifier)# match protocol iccp<br/>
sonic(config-classifier)# match protocol vrrp<br/>
sonic(config-classifier)# match protocol vrrpv6<br/>
sonic(config-classifier)# exit<br/><br/>

sonic(config)# copp-action copp-system-arp <br/>
sonic(config-action)# set trap-action copy <br/>
sonic(config-action)# set trap-priority 3 <br/>
sonic(config-action)# set trap-queue 3 <br/>
sonic(config-action)# police meter-type pps <br/>
sonic(config-action)# police mode sr_tcm red drop<br/>
sonic(config-action)# police cir 6000 cbs 6000<br/>
sonic(config-action)# exit<br/>
sonic(config)# copp-action copp-system-lldp<br/>
sonic(config-action)# set trap-action trap<br/>
sonic(config-action)# set trap-priority 7<br/>
sonic(config-action)# set trap-queue 7<br/>
sonic(config-action)# police meter-type pps<br/>
sonic(config-action)# police mode sr_tcm red drop<br/>
sonic(config-action)# police cir 1000 cbs 1000<br/>
sonic(config-action)# exit<br/>
sonic(config)# copp-action copp-system-bgp<br/>
sonic(config-action)# set trap-action trap<br/>
sonic(config-action)# set trap-priority 4 <br/>
sonic(config-action)# set trap-queue 4 <br/>
sonic(config-action)# police meter-type pps <br/>
sonic(config-action)# police mode sr_tcm red drop<br/>
sonic(config-action)# police cir 10000 cbs 10000<br/>
sonic(config-action)# exit<br/>
sonic(config)# copp-action copp-system-icmp<br/>
sonic(config-action)# set trap-action trap<br/>
sonic(config-action)# set trap-priority 3  <br/>
sonic(config-action)# set trap-queue 11<br/>
sonic(config-action)# police meter-type pps <br/>
sonic(config-action)# police mode sr_tcm red drop<br/>
sonic(config-action)# police cir 1000 cbs 1000<br/>
sonic(config-action)# exit<br/>
sonic(config)# copp-action copp-system-igmp<br/>
sonic(config-action)# set trap-action trap<br/>
sonic(config-action)# set trap-priority 4<br/>
sonic(config-action)# set trap-queue 4 <br/>
sonic(config-action)# police meter-type pps <br/>
sonic(config-action)# police mode sr_tcm red drop <br/>
sonic(config-action)# police cir 6000 cbs 6000 <br/>
sonic(config-action)# exit <br/>
sonic(config)# copp-action copp-system-ip2me <br/>
sonic(config-action)# set trap-action trap <br/>
sonic(config-action)# set trap-priority 2  <br/>
sonic(config-action)# set trap-queue 2 <br/>
sonic(config-action)# police meter-type pps <br/>
sonic(config-action)# police mode sr_tcm red drop<br/>
sonic(config-action)# police cir 6000 cbs 6000<br/>
sonic(config-action)# exit<br/>
sonic(config)# copp-action copp-system-l3mtu<br/>
sonic(config-action)# set trap-action trap<br/>
sonic(config-action)# set trap-priority 1 <br/>
sonic(config-action)# set trap-queue 1 <br/>
sonic(config-action)# police meter-type pps <br/>
sonic(config-action)# police mode sr_tcm red drop<br/>
sonic(config-action)# police cir 500 cbs 500<br/>
sonic(config-action)# exit<br/>
sonic(config)# copp-action copp-system-ospf<br/>
sonic(config-action)# set trap-action copy<br/>
sonic(config-action)# set trap-priority 5 <br/>
sonic(config-action)# set trap-queue 5 <br/>
sonic(config-action)# police meter-type pps <br/>
sonic(config-action)# police mode sr_tcm red drop<br/>
sonic(config-action)# police cir 10000 cbs 10000<br/>
sonic(config-action)# exit<br/>
sonic(config)# copp-action copp-system-pim<br/>
sonic(config-action)# set trap-action trap<br/>
sonic(config-action)# set trap-priority 4 <br/>
sonic(config-action)# set trap-queue 4 <br/>
sonic(config-action)# police meter-type pps <br/>
sonic(config-action)# police mode sr_tcm red drop<br/>
sonic(config-action)# police cir 10000 cbs 10000<br/>
sonic(config-action)# exit<br/>
sonic(config)# copp-action copp-system-ptp<br/>
sonic(config-action)# set trap-action trap<br/>
sonic(config-action)# set trap-priority 7 <br/>
sonic(config-action)# set trap-queue 24 <br/>
sonic(config-action)# police meter-type pps <br/>
sonic(config-action)# police mode sr_tcm red drop<br/>
sonic(config-action)# police cir 10000 cbs 10000<br/>
sonic(config-action)# exit<br/>
sonic(config)# copp-action copp-system-sflow<br/>
sonic(config-action)# set trap-action trap<br/>
sonic(config-action)# set trap-priority 8 <br/>
sonic(config-action)# set trap-queue 8 <br/>
sonic(config-action)# police meter-type pps <br/>
sonic(config-action)# police mode sr_tcm red drop<br/>
sonic(config-action)# police cir 16000 cbs 16000<br/>
sonic(config-action)# exit<br/>
sonic(config)# copp-action copp-system-pvrst<br/>
sonic(config-action)# set trap-action trap<br/>
sonic(config-action)# set trap-priority 6 <br/>
sonic(config-action)# set trap-queue 6 <br/>
sonic(config-action)# police meter-type pps <br/>
sonic(config-action)# police mode sr_tcm red drop<br/>
sonic(config-action)# police cir 16000 cbs 16000<br/>
sonic(config-action)# exit<br/>
sonic(config)# copp-action copp-system-subnet<br/>
sonic(config-action)# set trap-action trap<br/>
sonic(config-action)# set trap-priority 10 <br/>
sonic(config-action)# set trap-queue 10 <br/>
sonic(config-action)# police meter-type pps <br/>
sonic(config-action)# police mode sr_tcm red drop<br/>
sonic(config-action)# police cir 6000 cbs 6000<br/>
sonic(config-action)# exit  <br/>
sonic(config)# copp-action copp-system-suppress<br/>
sonic(config-action)# set trap-action trap<br/>
sonic(config-action)# set trap-priority 5 <br/>
sonic(config-action)# set trap-queue 9 <br/>
sonic(config-action)# police meter-type pps <br/>
sonic(config-action)# police mode sr_tcm red drop<br/>
sonic(config-action)# police cir 5000 cbs 5000<br/>
sonic(config-action)# exit<br/>
sonic(config)# copp-action copp-system-iccp<br/>
sonic(config-action)# set trap-action trap<br/>
sonic(config-action)# set trap-priority 5 <br/>
sonic(config-action)# set trap-queue 5 <br/>
sonic(config-action)# police meter-type pps <br/>
sonic(config-action)# police mode sr_tcm red drop<br/>
sonic(config-action)# police cir 5000 cbs 5000<br/>
sonic(config-action)# exit<br/>sonic(config)# copp-action copp-system-lacp<br/>
sonic(config-action)# set trap-action trap<br/>
sonic(config-action)# set trap-priority 25 <br/>
sonic(config-action)# set trap-queue 25 <br/>
sonic(config-action)# police meter-type pps <br/>
sonic(config-action)# police mode sr_tcm red drop<br/>
sonic(config-action)# police cir 1000 cbs 1000<br/>
sonic(config-action)# exit<br/><br/>

sonic(config)# policy copp-system-policy type copp<br/>
sonic(config-policy)# class copp-system-arp<br/>
sonic(config-policy-flow)# set copp-action copp-system-arp<br/>
sonic(config-policy-flow)# exit<br/>
sonic(config-policy)# class copp-system-lldp<br/>
sonic(config-policy-flow)# set copp-action copp-system-lldp<br/>
sonic(config-policy-flow)# exit<br/>
sonic(config-policy)# class copp-system-bgp<br/>
sonic(config-policy-flow)# set copp-action copp-system-bgp<br/>
sonic(config-policy-flow)# exit<br/>
sonic(config-policy)# class copp-system-icmp<br/>
sonic(config-policy-flow)# set copp-action copp-system-icmp<br/>
sonic(config-policy-flow)# exit<br/>
sonic(config-policy)# class copp-system-igmp<br/>
sonic(config-policy-flow)# set copp-action copp-system-igmp<br/>
sonic(config-policy-flow)# exit<br/>
sonic(config-policy)# class copp-system-ip2me<br/>
sonic(config-policy-flow)# set copp-action copp-system-ip2me<br/>
sonic(config-policy-flow)# exit<br/>
sonic(config-policy)# class copp-system-lacp<br/>
sonic(config-policy-flow)# set copp-action copp-system-lacp<br/>
sonic(config-policy-flow)# exit<br/>
sonic(config-policy)# class copp-system-l3mtu<br/>
sonic(config-policy-flow)# set copp-action copp-system-l3mtu<br/>
sonic(config-policy-flow)# exit<br/>
sonic(config-policy)# class copp-system-ospf<br/>
sonic(config-policy-flow)# set copp-action copp-system-ospf<br/>
sonic(config-policy-flow)# exit<br/>
sonic(config-policy)# class copp-system-pim<br/>
sonic(config-policy-flow)# set copp-action copp-system-pim<br/>
sonic(config-policy-flow)# exit<br/>
sonic(config-policy)# class copp-system-ptp<br/>
sonic(config-policy-flow)# set copp-action copp-system-ptp<br/>
sonic(config-policy-flow)# exit<br/>
sonic(config-policy)# class copp-system-sflow<br/>
sonic(config-policy-flow)# set copp-action copp-system-sflow<br/>
sonic(config-policy-flow)# exit<br/>
sonic(config-policy)# class copp-system-pvrst<br/>
sonic(config-policy-flow)# set copp-action copp-system-pvrst<br/>
sonic(config-policy-flow)# exit<br/>
sonic(config-policy)# class copp-system-subnet<br/>
sonic(config-policy-flow)# set copp-action copp-system-subnet<br/>
sonic(config-policy-flow)# exit<br/>
sonic(config-policy)# class copp-system-suppress<br/>
sonic(config-policy-flow)# set copp-action copp-system-suppress<br/>
sonic(config-policy-flow)# exit<br/>
sonic(config-policy)# class copp-system-iccp<br/>
sonic(config-policy-flow)# set copp-action copp-system-iccp<br/>
sonic(config-policy-flow)# exit<br/>


##### 3.2.3 Verify modification of user defined CoPP classifier policer attributes while traffic is ON

| **Test ID**    | **Copp323**                                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify modification of user defined CoPP classifier attributes while traffic is ON** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify modification of the user defined CoPP classifier when the classifier is already in applied state with traffic <BR/>Verify add or delete of a protocol inside the same classifier <BR/>Verify the modification of  policer rate attributes like CIR, CBS, PIR and PBS on the fly while traffic is ON and policer in applied state with rate limit tolerance value of 5% to get best possible accuracy.<BR/>Verify that copp policy config by using show policy type copp command. <br/>Verify that classifier config for each of the above step using show policy type copp command. <br/>Verify packets drop per sec using the show command bcmcmd "show c cpu".<br/>.NOTE: In pps mode, the CBS and PBS values are also in units of packets. We need to test the burst parameters by adjusting the TGEN traffic below the PPS rate limit but above the pkt burst size. For example, we would expect to see queue drops from "show queue counters CPU" when sending CPU traffic at 2000 pps using a single burst when configured at a higher rate limit (example 6000pps) but lower burst size such as 1000 pkts. |

##### 3.2.4 Verify modification of the CoPP classifier CPU queue and priority while traffic is ON

| **Test ID**    | **Copp324**                                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify modification of the CoPP classifier CPU queue and priority while traffic is ON** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify modification of the CoPP classifier CPU queue and priority when the classifier is already in applied state with traffic <BR/>Verify add or delete of a protocol inside the same classifier <BR/>Verify the modification of  CPU queue and priority <BR/>Verify that copp policy config by using show policy type copp command. <br/>Verify packets drop per sec using the show command bcmcmd "show c cpu".<br/>Verify the new queue and priority taking into effect after change using the show command bcmcmd "show c cpu". |

##### 3.2.5 Verify modification of the CoPP classifier trap action while traffic is ON

| **Test ID**    | **Copp325**                                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify modification of the CoPP classifier trap action while traffic is ON** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify modification of the CoPP classifier trap action from forward to drop when the classifier is already in applied state with traffic <BR/>Verify add or delete of a protocol inside the same classifier <BR/>Verify the modification of  trap action <BR/>Verify that copp policy config by using show policy type copp command. <br/>Verify the new trap action taking effect after change with traffic.<BR/>Please note that not all classifier trap actions are supported for all types of protocols.. <br/><br/>Here we change in trap actions as trap and drop but not copy. |

##### 3.2.6 Verify higher priority control traffic may starve out due to congestion in lower priority control traffic 

| **Test ID**    | **Copp326**                                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify higher priority control traffic may starve out due to congestion in lower priority control traffic** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Configure single classifier copp policy with two control protocol using the same CPU queue and send traffic. <br/>Verify that copp policy config by using show policy type copp command. <br/>Verify higher priority control traffic may starve out due to congestion in lower priority control traffic up to some extent due to CPU queue scheduling is round robin in Buzznik+<BR/>NOTE: With all CPU traffic types in separate queues, there will be a slight additional hit to latency in congested scenarios (12 queues in Buzznik increased to 20 queues in Buzznik+).<br/> In a future enhancement, we will change to weighted round robin or strict priority with committed rates. |

##### 3.2.7 Verify system running all the user defined CoPP classes, action groups and binding 

| **Test ID**    | **Copp327**                                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify system running all the user defined CoPP classes, action groups and binding** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify system running all the user defined CoPP classes, action groups and binding working fine<BR/>Here we are not configuring all user defined classes in copp policy. <br/>Verify the trap_queue, priority, CIR etc for each control traffic queue using the command below.<BR/>sonic# show policy type copp <br/>Policy copp-system-policy Type copp <br/>  Flow copp-user-bgp<br/>     trap-action trap<br/>     trap-priority 5<br/>     trap-queue 5<br/>     ... and so on <br/>Verify other show commands like show classifiers match-type copp, show copp actions etc. <br/>sonic# show copp policy<br/> |

- **Show policy type copp should show as below by default**

sonic# show policy type copp<br/>
Policy copp-system-policy Type copp<br/>
  &nbsp;Flow copp-user-system-arp<br/>
  &nbsp;&nbsp;  Action copp-user-arp<br/>
  &nbsp;&nbsp;&nbsp;    trap-action: copy<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority: 3<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue: 3<br/>
  &nbsp;&nbsp;&nbsp;    police cir 6000 cbs 6000<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/>
  &nbsp;Flow copp-user-bgp<br/>
  &nbsp;&nbsp;  Action copp-user-bgp<br/>
  &nbsp;&nbsp;&nbsp;    trap-action trap<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority 4<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue 4<br/>
  &nbsp;&nbsp;&nbsp;    police cir 10000 cbs 10000<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/>
  &nbsp;Flow copp-user-lldp<br/>
  &nbsp;&nbsp;  Action copp-user-lldp<br/>
  &nbsp;&nbsp;&nbsp;    trap-action trap<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority 7<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue 7<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;    police cir 1000 cbs 1000<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/>
  &nbsp;Flow copp-user-icmp<br/>
  &nbsp;&nbsp;  Action copp-user-icmp<br/>
  &nbsp;&nbsp;&nbsp;    trap-action: copy<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority: 3<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue: 11<br/>
  &nbsp;&nbsp;&nbsp;    police cir 1000 cbs 1000<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/>
  &nbsp;Flow copp-user-igmp<br/>
  &nbsp;&nbsp;  Action copp-user-igmp<br/>
  &nbsp;&nbsp;&nbsp;    trap-action trap<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority 4<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue 4<br/>
  &nbsp;&nbsp;&nbsp;    police cir 6000 cbs 6000<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop  <br/>
  &nbsp;Flow copp-user-ip2me<br/>
  &nbsp;&nbsp;  Action copp-user-imp2me<br/>
  &nbsp;&nbsp;&nbsp;    trap-action trap<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority 2<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue 2<br/>
  &nbsp;&nbsp;&nbsp;    police cir 6000 cbs 6000<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop  <br/>
  &nbsp;Flow copp-user-mtu<br/>
  &nbsp;&nbsp;  Action copp-user-mtu<br/>
  &nbsp;&nbsp;&nbsp;    trap-action trap<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority 1<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue 1<br/>
  &nbsp;&nbsp;&nbsp;    police cir 500 cbs 500<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/> 
  &nbsp;Flow copp-user-ospf<br/>
  &nbsp;&nbsp;  Action copp-user-ospf<br/>
  &nbsp;&nbsp;&nbsp;    trap-action copy<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority 5<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue 5<br/>
  &nbsp;&nbsp;&nbsp;    police cir 10000 cbs 10000<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/> 
  &nbsp;Flow copp-user-pim<br/>
  &nbsp;&nbsp;  Action copp-user-pim<br/>
  &nbsp;&nbsp;&nbsp;    trap-action trap<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority 4<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue 4<br/>
  &nbsp;&nbsp;&nbsp;    police cir 10000 cbs 10000<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop  <br/>
  &nbsp;Flow copp-user-ptp<br/>
  &nbsp;&nbsp;  Action copp-user-ptp<br/>
  &nbsp;&nbsp;&nbsp;    trap-action trap<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority 7<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue 23<br/>
  &nbsp;&nbsp;&nbsp;    police cir 10000 cbs 10000<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/> 
  &nbsp;Flow copp-user-sflow<br/>
  &nbsp;&nbsp;  Action copp-user-sflow<br/>
  &nbsp;&nbsp;&nbsp;    trap-action copy<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority 8<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue 8<br/>
  &nbsp;&nbsp;&nbsp;    police cir 16000 cbs 16000<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/> 
  &nbsp;Flow copp-user-pvrst<br/>
  &nbsp;&nbsp;  Action copp-user-pvrst<br/>
  &nbsp;&nbsp;&nbsp;    trap-action trap<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority 6<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue 6<br/>
  &nbsp;&nbsp;&nbsp;    police cir 16000 cbs 16000<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/> 
  &nbsp;Flow copp-user-subnet<br/>
  &nbsp;&nbsp;  Action copp-user-subnet<br/>
  &nbsp;&nbsp;&nbsp;    trap-action trap<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority 10<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue 10<br/>
  &nbsp;&nbsp;&nbsp;    police cir 6000 cbs 6000<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/> 
  &nbsp;Flow copp-user-suppress<br/>
  &nbsp;&nbsp;  Action copp-user-suppress<br/>
  &nbsp;&nbsp;&nbsp;    trap-action trap<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority 5<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue 9<br/>
  &nbsp;&nbsp;&nbsp;    police cir 5000 cbs 5000<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/> 
  &nbsp;Flow copp-user-iccp<br/>
  &nbsp;&nbsp;  Action copp-user-iccp<br/>
  &nbsp;&nbsp;&nbsp;    trap-action trap<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority 5<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue 5<br/>
  &nbsp;&nbsp;&nbsp;    police cir 5000 cbs 5000<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/>
  &nbsp;Flow copp-user-lacp<br/>
  &nbsp;&nbsp;  Action copp-user-lacp<br/>
  &nbsp;&nbsp;&nbsp;    trap-action trap<br/>
  &nbsp;&nbsp;&nbsp;    trap-priority 25<br/>
  &nbsp;&nbsp;&nbsp;    trap-queue 25<br/>
  &nbsp;&nbsp;&nbsp;    police cir 1000 cbs 1000<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      meter-type: packets<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      mode: sr_tcm<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;      red-action: drop <br/>

##### 3.2.8 Verify control traffic not matching all the applied matching protocols is classified as per copp-system-default class

| **Test ID**    | **Copp328**                                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify control traffic not matching all the applied matching protocols is classified as per copp-system-default class** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | STEP 1: Configure few user defined CoPP class. <BR/>STEP 2: Apply the user defined class to CoPP policy after making sure this class match protocol does not exist in the applied policy. <BR/>STEP 3: Now send some control traffic which does not match any of the system defied copp class and user defined classes.<br/> STEP 4: Verify the control traffic gets classified as per default copp class trap queue and CIR and CBS value. |

##### 3.2.9 Verify copp functionality for all default copp system classes applied to the copp system policy

| **Test ID**    | **Copp329**                                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify copp functionality for all default copp system classes applied to the copp system policy** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | 1.Make sure all the default copp classes with their copp action groups present in the box.  2.Make sure the default binding present for all the classes with corresponding copp action group in copp system policy.  3. Verify the copp system policy will show only one class and action group applied. Intention here is to validate some of the protocols in the default copp system classifiers. NOTE: Verifying all the control protocols with traffic is not in scope. We will validate most of the control protocols with traffic but not all. |

### 3.3 Negative

#### 3.3.1 Negative Test Cases

##### 3.3.1 Verify default CoPP policy "copp-system-policy" can not be deleted

| **Test ID**    | **Copp331**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify default CoPP policy "copp-system-policy" can not be deleted** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify default CoPP policy "copp-system-policy" can not be deleted<br/> |

##### 3.3.2 Verify one protocol can be bound to CoPP policy

| **Test ID**    | **Copp332**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify one protocol can be bound to CoPP policy**          |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify only one protocol can be bound to CoPP policy <br/>Same protocols can be added to multiple classifiers.<br/>When both classifiers are applied to the system copp policy at the same time. <br/>Than, this will be user configuration error. <br/>System will not actively prevent this config. |

##### 3.3.3 Verify default classifier is not removable from CoPP policy

| **Test ID**    | **Copp333**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify default classifier is not removable from CoPP policy** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify default classifier is not removable from CoPP policy<br/>But user defined copp classifiers can be removed from policy.<br/>copp-system-* and user defined classifiers can be removed from COPP Policy.  Only specifically the "copp-system-default" classifier cannot be removed. |

##### 3.3.4 Verify user cannot create new policies of type 'copp'

| **Test ID**    | **Copp334**                                                |
| -------------- | ---------------------------------------------------------- |
| **Test Name**  | **Verify user cannot create new policies of type 'copp'**  |
| **Test Setup** | **Topology 1**                                             |
| **Type**       | **Functional**                                             |
| **Steps**      | Verify user cannot create new policies of type 'copp'<br/> |

##### 3.3.5 Verify CoPP across CoppMgrd process restart 

| **Test ID**    | **Copp335**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP across CoppMgrd process restart**              |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify CoPP configuration and traffic rate limiting behavior etc. across CoppMgrd process restart<br/> |

##### ~~3.3.6 Verify application of copp class with conflicting match protocols will throw valid error (not supported for now)~~ 

| **Test ID**    | **Copp336**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **~~Verify application of copp class with conflicting match protocols will throw valid error(not supported for now)~~** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | ~~Verify application of copp class to system copp policy with conflicting match protocols will throw valid error. <br/>User can create copp classifier with duplicate match protocol (meaning the same match protocol which is already in applied default system copp policy). While applying the classifier to default system policy, it will throw error due to usage of duplicate match protocol.<br>If user remove the class from system copp policy having conflicting match protocol, new user defined class with same match protocol will be allowed.~~ |

##### 3.3.7 Verify CoPP across SWSS docker restart

| **Test ID**    | **Copp337**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP across SWSS docker restart**                   |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify CoPP configuration and traffic rate limiting behavior etc. across swss docker restart. |

### 3.4 Reboot/Reload/Upgrade Test Cases

##### 3.4.1 Verify CoPP configuration across warm reboot

| **Test ID**    | **Copp341**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP configuration across warm reboot**             |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify CoPP configuration across warm reboot.<br/>1. Verify that CoPP config is restored after warmboot.<br/>2. Verify that traffic to CPU continues to get rate-limited as per configured CoPP policy after warmboot<br/>2. Verify that CoPP policy is active after warmboot on the protocols that had classifers before warmboot |

##### 3.4.2 Verify CoPP configuration across fast reboot

| **Test ID**    | **Copp342**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP configuration across fast reboot**             |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify CoPP configuration across fast reboot.<br/>1. Verify that CoPP config is restored after fast reload.<br/>2. Verify that traffic to CPU continues to get rate-limited as per configured CoPP policy after fast reload<br/>2. Verify that CoPP policy is active after warmboot on the protocols that had classifers before fast reload. |

##### 3.4.3 Verify CoPP configuration across system reload

| **Test ID**    | **Copp343**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP configuration across system reload**           |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify CoPP configuration across system reload.<br/>1. Verify that CoPP config is restored after system reload.<br/>2. Verify that traffic to CPU continues to get rate-limited as per configured CoPP policy after system reload<br/>2. Verify that CoPP policy is active after warmboot on the protocols that had classifers before system reload. |

##### 3.4.4 Verify CoPP config and functionality across firmware upgrade and downgrade 

| **Test ID**    | **Copp343**                                                  |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP config and functionality across firmware upgrade and downgrade** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify CoPP config and functionality across CoPP supported firmware upgrade and downgrade. <BR/>1. Verify that CoPP config is restored after firmware upgrade or downgrade. <BR/>2. Verify that traffic to CPU continues to get rate-limited as per configured CoPP policy after system reload.<BR/> 3. Verify that CoPP policy is active after firmware upgrade or downgrade.<br/>NOTE: We will verify this across CoPP supported firmware only. In case we upgrade from CoPP non supported build to CoPP supported build, user should understand the default CoPP setting will differ. In case we downgrade from CoPP supported build to CoPP not supported build, user need to make sure to remove all user defined CoPP config prior to downgrade. |

### 3.5 Stress,Scale and Performance</span>

#### 3.5.1 Scale test case

##### 3.5.1.1 Verify CoPP configuration with max number of CoPP classifiers configured

| **Test ID**    | **Copp3511**                                                 |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP configuration with max number of CoPP classifiers configured** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify CoPP configuration with max number or 64 no of of CoPP classifiers configured |

##### 3.5.1.2 Verify CoPP classifier configured with max no of CPU queues configured

| **Test ID**    | **Copp3512**                                                 |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP classifier configured with max no of CPU queue configured** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify CoPP configuration with max number of CPU queues configured or 20 queues. |

#### 3.5.2 Stress test case

##### 3.5.2.1 Verify CoPP policy classifier behavior when box is under stress 

| **Test ID**    | **Copp3521**                                                 |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP policy classifier behavior when box is under stress** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify CoPP policy classifier behavior when box is under the stress.<br/>We need to configure BFD session aggressive timers or multiple ACL log actions hitting data traffic on the same port under test to stress the box while testing CoPP policy.<br/>Need to check with dev team to understand the if any other step required to stress the box while testing applied non default CoPP policy.<BR/>High rate ARP traffic has been known to cause this problems. We can try this step. |

#### 3.5.3 Performance test case

##### 3.5.3.1 Verify CoPP policy classifier performance test when control and data traffic ingress with 100% line rate

| **Test ID**    | **Copp3531**                                                 |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP policy classifier performance test when control and data traffic ingress with 100% line rate** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify CoPP policy classifier performance test by running CoPP related control along with data traffic at line on the same port.<br/>Verify that CoPP Policy is working fine for each control protocol even if port is congested with control and data traffic is running at 100% of line rate.<BR/>Other than 100% line rate of control and data traffic, we need to change the rate limit value per individual protocol with 5% tolereance and mesaure 3 samples for each control protocol to record the acuracy of rate limiting values configured. |

### 3.6 Management

#### 3.6.1 SNMP test case
#### 3.6.2 gNMI test Case
#### 3.6.3 Verify CoPP specific OCYANG test case

#### 3.6.3.1 Verify CoPP policy classifier config GET, SET, POST, PUSH, DELETE operation using ocyang or sonicyang command which ever applicable

| **Test ID**    | **Copp3631**                                                 |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify CoPP policy classifier config GET, SET, POST, PUSH, DELETE operation using oc-yang or sonic-yang command which over applicable** |
| **Test Setup** | **Topology 1**                                               |
| **Type**       | **Functional**                                               |
| **Steps**      | Verify CoPP policy classifier config GET, SET, POST, PUSH, DELETE operation using ocyang or sonicyang command.<br/>The CoPP Mgmt HLD for B+ is not yet updated with ocyang or sonicyang command support. <br/>This test case will be updated as when HLD is updated. |

## Reference Links

- [SONiC CoPP HLD link] http://gerrit-lvn-07.lvn.broadcom.net:8083/c/sonic/documents/+/20793/9/copp/SONiC_CoPP.md#237
- [SONIC CoPP Support Matrix] https://docs.google.com/spreadsheets/d/1oj5X-jgHw3myZXCtltXq1IhukRA6RwaWu5tGdTD0LmY/edit#gid=1989870311
