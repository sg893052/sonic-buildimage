# L3 Vlan Interface Scaling
Layer 3 Vlan Interface Scaling

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
| 0.1 | 11/25/2020  |   Amulya Makam     | Initial version                   |

# About this Manual
This document provides information about the Layer 3 Vlan interface scaling achieved in SONIC 4.0.0 release. 

# Scope
This document describes the high level design of Layer 3 Vlan interface scaling achieved using mystation tcam optimization and l2 table for mac termination. Implementation of warm reboot support for upgrade scenario.
Supported platforms include TD3

# Definition/Abbreviation
### Table 1: Abbreviations

| **Term**          | ***Meaning***                              |
|-------------------|--------------------------------------------|
| SONIC             |  Software for Open Networking in the Cloud |
| SVI               |  Switch Virtual Interface                  |
| SAG               |  Static Anycast Gateway                    |
| MCLAG             |  Multi Chassis Link Aggregation            |
| VSI               |  Virtual Switch Interface                  |

## 1.1 Functional Requirements
# 1 Requirement Overview
SONIC currently supports a maximum of 1k L3 Vlan interfaces on Broadcom hardware. By design L3 Interface scaling is limited by MyStationTcam entries in HW. One l3 interface in SONIC will consume one myStationTcam entry per MAC, if the L3 interface has multiple MAC say for SAG/VRRP, then as many number of MyStationTcam entries will be used. This is because for every L3 Interface in addition to MAC either Vlan or the phy port is used as the key. So if we have 1K SVI then it will consume all the available MyStationTcam entries (1K).

## 1.1 Functional Requirements
Scaling improvements
|--------------------------------------|------|-----|-----|-----|
| MAC                                  | TD3  | TD4 | TH  | TH2 |
|--------------------------------------|------|-----|-----|-----|
|System MAC configured SVI             | 4k   | 4k  | 4k  | 4k  |
|MCLAG Gateway MAC with same ip SVI    | 4k   | 4k  | 4k  | 4k  |
|MCLAG with unique ip SVI              | 4k   | 4k  | 1k  | 1k  |
|SAG MAC configured SVI                | 4k   | 4k  | 1k  | 1k  |
|VRRP MAC SVI                          | 4k   | 4k  | 1k  | 1k  |
|--------------------------------------|------|-----|-----|-----|

## 1.2 Configuration and Management Requirements
No new configuration or show commands introduced.

## 1.3 Scalability Requirements
Covered in Functional requirements

## 1.4 Warm Boot Requirements
Upgrade to higher version should take care of installing one entry per MAC and delete the individual station tcam entries per SVI on TH/TH2 platforms
Upgrade to higher version should take care of moving to l2 table from myStationTcam for all SVI interfaces on TD3 platform.

# 2 Design

## 2.1 Use L2 table for inner mac termination for TD3 & TD4
TD4 already uses L2 table for inner mac termination. No change for TD4 platform.
In TD3, for all SVI interfaces, L2 table will be programmed instead of MyStationTcam.
Entry will be added with respective mac,vfi and flags as BCM_L2_STATIC. One entry per each SVI will be programmed.If 4k VRRP SVI interfaces are configured ,8k L2 table entries will be consumed.Rest of the cases,4k L2 tables entries will be consumed.

## 2.2  MyStationTcam lookup key modification
Use only MAC as the key for MyStationTcam entries, this will give way to use only one MyStationTcam entry for all SVI interfaces, i.e, we could be able to scale to 4K L3 Interfaces. This change is for the following 2 MACs only.
1.	System MAC 
2.	McLAG Gateway MAC 
No change in VRRP VMAC programming and SAG MAC programming
For SVI interfaces the lookup keys is only (DMAC)
For Phy interfaces / RouterPorts the lookup key is retained the same [Port Id + DMAC]
For LAG router interfaces the lookup key is retained the same [Trunk Id + DMAC]

## 2.2.1 MyStationTcam programming
A new data structure is added and is managed by the sai data manager.This data structure handles the list of Vlan interfaces associated with the given mac and the corresponding MyStationTcam hardware id. 
When the first SVI interface configured with any of the above macs the corresponding tcam entry gets added. When the last SVI interface associated with the mac is deleted , the corresponding tcam entry will be deleted.

## 2.2.2 Routing behaviour differences
With the key change to only (MAC) from (MAC,Vlan), if the SVI interfaces are configured each with one type of MAC. Station tcam will look like below now.
For example 
1. System MAC - 3c2c992ed875
2. MCLAG Gateway MAC - 050608060500
drivshell>d chg my_station_tcam
d chg my_station_tcam
MY_STATION_TCAM.ipipe0[0]: <VALID=1,MASK=0x00000000ffffffffffff,MAC_ADDR_MASK=0xffffffffffff,MAC_ADDR=0x3c2c992ed875,KEY=0x000000003c2c992ed875,IPV6_TERMINATION_ALLOWED=1,IPV4_TERMINATION_ALLOWED=1,DATA=0x18,>
MY_STATION_TCAM.ipipe0[3]: <VALID=1,MASK=0x00000000ffffffffffff,MAC_ADDR_MASK=0xffffffffffff,MAC_ADDR=0x050608060500,KEY=0x00000000050608060500,IPV6_TERMINATION_ALLOWED=1,IPV4_TERMINATION_ALLOWED=1,DATA=0x18,>

### 2.2.3 Special cases
If a packet comes with (DA=System MAC ,Vlan = SAG vlan or MCLAG vlan or L2 vlan). It will be subjected to routing due to current station tcam entry.
If a packet comes with (DA=MCLAG Gateway MAC, Vlan = !MCLAG Vlan). It will be subjected to routing.
If a packet comes with (DA=Any of the above two macs, Vlan=L2). It will be subjected to routing.
Inorder to address these cases, added below  IFP drop rules during system bringup. Also, counters attached to each of the drop rules.

### 2.3 VSI Profile
For each SVI interface there is a VSI profile maintained to classify the SVI interfaces.
2 VSI profile bits are consumed. One to identify the SAG MAC vlan and another to identify the MCLAG Gateway MAC vlan.

#define _BRCM_SAI_VSI_L3_INTF                 (1<<0)
#define _BRCM_SAI_VSI_SUPPRESS_NEIGH_L3_INTF  (1<<1)
#define _BRCM_SAI_VSI_SAG_MAC_L3_INTF         (1<<2)------->SAG MAC VSI
#define _BRCM_SAI_VSI_ROUTER_MAC_L3_INTF      (1<<3)------->MCLAG Gateway MAC VSI
#define _BRCM_SAI_MAX_VSI_PROFILE_BITS_LENGTH  4

### 2.4 IFP Drop rules
Below drop rules will be programmed only in TH/TH2 devices. 
1.	DA=System MAC      Vlan = SAG VSI profile     ---> Drop
2.	DA=System MAC      Vlan = MCLAG VSI profile   ---> Drop
3.	DA=MCLAG GWMAC     Vlan = !MCLAG VSI profile  ---> Drop 
4.      DA=System MAC      Vlan = L2 vlan             ---> Drop

# 4 Warm Boot Support
Upgrade scenario will be supported. It will be make before break. On upgrade to higher version, in TH/TH2 devices one entry per each of the MACs will be programmed first, followed by deletion of all the individual entries programmed per each SVI interface. Incase of TD3 device, l2 table entry will be added first and then corresponding myStationTcam entry will be deleted.

# 5 Unit Test
Configure setup with base MCLAG config.
Configure SAG MAC,MCLAG Gateway MAC.
Configure SAG SVI interface, MCLAG Gateway MAC SVI interface and plain L3 SVI interface.

Testcase | Validate                                           | Remarks                  |
---------|----------------------------------------------------|--------------------------|
     1.  | Send l3 pkt with DA= System MAC vlan=SAG vlan      | Packet should be dropped |
     2.  | Send l3 pkt with DA= System MAC vlan=MCLAG vlan    | Packet should be dropped |
     3.  | Send l3 pkt with DA= System MAC vlan=L2 vlan       | Packet should be dropped |
     4.  | Send l3 pkt with DA= System MAC vlan=L3 vlan       | Packet should be routed  |
     5.  | Send l3 pkt with DA= SAG MAC vlan=MCLAG vlan       | Packet should be dropped |
     6.  | Send l3 pkt with DA= SAG MAC vlan=SAG vlan         | Packet should be routed  |
     7.  | Send l3 pkt with DA= SAG MAC vlan=L3 vlan          | Packet should be dropped |
     8.  | Send l3 pkt with DA= SAG MAC vlan=L2 vlan          | Packet should be dropped |
     9.  | Send l3 pkt with DA= MCLAG Gw MAC vlan=L2 vlan     | Packet should be dropped |
     10. | Send l3 pkt with DA= MCLAG Gw MAC vlan=SAG vlan    | Packet should be dropped |
     11. | Send l3 pkt with DA= MCLAG Gw MAC vlan=L3 vlan     | Packet should be dropped |
     12. | Send l3 pkt with DA= MCLAG Gw MAC vlan=MCLAG vlan  | Packet should be routed  |


# 6 Internal Design Information
NA

