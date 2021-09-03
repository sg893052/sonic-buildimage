SQA Test Plan

# CPU Packet KNET Debug Counters test plan 


#  SONiC 4.0 Project and Cyrus Release

Rev 1.9

[TOC]

## Test Plan Revision History

| Rev  | Date       | Author                        | Change Description                                    |
| ---- | ---------- | ----------------------------- | ----------------------------------------------------- |
| 1    | 05/19/2021 | Naveen Nag     | Initial Version                                       |
| 2 | 06/30/2021 | Naveen Nag | Changed the test case ID |
| 3 | 09/02/2021 | Naveen Nag | Modified few test cases to cover interface statistics |
|      |            |            |                    |
|      |            |            |                    |
|      |            |            |                    |

## List of Reviewers

| Function | Name |
| :------: | :--: |
|          |      |

## List of Approvers

| Function | Name | Date Approved |
| :------: | :--: | :-----------: |
|          |      |               |

## Definition/Abbreviation

| **Term** | **Meaning**           |
| -------- | --------------------- |
| KNET     | Kernel Network Driver |
|          |                       |

## Introduction

### Objective 

The main objective of this document is to cover the test cases that will focus on verifying the CPU packet debug counters by pumping different protocol packets which are punted to the CPU.

## Feature Overview

The CPU Packet Debug Counters provides BRCM KNET driver level Rx and Tx statistics for each protocol type. SONiC KNET currently provides CPU packet counters based on CPU queues which indirectly provides Rx per protocol counters since each protocol is assigned a separate CPU queue. This feature enhances CPU packet statistics by adding per protocol counters for CPU Rx and Tx directions

## 1 Test Focus Areas

### 1.1 Functional Requirements



1. Support Tx and Rx per protocol KNET driver level packet counters from KNET procfs
2. Support Tx and Rx per protocol Linux/KNET dropped packet counters from KNET procfs
3. Support clearing of Tx and Rx counters
4. Output of statistics will be similar to the following format

The supported protocols are 

1. UDLD
2. STP 
3. PTP   
4. LLDP    
5. VRRP   
6. ICCP   
7. OSPF       
8. BGP   
9. PIM  
10. IGMP      
11. ARP    
12. DHCP  
13. ICMP      
14. IP2ME   
15. IP2ME_SUBNET    
16. IPMTU  
17.  SFLOW              

### **1.2 Protocol Classification Criteria**

| Protocols            | Queue |  Classification Criteria
|----------------------|-------|---------------------------- 
| LACP                 | 23    | (EType==0x8809) && (MAC DA == {01:80:C2:00:00:02}) 
| UDLD                 | 22    | (MAC DA == {01:00:0C:CC:CC:CC})
| STP                  | 21    | (MAC DA == {01:80:C2:00:00:00})
| PVRSTP               | 21    | (MAC DA == {01:00:0C:CC:CC:CD}
| BFDv4                | 20    | (EType==0x800) && (IpProtocol == 17) && (L4DstPort == 3784 or 4784)
| BFDv6                | 20    | (EType==0x86DD) && (IpProtocol == 17) && (L4DstPort == 3784 or 4784)
| PTP                  | 19    | (EType==0x88F7)  
| LLDP                 | 18    | (EType==0x88CC) && (MAC DA == {01:80:C2:00:00:00} or {01:80:C2:00:00:03} or {01:80:C2:00:00:0e})
| VRRP                 | 17    | (EType==0x800) && (IpProtocol == 112)
| ICCP                 | 16    | (IpProtocol == 6) && (IpProtocol == 8888)
| OSPFv4               | 15    | (EType==0x800) && (IpProtocol == 89)
| BGPv4                | 14    | (EType==0x800) && (IpProtocol == 6) && (L4Port == 179)
| BGPv6                | 14    | (EType==0x86DD) && (IpProtocol == 6) && (L4Port == 179)
| PIM                  | 13    | (IpProtocol == 103)
| IGMP                 | 12    | (EType==0x800) && (IpProtocol == 2)
| ARP Request          | 10    | (EType == 0x806) && (ArpOpCode == 1)
| ARP Reply            | 10    | (EType == 0x806) && (ArpOpCode == 2)
| ARP                  | 10    | (EType == 0x806)
| NDP                  | 10    | (EType == 0x86DD) && (IPv6NextHeader == 58) && (IPv6HopLimit == 255) && (Type Code is 133 ~ 137)
| DHCPv4               | 9     | (EType == 0x800) && (IpProtocol == 17) && ((L4SrcPort == 68) && (L4DstPort == 67)) or (L4SrcPort == 67) && (L4DstPort == 68))
| DHCPv6               | 9     | (EType == 0x86DD) && (IpProtocol == 17) && (L4DstPort == 547 or 546)
| ICMP Echo Request    | 8     | (IpProtocol == 1) && (ICMP type == 0)
| ICMP Echo Reply      | 8     | (IpProtocol == 1) && (ICMP type == 8)
| ICMP                 | 8     | (IpProtocol == 1)
| SSH                  | 7     | (IpProtocol == 6)  && ((L4Port == 22))|
| INET                 | 7     | (Etype==0x800) 
| INETv6               | 7     | (Etype==0x88DD)
| Subnet               | 6     | Not classified (Rx queue counters only)
| NAT                  | 5     | Not classified (Rx queue counters only)
| MTU                  | 4     | Not classified (Rx queue counters only)
| Sflow                | 3     | Rx: Pkt sample metadata,  Tx: (L4Port == 6343 for inband sflow datagram to collector)
| TTL                  | 0     | TTL == 0 or TTL == 1

### 1.3 LINK_DOWN pkt drops

* The following command can directly set netdevice link status down while physical link is up.  Packets will continue to be punted up to CPU but will drop with LINK_DOWN reason. CPU Tx direction should also show drops 
```
DUT# echo "Ethernet0=down" > /proc/bcm/knet/link
DUT# cat /proc/bcm/knet/link
Software link status:
  Ethernet0      down
```
### 1.4 NO_BUFFER Tx pkt drops
* Decrease the KNET Tx buffers to a low value (8 or 4) to increase chance of congestion then send flood ping to congest the CPU Tx path and induce out of DMA buffer resource Tx drops. This operation should be used for testing only with all interfaces down.  Changing the max number of Tx buffers may cause a crash if packets are being processed in KNET.
```
echo "max_tx_dcbs=4" > /proc/bcm/knet/dma
```
* Then check the Tx high watermark stats after sending CPU Tx burst.  
```
DUT# cat /proc/bcm/knet/dstats  | grep "Tx used DCBs hi wm"
  Tx used DCBs hi wm           4
```





## 2 Topologies 

2.1 Topology

![system](CPU_Pkt_Path_Debug_4.0.png "Topology")

## 3 Test Case and objectives

### **3.1 Functional Test Cases**

In order to verify the CPU counters we need to create a custom packet from TGEN for the respective protocols and send a burst of traffic. Most of the test case will use custom packets unless specified. 

#### **3.1.1 To verify the UDLD protocol CPU pkt counter** ####

| **Test ID**    | CPU_KNET_DEBUG_FUNC_311                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | To verify the UDLD protocol CPU pkt counter                  |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) As per the topology, enable UDLD on both the edge ports<br/> 2) From TGEN send UDLD custom packet with a large burst of traffic<br/> 3) Upon receiving the traffic, verify the UDLD Rx & TX CPU counters are being incremented  <br/> 4) Stop the traffic and clear the CPU counters <br/> 5) Verify the counters are cleared and now pump in the traffic again  <br/> 6) Verify only the UDLD counters are incremented again.  <br/> |



#### **3.1.2 To verify the STP protocol CPU pkt counter** ####

| **Test ID**    | CPU_KNET_DEBUG_FUNC_312                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | To verify the STP protocol CPU pkt counter                   |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) As per the topology, enable STP on both the edge ports and make sure the STP states are established<br/> 2) Set the bridge priority such that TGEN is the root bridge and STP BPDU's are sent out to the dut<br/> 3) Verify the CPU counters and make sure STP Rx & TX counter is getting incrementing  <br/> 4) We need to simulate the Rx error by shutting down the net device using the internal command as mentioned in section 1.4  while keeping the physical interface up and running<br/> 5) Now verify Rx error is incrementing both in global and interface levels  <br/> 6) Disable the simulation and make sure CPU Rx counter is incrementing instead of Rx err  <br/> 7) Change the root bridge to Dut and verify now the CPU Tx counter is incremented  <br/> 8) We need to simulate the Tx error by decreasing the KNET buffer to pile up the CPU queue and pump in traffic  <br /> 9) Now verify the Tx error is incrementing, clear the counters and make sure the counters are cleared and Tx error counter is incremented again once the traffic starts to flow<br /> |



#### **3.1.3 To verify the PTP and LLDP protocol CPU pkt counter** ####

| **Test ID**    | CPU_KNET_DEBUG_FUNC_313                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | To verify the PTP and LLDP  protocol CPU pkt counter         |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Create a customized PTP pkt header in the TGEN <br/> 2) Configure PTP protocol on the switch<br/> 3) Start sending the stream and verify the PTP counter is incrementing<br/> 4) With the traffic running, create a customized LLDP pkt header<br/> 5) Start the stream and verify both the LLDP tx and rx counter is incrementing  <br/> 6) With both the stream running, make sure both LLDP and PTP counters are incrementing <br/> |



#### **3.1.4 To verify the VRRP protocol CPU pkt counter** ####

| **Test ID**    | CPU_KNET_DEBUG_FUNC_314                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | To verify the VRRP protocol CPU pkt counter                  |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Enable VRRP on the dut and make sure the edge port is part of the vlan on which VRRP is enabled<br/> 2) On the spirent port create the a VRRP stream and start sending traffic <br/> 3) Verify the CPU counters and make sure VRRP Rx & TX counter is getting incrementing  <br/> 4) Clear and make sure the counter is incremented again <br/> 5) Execute "show knet <intf> stats protocol" and verify the VRRP rx error counters are incrementing<br /> |



#### **3.1.5 To verify the OSPF protocol CPU pkt counter** ####

| **Test ID**    | CPU_KNET_DEBUG_FUNC_315                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | To verify the OSPF  protocol CPU pkt counter                 |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure OSPF on the dut <br/> 2) Create a OSPF host and make sure OSPF neighborship is established<br/> 3) Verify both Tx and Rx counter is being incremented since the OSPF neighborship is established<br/> 4) Set a aggressive OSPF timer to pump in more OSPF control pkt <br/> 5) Verify the counters accordisngly   <br/> |





#### **3.1.6 To verify the BGP protocol CPU pkt counter** ####

| **Test ID**    | CPU_KNET_DEBUG_FUNC_316                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | To verify the BGP  protocol CPU pkt counter                  |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure BGP on the dut <br/> 2) Create a BGP host and make sure BGP adjacencies are established<br/> 3) Verify both Tx and Rx counter is being incremented since the BGP neighborship is established<br/> 4) Set a aggressive BGP 3/9 timer to pump in more BGP control pkt <br/> 5) Verify the counters accordingly both globally and interface specific as well.   <br/> |



#### **3.1.7 To verify the ICCP protocol CPU pkt counter** ####

| **Test ID**    | CPU_KNET_DEBUG_FUNC_317                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | To verify the ICCP protocol CPU pkt counter                  |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Create a customized ICCP pkt header in the TGEN <br/> 2) Configure Mclag protocol on the switch and make sure the domain id is same<br/> 3) Start sending the stream and verify the ICCP counter is incrementing<br/> |



#### **3.1.8 To verify the PIM and IGMP protocol CPU pkt counter** ####

| **Test ID**    | CPU_KNET_DEBUG_FUNC_318                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | To verify the PIM and IGMP protocol CPU pkt counter          |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Create a customized PIM pkt header in the TGEN <br/> 2) Configure PIM and IGMP protocol on the switch<br/> 3) Start sending the stream and verify the PIM counter is incrementing<br/> 4) With the traffic running, create a customized IGMP pkt header<br/> 5) Start the stream and verify the IGMP rx counter is incrementing  <br/> 6) With both the stream running, make sure both PIM and IGMP counters are incrementing <br/> |

#### **3.1.9 To verify the ARP, DHCP and ICMP protocol CPU pkt counter** ####

| **Test ID**    | CPU_KNET_DEBUG_FUNC_319                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | To verify the ARP, DHCP and ICMP protocol CPU pkt counter    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Create a customized ARP pkt header in the TGEN <br/> 2) Configure an IP address in the same subnet on the switch<br/> 3) Start sending the stream and verify the ARP counter is incrementing<br/> 4) With the traffic running, create a customized DHCP pkt header<br/> 5) Start the stream and verify the DHCP counter is incrementing  <br/> 6) With both the stream running, make sure both ARP and DHCP counters are incrementing <br/> 7) Create a ICMP request packet and start the stream , verify the ICMP Tx & Rx counter are being incremented<br /> 8) With all the 3 streams running, verify ARP, DHCP and ICMP Rx counter getting incremented<br /> 9) Now create Ipv6 ICMP request packet and start the stream<br /> 10) Verify the Ip6 ICMP counter is incrementing as well along with the other 3 counters<br /> |



#### **3.1.10 To verify the IP2ME and IP2ME_SUBNET CPU pkt counter** ####

| **Test ID**    | CPU_KNET_DEBUG_FUNC_310                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | To verify the IP2ME and IP2ME_SUBNET CPU pkt counter         |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Create a customized IP pkt header with the IP address 172.10.10.10/24 in the TGEN <br/> 2) Configure an IP address - 172.10.10.10/24 on the switch<br/> 3) Start sending the stream and verify the packets are punted to CPU since the traffic belongs to my IP  <br />4) Verify the CPU counter, IP2ME counter should get incremented<br/> 5) Now change the IP address to a differnet subnet and verify that the traffic is not sent to CPU   <br/> 6) The counters shouldn't increment as well  <br/> 7) Assign a different IP with in the same subnet and verify that now IP2ME_SUBNET counter is incremented instead of IP2ME<br /> 8) Upon clearing the counters,  make sure the counters are cleared and incremented as expected.<br /> |



#### **3.1.11 To verify the IPMTU CPU pkt counter** ####

| **Test ID**    | CPU_KNET_DEBUG_FUNC_311                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | To verify the IPMTU CPU pkt counter                          |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Create a customized IP pkt header with packet size 5000 in the TGEN <br/> 2) Let the MTU on the switch be the default one - 1550 <br/> 3) Start sending the stream and verify the packets needs to be fragmented since the pkt size is more than MTU  <br />4) Verify the CPU counter, IPMTU counter should get incremented<br/> 5) Now change the MTU to grater than 5000, the counter shouldn't increment since the fragmentation is not happening  <br/> 6) Revert back the MTU to default and verify the counter is incremented again <br/> |



#### **3.1.12 To verify the SFLOW CPU pkt counter** ####

| **Test ID**    | CPU_KNET_DEBUG_FUNC_312                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | To verify the SFLOW CPU pkt counter                          |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure  a stream on the TGEN to sending at a defined polling interface  <br/> 2) Enable Sflow globally and configure polling interval, collector and agent-id to 10 gig interface <br/> 3) On the interface, enable Sflow and verify the samples are collected at the interval as configured, <br />4) Verify the CPU counter, SFLOW counter should get incremented<br/> 5) Upon disabling the sflow, counter should start incrementing <br/> 6) Enable back and verify the sflow counter is incremented as before <br/> |



#### **3.1.13 To verify the LACP CPU pkt counter** ####

| **Test ID**    | CPU_KNET_DEBUG_FUNC_313                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | To verify the LACP CPU pkt counter                           |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure  a LACP between the 2 nodes  <br/> 2) Verify the portchannel is up and running <br/> 3) Remove the interface from portchannel on one of the node <br />4) The LACP pkt from the other node will still be received on this interface and this will be punted to CPU<br/> 5) Execute "show knet <intf> stats protocol" and verify the Kernal_drop is incrementing under LACP <br/> 6) Clear the counter and verify the same <br/> 5) Similarly configure a different portchannel number on either side of the nodes and verify the drop counter is incrementing <br /> |



## **Reference Links**

 HLD document -->

https://github.com/BRCM-SONIC/sonic_doc_private/pull/148



