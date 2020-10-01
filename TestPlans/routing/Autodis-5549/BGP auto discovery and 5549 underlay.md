#  SQA Test Plan
# BGP Auto Discovery and 5549 Underlay Testplan
#  SONiC 3.0 Project and Buzznik Release
[TOC]
# Test Plan Revision History
| Rev | Date | Author | Change Description |
|:---:|:-----------:|:------------------:|-----------------------------|
| 0.1 | 10/11/2019 | Manisha Joshi | Initial version |

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
|     |        |



# Feature Overview



# 1 Test Focus Areas
This document covers test cases designed to verify BGP auto discovery and 5549 underlay functionalities on SONiC 3.0 release.
Below is the link to  high level design of above features

http://gerrit-lvn-07.lvn.broadcom.net:8083/c/sonic/documents/+/10308


# 2 Topologies
## 2.1 Topology 1

![PIM-SSM](PIM_SSM.PNG "Figure 1: PIM-SSM")


# 3 Test  Case and Objectives
## 3.1 CLI
### 3.1.1     Validate all PIM-SSM related CLI config/show commands with boundary and out of range values

| **Test ID** | **FtOpSoRo5549Cli001** |
|--------|:----------------|
| **Test Name** | **Verify cli command for enabling/disabling ALPM mode** |
| **Test Setup** | **Topology1** |
| **Type** | **CLI** |
| **Steps** | **1) Configure SSM-map with invalid multicast group address range and out of range values and verify it gets rejected<br/>2) Configure invalid and out of boundary values for pim dr-priority,Hello timers,hold-down timers,igmp query interval and verify CLI gets rejected with appropriate error messages** |
## 3.2 Functional
### 3.2.1     **Verify eBGP session with auto generated link local using peer group over port channel**

| **Test    ID** | **FtOpSoRoAutoDisFun001**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with auto   generated link local using peer group over port channel** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 2) Configure BGP neighbors on both the DUTs using interface and   peer group command. 3) Verify that IP address is not configured and BGP   session comes up over link local address. 4) Send traffic from DUT1 to DUT3   and verify 5) Flap the interface and reverify traffic. 6) Remove and readd   the interface and reverify traffic** |

### 3.2.2     **Verify eBGP session with auto generated link local using IPv6 listen range over physical interface**

| **Test    ID** | **FtOpSoRoAutoDisFun002**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with auto   generated link local using IPv6 listen range over physical interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure BGP neighbors on both   the DUTs using interface and listen range commands. 2) Verify that IP address   is not configured and BGP session comes up over link local address. 3) Send   traffic from DUT1 to DUT3 and verify 4) Flap the interface and reverify traffic.** |



### 3.2.3     **Verify eBGP session with auto generated link local using IPv6 listen range over port channel**

| **Test    ID** | **FtOpSoRoAutoDisFun003**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with auto   generated link local using IPv6 listen range over port channel** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 2) Configure BGP neighbors on both the DUTs using interface and   listen range commands. 3) Verify that IP address is not configured and BGP   session comes up over link local address. 4) Send traffic from DUT1 to DUT3   and verify 5) Flap the interface and reverify traffic. 6) Remove and readd   the interface and reverify traffic** |

### 3.2.4     **Verify eBGP session with auto generated link local using IPv6 listen limit and listen range over port channel**

| **Test    ID** | **FtOpSoRoAutoDisFun004**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with auto   generated link local using IPv6 listen limit and listen range over port   channel** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 2) Configure BGP neighbors on both the DUTs using interface,   listen range and listen limit commands. 3) Verify that IP address is not   configured and BGP session comes up over link local address. 4) Send traffic   from DUT1 to DUT3 and verify 5) Flap the interface and reverify traffic. 6)   Modify the range limits and reverify traffic** |

### 3.2.5     **Verify eBGP session with manually configured link local using peer group over port channel**

| **Test    ID** | **FtOpSoRoAutoDisFun005**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with manually   configured link local using peer group over port channel** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and manually configure a link local address 2) Configure BGP   neighbors on both the DUTs using interface and peer group command. 3) Verify   that IP address is not configured and BGP session comes up over link local   address. 4) Send traffic from DUT1 to DUT3 and verify 5) Flap the interface   and reverify traffic. 6) Remove and readd the interface and reverify traffic** |


### 3.2.6     **Verify eBGP session with manually configured link local using IPv6 listen range over port channel**

| **Test    ID** | **FtOpSoRoAutoDisFun006**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with manually   configured link local using IPv6 listen range over port channel** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and manually configure a link local address 2) Configure BGP   neighbors on both the DUTs using interface and listen range commands. 3)   Verify that IP address is not configured and BGP session comes up over link   local address. 4) Send traffic from DUT1 to DUT3 and verify 5) Flap the   interface and reverify traffic. 6) Remove and readd the interface and   reverify traffic** |

### 3.2.7     **Verify eBGP session with manually configured link local using IPv6 listen limit and listen range over port channel**

| **Test    ID** | **FtOpSoRoAutoDisFun007**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with manually   configured link local using IPv6 listen limit and listen range over port   channel** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and manually configure a link local address 2) Configure BGP   neighbors on both the DUTs using interface, listen range and listen limit   commands. 3) Verify that IP address is not configured and BGP session comes   up over link local address. 4) Send traffic from DUT1 to DUT3 and verify 5)   Flap the interface and reverify traffic. 6) Modify the range limits and   reverify traffic** |



### 3.2.8     **Verify eBGP session with auto generated link local and add a route-map to change the next hop to IPv6 global address**

| **Test    ID** | **FtOpSoRoAutoDisFun008**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with auto   generated link local and add a route-map to change the next hop to IPv6   global address** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 2) Configure BGP neighbors on both the DUTs using interface   command. 3) Add a route map to modify the next hop address t IPv6 global   address and verify BGP session comes up. 4) Send traffic from DUT1 to DUT3   and verify 5) Flap the interface and reverify traffic. 6) Remove and readd   the interface and reverify traffic** |

### 3.2.9     **Verify eBGP session with auto generated link local and add a route-map to change the next hop to IPv4 address**

| **Test    ID** | **FtOpSoRoAutoDisFun009**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with auto   generated link local and add a route-map to change the next hop to IPv4 address** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 2) Configure BGP neighbors on both the DUTs using interface   command. 3) Add a route map to modify the next hop address IPv4 address and   verify BGP session comes up. 4) Send traffic from DUT1 to DUT3 and verify 5)   Flap the interface and reverify traffic. 6) Remove and readd the interface   and reverify traffic** |

### 3.2.10     **Verify eBGP session with auto generated link local after route dampening configuration**

| **Test    ID** | **FtOpSoRoAutoDisFun0010**                                   |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with auto   generated link local after route dampening configuration** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 2) Configure BGP neighbors on both the DUTs using interface and   route dampening command. 3) Verify that IP address is not configured and BGP   session comes up over link local address. 4) Send traffic from DUT1 to DUT3   and verify 5) Flap the interface and reverify traffic. 6) Remove and readd   the interface and reverify traffic** |

### 3.2.11     **Verify eBGP session with auto generated link local and modify the session to configure static neighbor and listen range configuration**

| **Test    ID** | **FtOpSoRoAutoDisFun0011**                                   |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with auto   generated link local and modify the session to configure static neighbor and   listen range configuration** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 2) Configure BGP neighbors on both the DUTs using interface   command. 3) Verify that IP address is not configured and BGP session comes up   over link local address. 4) Send traffic from DUT1 to DUT3 and verify 5) Remove   the listen range command and modify it to a static neighbor and reverify** |

### 3.2.12	**Verify eBGP session with auto generated link local on one side and  manually configured link local on the other side**

| **Test    ID** | **FtOpSoRoAutoDisFun0012**                                   |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with auto   generated link local on one side and    manually configured link local on the other side** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and on end manually configure IPv6 link local address 2) Configure   BGP neighbors on both the DUTs using interface and listen range commands. 3)   Verify that IP address is not configured and BGP session comes up over link   local address. 4) Send traffic from DUT1 to DUT3 and verify 5) Flap the   interface and reverify traffic. 6) Remove and readd the interface and   reverify traffic** |

### 3.2.13	**Verify eBGP session with auto generated link local and ECMP between phyical and port channel**

| **Test    ID** | **FtOpSoRoAutoDisFun0013**                                   |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with auto   generated link local and ECMP between phyical and port channel** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 2) Configure two BGP sessions one on physical and other one on   port channel using interface and listen range commands. 3) Verify that IP   address is not configured and BGP session comes up over link local address.   4) Send traffic from DUT1 to DUT3 and verify 5) Disable the port channel and   verify traffic moves only on physical and vice versa** |

### 3.2.14	**Verify eBGP session with IPv4 traffic is forwarded to a IPv4 neighbor after receiving traffic over link local**

| **Test    ID** | **FtOpSoRoAutoDisFun0014**                                   |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with IPv4   traffic is forwarded to a IPv4 neighbor after receiving traffic over link   local** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 2) Configure BGP neighbors on both the DUTs using interface   command. 3) Verify that IP address is not configured and BGP session comes up   over link local address. 4) Configure BGP v4 session between DUT2 and DUT3, Send   traffic from DUT1 to DUT3 and verify** |

### 3.2.15	**Verify eBGP session with auto generated link local after enabling and disabling IPv6 at the interface**

| **Test    ID** | **FtOpSoRoAutoDisFun0015**                                   |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with auto   generated link local after enabling and disabling IPv6 at the interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 2) Configure BGP neighbors on both the DUTs using interface and   peer group command. 3) Verify that IP address is not configured and BGP   session comes up over link local address. 4) Send traffic from DUT1 to DUT3   and verify 5) Disable ipv6 at the interface and reverify** |

### 3.2.16	**Verify eBGP session after configuring multiple link local addresses**

| **Test    ID** | **FtOpSoRoAutoDisFun0016**                                   |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session after   configuring multiple link local addresses** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and manually configure more than one link local on the   interfaces 2) Configure BGP neighbors on both the DUTs using interface and   peer group command. 3) Verify that IP address is not configured and BGP   session comes up over link local address. 4) Send traffic from DUT1 to DUT3   and verify 5) remove/add each address and verify** |

### 3.2.17	**Verify eBGP session after altering between auto generated and manually configured link local addresses**

| **Test    ID** | **FtOpSoRoAutoDisFun0017**                                   |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session after altering  between auto generated and manually configured link local addresses** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and manually configure link local on the interface 2) Configure   BGP neighbors on both the DUTs using interface and peer group command. 3)  Verify that IP address is not configured and BGP session comes up over link   local address. 4) Send traffic from DUT1 to DUT3 and verify 5) Remove the   manually configured link local and verify that traffic goes fine on auto   generated address** |

### 3.2.18	**Verify BGP unnumbered session after clear bgp neighbor **

| **Test    ID** | **FtOpSoRoAutoDisFun0018**                                   |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify BGP unnumbered session after clear bgp neighbor**   |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and manually configure more than one link local on the   interfaces 2) Configure BGP neighbors on both the DUTs using interface and   peer group command. 3) Verify that IP address is not configured and BGP   session comes up over link local address. 4) Send traffic from DUT1 to DUT3   and verify 5) Clear bgp neighbors and veriiy** |

### 3.2.19	**Verify BGP unnumbered session after clear ndp**

| **Test    ID** | **FtOpSoRoAutoDisFun0019**                                   |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify BGP unnumbered session   after clear ndp**          |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and manually configure more than one link local on the   interfaces 2) Configure BGP neighbors on both the DUTs using interface and   peer group command. 3) Verify that IP address is not configured and BGP   session comes up over link local address. 4) Send traffic from DUT1 to DUT3   and verify 5) Clear ndp before and after BGP session and verify** |

### 3.2.21	Verify eBGP session with auto generated link local after adding and removing bgp configuration globally

| **Test    ID** | **FtOpSoRoAutoDisFun0020**                                   |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with auto   generated link local after adding and removing bgp configuration globally** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and manually configure more than one link local on the   interfaces 2) Configure BGP neighbors on both the DUTs using interface and   peer group command. 3) Verify that IP address is not configured and BGP   session comes up over link local address. 4) Send traffic from DUT1 to DUT3   and verify 5) Remove and read BGP globally and reverify traffic** |

### 3.2.21	**Verify eBGP session with auto generated link local and modify the session to take IPv6 global address**

| **Test    ID** | **FtOpSoRoAutoDisFun0021**                                   |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with auto   generated link local and modify the session to take IPv6 global address** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and manually configure more than one link local on the   interfaces 2) Configure BGP neighbors on both the DUTs using interface and   listen range command. 3) Verify that IP address is not configured and BGP   session comes up over link local address. 4) Send traffic from DUT1 to DUT3   and verify 5) Add global IPv6 address and verify session comes up using the   global address. 6) Add IPv4 address and verify session comes up using tha.   Remove and reverify that session comes up using link local address** |

### 3.2.22	**Verify eBGP session with auto generated link local with redistributed connected**

| **Test    ID** | **FtOpSoRoAutoDisFun0022**                                   |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with auto   generated link local with redistributed connected** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 2) Configure BGP neighbors on both the DUTs using interface and   peer group command. 3) Verify that IP address is not configured and BGP   session comes up over link local address after redistributing connected. 4)   Send traffic from DUT1 to DUT3 and verify 5) Add global IPv6 address and   verify session comes up using the global address. 6) Add IPv4 address and   verify session comes up using that. Remove and reverify that session comes up   using link local address** |

### 3.2.23	**Verify eBGP session with global IPv6 address using IPv6 listen range over physical interface**

| **Test    ID** | **FtOpSoRoAutoDisFun023**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global   IPv6 address using IPv6 listen range over physical interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure BGP neighbors on both   the DUTs using global IPv6 address and listen range commands. 2) Configure   BGP neighbors using listen range commands. 3) Send traffic from DUT1 to DUT3   and verify 4) Flap the interface and reverify traffic.** |

### 3.2.24	**Verify eBGP session with global IPv6 address using IPv6 listen range over port channel**

| **Test    ID** | **FtOpSoRoAutoDisFun024**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global   IPv6 address using IPv6 listen range over port channel** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and assign a global IPv6 2) Configure BGP neighbors using listen   range commands. 3) Send traffic from DUT1 to DUT3 and verify 4) Flap the   interface and reverify traffic. 5) Flap the interface and reverify traffic.   6) Remove and readd the interface and reverify traffic** |

### 3.2.25	**Verify eBGP session with global IPv6 address using IPv6 listen limit and listen range over port channel**

| **Test    ID** | **FtOpSoRoAutoDisFun25**                                     |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global   IPv6 address using IPv6 listen limit and listen range over port channel** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and assign a global IPv6 address 2) Configure BGP neighbors on   both the DUTs using listen range and listen limit commands. 4) Send traffic   from DUT1 to DUT3 and verify 5) Flap the interface and reverify traffic. 6)   Modify the range limits and reverify traffic** |

### 3.2.26	**Verify eBGP session with global IPv6 address and add a route-map to change the next hop to IPv6 global address**

| **Test    ID** | **FtOpSoRoAutoDisFun026**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global   IPv6 address and add a route-map to change the next hop to IPv6 global   address** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and assign a global IPv6 address 2) Configure BGP neighbors on   both the DUTs using listen range command. 3) Add a route map to modify the   next hop address t IPv6 global address and verify BGP session comes up. 4)   Send traffic from DUT1 to DUT3 and verify 5) Flap the interface and reverify   traffic. 6) Remove and readd the interface and reverify traffic** |

### 3.2.27	**Verify eBGP session with global IPv6 address after route dampening configuration**

| **Test    ID** | **FtOpSoRoAutoDisFun027**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global   IPv6 address after route dampening configuration** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and assign a global IPv6 address 2) Configure BGP neighbors on   both the DUTs using listen range and route dampening command. 3) Send traffic   from DUT1 to DUT3 and verify 4) Flap the interface and reverify traffic. 4)   Remove and readd the interface and reverify traffic** |

### 3.2.28	**Verify eBGP session with global IPv6 address and modify the session to configure static neighbor and listen range configuration**

| **Test    ID** | **FtOpSoRoAutoDisFun028**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global   IPv6 address and modify the session to configure static neighbor and listen   range configuration** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and assign a global IPv6 address 2) Configure BGP neighbors on   both the DUTs using listen range and limit command. 3) Send traffic from DUT1   to DUT3 and verify 4) Remove the listen range command and modify it to a   static neighbor and reverify** |

### 3.2.29	**Verify eBGP session with global IPv6 address and ECMP between physical and port channel**

| **Test    ID** | **FtOpSoRoAutoDisFun029**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global   IPv6 address and ECMP between physical and port channel** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and assign a global IPv6 address 2) Configure two BGP sessions   one on physical and other one on port channel using interface and listen   range commands 3) Send traffic from DUT1 to DUT3 and verify 4) Disable the   port channel and verify traffic moves only on physical and vice versa** |

### 3.2.30	**Verify eBGP session with global IPv6 address over a loopback interface and redistribute connected**

| **Test    ID** | **FtOpSoRoAutoDisFun030**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global   IPv6 address over a loopback interface and redistribute connected** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and assign a global IPv6 address 2) Configure two BGP sessions  listen range commands. 3) Verify that IP   address is not configured and BGP session comes up over link local address.   4) Send traffic from DUT1 to DUT3 and verify 5) Disable the port channel and   verify traffic moves only on physical and vice versa** |

### 3.2.31	**Verify multihop BGP session with global IPv6 address**

| **Test    ID** | **FtOpSoRoAutoDisFun031**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify multihop BGP session with   global IPv6 address**   |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 DUT2 and assign a global IPv6 address  2) Configure BGP multihop session over   loopback using listen range command. 3) Send end to end traffic and verify** |

### 3.2.32	**Verify BGP session with global IPv6 address after removing and adding loopback interface**

| **Test    ID** | **FtOpSoRoAutoDisFun032**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify BGP session with global   IPv6 address after removing and adding loopback interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 DUT2 and assign a global IPv6 address  2) Configure BGP multihop session over   loopback using listen range command. 3) Send end to end traffic and verify 4)   Removing the loopback interface and reverify** |

### 3.2.33	**Verify BGP session with global IPv6 address after modifying loopback address**

| **Test    ID** | **FtOpSoRoAutoDisFun033**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify BGP session with global   IPv6 address after modifying loopback address** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 DUT2 and assign a global IPv6 address  2) Configure BGP multihop session over   loopback using listen range command. 3) Send end to end traffic and verify 4)   Modify the loopback address and reverify** |

### 3.2.34	**Verify eBGP session with global IPv6 address have /127 subnet**

| **Test    ID** | **FtOpSoRoAutoDisFun034**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global   IPv6 address have /127 subnet** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and configure global IPv6 address with /127 subnet2) Configure   BGP neighbors on both the DUTs using listen range command. 3) Verify that IP   address is not configured and BGP session comes up over link local address.   4) Send traffic from DUT1 to DUT3 and verify 5) Disable ipv6 at the interface   and reverify** |

### 3.2.35	Verify eBGP session with global  IPv6 address after configuring multiple global IPv6 addresses on the interface

| **Test    ID** | **FtOpSoRoAutoDisFun035**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global  IPv6 address after configuring multiple global IPv6 addresses on the interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and configure more than one IPv6 addresses on the interfaces 2)   Configure BGP neighbors on both the DUTs using listen range command. 3)   Verify that IP address is not configured and BGP session comes up over link   local address. 4) Send traffic from DUT1 to DUT3 and verify 5) remove/add   each address and verify** |

### 3.2.36	Verify eBGP session with global IPv6 address after disabling and enabling IPv6 at the interface 

| **Test    ID** | **FtOpSoRoAutoDisFun036**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global IPv6 address after disabling and enabling IPv6 at the interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and configure global IPv6 address 2) Configure BGP neighbors on   both the DUTs using listen range command. 3) Verify that IP address is not   configured and BGP session comes up over link local address. 4) Send traffic   from DUT1 to DUT3 and verify 5) Disable ipv6 at the interface and reverify** |

### 3.2.37	Verify eBGP session with global IPv6 address after clear ipv6 bgp neighbor and clear bgp neighbor

| **Test    ID** | **FtOpSoRoAutoDisFun037**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global IPv6 address after clear ipv6 bgp neighbor and clear bgp neighbor** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and configure global IPv6 address 2) Configure BGP neighbors on   both the DUTs using listen range command. 3) Verify that IP address is not   configured and BGP session comes up over link local address. 4) Send traffic   from DUT1 to DUT3 and verify 5) Clear ipv6 and ip bgp neighbors** |

### 3.2.38	**Verify eBGP session with global IPv6 address after clear ndp** 

| **Test    ID** | **FtOpSoRoAutoDisFun038**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global  IPv6 address after clear ndp** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and manually configure more than one link local on the   interfaces 2) Configure BGP neighbors on both the DUTs using interface and   peer group command. 3) Verify that IP address is not configured and BGP   session comes up over link local address. 4) Send traffic from DUT1 to DUT3   and verify 5) Clear ndp neighbors** |


### 3.2.39	**Verify eBGP session with global IPv6 address after adding and removing bgp configuration globally**

| **Test    ID** | **FtOpSoRoAutoDisFun039**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global   IPv6 address after adding and removing bgp configuration globally** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 with global IPv6 address 2) Configure BGP neighbors on both the   DUTs using listen range command. 3) Send traffic from DUT1 to DUT3 and verify   5) Remove and read BGP globally and reverify traffic** |

### 3.2.40	**Verify eBGP session with global IPv4 address using IPv4 listen range over port channel**

| **Test    ID** | **FtOpSoRoAutoDisFun040**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global IPv4 address using IPv4 listen range over port channel** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) ) Configure port channel between DUT1 and DUT2 and assign IPv4 address 2) Configure BGP neighbors on both the   DUTs using IPv4 address and listen range commands. 3) Configure BGP neighbors using listen range commands. 3) Send traffic from DUT1 to DUT3 and verify 4)   Flap the interface and reverify traffic.** |

### 3.2.41	**Verify eBGP session with global IPv4 address using IPv4 listen range over physical interface**

| **Test ID**    | **FtOpSoRoAutoDisFun041**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with global IPv4 address using IPv4 listen range over physical interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure BGP neighbors on both   the DUTs using IPv4 address and listen range commands. 2) Configure BGP   neighbors using listen range commands. 3) Send traffic from DUT1 to DUT3 and   verify 4) Flap the interface and reverify traffic.** |

### 3.2.42	**Verify eBGP session with IPv4 address using IPv4 listen limit and listen range over physical interface**

| **Test    ID** | **FtOpSoRoAutoDisFun042**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with IPv4   address using IPv4 listen limit and listen range over physical interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **2) Configure BGP neighbors on both the DUTs   using listen range and listen limit commands. 4) Send traffic from DUT1 to   DUT3 and verify 5) Flap the interface and reverify traffic. 6) Modify the   range limits and reverify traffic** |

### 3.2.43	Verify eBGP session with IPv4  address using IPv4 listen limit and listen range over port channel

| **Test    ID** | **FtOpSoRoAutoDisFun043**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with IPv4  address using IPv4 listen limit and listen range over port channel** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure port channel between   DUT1 and DUT2 and assign a IPv4 address 2) Configure BGP neighbors on both   the DUTs using listen range and listen limit commands. 4) Send traffic from   DUT1 to DUT3 and verify 5) Flap the interface and reverify traffic. 6) Modify   the range limits and reverify traffic** |

### 3.2.44	Verify IPv4 unnumbered basic functionality

| **Test    ID** | **FtOpSoRoAutoDisFun044**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify IPv4 unnumbered basic   functionality**             |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure IP unnumbered, send   end to end traffic and verify** |

### 3.2.45	**Verify IPv4 unnumbered after adding and removing IPv4 address**

| **Test    ID** | **FtOpSoRoAutoDisFun045**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify IPv4 unnumbered after adding and removing IPv4 address** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure IP unnumbered, send   end to end traffic and verify** |

### 3.2.46	Verify IPv4 unnumbered after modifying IPv4 address

| **Test    ID** | **FtOpSoRoAutoDisFun0046**                                   |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify IPv4 unnumbered after modifying IPv4 address**      |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure IP unnumbered, send   end to end traffic and verify** |

### 3.2.47	Verify IPv4 unnumbered over loopback after adding and removing loopback address

| **Test    ID** | **FtOpSoRoAutoDisFun047**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify IPv4 unnumbered over loopback after adding and removing loopback address** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure IP unnumbered, send   end to end traffic and verify** |

### 3.2.48	Verify IPv4 unnumbered over loopback after adding removing loopback interface

| **Test    ID** | **FtOpSoRoAutoDisFun048**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify IPv4 unnumbered after   adding and removing loopback interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure IP unnumbered, send   end to end traffic and verify** |



## 3.3 Negative

## 3.4 Reboot
### 3.4.1    Verify BGP uunumbered on default vrf and non-default vrf after a reboot

| **Test    ID** | **FtOpSoRoAutoDisFun049**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify BGP unnumbered on default vrf and non-default vrf after a reboot** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure BGP unnumbered and listen range and verify after a cold reboot** |

### 3.4.2    Verify BGP unnumbered on default vrf and non-default vrf after a warm reboot

| **Test    ID** | **FtOpSoRoAutoDisFun050**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify BGP unnumbered on default vrf and non-default vrf after a warm reboot** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure BGP unnumbered and listen range and verify after a warm reboot** |

### 3.4.3    Verify BGP unnumbered on default vrf and non-default vrf after a config reload

| **Test    ID** | **FtOpSoRoAutoDisFun051**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify BGP unnumbered on default vrf and non-default vrf after a config reload** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure BGP unnumbered and listen range and verify after a config reload** |

### 3.4.4    Verify eBGP session with IPv6, IPv4 address and listen range on default vrf and non-default vrf after a reboot

| **Test    ID** | **FtOpSoRoAutoDisFun052**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with IPv4, IPv6 address and listen range nn default vrf and non-default vrf after a reboot** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure BGP session with IPv4, IPv6 address and listen range and verify after a cold reboot** |

### 3.4.5    Verify eBGP session with IPv4, IPv6 address and listen range on default vrf and non-default vrf after a warm reboot

| **Test    ID** | **FtOpSoRoAutoDisFun053**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with IPv4, IPv6 address and listen range on default vrf and non-default vrf after a warm reboot** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure BGP session with IPv4, IPv6 address and listen range and verify after a warm reboot** |

### 3.4.6    Verify eBGP session with IPv4, IPv6 address and listen range on default vrf and non-default vrf after a config reload

| **Test    ID** | **FtOpSoRoAutoDisFun054**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify eBGP session with IPv4, IPv6 address and listen range on default vrf and non-default vrf after a config reload** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure BGP session with IPv4, IPv6 address and listen range and verify after a config reload** |

### 3.4.7    Verify IP uunumbered on default vrf and non-default vrf after a reboot

| **Test    ID** | **FtOpSoRo5549Fun055**                                       |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify IP unnumbered on default vrf and non-default vrf after a reboot** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure IP unnumbered and listen range and verify after a cold reboot** |

### 3.4.8    Verify IP unnumbered on default vrf and non-default vrf after a warm reboot

| **Test    ID** | **FtOpSoRo5549Fun056**                                       |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify IP unnumbered on default vrf and non-default vrf after a warm reboot** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure IP unnumbered and listen range and verify after a warm reboot** |

### 3.4.9    Verify IP unnumbered on default vrf and non-default vrf after a config reload

| **Test    ID** | **FtOpSoRoAutoDisFun057**                                    |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify IP unnumbered on default vrf and non-default vrf after a config reload** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | **1) Configure BGP unnumbered and listen range and verify after a config reload** |

### 

## 3.5 Stress,Scale and Performance
### 3.5.1    Verify  maximum BGP unnumbered sessions 

| **Test ID**    | **FtOpSoRoAutoSc001**                                    |
| -------------- | -------------------------------------------------------- |
| **Test Name**  | **Verify  maximum BGP unnumbered sessions**              |
| **Test Setup** | **Topology1**                                            |
| **Type**       | **Functional**                                           |
| **Steps**      | **Configure maximum BGP unnumbered sessions and verify** |

### 3.5.2    Verify  maximum IP unnumbered sessions

| **Test ID**    | FtOpSoRoAutoSc001                                       |
| -------------- | ------------------------------------------------------- |
| **Test Name**  | **Verify maximum IP unnumbered sessions**               |
| **Test Setup** | **Topology1**                                           |
| **Type**       | **Functional**                                          |
| **Steps**      | **Configure maximum IP unnumbered sessions and verify** |

### 

## 3.6 Management

### 3.6.1    SNMP
### 3.6.2    gNMI
### 3.6.3     REST API

# 4 Reference Links

