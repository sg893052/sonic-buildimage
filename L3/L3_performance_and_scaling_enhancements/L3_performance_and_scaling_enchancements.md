# L3 Scaling and Performance Enhancements
Layer 3 Scaling and Performance Enhancements
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
| 0.1 | 04/18/2019  |   Arvind      | Initial version                   |

# About this Manual
This document provides information about the Layer 3 performance and scaling improvements done in Sonic 2.0 release .
# Scope
This document describes the high level design of Layer 3 performance and scaling improvement.



# 1 Requirement Overview
## 1.1 Functional Requirements

 - __Scaling improvements__
	 - Support 1k L3 interfaces
	 - Support for 32k IPv4 Host entries
	 - Support for 16k IPv6 Host entries
	 - Support for 32k next-hop entries


 - __Performance improvements__
   - Reduce the IPv4 and IPv6 route programming time
   - Reduce unknown IPv4/IPv6 host learning time
   - Reduce the time taken to display output with the following *"show commands"*
     - **_show arp_**
     - **_show ndp_**
     - **_show ip interface_**
     - **_show ip route_**
     - **_show ipv6 route_**

 - __Hardening items__
   
   Listed below are the items for hardening in Sonic 2.0 release, which will be covered in our testing.
   
   - **Route scaling**
   
     On all Broadcom platforms ALPM is enabled by default  The following tables show the route scaling for Tomhawk,Tomahawk-2 and Trident-3 platforms
   
     | Route                    | Scale in AS7712 | Scale in AS7816 | Scale in AS73262 |
     | ------------------------ | --------------- | --------------- | ---------------- |
     | IPv4                     | 128k            | 200k            | 120k             |
     | IPv6 prefix <=64b        | 48k             | 50k             | 65k              |
     | IPv6 prefix >64b         | 14k             | 25k             | 40k              |
     | IPv4 + IPv6 prefix <=64b | 32k +32k        | 46k + 36k       | 43k +36k         |
   
     
   
   - **ECMP**
   
     The following ECMP scaling combinations will be using in testing.
   
     - 512 groups X 32 paths
   
     - 256 groups X 64 paths
   
     - 128 groups X 128 paths
     
       


## 1.2 Configuration and Management Requirements
No new configuration or show commands introduced.

## 1.3 Scalability Requirements
Covered in Functional requirements


## 1.4 Warm Boot Requirements

There are no specific changes done for Warm-boot in this feature, however  testing will done to make sure no change affect the warm boot time. 

# 2 Design

## 2.1 Scaling improvements
### 2.1.1. Increase the number of L3 interfaces

Sonic currently supports a maximum of 760 L3 interfaces on Broadcom hardware.

The Layer3 interfaces include

- Router interfaces
- L3 Port Channels
- L3 Vlan Interfaces 

We will increase the scale of L3 interfaces to 1K in Sonic 2.0

The current bottleneck for scaling L3 interfaces is the space required to install the trap entries for IP direct broadcast in the Hardware

In SAI 3.6 currently the tcam entry width used of the trap entry for IP direct broadcast is 480 bit so we don't have enough TCAM space to program trap entry for all the 1k L3 interfaces. SAI returns error after configuring 760 L3 interfaces. To increase the L3 interface scaling, we will be allocating a new TCAM db with a key length of 80 bits for IP direct broadcast trap entries. 

Additionally, in SAI 3.6 VLAN 4095 will be shared for all L3 ports and L3 Port-channels as opposed to using one vlan for every L3 port or port-channel previously. This change will be remove the need for reserving Vlan range for L3 ports or L3 Port-channels. 

### 2.1.2 Improvement in number of Host Entries

Sonic currently supports around 2400 host entries.

The reason for this is the current setting from the arp entries in the ARP module in kernel. With the current setting ARP entries are getting purged from kernel(either through age-out of 60 seconds or purging in 5 seconds because of garbage collector ) while the later set of arp entries was still getting added.  There was no keep-alive to keep the arp entries intact .

The sequence of add/remove is in such a way that we were never able to cross ~2400 entries .

In the kernel Arp module the following attributes governs how many Arp entries are key in the Kernel Arp cache
>gc_thresh1 (since Linux 2.2)
The minimum number of entries to keep in the ARP cache. The garbage collector will not run if there are fewer than this number of entries in the cache. Defaults to 128.

>gc_thresh2 (since Linux 2.2)
The soft maximum number of entries to keep in the ARP cache. The garbage collector will allow the number of entries to exceed this for 5 seconds before collection will be performed. Defaults to 512.

>gc_thresh3 (since Linux 2.2)
The hard maximum number of entries to keep in the ARP cache. The garbage collector will always run if there are more than this number of entries in the cache. Defaults to 1024.

To increase the number of host entries these attributes will be changed to following values
>net.ipv4.neigh.default.gc_thresh1=42000
>net.ipv4.neigh.default.gc_thresh2=43000
>net.ipv4.neigh.default.gc_thresh1=44000

With these changes we will be able to support 32k IPv4/16K IPv6 host entires 

### 2.1.3 Support of 32k Next-hops

The support for 32k Next-hops was constrained by the ARP/ND scaling and L3 interface scaling.
After the improvements done in L3 interface and ARP/ND scaling as mentioned above, we target to scale to 32k nexthops in Sonic 2.0

## 2.2 Performance Improvements

This section elaborates the changes done to improve L3 performance in Sonic

### 2.2.1 Route installation time

The tables below captures the baseline route programming time for  IPv4 and IPv6 prefixes Sonic.

The following testing method was used for measuring the route programming time

- Start a script which  get a the output of bcrm command "l3   defip show | wc -l" to get the number of routes programmed in HW       
- Start   injecting routes from the TGEN 
- Script notes the time when the first route was installed (T1) 
- Script notes the time when the last route was installed(T2) 
- Time   to program routes = T2 -T1
- Repeat   the test 3 times and note down the average.                      

### Table 2: IPv4 prefix route programming time

| Routes                  | time taken on AS7712 | time taken on AS7816 | time take on AS73262 |
| ----------------------- | -------------------- | -------------------- | -------------------- |
| 10k IPv4 prefix routes  | 11 seconds           | 10 seconds           | 5 seconds            |
| 30k IPv4 prefix routes  | 30 seconds           | 27 seconds           | 16 seconds           |
| 60k IPv4  prefix routes | 48 seconds           | 48 seconds           | 31 seconds           |
| 90k IPv4 prefix routes  | 68 seconds           | 68 seconds           | 50 seconds           |



### Table 3: IPv6 prefix route programming time

| Routes                             | time taken on AS7712 | time taken on AS7816 | time take on AS73262 |
| ---------------------------------- | -------------------- | -------------------- | -------------------- |
| 10k IPv6 route with prefixes <=64b | 10 seconds           | 11 seconds           | 6 seconds            |
| 30k IPv6 route with prefixes >64b  | 30 seconds           | 27 seconds           | 17 seconds           |
| 10k IPv6 route with prefixes > 64b | 11 seconds           | 10 seconds           | 5 seconds            |



#### 2.2.1.1 Proposed optimizations for reducing the route programming time.

- <u>Using sairedis bulk route apis</u>

  In Sonic architecture, the routeorch submodule in Orchagent processes route table updates from the APP_DB and calls the sairedis apis to put these routes in ASIC_DB. 
  Currently Orchagent processes each route and puts in the ASIC_DB one at a time. The redis pipelining allows for some level of bulking when putting entries in ASIC_DB but still one DB message is generated for every route.

  Further bulking can be done by using the sairedis bulk apis for route creation and deletion.

  By using Sairedis bulk apis, orchagent will call these apis with a list of routes and their attirbutes.
  The meta_sai layer in sairedis iterates over this route list and creates the meta objects for every route but only one redis DB message will be generated for the route list. Therefore using the sairedis bulk apis reduces the number of redis messages. The ASIC doesn't support bulk route creation/deletion,so syncd still processes one route at time and  updates the ASIC.

  So, the saving achieved by using bulk APIs will be number of redis message generated.

  In Sonic 2.0 bulking of Route updates will be enabled in Orchagent. Orchagent will bulk 64 updates and send to sairedies. 
  A new timer will be introduced in orchagent to flush the outstanding updates after timeout.

- <u>Optimization in Fpmsyncd</u>

  Currently in Sonic, the kernel route table is updated very fast , 10k routes are updated in a second,  but there is delay in fpmsyncd in updating the APP_DB.  It take 9 seconds for the 10k routes to be put in the APP_DB. 

  One reason for this the time taken by fpmsyncd to read the fpm socket and processes  the netlink message coming from Zebra and updates the APP_DB. 

  We will add optimization in Fpmsyncd to process the netlink messages faster, thereby updating the APP_DB at a faster rate.
 

- <u>Optimization in the sairedis</u>.

  In Sairedies every sai object is serialized in JSON format while creating the meta sai objects and updating the ASIC_DB.  For serializing it uses json dump functionality to convert the objects in JSON. format. 
  This json dump function is provided by a open source JSON library. [[link to the open-source project](https://github.com/nlohmann/json)]

  Currently Sonic uses version 2.0 of this library, latest version however is version 3.6.1
  There have a been a few bug fixes/improvements done to the *dump()* from v 2.0 to v 3.6.
  We will cherry-picking some of this fixes to improve the performance.

With the above mentioned optimizations we target to get 30% reduction in the route programming time in Sonic 2.0 release.

### 3.2.2 Reduce the unknown host learning time

For measuring time taken to learn ARP entries we used a script . The operation done by the script was to :-

- Record the current system time
- Find out the number of arp entries though show cmd . Here there is no need to dump all arp entries , only count is enough .
- Sleep for 0.5 seconds before next iteration

These steps were executed in a loop in the script. The script run is triggered first then arp packets for hosts were sent from spirent. The initial time is recorded when the script first observes the number of arp entries start to increase. After a time interval the number of arp entries will not increase and will remain constant . This time is recorded as final time. The difference of final time and initial time gives the learn time for scaled arp entries . For ND entries instead of show arp , we use show ndp in the script.

**Optimizations of reducing the learning time**

- One of the reasons high learn time is because of linux garbage collector kicking in. While the arp learning is still in progress, because of threshold values getting crossed the purging starts. Due to this the total arp number shown during this process is not stable and takes higher time to stabilize. The changes proposed to increase the host entry scaling will also improve the performance

- Increase rate of the ARP/ND packets coming to the CPU. Currently the max rate for ARP/ND is 600 packets, we will be increasing it to higher number(8000) to improve the learning time.

  

###3.3 show CLI command enchancements

#### 3.3.1 show arp/ndp command improvement.

The current implemetation of the cli script for "show arp" or "show ndp" fetches the whole FDB table to get the outgoing interface incase the L3 interface is a VLAN L3 Interface. 
This slows down the show command. We will make changes to the CLI script to get FDB entries only for this specific ARP/ND instead of getting the whole FDB table. 

These changes will improve the performance of the command significantly

# 4 Warm Boot Support
No specific changes are planned for Warm boot support as these are exisiting features.

However, testing will done to make sure the changes done, for scaling or performance improvements, won't affect the Warm boot functionality. 

# 5 Unit Test
[UT doc](https://docs.google.com/spreadsheets/d/1s737l3mw9tagTf7ByiMuvNcqtB-bbnirLaOcU5TU9TU/edit#gid=650565969)

# 6 Internal Design Information

Internal BRCM information to be removed before sharing with the community

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTQ0MDM0ODU0NSwtMTUzOTc3NTY3NSw5OT
M5NDE1NzAsMTM1NDA2NTgwMSw2MDExODQ0Ml19
-->
