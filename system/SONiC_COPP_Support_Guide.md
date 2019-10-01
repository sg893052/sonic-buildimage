                                                  
# COPP Support Document               
#### Rev 0.1 

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)            
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)                        
  * [Definition/Abbreviation](#definitionabbreviation)
  * [Protocol Traps Supported](#protocol-traps-supported)
  * [COPP Configuration](#copp-configuration)
	  * [Default COPP Settings](#default-copp-settings)
	  * [Global CPU Rx Rate Limit](#global-cpu-rx-rate-limit)
      * [COPP Config Commands](#copp-config-commands)
      * [COPP Show Commands](#copp-show-commands)
  * [CPU Scheduling Behaviour](#cpu-scheduling-behaviour)
	  * [CPU Rx](#cpu-rx)
	  * [CPU Tx](#cpu-tx)
# List of Tables                                      
* [Table 1:Abbreviations](#table-1-abbreviations)
* [Table 2:Protocol Traps](#table-2-protocol-traps)
* [Table 3:Default COPP](#table-3-default-copp)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 09/30/2019  |   Driver Team    | Initial version                   |

# About this Manual
This document provides supporting information about COPP in Broadcom SONiC to PMs, solution engineers and other wider audience.
# Scope                                                                                  
This document describes COPP settings, implementation, configuration and associated CPU path, scaling information in Broadcom SONiC

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| ARP                      | Address Resolution Protocol |
| BFD                      | Bidirectional Forwarding Detection  |
| BGP                      | Border Gateway Protocol             |
| DHCP                     | Dynamic Host Configuration Protocol |
| LACP                      | Link Aggregation Control Protocol      |
| NDP                      | Neighborhood Discovery Protocol |

# Protocol Traps Supported
The table below lists all protocol traps supported in Arlo release.

### Table 2: Protocol Traps
|    Protocols      | Available from Release | Classification Criteria |
|----------------|-------------|-----------------|
|TTL Error|1.0 | TTL == 0 or TTL == 1|
|LACP |1.0|(EType==0x8809) && (MAC DA == {01:80:C2:00:00:02}) |
|BGP v4| 1.0 |(EType==0x800) && (IpProtocol == 6) && "For Us" && ((L4SrcPort == 179) or (L4DstPort == 179)) |
|BGP v6| 1.0 |(EType==0x86DD) && (IpProtocol == 6) && "For Us" && ((L4SrcPort == 179) or (L4DstPort == 179)) |
|DHCP v4|1.0|(EType == 0x800) && (IpProtocol == 17) && ((L4SrcPort == 68) && (L4DstPort == 67)) or (L4SrcPort == 67) && (L4DstPort == 68)) |
|DHCP v6|1.0|(EType == 0x86DD) && (IpProtocol == 17) && (L4DstPort == 547 or 546)|
|SSH|1.0|(IpProtocol == 6) && "For Us" && ((L4SrcPort == 22) or (L4DstPort == 22))|
|SNMP|1.0|(IpProtocol == 17) && "For Us" && ((L4SrcPort == 161) or (L4DstPort == 161))|
|NDP|1.0|(EType == 0x86DD) && (IPv6NextHeader == 58) && (IPv6HopLimit == 255) && (Type Code is 133 ~ 137) |
|ARP|1.0|(EType == 0x806)|
|LLDP|1.0|(EType==0x88CC) && (MAC DA == {01:80:C2:00:00:00} or {01:80:C2:00:00:03} or {01:80:C2:00:00:0e}) |
|UDLD|1.0|(MAC DA == {01:00:0C:CC:CC:CC}|
|BFD v4|2.0|(EType==0x800) && (IpProtocol == 17) && "For Us" && (L4DstPort == 3784 or 4784)|
|BFD v6|2.0|(EType==0x86DD) && (IpProtocol == 17) && "For Us" && (L4DstPort == 3784 or 4784)|
|STP|2.0|(MAC DA == {01:80:C2:00:00:00})|
|PVRST|2.0|(MAC DA == {01:00:0C:CC:CC:CD}|
|VRRP|2.0|(EType==0x800) && (IpProtocol == 112)|

# COPP Configuration 

## Default COPP settings
The default COPP settings makes use of 8 CPU Rx queues to assign various control traffic to CPU. COPP has been reconfigured to group CPU control traffic types based on rates and latency requirements. This allows separation of queues for potentially higher rate protocols (example: scaled STP, BGP, ARP) from lower rate ones and apply appropriate rate limits. Also, protocols with latency requirements (keep-alive protocols) can be separated from higher rate protocols.

The table below lists Broadcom SONiC default CPU queue mappings, protocol scaling support and rate-limits.

### Table 3: Default COPP

|    Protocols      |CPU Queue |Rate Limit (PPS) | Comments |
|----------------|-------------|-----------------|---------|
|BFD, LACP, UDLD, LLDP| 7    |6000 | Latency sensitive group
|STP, RPVST         |6| 16000 |High scale/rate group
|VRRP   |5|600| |
|BGP | 4 | 10000 | Minimize convergence of high scale routes |
| ARP Req, ARP Resp, NDP | 3 | 6000 | Increase rate for L2 performance |
| IP2ME, DHCP | 2 | 6000 | Increase rate for ZTP inband download |
|SRC/DST NAT miss, IP MTU | 1 | 600 | Potentially line rate |
| Default | 0 | 600 | |

## Global CPU Rx Rate Limit
On top of above mentioned HW rate limiters, there is a global SW CPU Rx rate limit of 30K packets/second that can be configured dynamically with CLI mentioned below.

## COPP Config Commands

**config copp rx-rate**

This command configures the global max rate limit to CPU in packets/second.

  - Usage:  
    config copp rx-rate <100-100000>

- Example:
   ```
   root@sonic:/home/admin# config copp rx-rate 20000
   ```

**config copp rx-burst**

This command configures the global max burst limit to CPU in number of packets.

  - Usage:  
    config copp rx-burst <10-10000>

- Example:
   ```
   root@sonic:/home/admin# config copp rx-burst 2000
   ```

## COPP Show Commands

**show copp config**  
This command displays the COPP configuration.

  - Usage:  
    show copp config

- Example:
   ```
   admin@sonic:~$ show copp config
   {
     "COPP_TABLE:default": {
       "value": {
         "cbs": "600", 
         "cir": "600", 
         "meter_type": "packets", 
         "mode": "sr_tcm", 
         "queue": "0", 
         "red_action": "drop"
       }
     }, 
     "COPP_TABLE:trap.group.arp": {
       "value": {
         "cbs": "6000", 
         "cir": "6000", 
         "meter_type": "packets", 
         "mode": "sr_tcm", 
         "queue": "3", 
         "red_action": "drop", 
         "trap_action": "copy", 
         "trap_ids": "arp_req,arp_resp,neigh_discovery", 
         "trap_priority": "3"
       }
     }
   }
   ```
**show copp rate-limit**

This command displays the global max rate limit to CPU and the current rate of packets to CPU.

  - Usage:  
    show copp rate-limit

- Example:
   ```
   admin@sonic:~$ show copp rate-limit
   Rx0 max rate     30000
   Rx0 max burst     3000
   Rx0 rate             0
   Rx0 tokens        3000
   ```

# CPU Scheduling Behaviour
## CPU Rx
The CPU Rx queues are scheduled in Weighted Fair Queue Round Robin fashion which give more weight to CPU queue with higher priority.  The weight for each queue is pre-determined and not user configurable.

## CPU Tx
CPU Tx is configured to send to 
-  Internal UC Queue 9 with Strict priority scheduling above all data queues 0-7 for devices that support 10 physical port UC queues,
- Queue 7, for devices that support 8 physical port UC queues, . 

CPU Tx queue is not user configurable.

