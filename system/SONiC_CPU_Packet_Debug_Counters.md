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
| 0.1 | 04/13/2019  |   Michael Li       | Initial version                   |

# About this Manual
This document provides general information about the SONiC CPU Packet Debug Counters feature implementation in SONiC.
# Scope
This document describes the high level design of SONiC CPU Packet Debug Counters feature.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| XYZ                      | Term description                    |

# 1 Feature Overview
The CPU Packet Debug Counters provides BRCM KNET driver level Rx and Tx statistics for each protocol type. SONiC KNET currently provides CPU packet counters based on CPU queues which indirectly provides Rx per protocol counters since each protocol is assigned a separate CPU queue. This feature enhances CPU packet statistics by adding per protocol counters for CPU Rx and Tx directions.

## 1.1 Requirements

### 1.1.1 Functional Requirements

1. Support Tx and Rx per protocol KNET driver level packet counters from KNET procfs
2. Support Tx and Rx per protocol Linux/KNET dropped packet counters from KNET procfs
3. Support clearing of Tx and Rx counters
4. Output of statistics will be similar to the following format:

```
DUT# cat /proc/bcm/knet-cb/pkt_stats
Total rx = 101127
Total tx = 235644
protocol          rx   rx_err       tx   tx_err
--------------------------------------------------
UDLD               0        0        0        0
STP                0        0        0        0
PTP                0        0        0        0
LLDP           24291        0       54        0
VRRP               0        0        0        0
ICCP               0        0        0        0
OSPF               0        0        0        0
BGP                0        0        0        0
PIM                0        0        0        0
IGMP               0        0        0        0
ARP_SUPPRESS       0        0        0        0
ARP             8953        0     9103        0
DHCP               0        0        0        0
ICMP               0        0        0        0
IP2ME              0        0        0        0
IP2ME_SUBNET       0        0        0        0
NAT                0        0        0        0
IPMTU              0        0        0        0
SFLOW              0        0        0        0
DROP               0        0        0        0
UNSPEC             0        0        0        0
```

### 1.1.2 Configuration and Management Requirements
Statistics will be provided via KNET procfs file

