

# Feature Name                                                  
Support VRF in BFD                             
# High Level Design Document                                   
#### Rev 0.1                                                   

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)            
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)                        
  * [Definition/Abbreviation](#definitionabbreviation)
                                                      
# List of Tables                                      
[Table 1: Abbreviations](#table-1-abbreviations)      

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 10/04/2019  |   Sudhanshu Kumar  | Initial version                   |

# About this Manual
This document provides general information about the VRF support for BFD feature in FRR in SONIC.
# Scope                                                                                  
This document describes the high level design for VRF support in BFD, with software implementation. FRR supports BFD with VRF. Our aim is to update this feature in FRR in SONIC with show CLI related enhancements and defect fixes.  

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| BFD                      | Bidirectional Forwarding Detection  |
| BGP                      | Border Gateway Protocol             |
| GR                       | Graceful Restart                    |

# 1 Requirement Overview
## 1.1 Functional Requirements
 1. Support monitoring of forwarding path failure for BGP neighbor with VRF. 
 2. Support BFD single hop sessions with VRF.                                
 3. Support BFD multi hop sessions with VRF.                                 
 4. Support Asynchronous mode of operation with VRF.                         
 5. Support Echo mode of operation with VRF.                                 
 6. Support IPv4 address family with VRF.                                    
 7. Support IPv6 address family with VRF.                                    
 8. Support LAG interface with VRF.                                          
 9. Support ECMP paths for multi hop session with VRF.                                                                                                        
## 1.2 Configuration and Management Requirements                    
BFD with VRF will support IS-CLI. The changes to be done for supporting IS-CLI for all BFD config is out of scope for this document.
The FRR CLI for VRF support in BFD are located at the following location. 
[BFD changes for VRF](http://docs.frrouting.org/en/latest/bfd.html)                     
## 1.3 Scalability Requirements                                     
Check the BFD HLD. 
[BFD HLD For SONIC](https://github.com/Azure/SONIC/blob/master/bfd/BFD_Enhancement_HLD.md)
## 1.4 Warm Boot Requirements                                                                       
BFD with VRF should support planned/unplanned restart of BGP container.  Check the BFD HLD for more information.                                    

# 2 Functionality
## 2.1 Target Deployment Use Cases
BFD supports creation of single hop and multi hop sessions to monitor forwarding path failure.
Both iBGP and eBGP support single and multihop BFD sessions. We support  statically and dynamically configured BFD peers over VRF.
## 2.2 Functional Description                                                                           
Bidirectional Forwarding Detection, (BFD) is a protocol defined by the BFD working group at IETF. The protocol defines a method of rapid detection of the failure of a forwarding path by checking that the BFD peer marked as the neighboring router, which may be single or multihops away, is alive. The protocol will be able to detect end-to-end forwarding path failure in milliseconds depending on the actual configuration.  Currently a Routing Protocol takes a few seconds (from 3 seconds to 180 seconds or even more) to detect that the neighbouring router is not operational causing packet loss due to incorrect routing information. BFD is designed to provide a rapid forwarding path failure detection service to a Routing Protocol in a few milliseconds.   
BFD with VRF provides a domain for the BFD packets. When we enable BFD on an interface, it is checked against that particular VRF. We enable BFD on the interface when the VRF exists and the interface is bound to that VRF.  When VRF is associated with an BFD session, BFD packets are routed in that particular VRF domain.                                                                                                                        

# 3 Design
                                                                                                                    
## 3.1 Overview   
BFD subscribes to the VRF add, delete, enable and disable messages.  When a new VRF is created, BFD gets this notification and stores this VRF. Also, it creates 6 new sockets ( single hop, multihop and echo mode, each for ipv4 and ipv6) for receiving BFD control messages. These sockets are created as part of default VRF.

A BFD session is created using protocol BGP. This session contains the VRF information. The VRF can be enabled or disabled by configuration. Therefore, BFD maintains a list of BFD observers. These observers subscribe to the VRF events and can enable/disable BFD sessions accordingly. These observers also help in enabling/disabling the BFD session based on interface up/down events.
BFD packets are processed per interface. Each interface can belong to only one VRF.   
The message exchange betwen Zebra and BFD has been modified to include vrf-id as a parameter.                                                                                                                                                    
## 3.2 CLI
### 3.2.1 Data Models
NA                   
### 3.2.2 Configuration Commands

Config commands as  in FRR BGP container is described at below link.  
[Config commands](http://docs.frrouting.org/en/latest/bfd.html)
```
    sonic(config)# bfd
    sonic(config-bfd)# peer 1.1.1.1 vrf Vrf-1 
    sonic(config-bfd-peer)# detect-multiplier 5 
```   
### 3.2.3 Show Commands

Show commands as  in FRR BGP container is described at below link.  
[Show commands](http://docs.frrouting.org/en/latest/bfd.html)  

```  
sonic# show bfd peers
BFD Peers:
        peer 24.1.1.2 vrf Vrf-1 interface Ethernet24
                ID: 3363546594
                Remote ID: 1111916348
                Status: up
                Uptime: 20 second(s)
                Diagnostics: ok
                Remote diagnostics: ok
                Peer Type: dynamic
                Local timers:
                        Detect-multiplier: 3
                        Receive interval: 300ms
                        Transmission interval: 300ms
                        Echo transmission interval: 50ms
                Remote timers:
                        Detect-multiplier: 3
                        Receive interval: 300ms
                        Transmission interval: 300ms
                        Echo transmission interval: 50ms  

sonic# show bfd vrf Vrf-1 peer 24.1.1.2      
BFD Peer:
        peer 24.1.1.2 vrf Vrf-1
                ID: 4134032180
                Remote ID: 0
                Status: down
                Downtime: 22 minute(s), 53 second(s)
                Diagnostics: ok
                Remote diagnostics: ok
                Peer Type: configured
                Local timers:
                        Detect-multiplier: 3
                        Receive interval: 300ms
                        Transmission interval: 300ms
                        Echo transmission interval: 50ms
                Remote timers:
                        Detect-multiplier: 3
                        Receive interval: 1000ms
                        Transmission interval: 1000ms
                        Echo transmission interval: 0ms                                                                                           
```  

show bfd peers brief command  did not have an option for vrf-name. It has been modified as below.

**show bfd vrf \<vrf-name\> peers  brief**
Show all the BFD peer for a particular VRF in brief, sample output as below:
This command is available in FRR vtysh shell. A new column for VRF has been added.

``` 
sonic# show bfd vrf Vrf-1 peers  brief 
Session count: 1
SessionId  LocalAddress         PeerAddress      Status    Vrf
=========  ============         ===========      ======    =======
3363546594 24.1.1.1             24.1.1.2         up        Vrf-1

```
### 3.2.4 Debug Commands
Debug commands as  in FRR BGP container is described at below link.  
[Debug commands](http://docs.frrouting.org/en/latest/bfd.html)
Debug Command syntax for BFD is as below :  
**[no] debug bfd**

### 3.2.5 REST API Support                           
NA                                                   

# 4 Flow Diagrams
NA

# 5 Serviceability and Debug
**Configuring BFD**         
In order to monitor a forwarding path BFD need triggers from the application to create BFD session with the remote peer. Below are the steps to enable BFD in BGP.

```
    sonic# 
    sonic# conf t
    sonic(config)# router bgp 1 vrf Vrf-1
    sonic(config-router)# neighbor 1.1.1.1 remote-as 7
    sonic(config-router)# neighbor 1.1.1.1 bfd        
```                                                   

Different timer values can be configured to achieve the desired failure detection time. Below configurations can be used to configure BFD timers.

```
    sonic(config)# bfd
    sonic(config-bfd)# peer 1.1.1.1 vrf Vrf-1 
    sonic(config-bfd-peer)# detect-multiplier 5 
    sonic(config-bfd-peer)# receive-interval 200 
    sonic(config-bfd-peer)# transmit-interval 200
```                                              
**Show Output**                                  
Below output shows BFD session in UP state established for BGP protocol.

```
    sonic# show bfd peers
    BFD Peers:
        peer 24.1.1.2 vrf Vrf-1 interface Ethernet24
                ID: 3363546594
                Remote ID: 1111916348
                Status: up
                Uptime: 1 hour(s), 1 minute(s), 43 second(s)
                Diagnostics: ok
                Remote diagnostics: ok
                Peer Type: configured
                Local timers:
                        Detect-multiplier: 100
                        Receive interval: 300ms
                        Transmission interval: 300ms
                        Echo transmission interval: 50ms
                Remote timers:
                        Detect-multiplier: 3
                        Receive interval: 300ms
                        Transmission interval: 300ms
                        Echo transmission interval: 50ms
```
**BFD Counters**
Below output shows BFD counters for particular VRF for peers
```
sonic# show bfd vrf Vrf-1 peers counters 
BFD Peers:
        peer 24.1.1.2 vrf Vrf-1 interface Ethernet24
                Control packet input: 11109 packets
                Control packet output: 11135 packets
                Echo packet input: 0 packets
                Echo packet output: 0 packets
                Session up events: 1
                Session down events: 0
                Zebra notifications: 2
sonic# show bfd vrf Vrf-1 peer 24.1.1.2 counters 
        peer 24.1.1.2 vrf Vrf-1
                Control packet input: 0 packets
                Control packet output: 1379 packets
                Echo packet input: 0 packets
                Echo packet output: 0 packets
                Session up events: 0
                Session down events: 0
                Zebra notifications: 0

```
There is no option for clearing BFD counters for mutiple neighbors, but we can clear the BFD counters for a particular neighbor.
```
sonic# clear bfd vrf Vrf-1 peer 24.1.1.2 counters 
```
# 6 Warm Boot Support
Refer to the BFD HLD for SONIC.
[BFD HLD for SONIC](https://github.com/Azure/SONiC/blob/master/bfd/BFD_Enhancement_HLD.md)

# 7 Scalability
Total number of BFD sessions supported in system are as mentioned in BFD HLD.
 [BFD HLD For SONIC](https://github.com/Azure/SONiC/blob/master/bfd/BFD_Enhancement_HLD.md)

# 9 Unit Test
The existing Unit test cases for BFD will be modified to support VRF.

|**Test-Case ID**|**Test Title**|**Test Scenario**|  
|----------------|--------------|-----------------|  
|                | BFD for BGP IPv4 single-hop |
1|  | Verify BFD session establishment for VRF.|
2|  | Verify BFD packet transmission as per configured interval for VRF.|
3|  | Verify BFD state notification to BGP for VRF.
4|  | Verify BFD session establishment trigger from BGP for VRF.
5|  | Verify change of BFD transmission configuration at run time for VRF.  
6|  | Verify deletion of BFD session by BGP config for VRF.
7|  | Verify timeout of BGP session for VRF.
8|  | Verify timeout notification to BGP for VRF.
9|  | Verify deletion of BFD session after timeout for VRF. |
 ||BFD for BGP IPv4 single-hop over LAG |  
10| | Verify BFD session establishment  for VRF.  
11||Verify session flap on LAG down  for VRF.
12||Verify session flap on LAG member down for VRF.
13||Verify session flap on LAG member UP for VRF.
 ||BFD for BGP IPv4 multi-hop |
14| | Verify BFD session establishment for VRF.
15| |Verify BFD packet transmission as per configured interval for VRF.
16| |Verify BFD state notification to BGP for VRF.
17| |Verify BFD session establishment trigger from BGP for VRF.
18| |Verify change of BFD transmission configuration at run time for VRF.
19| |Verify deletion of BFD session by BGP config for VRF.
20| |Verify timeout of BGP session for VRF.
21| |Verify timeout notification to BGP for VRF.
22||Verify deletion of BFD session after timeout.Verify BFD session establishment for VRF.
 ||BFD for BGP IPv4 Multi-hop over LAG|
23| |Verify BFD session establishment for VRF.
24| |Verify session flap on LAG down for VRF.
25| |Verify session flap on LAG member down for VRF.
26| |Verify session flap on LAG member UP for VRF.
 ||BFD for BGP IPv4 Multi-hop with ECMP|
27| | Verify BFD session establishment for BGP neighbour having ECMP paths for VRF.
28| |Verify BFD session switch to next available ECMP path on active path DOWN  for VRF.
29| |Verify BFD session timeout on all ECMP path DOWN  for VRF.
30| |Verify BFD session timeout when an intermediate path is DOWN  for VRF.
|                | BFD for BGP IPv6 single-hop |
31|  | Verify BFD session establishment for VRF.|
32|  | Verify BFD packet transmission as per configured interval for VRF.|
33|  | Verify BFD state notification to BGP for VRF.
34|  | Verify BFD session establishment trigger from BGP for VRF.
35|  | Verify change of BFD transmission configuration at run time for VRF.  
36|  | Verify deletion of BFD session by BGP config for VRF.
37|  | Verify timeout of BGP session for VRF.
38|  | Verify timeout notification to BGP for VRF.
39|  | Verify deletion of BFD session after timeout for VRF. |
 ||BFD for BGP IPv6 single-hop over LAG |  
40| | Verify BFD session establishment  for VRF.  
41||Verify session flap on LAG down  for VRF.
42||Verify session flap on LAG member down for VRF.
43||Verify session flap on LAG member UP for VRF.
 ||BFD for BGP IPv6 multi-hop|
44| |Verify BFD session establishment with link-local address for VRF.
45| |Verify BFD session establishment with global address  for VRF.
46| |Verify BFD packet transmission as per configured interval for VRF.
47| |Verify BFD state notification to BGP for VRF.
48| |Verify BFD session establishment trigger from BGP for VRF.
49| |Verify change of BFD transmission configuration at run time for VRF.
50| |Verify deletion of BFD session by BGP config for VRF.
51| |Verify timeout of BGP session for VRF.
52| |Verify timeout notification to BGP for VRF.
53| |Verify deletion of BFD session after timeout for VRF.
 ||BFD for BGP IPv6 Multi-hop over LAG|
54| |Verify BFD session establishment.
55| |Verify session flap on LAG down for VRF.
56| |Verify session flap on LAG member down for VRF.
57| |Verify session flap on LAG member UP for VRF.
  ||BFD for BGP IPv6 Multi-hop with ECMP|
58| |Verify BFD session establishment for BGP neighbour having ECMP paths for VRF.
59| |Verify BFD session switch to next available ECMP path on active path DOWN for VRF.
60| |Verify BFD session timeout on all ECMP path DOWN for VRF.
61| |Verify BFD session timeout when a intermediate path is DOWN for VRF.
  ||BFD CLI|
62| |Verify CLI to cofigure BFD for BGP  for VRF.
63| |Verify CLI to configure transmit interval for VRF.
64| |Verify CLI to configure receive interval for VRF.
65| |Verify CLI to configure detection multiplier for VRF.
66| |Verify CLI to configure echo multiplier for VRF.
67| |Verify CLI to enable echo mode for VRF.
68| |Verify CLI to disable echo mode for VRF.
69| |Verify CLI to shutdown BFD peer for VRF.
70| |Verify CLI to configure static IPv4 single hop peer for VRF.
71| |Verify CLI to configure static IPv4 multi hop peer for VRF.
72| |Verify CLI to configure static IPv4 single hop peer with local address  for VRF.
73| |Verify CLI to configure static IPv4 single hop peer with interface for VRF.
74| |Verify CLI to configure static IPv4 single hop peer for VRF.
75| |Verify CLI to configure static IPv4 multi hop peer for VRF.
76| |Verify CLI to configure static IPv4 single hop peer with local address for VRF.
77| |Verify CLI to configure static IPv4 single hop peer with interface for VRF.
78| |Verify CLI to configure static IPv6 single hop peer for VRF.
79| |Verify CLI to configure static IPv6 multi hop peer for VRF.
80| |Verify CLI to configure static IPv6 single hop peer with local address for VRF.
81| |Verify CLI to configure static IPv6 single hop peer with interface for VRF.
82| |Verify CLI to configure static IPv6 single hop peer for VRF.
83| |Verify CLI to configure static IPv6 multi hop peer for VRF.
84| |Verify CLI to configure static IPv6 single hop peer with local address for VRF.
85| |Verify CLI to configure static IPv6 single hop peer with interface for VRF.
86| |Verify CLI to configure static IPv4 single hop peer for VRF.
87| |Verify CLI to un-configure static IPv4 multi hop peer for VRF.
88| |Verify CLI to un-configure static IPv4 single hop peer with local address for VRF.
89| |Verify CLI to un-configure static IPv4 single hop peer with interface for VRF.
90| |Verify CLI to un-configure static IPv4 single hop peer for VRF.
91| |Verify CLI to un-configure static IPv4 multi hop peer for VRF.
92| |Verify CLI to un-configure static IPv4 single hop peer with local address for VRF.
93| |Verify CLI to un-configure static IPv4 single hop peer with interface for VRF.
94| |Verify CLI to un-configure static IPv6 single hop peer for VRF.
95| |Verify CLI to un-configure static IPv6 multi hop peer for VRF.
96| |Verify CLI to un-configure static IPv6 single hop peer with local address for VRF.
97| |Verify CLI to un-configure static IPv6 single hop peer with interface for VRF.
98| |Verify CLI to un-configure static IPv6 single hop peer for VRF.
99| |Verify CLI to un-configure static IPv6 multi hop peer for VRF.
100| |Verify CLI to un-configure static IPv6 single hop peer with local address for VRF.
101| |Verify CLI to un-configure static IPv6 single hop peer with interface for VRF.
102| |Verify CLI to display IPv4 Peer for VRF.
103| |Verify CLI to display IPv4 peer with local address for VRF.
104| |Verify CLI to display IPv4 peer with interface for VRF.
105| |Verify CLI to display IPv6 Peer for VRF.
106| |Verify CLI to display IPv6 peer with local address for VRF.
107| |Verify CLI to display IPv6 peer with interface for VRF.
108| |Verify CLI to display IPv4 multihop Peer for VRF.
109| |Verify CLI to display IPv6 multihop Peer for VRF.
110| |Verify config save and reload of BFD configuration for VRF.
111| |Verify unsaved config loss after relaod for VRF.
  ||BFD static peer|
112| |Verify BFD static IPv4 single hop peer establishment for VRF.
113| |Verify BFD static IPv4 multi hop peer stablishment for VRF.
114| |Verify BFD static IPv4 single hop peer stablishment with local address for VRF.
115| |Verify BFD static IPv4 single hop peer with interface for VRF.
116| |Verify BFD static IPv6 single hop peer establishment for VRF.
117| |Verify BFD static IPv6 multi hop peer stablishment for VRF.
118| |Verify BFD static IPv6 single hop peer stablishment with local address for VRF.
119| |Verify BFD static IPv6 single hop peer with interface for VRF.
 ||BFD echo mode|
120| |Verify BFD session with echo mode for VRF.
121| |Verify BFD echo mode packet transmission as per configured interval for VRF.
122| |Verify echo mode timeout of BGP session for VRF.
123| |Verify echo mode timeout notification to BGP for VRF.
124| |Verify echo mode deletion of BFD session after timeout for VRF.
# 10 Internal Design Information
NA