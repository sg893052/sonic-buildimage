

#  SQA Test Plan
# ACL Rate limiting and ACL Enhancements
#  SONiC 3.0 Project and Buzznik Release
[TOC]
# List of Reviewers

| Function |        Name         |
| :------: | :-----------------: |
|    QA    |   Anil Kolkaleti    |
|    QA    |   Giri Babu Sajja   |
|   Dev    | Abhishek Dharwadkar |
|   Dev    |     Amin Alavi      |
|   Dev    |                     |

# List of Approvers


| Function |      Name       | Approved on |
| :------: | :-------------: | ----------- |
|   Dev    |   Amin Alavi    |             |
|    QA    | Giri Babu Sajja |             |



# Revision
| Rev  | Date       | Author                | Change Description     |
| ---- | ---------- | --------------------- | ---------------------- |
| 0.1  | 05/11/2019 | Mahanti Nageswara Rao | Initial version        |
| 0.2  | 13/12/2019 | Mahanti Nageswara Rao | ACL enhancements added |

# Definition/Abbreviation

| **Term** | **Meaning** |
| -------- | --------------------------------- |
|ACL    | Access Control List |
|PBR | Policy Based Routing |
|SPAN | Switch Port ANalyzer |
|sFlow | Sampled flow |
|MQC | Modular QoS CLIs |
|DSCP | Differentiated Services Code Point |
|PCP | Port control protocol |

# 
# Feature Overview
 ACL rate limiting
 ACL rate limiting is added in Buzznik as part of ACL enhancements and Flow Based Services. A policy provides information on the action that needs to be applied to the flows identified by the classifiers.  A policy consists of multiple sections or stanzas, where each section consists of a classifier and its corresponding actions. Each section has a priority associated with it which will indicate  the order in which the ACL will be programmed . Policies can be of different types based on the kind of actions supported. Only policies of type QoS are supported in SONiC 3.0. QoS Policies only support the following actions

1. DSCP Remarking : Set DSCP in IPv4 or IPv6 header to the new value specified in the configuration matches.

2. PCP Remarking : Set PCP value in 802.1Q VLAN header to the new value specified in the configuration.

3. Policing : Apply rate-limiting parameters.

   When a policy is applied to an interface at ingress or egress, action will be taken in case of match, only traffic is ingressing or egressing from that interface. Traffic ingressing or egressing from other interfaces will not be affected. A policy can be applied in the Ingress and Egress direction.

   A policy can be applied to the Port, LAG,VLAN ( Traffic tagged with or classified into the VLAN will be affected if matched) or Switch (All traffic flowing through the switch will be affect if matched).

   3.1 Policy of type Monitoring

   Monitoring policies only support the following actions

   1. SPAN/ERSPAN session using session name
   
   Note: The following are ASIC limitations that must be noted when configuring   the policies.  
   
   1. Only the following combination of ACLs are supported in  a policy due to key width limitations        1. L2 + IPv4        2. IPv4 + IPv6
   
   2. All applied policies of the same type must have the same ACL key combinations across all interfaces. For example, it's not valid to apply QoS Policy P1 on Ethernet0 which uses L2 ACL1 and IPv4 ACL2 and QoS Policy P2 on Ethernet4 which uses IPv4 ACL3 and IPv6 ACL4.
   

ACL Enhancements:
ACL enhancements tests are covered separately in session 3.8 in the document.
ACL enhancements for table of type l3 and l3v6
DSCP, ICMP(v6) Type and ICMP(v6) Code keys are enabled on tables of type l3(v6) along with mirror table.

ACL enhancements for table of type mirror
ACL tables of type mirror will support additional match criteria for VLAN and PCP along with existing L3 and L4 header keys.

L2 ACLs
Layer 2 ACL can be used to filter traffic based on MAC header fields like Source MAC, Destination MAC, VLAN etc. Layer 2 ACLs can be used to filter traffic of any ether type including IPv4 and IPv6. Layer 2 ACLs can be applied to all interfaces i.e Ethernet, Port Channel, VLAN or Switch.

VLAN ACLs
VLAN ACLs provide traffic filtering for all traffic that is bridged within the same VLAN or routed in or out of a VLAN. VLAN ACLs are applied to both bridged and routed traffic. VLAN ACLs supports MAC, IPv4 or IPv6 ACLs.

Switch ACLs
Switch ACLs provide traffic filtering for all bridged or routed traffic in the switch. Switch ACLs supports MAC, IPv4 or IPv6 ACLs.

# Test Approach

######  What will be part of module config?

1. Configuring vlans, acls, portchannel, classifiers  in the module configuration.
2. Configuring of acls based on the requirement.
3. Configuring of portchannel in the topology.

###### What tests will be covered / clubbed together?


1. Functionality tests will be automated and clubbed into three functions in the test module.
2. Functional interaction tests will be automated and clubbed into two functions in the test module.
###### What 3rd party tools to be tested for?

​	None

###### In which area’s common warm-boot, cold boot , fast-boot file, corresponding test of the given feature gets covered?

Warm boot, Cold boot and fast boot will be covered as part of existing common 	files test_switching_fast_reboot.py, test_switching_fast_reboot.py and test_switching_save_reboot.py. respectively.

###### List of tests which can not be automated and why?

None

###### What is the plan for covering secondary UI (Ex:- gNMI / KLISH / REST) and how much?

IS-CLI(KLISH)/gNMI and REST apis are not supported on ACL rate-limit.

# 1 Test Focus Areas

## 1.1 CLI
N/A
CLI testing is covered as part of Automation infrastructure testing

## 1.2 Functional Testing 
  - ACL Rate-limiting support on interface,switch, vlan and port-channel.

## 1.3 Functional Interaction 
  - ACL rate-limiting with portchannel

    

## 1.4 Negative Testing

- traffic flow after removing policy

- Traffic flow after unbinding interface.

  

## 1.5 REST

N/A
REST tests are covered as part of Automation infrastrure testing.

## 1.6 gNMI
N/A
gNMI tests are covered as part of Automation infrastrure testing.
# 2 Topologies
## 2.1 Topology 1

<span style="color:red"> </span>![acl rate limiting](aclratelimiting.png "Figure:aclratelimiting ")




# 3 Test  Case and Objectives

## 3.1 CLI

**N/A
CLI testing is covered as part of Automation infrastructure testing.**

## 3.2 Functionality

#### 3.2.1 Verify that rate-limit with ingress ipv4 acl works fine when bound to switch, interface and vlan .


| **Test ID**    | **ft_acl_ipv4_in_rate_limit_intf_vlan**                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that rate-limit with ingress ipv4 acl works fine when bound to switch, interface and vlan .** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	1)  Create IPv4 ingress ACL.<br/>	2)  Create classifier and add acl using classifier update command.<br/>	3)  Create policy .<br/>	4)  Create flow using policy and classifier and set results as cir 10000<br/>          cbs 1000 pir 15000 pbs 1000)<br/>    5) Bind policy to  "Switch"<br/>    6) Send 20000 single burst traffic on one of the switch port.<br/>	7) Remove policy from switch<br/>    8) Bind policy to interface<br/>    9) Send full burst rate  traffic on interface.<br/>	10) Remove policy from interface<br/>	11) Add interface to the vlan<br/>	12) Bind policy to vlan<br/>    13) Send full burst rate  traffic on vlan interface.<br/>Expected results:<br/>	1) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to switch. Verify that green(confirmed), <br/>         yellow(exceed) and red(violated) packet/frame count is coming as <br/>         expected.<br/>	2) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to interface. Verify that green(confirmed), <br/>         yellow(exceed) and red(violated) packet/frame count is coming as <br/>         expected. <br/>	3) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to vlan. Verify that green(confirmed), <br/>         yellow(exceed) and red(violated) packet/frame count is coming as <br/>         expected.** |



#### 3.2.2 Verify that rate-limit with ingress ipv6 acl works fine when bound to switch, interface and vlan .


| **Test ID**    | **ft_acl_ipv6_in_rate_limit_intf_vlan**                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that rate-limit with ingress ipv6 acl works fine when bound to switch, interface and vlan .** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	1)  Create IPv6 ingress ACL.<br/>	2)  Create classifier and add acl using classifier update command.<br/>	3) Create policy.<br/>	4) Create flow using policy and classifier and set results as cir 10000<br/>          cbs 1000 pir 15000 pbs 1000)<br/>    5) Bind policy to  "Switch"<br/>    6) Send full burst rate  traffic on any of the switch interface.<br/>	7) Remove policy from switch<br/>    8) Bind policy to interface<br/>    9) Send full burst rate  traffic on interface.<br/>	10) Remove policy from interface<br/>	11) Add interface to the vlan<br/>	12) Bind policy to vlan<br/>    13) Send full burst rate  traffic on vlan interface.<br/>Expected results:<br/>	1) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to switch. Verify that green(confirmed), <br/>         yellow(exceed) and red(violated) packet/frame count is coming as <br/>         expected.<br/>	2) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to interface. Verify that green(confirmed), <br/>         yellow(exceed) and red(violated) packet/frame count is coming as <br/>         expected. <br/>	3) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to vlan. Verify that green(confirmed), <br/>         yellow(exceed) and red(violated) packet/frame count is coming as <br/>         expected.** |
#### 3.2.3 Verify that rate-limit with ingress l2 acl works fine when bound to switch, interface and vlan .



| **Test ID**    | **ft_acl_l2_in_rate_limit_intf_vlan**                        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that rate-limit with ingress ipv6 acl works fine when bound to switch, interface and vlan .** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	1)  Create mac ingress ACL.<br/>	2)  Create classifier and add acl using classifier update command.<br/>	3) Create policy.<br/>	4) Create flow using policy and classifier and set results as cir 10000<br/>          cbs 1000 pir 15000 pbs 1000)<br/>    5) Bind policy to  "Switch"<br/>    6) Send full burst rate  traffic on any of the switch interface.<br/>	7) Remove policy from switch<br/>    8) Bind policy to interface<br/>    9) Send full burst rate  traffic on interface.<br/>	10) Remove policy from interface<br/>	11) Add interface to the vlan<br/>	12) Bind policy to vlan<br/>    13) Send full burst rate  traffic on vlan interface.<br/>Expected results:<br/>	1) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to switch. Verify that green(confirmed), <br/>         yellow(exceed) and red(violated) packet/frame count is coming as <br/>         expected.<br/>	2) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to interface. Verify that green(confirmed), <br/>         yellow(exceed) and red(violated) packet/frame count is coming as <br/>         expected. <br/>	3) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to vlan. Verify that green(confirmed), <br/>         yellow(exceed) and red(violated) packet/frame count is coming as <br/>         expected.** |

#### 3.2.4 Verify that rate-limit with ipv4 egress acl works fine when bound to switch interface and vlan .


| **Test ID**    | **ft_acl_ipv4_eg_rate_limit_intf_vlan**                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that rate-limit with ipv4 egress acl works fine when bound to switch, interface and vlan .** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	1)  Create IPv4 ingress ACL.<br/>	2)  Create classifier and add acl using classifier update command.<br/>	3) Create policy.<br/>	4) Create flow using policy and classifier and set results as cir 10000<br/>          cbs 1000 )<br/>5) Bind policy to  "Switch"<br/>    6) Send full burst rate  traffic on any of the switch interface.<br/>	7) Remove policy from switch<br/>    8) Bind policy to interface<br/>    9) Send full burst rate  traffic on interface.<br/>	10) Remove policy from interface<br/>	11) Add interface to the vlan<br/>	12) Bind policy to vlan<br/>    13) Send full burst rate  traffic on vlan interface.<br/>Expected results:<br/>	1) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to switch. Verify that green(confirmed), <br/>          and red(violated) packet/frame count is coming as expected.<br/>	2) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to interface. Verify that green(confirmed), <br/>         and red(violated) packet/frame count is coming as expected. <br/>	3) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to vlan. Verify that green(confirmed), <br/>         and red(violated) packet/frame count is coming as expected.** |


#### 3.2.5 Verify that rate-limit with ipv6 egress acl works fine when bound to switch, interface and vlan .


| **Test ID**    | **ft_acl_ipv6_eg_rate_limit_intf_vlan**                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that rate-limit with ipv6 egress acl works fine when bound to switch, interface and vlan .** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	1)  Create IPv6 egress ACL.<br/>	2)  Create classifier and add acl using classifier update command.<br/>	3) Create policy.<br/>	4) Create flow using policy and classifier and set results as cir 10000<br/>          cbs 1000 )<br/>5) Bind policy to  "Switch"<br/>    6) Send full burst rate  traffic on any of the switch interface.<br/>	7) Remove policy from switch<br/>    8) Bind policy to interface<br/>    9) Send full burst rate  traffic on interface.<br/>	10) Remove policy from interface<br/>	11) Add interface to the vlan<br/>	12) Bind policy to vlan<br/>    13) Send full burst rate  traffic on vlan interface.<br/>Expected results:<br/>	1) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to switch. Verify that green(confirmed), <br/>          and red(violated) packet/frame count is coming as expected.<br/>	2) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to interface. Verify that green(confirmed), <br/>         and red(violated) packet/frame count is coming as expected. <br/>	3) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to vlan. Verify that green(confirmed), <br/>         and red(violated) packet/frame count is coming as expected.** |

#### 3.2.6 Verify that rate-limit with l2 egress acl works fine when bound to switch, interface and vlan .



| **Test ID**    | **ft_acl_l2_eg_rate_limit_intf_vlan**                        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that rate-limit with l2 egress acl works fine when bound to switch, interface and vlan .** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	1)  Create l2 egress ACL.<br/>	2)  Create classifier and add acl using classifier update command.<br/>	3) Create policy.<br/>	4) Create flow using policy and classifier and set results as cir 10000<br/>          cbs 1000 )<br/>     5) Bind policy to  "Switch"<br/>    6) Send burst rate  traffic on any of the switch interface.<br/>	7) Remove policy from switch<br/>    8) Bind policy to interface<br/>    9) Send full burst rate  traffic on interface.<br/>	10) Remove policy from interface<br/>	11) Add interface to the vlan<br/>	12) Bind policy to vlan<br/>    13) Send full burst rate  traffic on vlan interface.<br/>Expected results:<br/>	1) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to switch. Verify that green(confirmed), <br/>          and red(violated) packet/frame count is coming as expected.<br/>	2) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to interface. Verify that green(confirmed), <br/>         and red(violated) packet/frame count is coming as expected. <br/>	3) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to vlan. Verify that green(confirmed), <br/>         and red(violated) packet/frame count is coming as expected.** |

#### 3.2.7 Verify that different flows can be configured on a single policy. 



| **Test ID**    | **ft_acl_rate_limit_singlepolicy_multiflow_config**          |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that different flows can be configured on a single policy.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	1)  Create l2, IPv4 and IPv6 ACLs.<br/>	2)  Create  three different classifiers as class1, class2 and class3 and add  l2 acl to    class1, IPv4 acl to class2 and IPv6 acl to class3.<br/>     3) Create two policies as policy0 and policy1 .<br/>	4) create two flows using policy0 and classifiers class0 and class1 and set <br/>         results<br/>    5)create 2 more flows using policy1 and classifiers class1 and class2 and set <br/>         results<br/>    6) Bind policy0 to different interfaces<br/>	6) Send traffic on interfaces to check on rate limit policy .<br/>    7)  Unbind interfaces from policy0<br/>     8)  Bind policy1 to different interfaces<br/>     9) Send traffic on interfaces to check on rate limit policy .<br/>Expected results:<br/>	1) Verify successfully created rate-limit policy is bind to interfaces.<br/>	2) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to interface. Verify that green(confirmed), <br/>         yellow(exceed) and red(violated) packet/frame count is coming as <br/>         expected.<br/>       ** |



#### ## 

## 3.3 Functional Interaction

#### 3.3.1 Verify that rate-limit with ipv4 egress acl works fine when bound to port-channel .

| **Test ID**    | **ft_acl_ipv4_eg_rate_limit_port_channel**                   |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that rate-limit with ipv4 egress acl works fine when bound to port-channel .** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional interaction**                                   |
| **Steps**      | **Procedure<br/>	1)  Create ipv4 egress ACL.<br/>	2)  Create classifier and add acl using classifier update command.<br/>	3) Create flow using policy and classifier and set results as cir 10000<br/>          cbs 1000 )<br/>	4) create flow using classifier and set results<br/>	5) Bind policy to port-channel<br/>	6) Send full burst rate  traffic on port-channel.<br/>Expected results:<br/>	1) Verify successfully created rate-limit policy is bind to portchannel.<br/>	2) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to interface. Verify that green(confirmed), <br/>         and red(violated) packet/frame count is coming as expected.<br/>	** |

#### 3.3.2 Verify that rate-limit with ipv6 egress acl works fine when bound to port-channel .

| **Test ID**    | **ft_acl_ipv6_eg_rate_limit_port_channel**                   |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that rate-limit with ipv6 egress acl works fine when bound to port-channel .** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional interaction**                                   |
| **Steps**      | **Procedure<br/>	1)  Create ipv6 egress ACL.<br/>	2)  Create classifier and add acl using classifier update command.<br/>	3) Create flow using policy and classifier and set results as cir 10000<br/>          cbs 1000 )<br/>	4) create flow using classifier and set results<br/>	5) Bind policy to port-channel<br/>	6) Send full burst rate  traffic on port-channel.<br/>Expected results:<br/>	1) Verify successfully created rate-limit policy is bind to portchannel.<br/>	2) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to interface. Verify that green(confirmed), <br/>         and red(violated) packet/frame count is coming as expected. <br/>	** |

#### 3.3.3 Verify that rate-limit with l2 egress acl works fine when bound to port-channel .



| **Test ID**    | **ft_acl_l2_eg_rate_limit_port_channel**                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that rate-limit with l2 egress acl works fine when bound to port-channel .** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional interaction**                                   |
| **Steps**      | **Procedure<br/>	1)  Create l2 egress ACL.<br/>	2)  Create classifier and add acl using classifier update command.<br/>	3) Create flow using policy and classifier and set results as cir 10000<br/>          cbs 1000 )<br/>	4) create flow using classifier and set results<br/>	5) Bind policy to port-channel<br/>	6) Send full burst rate  traffic on port-channel.<br/>Expected results:<br/>	1) Verify successfully created rate-limit policy is bind to portchannel.<br/>	2) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to interface. Verify that green(confirmed), <br/>         and red(violated) packet/frame count is coming as expected. <br/>	** |

#### 3.3.4 Verify that rate-limit with ipv4 acl works fine when bound to port-channel .

| **Test ID**    | **ft_acl_ipv4_in_rate_limit_port_channel**                   |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that rate-limit with ipv4 acl works fine when bound to port-channel .** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional interaction**                                   |
| **Steps**      | **Procedure<br/>	1)  Create ipv4 ingress ACL.<br/>	2)  Create classifier and add acl using classifier update command.<br/>	3) Create flow using policy and classifier and set results as cir 10000<br/>          cbs 1000 pir 15000 pbs 1000)<br/>	4) create flow using classifier and set results<br/>	5) Bind policy to port-channel<br/>	6) Send full burst rate  traffic on port-channel.<br/>Expected results:<br/>	1) Verify successfully created rate-limit policy is bind to portchannel.<br/>	2) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to interface. Verify that green(confirmed), <br/>         yellow(exceed) and red(violated) packet/frame count is coming as <br/>         expected.** |

#### 3.3.5 Verify that rate-limit with ipv6 acl works fine when bound to port-channel .



| **Test ID**    | **ft_acl_ipv6_in_rate_limit_port_channel**                   |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that rate-limit with ipv6 acl works fine when bound to port-channel .** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	1)  Create ipv6 ingress ACL.<br/>	2)  Create classifier and add acl using classifier update command.<br/>	3) Create flow using policy and classifier and set results as cir 10000<br/>          cbs 1000 pir 15000 pbs 1000)<br/>	4) create flow using classifier and set results<br/>	5) Bind policy to port-channel<br/>	6) Send full burst rate  traffic on port-channel.<br/>Expected results:<br/>	1) Verify successfully created rate-limit policy is bind to portchannel.<br/>	2) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to interface. Verify that green(confirmed), <br/>         yellow(exceed) and red(violated) packet/frame count is coming as <br/>         expected.** |

#### 3.3.6 Verify that rate-limit with l2 acl works fine when bound to port-channel .



| **Test ID**    | **ft_acl_l2_in_rate_limit_port_channel**                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that rate-limit with ipv4 acl works fine when bound to port-channel .** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional interaction**                                   |
| **Steps**      | **Procedure<br/>	1)  Create l2 ingress ACL.<br/>	2)  Create classifier and add acl using classifier update command.<br/>	3)  Create flow using policy and classifier and set results as cir 10000<br/>          cbs 1000 pir 15000 pbs 1000)<br/>	4) create flow using classifier and set results<br/>	5) Bind policy to port-channel<br/>	6) Send full burst rate  traffic on port-channel.<br/>Expected results:<br/>	1) Verify successfully created rate-limit policy is bind to portchannel.<br/>	2) Verify  using acl hit counters that traffic  is sent, as per the rate-limit<br/>         policy when policy is bind to interface. Verify that green(confirmed), <br/>         yellow(exceed) and red(violated) packet/frame count is coming as <br/>         expected.** |

#### 

## 3.4 Negative testing

#### 3.4.1 Verify that traffic goes normal when a policy is removed from interface .



| **Test ID**    | **ft_acl_rate_limit_remove_policy**                          |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that traffic goes normal when a policy is removed from interface .** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Negative testing**                                         |
| **Steps*       | **Procedure<br/>	1)  Create an ACL.<br/>	2)  Create classifier and add acl using classifier update command.<br/>	3) Create policy.<br/>	4) create flow using classifier and set results<br/>	5) Bind policy to interface<br/>	6) Send full burst rate  traffic on interface.<br/>    7) delete policy<br/>    8) Send burst rate traffic again<br/>Expected results:<br/>	1) Verify successfully created rate-limit policy is bind to interface.<br/>	2) Verify that traffic is sent as per the rate-limit policy. <br/>	3) Verify that traffic rate limit is removed and goes at received/sent rate** |

#### 3.4.2 Verify that traffic is goes normal when an interface is unbound from policy during the traffic.



| **Test ID**    | **ft_acl_rate_limit_unbind_interface**                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that traffic is goes normal when an interface is unbound from policy during the traffic.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Negative testing**                                         |
| **Steps**      | **Procedure<br/>	1)  Create an ACL.<br/>	2)  Create classifier and add acl using classifier update command.<br/>	3) Create policy.<br/>	4) create flow using classifier and set results<br/>	5) Bind policy to Switch<br/>	6) Send full burst rate  traffic on interface.<br/>    7) Unbound the policy from switch and bind it to one of the interface<br/>    8) Send the traffic from policy configured interface<br/>    9) Unbound interface from the policy<br/>Expected results:<br/>	1) Verify successfully created rate-limit policy is bound to interface.<br/>	2) Verify that traffic is sent as per the rate-limit policy bounded to switch. <br/>    3) Verify that traffic is sent as per the rate-limit policy bounded to interface. <br/>	4) Verify that traffic rate limit is removed and goes at received/sent rate** |

#### 3.4.3 Verify that traffic is goes as per the rate limit after port shutdown/no shutdown.



| **Test ID**    | **ft_acl_rate_limit_shut_noshut_interface**                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that traffic is goes as per the rate limit after port shutdown/no shutdown.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Negative testing**                                         |
| **Steps**      | **Procedure<br/>	1)  Create an ACL.<br/>	2)  Create classifier and add acl using classifier update command.<br/>	3) Create policy.<br/>	4) create flow using classifier and set results<br/>	5) Bind policy to interface<br/>	6) Send continuous full burst rate  traffic on interface.<br/>    7) Shutdown and startup interface while traffic is going.<br/>Expected results:<br/>	1) Verify successfully created rate-limit policy is bind to interface.<br/>	2) Verify that traffic is sent as per the rate-limit policy. <br/>	3) Verify that traffic is sent as per the rate-limit policy after port comes back.** |

#### 3.4.4 Verify that rate limit doesn't take effect when policy with non existing classifier and when not existing policy applied to interface.



| **Test ID**    | **ft_acl_rate_limit_nonexist_policy**                        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that rate limit doesn't take effect when policy with non existing classifier and when not existing policy applied to interface.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Negative testing**                                         |
| **Steps**      | **Procedure<br/>	1)  Create an ACL.<br/>	2)  Create policy with not existing classifier.<br/>	4) create flow using policy and set results<br/>	5) Bind policy to interface<br/>	6) Send continuous full burst rate  traffic on interface.<br/>    7) Bind a non existing policy to interface<br/>Expected results:<br/>	1) Verify rate limit policy doesn't apply and traffic sent at received rate. <br/>	2) Verify rate limit policy doesn't apply and traffic sent at received rate. <br/>	** |



#### 

## 3.5 Scalability

#### 3.5.1 Verify that maximum number of flows created for a policy.

| **Test ID**    | **ft_acl_rate_limit_policy_scalability**                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that maximum no. of flows created for a policy**    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Scalability**                                              |
| **Steps**      | **Procedure<br/>	1)  Create different ACLs, classifiers and one policy <br/>    2)  Create max number of sections per policy flow<br/>Expected results:<br/>	1) Verify that maximum number of sections(flows) can be created<br/>         using policy and once it reaches the maximum limit, it should send<br/>        a message saying maximum entry limit reached. <br/>** |



## 3.6 Warm boot

​		N/A 

​		Warm boot testing will be covered as part of existing common file test_switching_warm_reboot.py,.

#### 

## 3.6 Fast boot

​		N/A

​    	Cold boot testing will be covered as part of existing common file  test_switching_fast_reboot.py

#### 

## 3.6 Cold boot

​	N/A; 

​	Fast boot testing will be covered as part of existing common file  test_switching_save_reboot.py.

#### 

## 3.6 REST

N/A
Flow based services does not support any REST APIs.

## 3.7 gNMI

## 3.8 ACL Enhancements

#### 3.8.1 Verify that ipv4 ingress acl works fine when bound to vlan.



| **Test ID**    | **ft_acl_v4_in_vlan**                                        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ipv4 ingress acl works fine when bound to vlan.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	1)  Create ipv4 ingress ACL table.<br/>	2)config different acl rules with src_ip,dst_ip,port_range,l4_src_port,<br/>       l4_dst_port,ip_protocol <br/>    3) Bind acl to vlan <br/>    4)Send matched traffic <br/> Expected results:<br/>	1) Verify ipv4 ingress acl table created successfully  <br/>    2)  Verify that acl rules created successfully as per configured 	<br/>   3)Verify that acl successfully bind to vlan <br/>   4) Verify using acl hit counters as per configured rule <br/>** |

#### 3.8.2 Verify that ipv4 ingress acl works fine when bound to switch.



| **Test ID**    | **ft_acl_v4_in_switch**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ipv4 ingress acl works fine when bound to switch.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	1)  Create ipv4 ingress ACL table.<br/>	2)config different acl rules with src_ip, dst_ip,port_range,l4_src_port,<br/>        l4_dst_port,ip_protocol <br/>    3) Bind acl to switch <br/>     4)Send matched traffic <br/>  Expected results:<br/>	1) Verify ipv4 ingress acl table created successfully  <br/>    2)  Verify that acl rules created successfully as per configured 	<br/>    3)Verify that acl successfully bind to switch <br/>    4) Verify using acl hit counters as per configured rule <br/>** |

#### 3.8.3 Verify that ipv6 egress acl works fine when bound to switch.



| **Test ID**    | **ft_acl_v6_eg_switch**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ipv6 egress acl works fine when bound to switch.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	1)  Create ipv6 egress ACL table.<br/>	2)config different acl rules with src_ip,dst_ip,port_range,l4_src_port,l4_<br/>       _port,ip_protocol,icmp <br/>     3) Bind acl to switch <br/>     4)Send matched traffic <br/>  Expected results:<br/>	1) Verify ipv6 egress acl table created successfully  <br/>     2)  Verify that acl rules created successfully as per configured 	<br/>     3)Verify that acl successfully bind to switch <br/>      4) Verify using acl hit counters as per configured rule <br/>** |

#### 3.8.4 Verify that ipv6 egress acl works fine when bound to vlan.



| **Test ID**    | **ft_acl_v6_eg_vlan**                        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ipv6 egress acl works fine when bound to vlan.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	1)  Create ipv6 egress ACL table.<br/>	2)config different acl rules with src_ip,dst_ip,port_range,l4_src_port,l4_dst_port,ip_protocol,icmp <br/> 3) Bind acl to vlan <br/> 4)Send matched traffic <br/> Expected results:<br/>	1) Verify ipv6 egress acl table created successfully  <br/> 2)  Verify that acl rules created successfully as per configured 	<br/> 3)Verify that acl successfully bind to vlan <br/>  4) Verify using acl hit counters as per configured rule <br/>** |

#### 3.8.5 Verify that ipv6 egress acl works fine when bound to interface.



| **Test ID**    | **ft_acl_v6_eg_intf**                                        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ipv6 egress acl works fine when bound to interface.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	1)  Create ipv6 egress ACL table.<br/>	2)config different acl rules with src_ip,dst_ip,port_range,l4_src_port,<br/>      l4_dst_port,ip_protocol,icmp <br/>    3) Bind acl to interface <br/>    4)Send matched traffic <br/>  Expected results:<br/>	1) Verify ipv6 egress acl table created successfully  <br/>   2)  Verify that acl rules created successfully as per configured 	<br/>   3)Verify that acl successfully bind to interface <br/>  4) Verify using acl hit counters as per configured rule <br/>** |

#### 3.8.6 Verify that ipv4 ingress acl works fine when bound to interface.



| **Test ID**    | **ft_acl_v4_in_intf**                                        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ipv4 ingress acl works fine when bound to interface.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	1)  Create ipv4 ingress ACL table.<br/>	2)config different acl rules with src_ip,dst_ip,port_range,l4_src_port,<br/>       l4_dst_port,ip_protocol,dscp <br/>   3) Bind acl to interface <br/>   4)Send matched traffic <br/>  Expected results:<br/>	1) Verify ipv4 ingress acl table created successfully  <br/>    2)  Verify that acl rules created successfully as per configured 	<br/>    3)Verify that acl successfully bind to interface <br/>    4)Verify using acl hit counters as per configured rule<br/>** |

#### 3.8.7 Verify that L2 ingress acl works fine when bound to interface.



| **Test ID**    | **ft_acl_l2_in_intf**                                        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that l2 ingress acl works fine when bound to interface.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	  1)  Create l2 ingress ACL table.<br/>	  2)config different acl rules with src_mac,dst_mac,ether_type <br/>      3) Bind acl to interface <br/>     4)Send matched traffic <br/>  Expected results:<br/>	1) Verify l2 ingress acl table created successfully  <br/>    2)  Verify that acl rules created successfully as per configured 	<br/>    3)Verify that acl successfully bind to interface <br/>    4) Verify using acl hit counters as per configured rule <br/>** |

#### 3.8.8 Verify that L2 egress acl works fine when bound to vlan.



| **Test ID**    | **ft_acl_l2_eg_vlan**                                        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that l2 egress acl works fine when bound to vlan.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	   1)  Create l2 ingress ACL table.<br/>	   2)config different acl rules with src_mac,dst_mac,ether_type <br/>       3) Bind acl to vlan <br/>       4)Send matched traffic <br/>  Expected results:<br/>	  1) Verify l2 egress acl table created successfully  <br/>      2)  Verify that acl rules created successfully as per configured 	<br/>      3)Verify that acl successfully bind to vlan <br/>     4) Verify using acl hit counters as per configured rule <br/>** |


#### 3.8.9 Verify that L2 ingress acl works fine when bound to portchannel.



| **Test ID**    | **ft_acl_l2_in_lag**                                         |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that l2 ingress acl works fine when bound to portchannel.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	   1)  Create l2 ingress ACL table.<br/>	   2)config different acl rules with src_mac,dst_mac,ether_type <br/>       3) Bind acl to portchannel <br/>       4)Send matched traffic <br/>  Expected results:<br/>	  1) Verify l2 ingress acl table created successfully  <br/>      2)  Verify that acl rules created successfully as per configured 	<br/>      3)Verify that acl successfully bind to portchannel <br/>      4) Verify using acl hit counters as per configured rule<br/>** |

#### 3.8.10 Verify that L2 egress acl works fine when bound to switch.



| **Test ID**    | **ft_acl_l2_eg_switch**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that l2 egress acl works fine when bound to switch.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br/>	    1)  Create l2 egress ACL table.<br/>	    2)config different acl rules with src_mac,dst_mac,ether_type <br/>        3) Bind acl to switch <br/>        4)Send matched traffic <br/>  Expected results:<br/>	    1) Verify l2 egress acl table created successfully  <br/>        2)  Verify that acl rules created successfully as per configured 	<br/>        3)Verify that acl successfully bind to switch <br/>        4)Verify using acl hit counters as per configured rule <br/>** |
####


N/A
Flow based services does not support any gNMI APIs.

# 4 Reference Links

 http://gerrit-lvn-07.lvn.broadcom.net:8083/c/sonic/documents/+/12373 	