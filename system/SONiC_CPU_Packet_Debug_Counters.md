# Feature Name
SONiC CPU Packet Debug Counters
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
| 0.1 | 04/13/2021  |   Michael Li       | Initial version                   |
| 0.2 | 08/16/2021  |   Michael Li       | Design detail updates             |
| 0.3 | 08/30/2021  |   Michael Li       | Added guidance for testing pkt drops
| 0.4 | 10/20/2021  |   Michael Li       | Pkt classification corrections and CLI output updates


# About this Manual
This document provides general information about the SONiC CPU Packet Debug Counters feature implementation in SONiC.
# Scope
This document describes the high level design of SONiC CPU Packet Debug Counters feature.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| KNET                     | Kernel Network Driver               |

# 1 Feature Overview
The CPU Packet Debug Counters provides BRCM KNET driver level Rx and Tx statistics for each protocol type at switch and interface levels. SONiC KNET currently provides CPU packet counters based on CPU queues which indirectly provides Rx per protocol counters since each protocol is assigned a separate CPU queue. This feature enhances CPU packet statistics by adding per protocol counters per interface for CPU Rx and Tx directions. Packet drop reason details per protocol/queue per interface are also added for more debug visibility.

## 1.1 Requirements

### 1.1.1 Functional Requirements

1. Support switch and per interface Tx and Rx per protocol KNET driver level packet counters
2. Support switch and per interface Tx and Rx per protocol Linux/KNET dropped packet counters
3. Support switch and per interface Tx and Rx KNET error reason detail counters
4. Support switch and per interface clearing of Tx and Rx counters

### 1.1.2 Configuration and Management Requirements
Statistics will be provided via click CLIs and KNET procfs files

### 1.1.3 Scaling and Performance Requirements

There should be minimal impact on CPU Pkt IO performance and latencies.  CPU Pkt IO performance will be profiled to measure the impact of adding pkt type processing on CPU Pkt IO performance and latencies.

### 1.1.4 Warm Boot Requirements
Not applicable

# 2 Functionality
## 2.1 Target Deployment Use Cases
This debugging enhancement provides  more granular debug counters and additional visibility into the CPU Pkt IO network driver (KNET) and Linux kernel pkt path that will help with debugging SONiC systems.

## 2.2 Functional Description
See feature overview

# 3 Design
## 3.1 Overview
Protocol classification logic will be added to the Broadcom KNET driver pkt Tx and Rx callbacks to identify protocols and enable pkt protocol type accounting per physical interface.  The statistics output will be available via KNET driver procfs files and click CLIs for convenience. 

### 3.1.1 Rx Classification and Packet drop counting
The Broadcom KNET driver filter infrastructure will be used to classify Rx packets (and CPU queues from pkt metadata) and tag the protocol type from added logic in the KNET callback module. This will allow managing KNET packet stats accounting to have per protocol and per interface information when managing stats accounting.

Any drops before KNET filter processing (matching src port) will not have src port information so pkt drops will count towards the global protocol switch counters and will not update the protocol interface counters.  After KNET filter processing determines the src port, both global and per interface protocol counters will be updated.

### 3.1.2 Tx Classification
The Broadcom KNET driver will be modified to provide a similar KNET filter infrastructure as the Rx path to allow classification of pkt types as they are received by the KNET driver from the Linux kernel.  The pkt skbuff will have destination port information from the Linux kernel so per interface protocol counters and any drop counters will be updated.

### 3.1.3 Protocol Classification Criteria
Packets will be parsed and classified according to the criteria in the following table. Any packets that cannot be classified will have counters updated in the "unknown" category.

References:
- https://www.iana.org/assignments/ieee-802-numbers/ieee-802-numbers.xhtml
- https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml
- https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml
- SAI Hostinterface protocol qualifier implementations

### **Table 1: Protocol Classification Criteria**

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
| ICCP                 | 16    | (IpProtocol == 6) && (L4DstPort == 8888)
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
| INETv6               | 7     | (Etype==0x86DD)
| Subnet               | 6     | Not classified (Rx queue counters only)
| NAT                  | 5     | Not classified (Rx queue counters only)
| MTU                  | 4     | Not classified (Rx queue counters only)
| Sflow                | 3     | Rx: Pkt sample metadata,  Tx: (L4Port == 6343 for inband sflow datagram to collector)
| TTL                  | 0     | TTL == 0 or TTL == 1

### 3.1.4 Packet Drop Error types
Errors in KNET (and in Rx kernel path) which result in packet drops are counted per pkt_type/queue on each interface.  Some common errors are listed below.
### **Table 2: Packet Drop Errors**
| Error                | Description
|----------------------|----------------------------------- 
| LINK_DOWN            | Kernel network interface was in down state            
| NO_SKB               | Packet SKB alloc failed
| NO_BUFFER            | No DMA buffer resource available (congested PktIO)
| KERNEL_DROP          | Rx packet drop in Linux kernel network stack
| NO_FILTER_MATCH      | Rx packet did not match any KNET filters (no handlers)
| UNKN_NETIF           | Unknown src network interface for Rx packet
| HW_RESET             | Packet dropped due to HW reset cleanup in progress

## 3.2 DB Changes
There are no new changes to SONiC DBs.

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
There are no new changes to Orchagent.
## 3.4 SyncD
There are no new changes to SyncD.

## 3.5 Manageability
There are no new changes to manageability infrastructure

## 3.6 CLI
KNET Packet stats at switch and interface levels can be shown through click commands or by dumping KNET pkt_stats procfs file.  Rx and Tx error counter details are available for pkt_type and rx queue counters at switch and interface levels.

### 3.6.1 Packet Type Stats
* Show pkt_type stats (real output will not show entries with zero counts)

```
DUT# show knet stats pkt-type      // show totals from all interfaces
DUT# show knet stats pkt-type -a   // show all individual interfaces
or
DUT# cat /proc/bcm/knet-cb/stats/pkt_type

admin@sonic:~$ show knet stats pkt-type
KNET Pkt Type Stats
Pkt Type                   Rx Pkts   Rx errors     Tx Pkts   Tx errors
----------------------  ----------  ----------  ----------  ----------
PKT_TYPE_UNKNOWN                 0           0           0           0
PKT_TYPE_LACP                  466        4310         468           0
PKT_TYPE_UDLD                    0           0           0           0
PKT_TYPE_STP                     0           0           0           0
PKT_TYPE_PVRSTP                  0           0           0           0
PKT_TYPE_BFDV4                   0           0           0           0
PKT_TYPE_BFDV6                   0           0           0           0
PKT_TYPE_PTP                     0           0           0           0
PKT_TYPE_LLDP                  720           0        1297           0
PKT_TYPE_VRRP                    0           0           0           0
PKT_TYPE_ICCP                    0           0        4314           0
PKT_TYPE_OSPF                    0           0           0           0
PKT_TYPE_BGPV4                   0           0           0           0
PKT_TYPE_BGPV6                   0           0           0           0
PKT_TYPE_PIM                     0           0           0           0
PKT_TYPE_IGMP                    0           0           0           0
PKT_TYPE_ARPREQ                 13           0          14           0
PKT_TYPE_ARPRPLY                14           0          13           0
PKT_TYPE_ARP                     0           0           0           0
PKT_TYPE_NDP                     0           0           0           0
PKT_TYPE_DHCPV4                  0           0           0           0
PKT_TYPE_DHCPV6                  0           0           0           0
PKT_TYPE_ICMP_ECHOREQ            0           0           0           0
PKT_TYPE_ICMP_ECHORPLY           0           0           0           0
PKT_TYPE_ICMP                    0           0           0           0
PKT_TYPE_SSH                     0           0           0           0
PKT_TYPE_INETV4               4313           0           0           0
PKT_TYPE_INETV6                  0           0           0           0
PKT_TYPE_SFLOW                   0           0           0           0
PKT_TYPE_TTL                     0           0           0           0
Total                         5526        4310        6106           0
```
* Show interface level pkt type stats

```
DUT# show knet stats pkt-type Ethernet0
or
DUT# cat /proc/bcm/knet-cb/stats/Ethernet0/pkt_type

Ethernet0 Pkt Type Stats
Pkt Type                   Rx Pkts   Rx errors     Tx Pkts   Tx errors
----------------------  ----------  ----------  ----------  ----------
PKT_TYPE_LLDP                   30           0          30           0
PKT_TYPE_ICCP                    0           0         875           0
PKT_TYPE_ARPREQ                  3           0           3           0
PKT_TYPE_ARPRPLY                 3           0           3           0
PKT_TYPE_INETV4                871           0           0           0
Total                          907           0         911           0
...
```
* Show interface level pkt type stats with error details
```
DUT# show knet stats pkt-type Ethernet0 -v
or
DUT# echo "detail=1" > /proc/bcm/knet-cb/stats/debug
DUT# cat /proc/bcm/knet-cb/Ethernet0/stats/pkt_type
DUT# echo "detail=0" > /proc/bcm/knet-cb/stats/debug

Ethernet0 Pkt Type Stats
Pkt Type                   Rx Pkts   Rx errors     Tx Pkts   Tx errors
----------------------  ----------  ----------  ----------  ----------
PKT_TYPE_LACP                  127         934         126           0
  Kernel netstack drop                     934                       0
PKT_TYPE_LLDP                  157           1         283           0
  Link down                                  1                       0
PKT_TYPE_ICCP                    0           0         933           0
PKT_TYPE_ARPREQ                  3           0           3           0
PKT_TYPE_ARPRPLY                 3           0           3           0
PKT_TYPE_INETV4                929           0           0           0
Total                         1219         935        1348           0
...
```

* Clear all KNET pkt statistics
```
DUT# sonic-clear knet stats
```

* Clear specific pkt type statistics 
```
DUT# show knet stats pkt-type -c
DUT# show knet stats pkt-type -c Ethernet0
or
DUT# echo "clear" > /proc/bcm/knet-cb/stats/pkt_type
DUT# echo "clear" > /proc/bcm/knet-cb/stats/Ethernet0/pkt_type
```
### 3.6.2 Rx Queue Stats
```
DUT# show knet stats rx-queue     // show totals from all interfaces
DUR# show knet stats rx-queue -a  // show all individual interfaces
or
DUT# cat /proc/bcm/knet-cb/stats/rx_queue

KNET Rx Queue Stats
Queue          Rx Pkts   Rx errors  Description
----------  ----------  ----------  --------------------------------
  10                10           0  arp_req,arp_resp,neigh_discovery
  16              1465           0  iccp
  18               243           1  lldp
  23               179        1471  lacp
Total             1897        1472
```

* Show interface level rx queue stats
```
DUT# show knet stats rx-queue Ethernet0
or
DUT# cat /proc/bcm/knet-cb/Ethernet0/stats/rx_queue

Ethernet0 Rx Queue Stats
Queue          Rx Pkts   Rx errors  Description
----------  ----------  ----------  --------------------------------
  10                10           0  arp_req,arp_resp,neigh_discovery
  16              1465           0  iccp
  18               243           1  lldp
  23               179        1471  lacp
Total             1897        1472
```

* Show interface level rx queue stats with error details
```
DUT# show knet stats rx_queue Ethernet0 -v
or
DUT# echo "detail=1" > /proc/bcm/knet-cb/stats/debug
DUT# cat /proc/bcm/knet-cb/stats/Ethernet0/rx_queue
DUT# echo "detail=1" > /proc/bcm/knet-cb/stats/debug

Queue          Rx Pkts   Rx errors  Description
----------  ----------  ----------  --------------------------------
  10                11           0  arp_req,arp_resp,neigh_discovery
  16              1591           0  iccp
  18               267           1  lldp
                                 1    Link down
  23               193        1597  lacp
                              1597    Kernel netstack drop
Total             2062        1598
```

* Clear Rx Queue stats
```
DUT# show knet stats rx-queue -c
DUT# show knet stats rx-queue Ethernet0 -c
or
DUT# echo "clear" > /proc/bcm/knet-cb/stats/rx_queue
DUT# echo "clear" > /proc/bcm/knet-cb/stats/Ethernet0/rx_queue
```

# 4 Flow Diagrams

# 5 Error Handling
Not applicable

# 6 Serviceability and Debug
The new debug counters introduced at switch and interface levels (with drop details) will be added to techsupport collection with 2 samples each

# 7 Warm Boot Support
No warmboot support. CPU Pkt IO counters are maintained in KNET kernel network driver which gets cleared on CPU reset on a warmboot operation.

# 8 Scalability
## 8.1 Profiling and Tuning
The CPU packet IO performance impact on throughput and latency with the addition of processing to identify pkt type for counters will be profiled. It is expected there will be minimal impact.

# 9 Unit Test
## 9.1 Functional Test Cases
### 9.1.1 Protocol Stats
1. Verify Rx and Tx pkt counters on switch/interface levels for each protocol
2. Verify KERNEL_DROP Rx drop counter on switch/interface levels for selected protocols
3. Verify LINK_DOWN Rx and Tx drop counters on switch/interface levels for selected protocols
4. Verify NO_BUFFER Tx drop counters on switch/interface levels for selected protocols
5. Verify clear stats for switch and interface level protocol counters

### 9.1.2 Rx Queue Stats
1. Verify Rx queues and description match COPP config
2. Verify Rx queue pkt counters on switch/interface levels for each protocol
3. Verify KERNEL_DROP Rx queue drop counter on switch/interface levels for selected protocols
4. Verify LINK_DOWN Rx queue drop counters on switch/interface levels for selected protocols
5. Verify clear stats for switch and interface level Rx queue counters


## 9.2 Warm Boot Test Cases
Not applicable

## 9.3 Negative Test Cases

# 10 Internal Design Information
## 10.1 Guidance on simulating triggers for testing packet drops
### 10.1.1 LINK_DOWN pkt drops
* The following command can directly set netdevice link status down while physical link is up.  Packets will continue to be punted up to CPU but will drop with LINK_DOWN reason. CPU Tx direction should also show drops 
```
DUT# echo "Ethernet0=down" > /proc/bcm/knet/link
DUT# cat /proc/bcm/knet/link
Software link status:
  Ethernet0      down
```
### 10.1.2 KERNEL_DROP Rx pkt drops
* Send LACP packets to the DUT without the src port configured on a portchannel. The Linux kernel will drop the packet since the src port has not been registered as a portchannel member in the teamd kernel module (portchannel kernel component).

### 10.1.3 NO_BUFFER Tx pkt drops
* Decrease the KNET Tx buffers to a low value (8 or 4) to increase chance of congestion then send flood ping to congest the CPU Tx path and induce out of DMA buffer resource Tx drops. This operation should be used for testing only with all interfaces down.  Changing the max number of Tx buffers may cause a crash if packets are being processed in KNET.
```
echo "max_tx_dcbs=4" > /proc/bcm/knet/dma
```
* Then check the Tx high watermark stats after sending CPU Tx burst.  
```
DUT# cat /proc/bcm/knet/dstats  | grep "Tx used DCBs hi wm"
  Tx used DCBs hi wm           4
```
## 10.2 Disabling CPU Pkt KNET debug stats
Add "enable_stats=0" kernel parameter when inserting the knetcb kernel module to completely disable KNET debug stats logic.  Modify the following file on the target and reboot.

```
vi /etc/init.d/opennsl-modules 

// on TD4 (ngknet modules)
insmod /lib/modules/4.19.0-9-2-amd64/extra/linux_ngknetcb.ko enable_stats=0

// on legacy devices
insmod /lib/modules/4.19.0-9-2-amd64/extra/linux-knet-cb.ko enable_stats=0
```