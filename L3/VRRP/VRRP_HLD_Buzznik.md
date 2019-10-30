# Virtual Router Redundency Protocol (VRRP) HLD

#### Rev 0.1



[TOC]



# List of Tables

[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev  |    Date    |       Author        | Change Description                                           |
|:--:|:--------:|:-----------------:|:------------------------------------------------------------:|
| 0.1  | 09/05/2019 |   Dilip Kumar   | Initial version                                              |
| 0.2 | 09/11/2019 | Dilip Kumar | Addressed review comments from Ben and others. Minor edits to complete the unifished sections. |
| 0.3 | 10/10/2019 | Vijay Kumar | Added VRRP version 3 support |
| 0.4 | 10/30/2019 | Preetham Singh | Added Static Anycast Gateway support |
|      |            |             |                    |
|      |            |             |                    |

# Definition/Abbreviation

### Table 1: Abbreviations

| **Term** | **Meaning**                                 |
| -------- | ------------------------------------------- |
| ARP      | Address Resolution Protocol                 |
| IP       | Internet Protocol                           |
| LAG      | Link Aggregation Group                      |
| LAN      | Local Area Network                          |
| MAC      | Media Access Control addresses              |
| VIP      | Virtual IP address                          |
| VLAN     | Virtual Local Area Network                  |
| VMAC     | Virtual MAC address                         |
| VRF      | Virtual Routing and Forwarding              |
| VRID     | Virtual Router Identifier                   |
| VRRP     | Virtual Router Redundency Protocol          |
| VRRPv3   | Virtual Router Redundency Protocol verson 3 |
| IPv6     | Internet Protocol version 6                 |
| SAG      | Static Anycast Gateway                      |
| VM       | Virtual Machine                             |
| MCLAG-VARP| Active-Active gateway redundancy on MCLAG  |

# About this Manual

This document provides general overview of VRRP feature implementation based on RFC 5798 & SAG feature implementation in SONiC. 



# 1 Introduction and Scope

Virtual Router Redundancy Protocol (VRRP) functionality is designed to eliminate the single point of
failure inherent in the static default routed environment. VRRP specifies an election protocol that
dynamically assigns responsibility of gateway router to one of the VRRP routers on a LAN. The VRRP
router controlling the IP(v6) address(es) associated with a virtual router is called the Master, and routes the
traffic. The election process provides dynamic fail-over in the forwarding responsibility should the Master
become unavailable. Any of the virtual router&#39;s IP(v6) addresses on a LAN can then be used as the default first
hop router by end-hosts. The advantage gained from using VRRP is a higher availability default path
without requiring configuration of dynamic routing or router discovery protocols on every end-host.

Static Anycast Gateway (SAG) allows multiple switches to simultaneously route packets from a common IP address in an active-active router configuration.
Each switch is configured with the same set of virtual IP addresses on corresponding VLAN interfaces and a common virtual MAC address.

Static Anycast Gateway feature provides ability to configure same set of gateway IPs on Vlan interfaces across routers without need to run any election protocol.
Unlike VRRP, SAG does not run any state machine for master router election. All access routers configured with same set of SAG IP provides gateway functionality.
This allows multiple routers to simultaneously route packets destined to these gateway IPs.
Each switch is configured with same set of Gateway IPs and common Gateway MAC.

In MCLAG deployments, SAG is preferred over MCLAG-VARP since SAG provides gateway functionality with Active-Active router configuration across MCLAG.

In IP Fabric deployments, SAG is preferred over VRRP and MCLAG-VARP since SAG provides gateway across MCLAG nodes which is necessary
for seamless VM move across servers connected to different leaf nodes.

# 2 Feature Requirements

## 2.1 Functional Requirements

Following requirements are addressed by the design presented in this document:

1. Support VRRPv2 (IPv4), VRRPv3(IPv4, IPv6), SAG(IPv4, IPv6) 

2. Support multiple VRRP instances (groups) per interface

3. Support VRRP on VLAN, PortChannel and Ethernet interfaces

4. Support Uplink interface tracking

5. Support preemption of a Master when a high priority VRRP node comes up

6. Support configurable priority for VRRP instance

7. Support configuration and management of various VRRP parameters

8. VRRP support on non-default VRF

9. Support REST access to VRRP objects

10. Support Static Anycast Gateway functionality on VLAN interface.

Following requirements are beyond scope of this release. 

1. SNMP and gNMI access support to VRRP objects



## 2.2 Configuration and Management Requirements

This feature will support configuration and display CLIs to control and monitor VRRP parameters

1. Support configuration of VRRP instances per interface

2. Support configuration of VIP, priority, hello interval and preemption for VRRP instance

3. Support configuration of uplink interface track

4. Support configuration of VRRP protocol version

5. Allow users to configure track interface weight to provide flexible policy of Master to Backup switchover

6. Support display of various VRRP parameters as well as states using CLIs.

7. Support configuration of IPv4 & IPv6 SAG on VLAN interfaces.

8. Support configuration of Global SAG gateway MAC.

   

## 2.3 Scalability Requirements

1. Max number of VRRP instances: 128
2. Max number of VRRP enabled interfaces: 128
3. Max number of VRRP instances per interface: 16
4. Max number of tracked interfaces per VRRP Instance: 8
5. Max IP addresses per VRRP instance: 4
7. Max number of SAG IPv4 instance on a interface: 16
8. Max number of SAG IPv6 instance on a interface: 16



## 2.4 Warm Boot Requirements

VRRP module is warm reboot compliant. That is, VRRP docker will be restarted as part of warm-reboot but will come up afresh and will build the state from scratch. The behavior of VRRP will be like Cold reboot.

SAG does not store any state information. After warm-reboot, SAG IPs will be programmed once corresponding Vlan interface comes up.



# 3 Feature Description

## 3.1 Target Deployment use cases

The following are some of the deployment use cases for VRRP and SAG

- The Leaf nodes of the Clos network to provide first hop redundency to connected devices
- SAG to provide Active-Active gateway redundancy in MCLAG deployment across MCLAG.
- SAG to provide Active-Active gateway redundancy in IP-Fabric deployment to provide same gateway across multiple leaf nodes.
 
## 3.1.1 VRRP & SAG Use cases:

### **VRRP on MCLAG:**
- VRRP when configured on MCLAG, Data traffic from Server can take sub-optimal path over MCLAG peer link.
- In these deployments, MCLAG-VARP OR SAG is preferred over VRRP.

![VRRP_MCLAG_USECASE1](image/VRRP_MCLAG_Usecase1.png "Figure 3.1.1 : VRRP on MCLAG")

### **SAG configured on MCLAG and remote leaf nodes, BGP-EVPN is used for ARP/ND Synchronization:**

- With this VM move from Server-1 to Server-2 will ensure non-disruptive data path from VM1, since same gateway exists on remote leaf node.
- BGP EVPN is used in these types of topologies, VMs on Server-1 and Server-2 will be configured with same SAG gateway
- Irrespective of leaf node on which ARP/nd request is originated to VM, ARP response will be consumed by receiving leaf node, learning corresponding VM.
  These MACIP route(Type-2) will be synchronized to all other leaf nodes using BGP EVPN.
  
![SAG_IPFABRIC_UC2](image/SAG_IPFabric_Usecase2.png "Figure 3.1.2 : SAG on IP Fabric")

### **SAG configured on MCLAG and remote leaf nodes interconnected with L2 Cloud:**

#### Issue:
- There is no ARP/ND Sync between MCLAG Pair and Leaf-3.
- If SAG alone is configured, ARP request from Leaf3 would use SAG IP as source.
- ARP response will get consumed by Leaf1 or Leaf2 and VM1 will remain unresolved on Leaf3 resulting in traffic loss

![SAG_NOREALIP_UC3](image/SAG_NOREALIP_UC3.png "Figure 3.1.3.1 : SAG Alone on Vlan interface")

#### Solution:
- SAG & Real interface IP is configured on MCLAG & remote Leaf nodes
- Since there is no ARP/ND synchronization across remote leaf nodes and across MCLAG, ARP/nd resolution should be triggered using unique real interface IP.
- ARP/ND request to VMs will be generated using real interface IP rather than SAG IP so that response will be received by the originator leaf node.

![SAG_L2_UC3](image/SAG_REALIP_UC3.png "Figure 3.1.3.2 : SAG + Real IP")

## 3.2 Functional Description of VRRP

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
Though VRRP standard protocol present in RFC 3768 and RFC 5798 are complete in itself, there are few
limitations/drawbacks of the protocol:

1. The point of failure which VRRP safeguards against is the interface on which VRRP instance is
present and the router as a whole. Even if all of the connectivity of master to the external network
fails, VRRP will still not trigger the failover to the backup gateway. Uplink interface tracking feature has been introduced to overcome this limitation
2. Only master router sends the advertisements, and backups just listen to them. Since backups do
not send any periodic message, it is difficult to ascertain that the backups are still active, and the
healthiness of the backup routers can not be known until the failover happens.
3. VRRP owner - if a VRRP instance's virtual IP address is same as the real interface IP address. VRRP owner has special privileges (priority is 255) and can preempt any other router acting as master. This usually causes unwanted temporary network disruption after the non-owner master had stabilized
after the failover from owner.
4. A non-owner master can not accept packets destined to the virtual IP address. Only the owner can
accept and respond to such packets.
5. Two virtual routers with same VRIDs but on different IP subnets can not co-exist on the same
VLAN because of the virtual MAC address clash.

### 3.2.1 Virtual Router Owner

VRRP instance whose virtual IP address (VIP) is same as real interface address is called the owner of virtual router and has the highest priority. VRRP owner is supported in SONIC's VRRP implementation.

### 3.2.2 Virtual MAC Address

Following virtual MAC addresses is used by the protocol (as per RFC 5798)

IPv4 case: **00-00-5e-00-01-{vrid}**

IPv6 case: **00-00-5e-00-02-{vrid}**

where, vrid is user configured 1-byte virtual router identifier. VRID has interface scope; that is, VRID has to be unique among the VRRP instances on an interface. However, same VRID can be used for two  or more virtual router instances across different interfaces.


### 3.2.3    Preemption

Preemption is turned on by default.  Even if preemption is disabled, it does not affect the owner router since owner preempts the active master. Mastership switchover causes unnecessary temporary network disruption.

### 3.2.4    VRRP Advertisement Frame

VRRP control packets have IP protocol type as 112 (reserved for VRRP). IPv4 and IPv6 VRRP keepalives are sent to VRRP multicast address 224.0.0.18 and FF02::12 respectively. Source MAC in VRRP control packets is virtual MAC address                                                               

### 3.2.5    ARP Request Handling 

Only master responds to the ARP requests for virtual IP address. In ARP replies sent by master, the source MAC in Ethernet header and ARP payload is virtual MAC address.

In case of SAG, all ARP requests destined to SAG Gateway IP address will be responded by any receiving leaf node on which SAG Gateway IP is configured.

In case of ARP request origination from interface where SAG and Interface IP both are configured:
    - If both Interface IP and SAG IP are in same subnet
        - ARP request will be generated with Interface IP as source IP.
    - If Interface IP and SAG IP are in different subnet
        - If destination is in SAG IP subnet, SAG IP will be used as source IP in ARP request
        - For all other IP interface IP will be used as source IP in ARP request.

### 3.2.6   Uplink Interface Tracking

Interfaces other than the VRRP instance interface can be tracked for up/down events. When interface-tracking is enabled in the VRRP instance configuration, the tracked interface's operational status will be monitored. When a interface operational down event is detected on a tracked-interface, the track-priority/weight is subtracted from the current router’s priority value. Similarly, when interface operational up event is detected on the tracked-interface, the track-priority/weight is added to the router’s current priority value.

The dynamic change of router priority can trigger mastership switchover if the preemption is enabled. **However, if the router is an owner, the mastership switchover will not happen**.

Maximum number of interfaces that can be tracked for a virtual router instance is 8.

## 3.3 Functional Description of SAG

In most of Leaf-Spine deployments, redundancy in Spine layer is required to achieve high availability and to prevent network service disruption. Modern layer 2 networks adopted loop-free and balanced path networks using Multi Chassis Link Aggregation topologies with LACP port channels, leaving loop control methods (STP) as second protection layer. Spines also supports layer 3 networks, using ECMP in a scalable network topology. For unicast redundancy in layer 3, a common method is to use Virtual Router Redundancy Protocol (VRRP) to provide a simple and unique gateway at Leaf level. Although VRRP provides redundancy, it is active-standby protocol and does not provide a balanced data traffic distribution over Multi Chassis Link Aggregated topologies. Also in IP-Fabric topologies VRRP cannot be used since it does not provide seamless VM move across servers with optimal routing between VMs.

SAG provides better data traffic balancing and faster redundancy convergence, implementing active-active First Hop Router Redundancy to provide active/active unicast IP routing. 

The primary benefit of using SAG is that all configured routers are active and are able to perform routing. SAG also provides rapid failover in the event of a link or switch failure, while enabling the sharing of IP forwarding load between both switches. SAG requires configuring the same gateway IP address on the appropriate VLAN interfaces of both peers, as well as a global unique gateway MAC address. SAG functions by having both switches respond to ARP requests and Gratuitous ARP for a configured IP address with the �gateway” MAC address. If IP routing is enabled, received packets will be routed as follows: when the DMAC of a packet destined to a remote network matches the configured “gateway” MAC address, each router locally forwards the traffic to its next hop destination.

### 3.3.1 Virtual MAC Address

For SAG, user has to explicitly configure SAG Gateway MAC on the switch for SAG to be active. The switch maps all SAG IP addresses to this MAC address.
If user configures SAG Gateway MAC, it will be applicable for both IPv4 and IPv6. User can explicitly enable or disable per address-family gateway status.

Upon changing SAG Gateway MAC, SAG gateway IP on all interfaces will be brought down and brought UP again with new gateway MAC.

# 4 Feature Design



## 4.1 Design Overview

### 4.1.1 Basic Approach

Keepalived (https://www.keepalived.org/) open source code is chosen for VRRP control plane code.

![VRRP Keepalived Design](image/VRRP_Keepalived_design.png "Figure : Design")


### 4.1.2 Container

A new container "vrrp" has been added to host VRRP protocol operation. VRRP source code is not maintained in SONIC repos. Instead, VRRP code is downloaded from keeplaived project repo at the compile time, patches are applied to it (for any fixes by us) and then compiled/linked with SONIC binary.

New container "vrrp" handles only VRRP functionality.
For SAG the configuration is programmed in kernel through 'vrrpmgrd' module and 'vrrpsyncd' programs the hardware entry in SAI.

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



![VRRP Arch](image/VRRP_architecture.png "Figure : Arch")

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

key = VRRP_TABLE:interface_name:vrid:address_family
                          ; Interface name string like Vlan1 or PortChannel002 or Ethernet4
                          ; vrid is an integer
; field = value
vrid     = 1*3DIGIT       ; VRRP Instance Identifier
address_family = "IPv4"/"IPv6"; Address Family of VRRP instances
vip      = ip_address     ; Virtual IPv4/IPv6 address. This is a list of IPv4/IPv6 addresses
priority = vrrp_priority  ; Priority of VRRP instance
adv_interval = 1*3DIGITS  ; Advertisement interval for VRRP. Default = 1sec
state    = vrrp_state     ; String denoting the state of VRRP instance
version  = vrrp_version   ; VRRP version. VRRP for IPv6 will always be version 3
pre_empt = "true"/"false" ; VRRP premption is enabled? Default is True
track_interface = track_interface ; List of interfaces tracked by a VRRP instance
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



Example:- Entery with multiple virtual IPs

**Key**: VRRP_TABLE:Vlan11:1

**Value**: 

​    'vrid': '1',
​    'vip': '4.1.1.100,4.1.1.200,4.1.1.201,4.1.1.202,', 

​    'priority': '50', 

​    'adv_interval': '1',
​    'state': '', 

​    'version': '2',
​    'pre_empt': 'True',
​    'track_interface':
​            'Ethernet7|weight|20,

​            PortChannel001|weight|40



**Key**: VRRP_TABLE:Vlan12:2

**Value**: 

​    'vrid': '2',
​    'vip': 'fe80::100,4::100', 

​    'priority': '50', 

​    'adv_interval': '1',
​    'state': '', 

​    'version': '3',
​    'pre_empt': 'True',
​    'track_interface':
​            'Ethernet8|weight|20,

​            PortChannel002|weight|40



Example:- VRRP for IPv6 address

**SAG_TABLE**

Producer:  config manager 

Consumer: VrrpMgr

Description: New table that stores SAG configuration for per interface. 

Schema:

```
;New table
;holds the SAG configuration per interface

key = SAG_TABLE:interface_name:address_family
                          ; Vlan Interface name string like Vlan1
address_family = "IPv4"/"IPv6"; Address Family of VRRP instances
gwip      = ip_address     ; Gateway IPv4/IPv6 address. This is a list of IPv4/IPv6 addresses
```



```
;New table
;holds the SAG_GLOBAL configuration per router

key = SAG_GLOBAL_TABLE:IP
gwmac      = mac     ; Gateway mac address for all the SAG Virtual IP
IPv4       = "enable"/"disable"   ; enable or disable IPv4 gateway globally. This is applicable for all IPv4 gateway across all VRFs
IPv6       = "enable"/"disable"   ; enable or disable IPv6 gateway globally. This is applicable for all IPv6 gateway across all VRFs
```



Example:- 

**Key**: SAG_TABLE:Vlan15

**Value**: 
​    'gwip': '15.1.1.100,16.1.1.100', 

### 4.2.2 APP_DB Changes

**VRRP_TABLE**

Producer:  VrrpMgr 

Consumer: VrrpOrch

Description: This is a new table that contains VRRP and SAG state information 

Schema:

```
; New table
; holds the VRRP state and VMAC information

key = VRRP_TABLE:interface_name:vip:type
    interface_name        ; interface name as a string. Vlan, Ethernet or PortChannel
    vip                   ; virtual IP address in a.b.c.d/32 or a:b:c::d format
    type                  ; IP(v6) address type string.
    
; field = value
vmac = virtual_mac_address ; Virtual MAC address associated with VRRP instance
```

Example:- 

**Key**: VRRP_TABLE:Vlan1000:[40.10.8.101/32:ipv4]

**Value**: "vmac":"00:00:5e:00:01:08"

## 4.3 Modules Design and Flows


### Static Anycast Gateway Event Flow Diagram:

#### SAG Add Sequence Diagram: 

![SAG_ADD_SEQ_DIAGRAM](image/SAG_ADD_SEQ_DIAGRAM.png "Figure 4.3.1 : SAG ADD Sequence Diagram")

```
sequenceDiagram
    participant config_db
    participant vrrpmgrd
    participant vrrpsyncd
    participant app_db
    participant kernel
    participant vrrporchagt
    participant SAI
    participant FdbOrch

    Note over vrrpmgrd: Global SAG gateway mac configured
    config_db->>vrrpmgrd: add static anycast gateway IP on Vlan
    alt is first SAG IP on Vlan?
        vrrpmgrd->>kernel: Create SAG MACVLAN interface under Vlan(vrid=0, mac=SAG MAC)
    end
    vrrpmgrd->>kernel: Add Gateway IP to Anycast gateway MACVLAN interface
     
   

    kernel->>vrrpsyncd: add SAG MACVLAN interface NETLINK event
    vrrpsyncd->>app_db: add app-vrrp-table entry with SAG IP and SAG MAC

   
   app_db->>vrrporchagt: add app-vrrp-table entry event
    alt is VRRP RIF for given VMAC not created?
    vrrporchagt->>SAI: Call sai_create_router_interface type virtual and given vrid & vmac
    Note over vrrporchagt: sai returns VRRP RIF to use for route table programming.
    end
    vrrporchagt->>SAI: Call sai_create_route_entry for SAG Gateway IP with nhop as VRRP RIF
    vrrporchagt->>FdbOrch: Delete Gateway MAC entry from L2 FDB
```


#### SAG Del Sequence Diagram: 

![SAG_DEL_SEQ_DIAGRAM](image/SAG_DEL_SEQ_DIAGRAM.png "Figure 4.3.2 : SAG DEL Sequence Diagram")


```
sequenceDiagram
    participant config_db
    participant vrrpmgrd
    participant vrrpsyncd
    participant app_db
    participant kernel
    participant vrrporchagt
    participant SAI
    participant FdbOrch

        config_db->>vrrpmgrd: del static anycast gateway IP on Vlan
    vrrpmgrd->>kernel: Del Gateway IP on Anycast gateway MACVLAN interface
    alt is last Gateway IP on Vlan?
    vrrpmgrd->>kernel: Del Anycast gateway MACVLAN interface
    end

    kernel->>vrrpsyncd: del SAG Gateway IP on Anycast gateway MACVLAN interface
    vrrpsyncd->>app_db: Update app-vrrp-table entry by deleting SAG IP

    app_db->>vrrporchagt: Update app-vrrp-table entry event
    vrrporchagt->>SAI: Call sai_delete_route_entry for SAG Gateway IP

    kernel->>vrrpsyncd: del SAG MACVLAN interface NETLINK event
    vrrpsyncd->>app_db: Delete app-vrrp-table entry for SAG IP and SAG MAC

    app_db->>vrrporchagt: del app-vrrp-table entry event
    vrrporchagt->>SAI: Call sai_delete_route_entry for SAG Gateway IP
    vrrporchagt->>SAI: Call sai_delete_router_interface type virtual, vrid=0 and vmac=gwmac
```


## 5 CLI

SONIC Click based configuration and monitoring CLIs have been introduced in SONIC for VRRP

### 5.1 Configuration Commands

#### 5.1.1 Configuration Commands for IPv4 VRRP

```
configure interface vrrp add <interface_name> <vrid>
This command adds/enables a IPv4 VRRP instance on an interface.
- interface_name - name of interface (Ethernet or Vlan or PortChannel) over which VRRP is to be enabled.
- vrid - VRRP instance identifier.
 
configure interface vrrp remove <interface_name> <vrid>
This command removes a IPv4 VRRP instance from an interface.

configure interface vrrp vip add <interface_name> <vrid> <virtual_ip_address>
This command adds a virtual IP address for a VRRP instance on an interface. User is allowed to create multiuple VIPs for a IPv4 VRRP instance. VIP must fall in the interface's subnet.
- interface_name - name of interface (Ethernet or Vlan or PortChannel) over which VIP is being added
- vrid - VRRP instance identifier.
- virtual_ip_address - VIP address in dotted decimal IPv4 address

configure interface vrrp vip remove <interface_name> <vrid> <virtual_ip_address>
This command deletes a already configured VIP from a IPv4 VRRP instance

configure interface vrrp priority <interface_name> <vrid> <priority>
This command configures priority for a VRRP instance
- priority - a number between 1 and 254 with 1 being the lowest and 254 being the highest priority. Default is 100. Priority 255 is reserved for owner VRRP router.

configure interface vrrp adv_interval <interface_name> <vrid> <interval>
This command configures VRRP periodic advertisement interval for a VRRP instance
- interval - a number between 1 and 255. Unit is in seconds. Default is 1sec. 

configure interface vrrp pre_empt enable <interface_name> <vrid>
This command enables premeption of a Master when a higher priority VRRP router arrives
- enable - Enable premeption. Default is enabled. 

configure interface vrrp pre_empt disable <interface_name> <vrid>
This command disables premeption of a Master when a higher priority VRRP router arrives
- disable - Disable premeption. Default is enabled. 

configure interface vrrp version <interface_name> <vrid> <version_num> 
This command configures VRRP protocol version for IPv4 VRRP instances

configure interface vrrp track_interface add <interface_name> <vrid> <track_interface> <weight>
This command adds a track interface to a VRRP Instance. A maximum of 8 track interfaces can be added to a VRRP instance. 
- track_interface - Interface to track. Interface can be Ethernet, Vlan or PortChannel
- weight - weight or importance assigned to the track_interface. When track interface goes down, the priority of VRRP instance will be reduced by weight

configure interface vrrp track_interface remove <interface_name> <vrid> <track_interface> 
This command removes an already configured track interface from a IPv4 VRRP Instance. 
```

#### 5.1.2 Configuration Commands for IPv6 VRRP

```
configure interface vrrp6 add <interface_name> <vrid>
This command adds/enables a IPv6 VRRP instance on an interface.
- interface_name - name of interface (Ethernet or Vlan or PortChannel) over which VRRP is to be enabled.
- vrid - VRRP instance identifier.
 
configure interface vrrp6 remove <interface_name> <vrid>
This command removes a IPv6 VRRP instance from an interface.

configure interface vrrp6 vip add <interface_name> <vrid> <virtual_ip_address>
This command adds a virtual IPv6 address for a IPv6 VRRP instance on an interface. User is allowed to create multiuple VIPs for a VRRP instance. VIP must fall in the interface's subnet.
- interface_name - name of interface (Ethernet or Vlan or PortChannel) over which VIP is being added
- vrid - VRRP instance identifier.
- virtual_ip_address - VIP address in dotted decimal IPv4 address

configure interface vrrp6 vip remove <interface_name> <vrid> <virtual_ip_address>
This command deletes a already configured VIP from a IPv6 VRRP instance

configure interface vrrp6 priority <interface_name> <vrid> <priority>
This command configures priority for a VRRP instance
- priority - a number between 1 and 254 with 1 being the lowest and 254 being the highest priority. Default is 100. Priority 255 is reserved for owner VRRP router.

configure interface vrrp6 adv_interval <interface_name> <vrid> <interval>
This command configures VRRP periodic advertisement interval for a VRRP instance
- interval - a number between 1 and 255. Unit is in seconds. Default is 1sec. 

configure interface vrrp6 pre_empt enable <interface_name> <vrid>
This command enables premeption of a Master when a higher priority VRRP router arrives
- enable - Enable premeption. Default is enabled. 

configure interface vrrp6 pre_empt disable <interface_name> <vrid>
This command disables premeption of a Master when a higher priority VRRP router arrives
- disable - Disable premeption. Default is enabled. 

configure interface vrrp6 version <interface_name> <vrid> <version_num> 
This command configures VRRP protocol version for IPv4 VRRP instances

configure interface vrrp6 track_interface add <interface_name> <vrid> <track_interface> <weight>
This command adds a track interface to a IPv6 VRRP Instance. A maximum of 8 track interfaces can be added to a VRRP instance. 
- track_interface - Interface to track. Interface can be Ethernet, Vlan or PortChannel
- weight - weight or importance assigned to the track_interface. When track interface goes down, the priority of VRRP instance will be reduced by weight

configure interface vrrp6 track_interface remove <interface_name> <vrid> <track_interface> 
This command removes an already configured track interface from a VRRP Instance. 
```

#### 5.1.3 Configuration Commands for SAG

```
config ip anycast-mac-address add <mac>
This command configures IPv4 gateway MAC for SAG
- mac - SAG gateway MAC to be applied on all interface IPv4 & IPv6 SAG IP.

config ip anycast-mac-address remove <mac>
This command unconfigures IPv4 gateway MAC for SAG
- mac - SAG gateway MAC to be removed from all interface IPv4 & IPv6 SAG IP.
        This will de-activate IPv4 & IPv6 SAG IP on all interfaces.

config ipv6 anycast-mac-address <enable/disable>
This command enables or disables IPv6 gateway MAC for SAG interfaces.

config interface ip anycast-address add <interface_name> <gwip>
This command configures SAG IPv4/IPv6 on an interface
- gwip - IPv4/IPv6 Static Anycast Gateway IP with prefix length.

config interface ip anycast-address remove <interface_name> <gwip>
This command unconfigures SAG IPv4/IPv6 on an interface
- gwip - IPv4/IPv6 Static Anycast Gateway IP with prefix length.
```


SONIC kLISH based configuration and monitoring CLIs have been introduced in SONIC for VRRP

```
[no] configure vrrp <vrid> address-family {ipv4 | ipv6}
This command configures/removes VRRP instance in IPv4 or IPv6 address-family.
- vrid - VRRP instance identifier.
 
[no] vip <vip>
This command configures/removes a virtual IP address for a VRRP instance on an interface. User is allowed to create multiuple VIPs for a IPv4 VRRP instance. VIP must fall in the interface's subnet. For IPv6 VRRP instance VIP could be any link-local address.
- virtual_ip_address - VIP address

priority <priority_value>
This command configures priority for a VRRP instance
- priority_value - a number between 1 and 254 with 1 being the lowest and 254 being the highest priority. Default is 100. Priority 255 is reserved for owner VRRP router.

adv_interval <int_value>
This command configures VRRP periodic advertisement interval for a VRRP instance
- int_value - a number between 1 and 255. Unit is in seconds. Default is 1sec. 

[no] pre_empt 
This command enables/disables premeption of a Master when a higher priority VRRP router arrives.  

[no] vrrpv3
This command configures/removes VRRP version 3 for a IPv4 VRRP instance. IPv4 VRRP instances are version 2 by default. 

[no] track_interface 
<track_ifname> <weight>
This command adds/removes a track interface to a VRRP Instance. A maximum of 8 track interfaces can be added to a VRRP instance. 
- track_ifname - Interface to track. Interface can be Ethernet, Vlan or PortChannel
- weight - weight or importance assigned to the track_interface. When track interface goes down, the priority of VRRP instance will be reduced by weight

```

SONIC kLISH based configuration and monitoring CLIs have been introduced in SONIC for SAG

```
[no] ip anycast-mac-address <vip>
This command configures/removes SAG IPv4 on an interface.
- vip - IPv4 Gateway address.
 
[no] ipv6 anycast-mac-address <vip>
This command configures/removes SAG IPv6 on an interface.
- vip - IPv6 Gateway address.

[no] ip anycast-address <mac>
This command configures/removes anycast mac address for all the IPv4 virtual addresses on all interfaces. 

[no] ipv6 anycast-address <enable/disable>
This command enables/disables anycast mac address for all the IPv6 virtual addresses on all interfaces. 

```

#### 

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

## 

```
show ip static-anycast-gateway
 - lists all the SAG Gateway IPv4 including their current state
 Sample output:-
Configured Anycast Gateway MAC address: 00:00:00:01:02:03
IPv4 Anycast Gateway MAC address: enable
Total number of gateway: 6
Total number of gateway admin UP: 6
Total number of gateway oper UP: 4
Interfaces    Gateway Address    Master    Admin/Oper
------------  -----------------  --------  ------------
Vlan2         2.0.101.100/24               up/up
Vlan200       200.0.0.10/24                up/up
              200.1.0.10/24                up/up
              200.1.0.11/32                up/up
Vlan300       31.1.1.4/24        Vrf3      up/down
              31.1.1.5/32                  up/down

show ipv6 static-anycast-gateway
 - lists all the SAG Gateway IPv6 including their current state
 Sample output:-
Configured Anycast Gateway MAC address: 00:00:00:01:02:03
IPv6 Anycast Gateway MAC address: enable
Total number of gateway: 6
Total number of gateway admin UP: 6
Total number of gateway oper UP: 4
Interfaces    Gateway Address    Master    Admin/Oper
------------  -----------------  --------  ------------
Vlan2         2100::1/64                   up/up
Vlan200       2002::4/64                   up/up
              2003::4/64                   up/up
              2004::4/64                   up/up
Vlan300       3002::4/64         Vrf3      up/down
              3003::4/64                   up/down

```


## 6 Serviceability and Debug

The existing logging mechanisms shall be used. Proposed debug framework shall be used for internal state dump.

**Sonic Show commands**
```
show ip interface
    - Displays active VRRP VIP on interface
```

**Sonic logging commands**
```
show logging vrrpsyncd
    - Displays logs generated by vrrpsyncd for programming VIP and VMAC for VRRP and SAG IP & Gateway MAC for SAG
show logging keepalive
    - Displays logs from keepalived docker where vrrpd process is run.
show logging orchagent
    - Displays logs from orchagent including vrrporchagt.
```

**Linux Debug commands**
```
ip addr show type macvlan
    - Lists MACVLAN interface in linux kernel corresponding to VRRP and SAG interfaces
    - Displays VMAC corresponding to each MACVLAN interface
    - Displays VIP configured on MACVLAN interface
    - Displays parent interface name under which VRRP/SAG is configured
    - Status of MACVLAN interface in kernel

ip -d link show type macvlan
    - Lists MACVLAN interface including various attributes for these netdevice

ip -s link show type macvlan
    - Lists MACVLAN interface and count of different types of RX/TX packets on these interfaces.

```


## 7 Warm Reboot Support

Currently, warm-reboot is not supported for VRRP. That is, warm-reboot will simply restart the VRRP docker without VRRP storing any data for warm restart. 

SAG is implemented in vrrpmgrd process which is placed in swss docker.
In case of swss docker warm reboot, since SAG information is maintained in kernel, when vrrpmgrd starts up it will recover SAG information from kernel.

## 8 Unit Test cases

| Test Cases                   | UT Action and observation                                    | Remarks                                                    |
| ---------------------------- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| Configuration                |                                                              |                                                            |
|                              | Validate IPv4 VRRP config on Physical Interface              |                                                            |
|                              | Validate IPv4 VRRP config on Ve Interface                    |                                                            |
|                              | Ensure that VMAC is generated based on VRID for IPv4 VRRP    |                                                            |
|                              | Ensure VMAC is programmed in FDB and VIP in LPM              |                                                            |
|                              | Validate IPv6 VRRP config on Physical Interface              |                                                            |
|                              | Validate IPv6 VRRP config on Ve Interface                    |                                                            |
|                              | Ensure that VMAC is generated based on VRID for IPv6 VRRP    |                                                            |
|                              | Ensure VMAC is programmed in FDB and VIP in LPM for IPv6 VRRP |                                                            |
|                              | Validate VRRP on more than one interface                     |                                                            |
|                              | Validate VRRP with more than one VIP per instance            |                                                            |
|                              | Validate multiple VRRP instance per interface                |                                                            |
|                              | configure SAG global gateway MAC                             |                                                            |
|                              | configure SAG with IPv4 and IPv6 address on Ve interface     |                                                            |
|                              | Ensure SAG is active and programmed properly in kernel       |                                                            |
|                              | Ensure SAG gateway mac is programmed in FDB and Gateway IP in LPM|                                                            |
|                              | configure additional SAG with IPv4 and IPv6 address on Ve interface and verify all programming|                                                            |
| Failover                     |                                                              |                                                            |
|                              | Validate VRRP master failover with priority change           |                                                            |
|                              | Validate VRRP master failover by disabling a vrrp group      | Add 'disable' feature if needed and feasible               |
|                              | Validate VRRP master failover by removing all virtual-ip     |                                                            |
|                              | Validate VRRP master failover by removing interface IPv4/IPv6 address |                                                            |
|                              | Validate VRRP master failover by deleting master VRRP session |                                                            |
|                              | Validate VRRP master failover by bringing down interface     |                                                            |
|                              | Validate VRRP master failover by deleting interface          |                                                            |
|                              | Validate SAG functionality by flapping associated Vlan interface |                                                            |
| VRRP parameter changes       |                                                              |                                                            |
|                              | Validate gratutious ARP                                      |                                                            |
|                              | Validate adv interval                                        |                                                            |
|                              | Validate version change for IPv4 VRRP instance               |                                                            |
| ARP/ND resolve to VIP        |                                                              |                                                            |
|                              | validate ARP resolution from host to VRRP session VIP        | Hosts should get ARP response with VMAC as source MAC      |
|                              | Perform Master failover and check that ARP resolution to VIP will happen with VMAC |                                                            |
|                              | validate ARP resolution from host to SAG gateway IP        | Hosts should get ARP response with gateway MAC as source MAC witout any duplicate ARP response      |
| Ping to VIP                  |                                                              |                                                            |
|                              | Validate ping to VRRP from host, backup & non vrrp router works |                                                            |
|                              | Validate ping to host from interface with interface IP and SAG gateway IP works | Ping should work on Vlan interface with SAG + Interface IP     |
| L3 Forwarding with VIP as GW |                                                              |                                                            |
|                              | Validate that IPv4/IPv6 traffic forwarding with VIP as gateway works from source to destination host |                                                            |
|                              | Perform VRRP master failover and check if traffic forwarding continues with new master |                                                            |
|                              | Validate that IPv4/IPv6 traffic forwarding with SAG as gateway works from source to destination host |                                                            |
| VRRP Owner                   |                                                              |                                                            |
|                              | Configure VRRP for IPv4/IPv6 session with VIP same as interface IP and ensure this session becomes master and priority of session becomes max=255 |                                                            |
|                              | Ensure ping to VIP works in owner case                       |                                                            |
|                              | Ensure ARP/nd resolution with VIP as gateway in owner case   | ARP/ND should get resolved with VMAC and not interface MAC |
|                              | Ensure ping and traffic forwarding continues after owner failover and comes back |                                                            |
|                              | Traffic forwarding works with VIP as gateway                 |                                                            |
| Interface tracking           |                                                              |                                                            |
|                              | Run below tests with Ping and traffic running                |                                                            |
|                              | Enable interface tracking in a VRRP group with priority, bring down the port and ensure VRRP session priority comes down and session transition from Master to Backup State |                                                            |
|                              | Enable the tracked interface and check if session priority gets restored and session becomes master |                                                            |
|                              | Repeat this test on Port-channel interface                   |                                                            |
|                              | Repeat this test by deleting and re-adding port-channel interface |                                                            |
