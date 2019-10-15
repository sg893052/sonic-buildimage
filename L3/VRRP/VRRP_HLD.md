# Virtual Router Redundency Protocol (VRRP) HLD

#### Rev 0.1



[TOC]



# List of Tables

[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev  |    Date    |       Author        | Change Description                                           |
|:--:|:--------:|:-----------------:|:------------------------------------------------------------:|
| 0.1  | 09/05/2019 |   Dilip Kumar   | Initial version                                              |
|      |            |             |                    |
|      |            |             |                    |
|      |            |             |                    |
|      |            |             |                    |

# Definition/Abbreviation

### Table 1: Abbreviations

| **Term** | **Meaning**                        |
| -------- | ---------------------------------- |
| ARP      | Address Resolution Protocol        |
| IP       | Internet Protocol                  |
| LAG      | Link Aggregation Group             |
| LAN      | Local Area Network                 |
| MAC      | Media Access Control addresses     |
|          |                                    |
|          |                                    |
| VIP      | Virtual IP address                 |
| VLAN     | Virtual Local Area Network         |
| VMAC     | Virtual MAC address                |
| VRF      | Virtual Routing and Forwarding     |
| VRID     | Virtual Router Identifier          |
| VRRP     | Virtual Router Redundency Protocol |
|          |                                    |

# About this Manual

This document provides general overview of VRRP feature implementation based on RFC 3768 in SONiC. 



# 1 Introduction and Scope

This document describes the Functionality and High level design of the VRRP feature.

The Virtual Router Redundancy Protocol (VRRP) functionality is designed to eliminate the single point of
failure inherent in the static default routed environment. VRRP specifies an election protocol that
dynamically assigns responsibility of gateway router to one of the VRRP routers on a LAN. The VRRP
router controlling the IP address(es) associated with a virtual router is called the Master, and routes the
traffic. The election process provides dynamic fail-over in the forwarding responsibility should the Master
become unavailable. Any of the virtual router&#39;s IP addresses on a LAN can then be used as the default first
hop router by end-hosts. The advantage gained from using VRRP is a higher availability default path
without requiring configuration of dynamic routing or router discovery protocols on every end-host.




# 2 Feature Requirements

## 2.1 Functional Requirements

Following requirements are addressed by the design presented in this document:

1. Support VRRP v2 (IPv4) 

2. Support multiple VRRP instances (groups) per interface

3. Support VRRP on VLAN, PortChannel interfaces

4. Support uplink port tracking

5. Support preemption of a Master when a high priority VRRP node comes up

6. Support configurable priority for VRPP instance

7. Support confuration and management of various VRRP parameters

    

Following requirements are beyond scope of this release. 

1. VRRPv3 (IPv6) support
2. REST, SNMP access support to VRRP objects
3. VRRP support on a user VRF



## 2.2 Configuration and Management Requirements

This feature will support CLI and other management interfaces supported in SONiC.

1. Support configuration VRRP instances per interface
2. Support configuration of VIP, priority, hello interval and premeption for VRRP instance
3. Support configuration of uplink track port
4. Allow users to configure track port weight to provide flexible policy of Master to Standby switchover
5. Support dispplay of various VRRP parameters as well as states using CLIs.
6. Each VRRP configuration entities are stored in config_db.json file



## 2.3 Scalability Requirements

1. Max number of VRRP instances: 128
2. Max number of VRRP enabled interfaces: 128
3. Max number of VRRP instances per interface: 16
4. Max number of tracked interfaces per VRRP Instance: 8
5. Max IP addresses per VRRP instances: 4



## 2.4 Warm Boot Requirements

VRRP module is warm reboot compliant. That is, VRRP docker will be restarted as part of warm-reboot but will come up afresh and will build the state from scratch. The behavior of VRRP will be like Cold reboot.



# 3 Feature Description

## 3.1 Target Deployment use cases

The following are some of the deployment use cases for VRRP

- The Leaf nodes of the CLOS network to provide first hop redundency to connected devices

  

## 3.2 Functional Description

VRRP specifies an election protocol to provide the virtual router function described earlier. All protocol
messaging is performed using IP multicast datagrams, thus the protocol can operate over a variety of
multiaccess LAN technologies supporting IP multicast. Each VRRP virtual router has a single well-known
MAC address allocated to it. This document currently only details the mapping to networks using the IEEE
802 48-bit MAC address. The virtual router MAC address is used as the source in all periodic VRRP
messages sent by the Master router to enable bridge learning in an extended LAN.
A virtual router is defined by its virtual router identifier (VRID) and a set of IP addresses. A VRRP router
may associate a virtual router with its real addresses on an interface, and may also be configured with
additional virtual router mappings and priority for virtual routers it is willing to backup. The mapping
between VRID and addresses must be coordinated among all VRRP routers on a LAN. However, there is
no restriction against reusing a VRID with a different address mapping on different LANs. The scope of
each virtual router is restricted to a single LAN.
To minimize network traffic, only the Master for each virtual router sends periodic VRRP Advertisement
messages. A Backup router will not attempt to preempt the Master unless it has higher priority. This
eliminates service disruption unless a more preferred path becomes available. It&#39;s also possible to
administratively prohibit all preemption attempts. The only exception is that a VRRP router will always
become Master of any virtual router associated with addresses it owns. If the Master becomes unavailable
then the highest priority Backup will transition to Master after a short delay, providing a controlled
transition of the virtual router responsibility with minimal service interruption.
The VRRP protocol design provides rapid transition from Backup to Master to minimize service
interruption, and incorporates optimizations that reduce protocol complexity while guaranteeing
controlled Master transition for typical operational scenarios. The optimizations result in an election
protocol with minimal runtime state requirements, minimal active protocol states, and a single message
type and sender. The typical operational scenarios are defined to be two redundant routers and/or distinct
path preferences among each router. A side effect when these assumptions are violated (i.e., more than two redundant paths all with equal preference) is that duplicate packets may be forwarded for a brief period during Master election. However, the typical scenario assumptions are likely to cover the vast majority of deployments, loss of the Master router is infrequent, and the expected duration in Master election convergence is quite small ( &lt;&lt; 1 second ). Thus the VRRP optimizations represent significant
simplifications in the protocol design while incurring an insignificant probability of brief network
degradation.
Though VRRP standard protocol present in RFC 3768 is complete in itself, there are few
limitations/drawbacks of the protocol:

1. The point of failure which VRRP safeguards against is the interface on which VRRP instance is
present and the router as a whole. Even if all of the connectivity of master to the external network
fails, VRRP will still not trigger the failover to the backup gateway. Uplink port tracking feature has been introduced to overcome this limitation
2. Only master router sends the advertisements, and backups just listen to them. Since backups do
not send any periodic message, it is difficult to ascertain that the backups are still active, and the
healthiness of the backup routers can not be known until the failover happens.
3. VRRP owner has special privileges and can preempt any other router acting as master. This
usually causes unwanted temporary network disruption after the non-owner master had stabilized
after the failover from owner.
4. A non-owner master can not accept packets destined to the virtual IP address. Only the owner can
accept and respond to such packets.
5. Two virtual routers with same VRIDs but on different IP subnets can not co-exist on the same
VLAN because of the virtual MAC address clash.

### 3.2.1 Virtual Router Owner

VRRP router that has virtual router's IPv4 address(es) as real interface address is the owner of virtual router and has the highest priority. VRRP owner is allowed.

### 3.2.2 Virtual MAC Address

Following virtual MAC addresses would be used by the protocol (as per RFC 5798)

IPv4 case: **00-00-5e-00-01-{vrid}**

where, vrid is user configured 1-byte virtual router identifier. Same VRID can not be used for two virtual router groups in the same vlan for same ip protocol. However, same VRID can appear in multiple VLANs.

### 3.2.3    Preemption

Preemption is turned on by default.  Even if preemption is disabled, it does not affect the owner router since owner preempts the active master. Mastership switchover causes unnecessary temporary network disruption.

### 3.2.4    VRRP Advertisement Frame

VRRP control packets have IP protocol type as 112 (reserved for VRRP), and are sent to VRRP multicast address 224.0.0.18. Source MAC in VRRP control packets is virtual MAC address                                                               

### 3.2.5    ARP Request  and ND message Handling 

Only master responds to the ARP requests for virtual IP address. In ARP replies sent by master, the source MAC in Ethernet header and ARP payload is virtual MAC address.

### 3.2.6   Port Tracking

Port other than the VRRP instance interface can be tracked for up/down events. When port-tracking is enabled in the VRRP instance configuration, the tracked port's link status will be monitored. When a link down event is detected on a tracked-port, the track-priority/weight is subtracted from the current router’s priority value. Similarly, when link up event is detected on the tracked-port, the track-priority/weight is added to the router’s current priority value.

The dynamic change of router priority can trigger mastership switchover if the preemption is enabled. **However, if the router is an owner, the mastership switchover will not happen**.

Maximum number of interfaces that can be tracked for a virtual router is 8.

# 4 Feature Design



## 4.1 Design Overview

### 4.1.1 Basic Approach

Keepalived open source code is choosen for VRRP control plane code.

![VRRP Keepalived Design](images/VRRP_Keepalived_design.PNG "Figure : Design")


### 4.1.2 Container

A new container "vrrp" has been added to host VRRP protocol operation.

### 4.1.3 SAI Overview

VRRP virtual router interface can be created by setting the attribute  “SAI_ROUTER_INTERFACE_ATTR_IS_VIRTUAL” with **create_router_interface** API.

     * @brief RIF creation is a virtual RIF.
     *
     * Create a Virtual RIF object, which only programs the ingress router MAC.
     * This simplifies the management of VRRP master router's configuration in
     * SAI adapter, as defined by RFC 5798 (or similar proprietary protocols).
     * Using a Virtual RIF allows SAI to optimize resources, so neighbor entries
     * cannot be learned on a Virtual RIF. On a virtual RIF following attributes
     * are invalid: ADMIN state, MTU size, packet action and multicast enable.
     * Alternatively VRRP can also be configured using native RIF objects without
     * using VIRTUAL attribute, with the expectation that SAI adapter will consume
     * resources that will not be used.
     *
     * @type bool
     * @flags CREATE_ONLY
     * @default false
     */
    
    SAI_ROUTER_INTERFACE_ATTR_IS_VIRTUAL,
## 4.2 DB Changes

At a high level below are some of the interactions between relevant components and the DB involved for VRRP support in SONiC architecture.



![VRRP Arch](images/VRRP_architecture.PNG "Figure : Arch")
__Figure 1: VRRP Architecture__

### 4.2.1 CONFIG_DB changes

**VRRP_TABLE**

Producer:  config manager 

Consumer: VrrpMgr

Description: New table that stores VRRP configuration for per interface + VRID. 

Schema:

```
;New table
;holds the VRRP configuration per interface and VRID

key = VRRP_TABLE:interface_name:vrid
                          ; Interface name string like Vlan1 or PortChannel002 or Ethernet4
                          ; vrid is an integer
; field = value
vrid     = 1*3DIGIT       ; VRRP Instance Identifier
vip      = ip_address     ; Virtual IP address. This is a list of IP addresses
priority = vrrp_priority  ; Priority of VRRP instance
adv_interval = 1*3DIGITS  ; Advertisement interval for VRRP. Default = 1sec
state    = vrrp_state     ; String denoting the state of VRRP instance
version  = vrrp_version   ; VRRP version. Value will always be 2 for this release
pre_empt = "true"/"false" ; VRRP premption is enabled? Default is True
track_interface = track_interface ; List of interface tracked by VRRP instnace
  <Interface_Name>|weight|<weight>; This is repeated for the configured tracking interfaces 
```

Example:- 

**Key**: VRRP_TABLE:Vlan11:1

**Value**: 

​    'vrid': '1',
​    'vip': '4.1.1.100,', 

​    'priority': '50', 

​    'adv_interval': '1',
​    'state': '', 

​    'version': '2',
​    'pre_empt': 'True',
​    'track_interface':
​            'Ethernet7|weight|20,

​            PortChannel001|weight|40

### 4.2.2 APP_DB Changes

**VRRP_TABLE**

Producer:  VrrpMgr 

Consumer: VrrpOrch

Description: This is a new table that contains VRRP state information 

Schema:

```
; New table
; holds the VRRP state and VMAC information

key = VRRP_TABLE:interface_name:vip:type
    interrace_name        ; interface name as a string. Vlan, Ethernet or PortChannel
    vip                   ; virtual IP address in a.b.c.d/32 format
    type                  ; IP address type string. only ipv4 supported currently
    
; field = value
vmac = virtual_mac_address ; Virtual MAC address asscoaited with VRRP
```

Example:- 

**Key**: VRRP_TABLE:Vlan1000:[40.10.8.101/32:ipv4](http://40.10.8.101/32:ipv4)

**Value**: "vmac":"00:00:5e:00:01:08"

## 4.3 Modules Design and Flows



<TODO: Vijay add some details here>


## 5 CLI

SONIC CLICK based configuration and monitoring CLIs have been introduced to 


### 5.1 Configuration Commands

```
configure interface vrrp add <interface_name> <vrid>
This command adds/enables a VRRP instance on an interface.
- interface_name - name of interface (Ethernet or Vlan or PortChannel) over which VRRP is to be enabled.
- vrid - VRRP instance identifier.
 
configure interface vrrp remove <interface_name> <vrid>
This command removes a VRRP instance from an interface.

configure interface vrrp vip add <interface_name> <vrid> <virtual_ip_address>
This command adds a virtual IP address for a VRRP instance on an interface. User is allowed to craete multiuple VIPs for a VRRP instance. VIP must fall in the interface's subnet.
- interface_name - name of interface (Ethernet or Vlan or PortChannel) over which VIP is being added
- vrid - VRRP instance identifier.
- virtual_ip_address - VIP address in dotted decimal IPv4 address

configure interface vrrp vip remove <interface_name> <vrid> <virtual_ip_address>
This command deletes a already configured VIP from a VRRP instance

configure interface vrrp priority <interface_name> <vrid> <priority>
This command configures priority for a VRRP instance
- priority - a number between 1 and 254. Default is 100.

configure interface vrrp adv_interval <interface_name> <vrid> <interval>
This command configures VRRP periodic advertisement interval for a VRRP instance
- interval - a number between 1 and 255. Unit is in seconds. Default is 1sec. 

configure interface vrrp pre_empt enable <interface_name> <vrid>
This command enables premeption of a Master when a higher priority VRRP router arrives
- enable - Enable premeption. Default is enabled. 

configure interface vrrp pre_empt disable <interface_name> <vrid>
This command disables premeption of a Master when a higher priority VRRP router arrives
- disable - Disable premeption. Default is enabled. 

configure interface vrrp track_interface add <interface_name> <vrid> <track_interface> <weight>
This command adds a track interface to a VRRP Instance. A maximum of 8 track interfaces can be added to a VRRP instance. 
- track_interface - Interface to track. Interface can be Ethernet, Vlan or PortChannel
- weight - weight or importance assigned to the track_interface. When track interface goes down, the priority of VRRP instance will be reduced by weight

configure interface vrrp track_interface remove <interface_name> <vrid> <track_interface> 
This command removes a already configured track interface from a VRRP Instance. 
```

### 5.2 Show Commands

```
show vrrp
 - lists all the VRRP instances including their current state
 Sample output:-
admin@sonic:~$ show vrrp
Interface_Name  VRID   State             VIP  Cfg_Prio  Curr_Prio
         Vlan1     1  Backup       4.1.1.100       100        120
         Vlan2     2  Master       4.1.2.100       100        100
         Vlan3     3  Backup       4.1.3.100       100        100
         Vlan4     4  Backup       4.1.4.100       100        100
         Vlan5     5  Master       4.1.5.100       100        100


show vrrp <interface_name> <vrid>
 - This command displays data about a VRRP instance in detail
 Sample output:-
admin@sonic:~$ show vrrp Vlan1 1
Vlan1, VRID 1
Version is 2
State is Backup
Virtual IP address:
  4.1.1.100
Virtual MAC address is 0000.5e00.0101
Track interface:
  Intfname       State  Priority
  Ethernet7      Up     10
  PortChannel001 Up     10
Configured Priority is 100, Current Priority is 120
Advertisement interval is 1 sec
Preemption is enabled
```


## 6 Serviceability and Debug

The existing logging mechanisms shall be used. Proposed debug framework shall be used for internal state dump.

## 7 Warm Reboot Support

Currently, warm-reboot is not supported for VRRP. That is, warm-reboot will simply restart the VRRP docker without VRRP storing any data for warm restart. 


