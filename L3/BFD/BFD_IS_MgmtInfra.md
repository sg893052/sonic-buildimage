# BFD
North bound interface and KLISH CLI support for BFD
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
| 0.1 | 11/08/2019  |   Sumit Agarwal    | Initial version                   |

# About this Manual
This document provides information about the northbound interface details for BFD.

# Scope
This document covers the "configuration" and "show" commands supported for BFD based on OpenConfig YANG. 

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| BFD                      | Bidirectional Forwarding Detection  |

# 1 Feature Overview
Add support for BFD create/set/get via CLI, REST and gNMI using openconfig-bfd-ext.yang and sonic-mgmt-framework container

## 1.1 Requirements
Provide management framework capabilities to handle:
- BFD configuration
- BFD session status query.

### 1.1.1 Functional Requirements

Provide management framework support to existing SONiC capabilities with respect to BFD.

### 1.1.2 Configuration and Management Requirements
- CLI configuration and show commands
- REST API support
- gNMI Support

Details described in Section 3.

## 1.2 Design Overview

### 1.2.1 Basic Approach
Provide transformer methods in sonic-mgmt-framework container for BFD handling

### 1.2.2 Container
All code changes will be done in management-framework container

### 1.2.3 SAI Overview
N/A

# 2 Functionality
## 2.1 Target Deployment Use Cases
Manage/configure BFD via CLI, gNMI and REST interfaces
## 2.2 Functional Description
Provide CLI, gNMI and REST support for BFD related commands handling

# 3 Design
## 3.1 Overview
1.  Transformer common app owns the Open config data models related to BFD (which means no separate app module required for handling BFD YANG objects).

-   openconfig-bfd-ext.yang

2.  Provide annotations for required objects so that transformer core and common app will take care of handling them.
    
3.  Provide transformer methods as per the annotations defined to take care of model specific logics and validations.
    
4.  Define SONiC YANG and Redis ABNF schema for the supported Open Config BFD models & objects.
    
5.  In bgpcfgd register for Redis DB events for the BFD and other related objects, so as to translate the Redis DB events to FRR-BFD CLI commands to configure FRR-BFD.
    
6.  Update frr.conf.j2 template for new FRR-BFD configurations supported in SONiC which will be used by sonic-cfggen to generate frr.conf file.

![BFD-IS](images/FRR-BGP-Unified-mgmt-frmwrk.png "Figure 1")

## 3.2 User Interface
### 3.2.1 Data Models
List of YANG models required for BFD management.

[openconfig-bfd.yang](https://github.com/openconfig/public/blob/master/release/models/bfd/openconfig-bfd.yang)

openconfig-bfd.yang cannot meet all the requirements to support configuration in FRR BFD.
A new YANG openconfig-bfd-ext.yang is defined as an extension of openconfig-bfd.yang

Supported YANG objects and attributes are as per below tree:
## openconfig-bfd
```diff
  +--rw bfd
+    +--rw oc-bfd-ext:sessions
+    |  +--rw oc-bfd-ext:single-hop* [remote-address interface vrf local-address]
+    |  |  +--rw oc-bfd-ext:remote-address                  oc-inet:ip-address
+    |  |  +--rw oc-bfd-ext:vrf                             -> /oc-ni:network-instances/network-instance/config/name
+    |  |  +--rw oc-bfd-ext:interface                       -> /oc-if:interfaces/interface/config/name
+    |  |  +--rw oc-bfd-ext:local-address                   string
+    |  |  +--rw oc-bfd-ext:enabled?                        boolean
+    |  |  +--rw oc-bfd-ext:desired-minimum-tx-interval?    uint32
+    |  |  +--rw oc-bfd-ext:required-minimum-receive?       uint32
+    |  |  +--rw oc-bfd-ext:detection-multiplier?           uint8
+    |  |  +--rw oc-bfd-ext:desired-minimum-echo-receive?   uint32
+    |  |  +--rw oc-bfd-ext:echo-active?                    boolean
+    |  +--rw oc-bfd-ext:multi-hop* [remote-address interface vrf local-address]
+    |     +--rw oc-bfd-ext:remote-address                 oc-inet:ip-address
+    |     +--rw oc-bfd-ext:vrf                            -> /oc-ni:network-instances/network-instance/config/name
+    |     +--rw oc-bfd-ext:interface                      -> /oc-if:interfaces/interface/config/name
+    |     +--rw oc-bfd-ext:local-address                  string
+    |     +--rw oc-bfd-ext:enabled?                       boolean
+    |     +--rw oc-bfd-ext:desired-minimum-tx-interval?   uint32
+    |     +--rw oc-bfd-ext:required-minimum-receive?      uint32
+    |     +--rw oc-bfd-ext:detection-multiplier?          uint8
+    +--rw oc-bfd-ext:bfd-state
+    |  +--ro oc-bfd-ext:single-hop-state* [remote-address interface vrf local-address]
+    |  |  +--ro oc-bfd-ext:remote-address                          oc-inet:ip-address
+    |  |  +--ro oc-bfd-ext:vrf                                     -> /oc-ni:network-instances/network-instance/config/name
+    |  |  +--ro oc-bfd-ext:interface                               -> /oc-if:interfaces/interface/config/name
+    |  |  +--ro oc-bfd-ext:local-address                           string
+    |  |  +--ro oc-bfd-ext:session-state?                          bfd-session-state
+    |  |  +--ro oc-bfd-ext:last-failure-time?                      oc-types:timeticks64
+    |  |  +--ro oc-bfd-ext:failure-transitions?                    uint64
+    |  |  +--ro oc-bfd-ext:local-discriminator?                    string
+    |  |  +--ro oc-bfd-ext:remote-discriminator?                   string
+    |  |  +--ro oc-bfd-ext:local-diagnostic-code?                  bfd-diagnostic-code
+    |  |  +--ro oc-bfd-ext:remote-diagnostic-code?                 bfd-diagnostic-code
+    |  |  +--ro oc-bfd-ext:remote-minimum-receive-interval?        uint32
+    |  |  +--ro oc-bfd-ext:demand-mode-requested?                  boolean
+    |  |  +--ro oc-bfd-ext:remote-authentication-enabled?          boolean
+    |  |  +--ro oc-bfd-ext:remote-control-plane-independent?       boolean
+    |  |  +--ro oc-bfd-ext:session-type?                           bfd-session-type
+    |  |  +--ro oc-bfd-ext:remote-multiplier?                      uint32
+    |  |  +--ro oc-bfd-ext:local-multiplier?                       uint32
+    |  |  +--ro oc-bfd-ext:negotiated-transmission-interval?       uint32
+    |  |  +--ro oc-bfd-ext:negotiated-receive-interval?            uint32
+    |  |  +--ro oc-bfd-ext:remote-desired-transmission-interval?   uint32
+    |  |  +--ro oc-bfd-ext:remote-echo-receive-interval?           uint32
+    |  |  +--ro oc-bfd-ext:last-up-time?                           oc-types:timeticks64
+    |  |  +--ro oc-bfd-ext:minimum-echo-interval?                  uint32
+    |  |  +--ro oc-bfd-ext:echo
+    |  |  |  +--ro oc-bfd-ext:active?                    boolean
+    |  |  |  +--ro oc-bfd-ext:transmitted-packets?       uint64
+    |  |  |  +--ro oc-bfd-ext:received-packets?          uint64
+    |  |  |  +--ro oc-bfd-ext:up-transitions?            uint64
-    |  |  |  +--ro oc-bfd-ext:last-packet-transmitted?   uint64
-    |  |  |  +--ro oc-bfd-ext:last-packet-received?      uint64
+    |  |  +--ro oc-bfd-ext:async
+    |  |  |  +--ro oc-bfd-ext:transmitted-packets?       uint64
+    |  |  |  +--ro oc-bfd-ext:received-packets?          uint64
+    |  |  |  +--ro oc-bfd-ext:up-transitions?            uint64
-    |  |  |  +--ro oc-bfd-ext:last-packet-transmitted?   uint64
-    |  |  |  +--ro oc-bfd-ext:last-packet-received?      uint64
-    |  |  +--ro oc-bfd-ext:remote-session-state?                   bfd-session-state
+    |  +--ro oc-bfd-ext:multi-hop-state* [remote-address interface vrf local-address]
+    |     +--ro oc-bfd-ext:remote-address                          oc-inet:ip-address
+    |     +--ro oc-bfd-ext:vrf                                     -> /oc-ni:network-instances/network-instance/config/name
+    |     +--ro oc-bfd-ext:interface                               -> /oc-if:interfaces/interface/config/name
+    |     +--ro oc-bfd-ext:local-address                           string
+    |     +--ro oc-bfd-ext:session-state?                          bfd-session-state
+    |     +--ro oc-bfd-ext:last-failure-time?                      oc-types:timeticks64
+    |     +--ro oc-bfd-ext:failure-transitions?                    uint64
+    |     +--ro oc-bfd-ext:local-discriminator?                    string
+    |     +--ro oc-bfd-ext:remote-discriminator?                   string
+    |     +--ro oc-bfd-ext:local-diagnostic-code?                  bfd-diagnostic-code
+    |     +--ro oc-bfd-ext:remote-diagnostic-code?                 bfd-diagnostic-code
+    |     +--ro oc-bfd-ext:remote-minimum-receive-interval?        uint32
+    |     +--ro oc-bfd-ext:demand-mode-requested?                  boolean
+    |     +--ro oc-bfd-ext:remote-authentication-enabled?          boolean
+    |     +--ro oc-bfd-ext:remote-control-plane-independent?       boolean
+    |     +--ro oc-bfd-ext:session-type?                           bfd-session-type
+    |     +--ro oc-bfd-ext:remote-multiplier?                      uint32
+    |     +--ro oc-bfd-ext:local-multiplier?                       uint32
+    |     +--ro oc-bfd-ext:negotiated-transmission-interval?       uint32
+    |     +--ro oc-bfd-ext:negotiated-receive-interval?            uint32
+    |     +--ro oc-bfd-ext:remote-desired-transmission-interval?   uint32
+    |     +--ro oc-bfd-ext:remote-echo-receive-interval?           uint32
+    |     +--ro oc-bfd-ext:last-up-time?                           oc-types:timeticks64
+    |     +--ro oc-bfd-ext:async
+    |     |  +--ro oc-bfd-ext:transmitted-packets?       uint64
+    |     |  +--ro oc-bfd-ext:received-packets?          uint64
+    |     |  +--ro oc-bfd-ext:up-transitions?            uint64
-    |     |  +--ro oc-bfd-ext:last-packet-transmitted?   uint64
-    |     |  +--ro oc-bfd-ext:last-packet-received?      uint64
-    |     +--ro oc-bfd-ext:echo
-    |     |  +--ro oc-bfd-ext:active?                    boolean
-    |     |  +--ro oc-bfd-ext:last-packet-transmitted?   uint64
-    |     |  +--ro oc-bfd-ext:last-packet-received?      uint64
-    |     |  +--ro oc-bfd-ext:transmitted-packets?       uint64
-    |     |  +--ro oc-bfd-ext:received-packets?          uint64
-    |     |  +--ro oc-bfd-ext:up-transitions?            uint64
-    |     +--ro oc-bfd-ext:remote-session-state?                   bfd-session-state
-    |     +--ro oc-bfd-ext:minimum-echo-interval?                  uint32
-    +--rw interfaces
-       +--rw interface* [id]
-          +--rw id                    -> ../config/id
-          +--rw config
-          |  +--rw id?                            string
-          |  +--rw enabled?                       boolean
-          |  +--rw local-address?                 oc-inet:ip-address
-          |  +--rw desired-minimum-tx-interval?   uint32
-          |  +--rw required-minimum-receive?      uint32
-          |  +--rw detection-multiplier?          uint8
-          |  +--rw enable-per-member-link?        boolean
-          +--ro state
-          |  +--ro id?                            string
-          |  +--ro enabled?                       boolean
-          |  +--ro local-address?                 oc-inet:ip-address
-          |  +--ro desired-minimum-tx-interval?   uint32
-          |  +--ro required-minimum-receive?      uint32
-          |  +--ro detection-multiplier?          uint8
-          |  +--ro enable-per-member-link?        boolean
-          +--rw interface-ref
-          |  +--rw config
-          |  |  +--rw interface?      -> /oc-if:interfaces/interface/name
-          |  |  +--rw subinterface?   -> /oc-if:interfaces/interface[oc-if:name=current()/../interface]/subinterfaces/subinterface/index
-          |  +--ro state
-          |     +--ro interface?      -> /oc-if:interfaces/interface/name
-          |     +--ro subinterface?   -> /oc-if:interfaces/interface[oc-if:name=current()/../interface]/subinterfaces/subinterface/index
-          +--rw micro-bfd-sessions
-          |  +--rw micro-bfd-session* [member-interface]
-          |     +--rw member-interface    -> ../config/member-interface
-          |     +--rw config
-          |     |  +--rw local-address?      oc-inet:ip-address
-          |     |  +--rw remote-address?     oc-inet:ip-address
-          |     |  +--rw member-interface?   -> /oc-if:interfaces/interface/config/name
-          |     +--ro state
-          |        +--ro local-address?                      oc-inet:ip-address
-          |        +--ro remote-address?                     oc-inet:ip-address
-          |        +--ro member-interface?                   -> /oc-if:interfaces/interface/config/name
-          |        +--ro session-state?                      bfd-session-state
-          |        +--ro remote-session-state?               bfd-session-state
-          |        +--ro last-failure-time?                  oc-types:timeticks64
-          |        +--ro failure-transitions?                uint64
-          |        +--ro local-discriminator?                string
-          |        +--ro remote-discriminator?               string
-          |        +--ro local-diagnostic-code?              bfd-diagnostic-code
-          |        +--ro remote-diagnostic-code?             bfd-diagnostic-code
-          |        +--ro remote-minimum-receive-interval?    uint32
-          |        +--ro demand-mode-requested?              boolean
-          |        +--ro remote-authentication-enabled?      boolean
-          |        +--ro remote-control-plane-independent?   boolean
-          |        +--ro async
-          |           +--ro last-packet-transmitted?   uint64
-          |           +--ro last-packet-received?      uint64
-          |           +--ro transmitted-packets?       uint64
-          |           +--ro received-packets?          uint64
-          |           +--ro up-transitions?            uint64
-          +--rw peers
-             +--ro peer* [local-discriminator]
-                +--ro local-discriminator    -> ../state/local-discriminator
-                +--ro state
-                   +--ro local-address?                      oc-inet:ip-address
-                   +--ro remote-address?                     oc-inet:ip-address
-                   +--ro subscribed-protocols*               identityref
-                   +--ro session-state?                      bfd-session-state
-                   +--ro remote-session-state?               bfd-session-state
-                   +--ro last-failure-time?                  oc-types:timeticks64
-                   +--ro failure-transitions?                uint64
-                   +--ro local-discriminator?                string
-                   +--ro remote-discriminator?               string
-                   +--ro local-diagnostic-code?              bfd-diagnostic-code
-                   +--ro remote-diagnostic-code?             bfd-diagnostic-code
-                   +--ro remote-minimum-receive-interval?    uint32
-                   +--ro demand-mode-requested?              boolean
-                   +--ro remote-authentication-enabled?      boolean
-                   +--ro remote-control-plane-independent?   boolean
-                   +--ro echo
-                   |  +--ro active?                    boolean
-                   |  +--ro last-packet-transmitted?   uint64
-                   |  +--ro last-packet-received?      uint64
-                   |  +--ro transmitted-packets?       uint64
-                   |  +--ro received-packets?          uint64
-                   |  +--ro up-transitions?            uint64
-                   +--ro async
-                      +--ro last-packet-transmitted?   uint64
-                      +--ro last-packet-received?      uint64
-                      +--ro transmitted-packets?       uint64
-                      +--ro received-packets?          uint64
-                      +--ro up-transitions?            uint64

```

### 3.2.2 CLI
#### 3.2.2.1 Configuration Commands

#### BFD mode
sonic-cli(config)#bfd

Enters the BFD configuration mode

#### Peer Creation

`peer <A.B.C.D|X:X::X:X> {multihop|local-address <A.B.C.D|X:X::X:X>|interface IFNAME|vrf NAME}`

```
sonic-cli(config-bfd)# peer 192.168.0.1 interface Ethernet0
```
#### Peer Deletion
`no peer <A.B.C.D|X:X::X:X> {multihop|local-address <A.B.C.D|X:X::X:X>|interface IFNAME|vrf NAME}`
```
sonic-cli(config-bfd)# no peer 192.168.0.1 interface Ethernet0
```
#### Detection Multiplier
`Default value is 3.`

`detect-multiplier (2-255)`

```
sonic-cli(conf-bfd-peer)# detect-multiplier 3
```

#### Receive Interval

`receive-interval in milliseconds, default value is 300msec`

`receive-interval (10-60000)`

```
sonic-cli(conf-bfd-peer)# receive-interval 300
```
#### Transmit Interval
`transmit-interval in milliseconds, default value is 300msec`

`transmit-interval (10-60000)`
```
sonic-cli(conf-bfd-peer)# transmit-interval 300
```
#### Echo Interval
`echo-interval in milliseconds, default value is 50msec`

`echo-interval (10-60000)`
```
sonic-cli(conf-bfd-peer)# echo-interval 50
```
#### Enable Echo mode
`echo-mode`
```
sonic-cli(conf-bfd-peer)# echo-mode
```
#### Disable Echo mode
`no echo-mode`
```
sonic-cli(conf-bfd-peer)# no echo-mode
```
#### Admin shutdown
`shutdown`
```
sonic-cli(conf-bfd-peer)# shutdown
```
#### Admin UP
`no shutdown`
```
sonic-cli(conf-bfd-peer)# no shutdown
```

#### 3.2.2.2 Show Commands
#### Display all BFD Peers
`show bfd peers [vrf NAME]`
```
sonic-cli# show bfd peers
BFD Peers:
        peer 192.168.0.1
                ID: 1
                Remote ID: 1
                Status: up
                Uptime: 1 minute(s), 51 second(s)
                Diagnostics: ok
                Remote diagnostics: ok
                Peer Type: dynamic
                Local timers:
                        Detect-multiplier: 3
                        Receive interval: 300ms
                        Transmission interval: 300ms
                        Echo transmission interval: disabled
                Remote timers:
                        Detect-multiplier: 3
                        Receive interval: 300ms
                        Transmission interval: 300ms
                        Echo transmission interval: 50ms

        peer 192.168.1.1
                label: router3-peer
                ID: 2
                Remote ID: 2
                Status: up
                Uptime: 1 minute(s), 53 second(s)
                Diagnostics: ok
                Remote diagnostics: ok
                Peer Type: dynamic
                Local timers:
                        Detect-multiplier: 3
                        Receive interval: 300ms
                        Transmission interval: 300ms
                        Echo transmission interval: disabled
                Remote timers:
                        Detect-multiplier: 3
                        Receive interval: 300ms
                        Transmission interval: 300ms
                        Echo transmission interval: 50ms
```
#### Display BFD peer brief
`show bfd peers [vrf NAME] brief`
```
sonic-cli# show bfd peers brief 
Session count: 1
SessionId  LocalAddress         PeerAddress      Status    Vrf
=========  ============         ===========      ======    =======
1          192.168.0.1          192.168.0.2         up     default
```
#### Display specific BFD peer
`show bfd peer <<A.B.C.D|X:X::X:X> [{multihop|local-address <A.B.C.D|X:X::X:X>|interface IFNAME|[vrf NAME]}]`
```
sonic-cli# show bfd peer 192.168.1.1
BFD Peer:
            peer 192.168.1.1
                label: router3-peer
                ID: 2
                Remote ID: 2
                Status: up
                Uptime: 3 minute(s), 4 second(s)
                Diagnostics: ok
                Remote diagnostics: ok
                Peer Type: dynamic
                Local timers:
                        Detect-multiplier: 3
                        Receive interval: 300ms
                        Transmission interval: 300ms
                        Echo transmission interval: disabled
                Remote timers:
                        Detect-multiplier: 3
                        Receive interval: 300ms
                        Transmission interval: 300ms
                        Echo transmission interval: 50ms
```


#### Display BFD peer counters
`show bfd peer counters <A.B.C.D|X:X::X:X> [{multihop|local-address <A.B.C.D|X:X::X:X>|interface IFNAME|[vrf NAME]}]`
```
sonic-cli# show bfd peers counters
BFD Peers:
     peer 192.168.2.1 interface Ethernet0
             Control packet input: 28 packets
             Control packet output: 28 packets
             Echo packet input: 0 packets
             Echo packet output: 0 packets
             Session up events: 1
             Session down events: 0
             Zebra notifications: 0

     peer 192.168.0.1
             Control packet input: 54 packets
             Control packet output: 103 packets
             Echo packet input: 965 packets
             Echo packet output: 966 packets
             Session up events: 1
             Session down events: 0
             Zebra notifications: 0

sonic-cli# show bfd peer 192.168.0.1 counters
     peer 192.168.0.1
             Control packet input: 126 packets
             Control packet output: 247 packets
             Echo packet input: 2409 packets
             Echo packet output: 2410 packets
             Session up events: 1
             Session down events: 0
             Zebra notifications: 0
```


#### 3.2.2.3 Debug Commands

`Enables packets related debugging for BFD`

`debug bfd packets`
```
sonic-cli# debug bfd packets
```

Yang files do not have support for debug command, debug command will be traslated to vtysh cli in the CLI actioner python script.

#### 3.2.2.4 Exec Commands
#### Clear BFD peer counters
`clear bfd peers command will only clear packet counters`

`clear bfd peer counters <A.B.C.D|X:X::X:X> [{multihop|local-address <A.B.C.D|X:X::X:X>|interface IFNAME}|[vrf NAME]]>`

```
sonic-cli# clear bfd peer counters 192.168.0.1 interface Ethernet0
```

Yang files do not have support for Exec command, Exec command will be translated to vtysh cli in the CLI actioner python script.

### 3.2.3 REST API Support

[BFD Yang](images/openconfig-bfd-ext.yang)

## 3.3 DB Changes
This section describes the changes made to different DB's for supporting BFD.
  ### 3.3.1 CONFIG DB
  
Two new table as below are defined to maintain BFD configuration.
  
  **BFD_PEER_SINGLE_HOP**
```
key remote-address interface vrf local-address 
local-address 
desired-minimum-tx-interval 
required-minimum-receive 
desired-minimum-echo-receive 
detection-multiplier 
echo-active 
```

**BFD_PEER_MULTI_HOP**
```
key remote-address interface vrf local-address 
desired-minimum-tx-interval
required-minimum-receive 
desired-minimum-echo-receive 
detection-multiplier 
```

# 4 Flow Diagrams
N/A

# 5 Error Handling
N/A

# 6 Serviceability and Debug
N/A

# 7 Warm Boot Support
N/A

# 8 Scalability
N/A

# 9 Unit Test
#### Configuration and Show via CLI

|**Test-Case ID**|**Test Scenario**|  
|----------------|-----------------|  
1| Verify BFD IPv4 session establishment.
2| Verify BFD IPv6 session establishment with vrf.
3| Verify BFD IPv6 session establishment with link-local address.
4| Verify BFD IPv6 session establishment with global address.
5| Verify BFD IPv6 session establishment with global address and vrf.
6| Verify CLI to configure transmit interval
7| Verify CLI to configure receive interval
8| Verify CLI to configure detection multiplier
9| Verify CLI to configure echo multiplier
10| Verify CLI to enable echo mode
11| Verify CLI to disable echo mode
12| Verify CLI to shutdown BFD peer.
13| Verify CLI to configure static IPv4 single hop peer with local address
14| Verify CLI to configure static IPv4 single hop peer with interface.
15| Verify CLI to configure static IPv4 single hop peer with local address and vrf.
16| Verify CLI to configure static IPv4 single hop peer with interface and vrf.
17| Verify CLI to configure static IPv6 single hop peer with local address
18| Verify CLI to configure static IPv6 single hop peer with interface.
19| Verify CLI to configure static IPv6 single hop peer with local address and vrf.
20| Verify CLI to configure static IPv6 single hop peer with interface and vrf.
21| Verify CLI to un-configure static IPv4 single hop peer with local address
22| Verify CLI to un-configure static IPv4 single hop peer with interface.
23| Verify CLI to un-configure static IPv4 single hop peer with local address and vrf
24| Verify CLI to un-configure static IPv4 single hop peer with interface and vrf.
25| Verify CLI to un-configure static IPv6 single hop peer with local address
26| Verify CLI to un-configure static IPv6 single hop peer with interface.
27| Verify CLI to un-configure static IPv6 single hop peer with local address and vrf.
28| Verify CLI to un-configure static IPv6 single hop peer with interface and vrf.
29| Verify CLI to un-configure static IPv6 single hop peer that doesn't exist.
30| Verify CLI to display IPv4 peer with local address.
31| Verify CLI to display IPv4 peer with interface.
32| Verify CLI to display IPv4 peer with local address and vrf.
33| Verify CLI to display IPv4 peer with interface and vrf.
34| Verify CLI to display non existent IPv4 peer.
35| Verify CLI to display IPv6 peer with local address.
36| Verify CLI to display IPv6 peer with interface.
37| Verify CLI to display IPv6 peer with local address and vrf.
38| Verify CLI to display IPv6 peer with interface and vrf.
39| Verify CLI to display non existent IPv6 peer.
40| Verify CLI to display IPv4 multihop Peer.
41| Verify CLI to display IPv6 multihop Peer.
42| Verify CLI to display IPv4 multihop Peer with vrf.
43| Verify CLI to display IPv6 multihop Peer with vrf.
44| Verify CLI to display non existent IPv6 multihop peer.
45| Verify CLI to display IPv4 Peer brief.
46| Verify CLI to display IPv6 Peer brief.
47| Verify CLI to display IPv4 Peer brief with vrf.
48| Verify CLI to display IPv6 Peer brief with vrf.
49| Verify CLI to display non existent IPv4 Peer brief.
50| Verify CLI to display IPv4 peer counters.
51| Verify CLI to display IPv6 peer counters.
52| Verify CLI to display IPv4 peer counters with vrf.
53| Verify CLI to display IPv6 peer counters with vrf.
54| Verify CLI to display IPv4 peer counters for an invalid peer.
55| Verify CLI to clear IPv4 peer counters.
56| Verify CLI to clear IPv6 peer counters.
57| Verify CLI to clear IPv4 peer counters with vrf.
58| Verify CLI to clear IPv6 peer counters with vrf.
59| Verify CLI to clear IPv6 peer counters for invalid peer.
60| Verify CLI by displaying multiple BFD IPv4 single hop session.
61| Verify CLI by displaying multiple BFD IPv6 single hop session.
62| Verify CLI by displaying multiple BFD IPv4 multi hop session.
63| Verify CLI by displaying multiple BFD IPv6 multi hop session.
64| Verify config save and reload of BFD configuration.
65| Verify unsaved config loss after reload.
66| Verify enabling BFD debug CLI.
67| Verify disablng BFD debug CLI.

#### Configuration via gNMI

Same as CLI configuration test, but using gNMI SET request

#### Get configuration via gNMI

Same as CLI show test, but using gNMI GET request, verify the JSON response.

#### Configuration via REST (PATCH)

Same as CLI configuration test, but using REST request

#### Get configuration via REST (GET)

Same as CLI show test, but using REST GET request, verify the JSON response.

# 10 Internal Design Information
N/A
