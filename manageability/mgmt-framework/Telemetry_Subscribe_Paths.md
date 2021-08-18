# Telemetry Subscribe YANG Paths

This document lists Telemetry ON_CHANGE and wildcard path subscription enabled YANG paths.

## Table of Contents

- [1 Overview](#1-overview)
  - [1.1 Background](#11-background)
  - [1.2 Scope](#12-scope)
  - [1.3 ON_CHANGE supported vs not supported](#13-on_change-supported-vs-not-supported)
- [2 OpenConfig YANG Modules](#2-openconfig-yang-modules)
  - [2.1 openconfig-network-instance](#21-openconfig-network-instance)
    - [2.1.1 IGMP Snooping](#211-igmp-snooping)
  - [2.2 openconfig-tam](#22-openconfig-tam)

## Revision

| Rev |     Date    |       Author       | Change Description                           |
|-----|-------------|--------------------|----------------------------------------------|
| 0.1 | 05/18/2021  | Sachin Holla       | Initial version                              |
| 0.2 | 07/30/2021  | Sachin Holla       | Overview section update                      |

## References

SONiC Telemetry Subscribe HLD<br>
https://github.com/BRCM-SONIC/sonic_doc_private/blob/master/manageability/mgmt-framework/Telemetry_Subscribe_RPC.md

gNMI Specification<br>
https://github.com/openconfig/reference/blob/master/rpc/gnmi/gnmi-specification.md

## 1 Overview

### 1.1 Background

SONiC telemetry server supports SAMPLE, POLL and ONCE subscriptions for any YANG path as long as
the subscribed path does not include any wildcard keys.
ON_CHANGE subscription and wildcard path support (for any subscription type) requires changes in
both infrastructure and the individual app code.
Infrastructure changes are implemented in SONiC 3.2.0 and 4.0.0 releases.
But not all apps are ready for it yet.
This document intends to list YANG modules that have been qualified by app teams
for wildcard paths and ON_CHANGE subscription.

### 1.2 Scope

This document lists YANG modules or paths that support ON_CHANGE subscription and
wildcard path subscription for all subscription types.
Paths that do not support ON_CHANGE subscription on purpose are also called out here.

However listing every YANG path and its preference is not practical.
Hence this document lists YANG modules and captures the special cases for each.
Eg. if ON_CHANGE is not supported for only few paths, then it can just list the ON_CHANGE unsupported pats.

### 1.3 ON_CHANGE supported vs not supported

Not all YANG paths will support ON_CHANGE subscription.
YANG nodes representing non-DB data or frequently changing data will not support ON_CHANGE.
App teams will capture this infomation in this document.
Note that if ON_CHANGE is not supported for a node, ON_CHANGE subscription cannot be supported
for its child paths or paths poiting to its parent containers.
This is implicit and apps may omit child node/leaf paths for brevity.
But SAMPLE or TARGET_DEFINED subscriptions are supported.

Few examples with a simplified openconfig-interfaces YANG model:

```text
module: openconfig-interfaces
  +--rw interfaces
     +--rw interface*
        +--rw name
        +--rw config
        |  +--rw enabled
        +--ro state
        |  +--ro enabled
        |  +--ro oper-status
        |  +--ro counters     ====> ON_CHANGE not supported
        |  |  +--ro in-octets
```

Let us say ON_CHANGE is not supported for `/interfaces/interface/state/counters` path.
Following table lists the Telemetry server's behavior for different combinations of subscription modes and paths.

| **Mode**       | **Subscribe Path**                       | **Result** |
|----------------|------------------------------------------|------------|
| ON_CHANGE      | /interfaces/interface[name=\*]           | error (child node *counters* does not support ON_CHANGE) |
| ON_CHANGE      | /interfaces/interface[name=\*]/config    | ON_CHANGE subscription               |
| ON_CHANGE      | /interfaces/interface[name=\*]/state     | error                                |
| ON_CHANGE      | /interfaces/interface[name=\*]/state/oper-status  | ON_CHANGE  subscription     |
| ON_CHANGE      | /interfaces/interface[name=\*]/state/counters     | error                       |
| ON_CHANGE      | /interfaces/interface[name=\*]/state/counters/in-octets | error                 |
| SAMPLE         | /interfaces/interface[name=\*]           | SAMPLE subscription                  |
| TARGET_DEFINED | /interfaces/interface[name=\*]           | SAMPLE subscription for /interfaces/interface[name=\*]/state/counters;<br>ON_CHANGE subscription for other paths |
| TARGET_DEFINED | /interfaces/interface[name=\*]/config    | ON_CHANGE subscription               |
| TARGET_DEFINED | /interfaces/interface[name=\*]/state     | SAMPLE subscription for /interfaces/interface[name=\*]/state/counters;<br>ON_CHANGE subscription for other paths |
| TARGET_DEFINED | /interfaces/interface[name=\*]/state/enabled      | ON_CHANGE subscription      |
| TARGET_DEFINED | /interfaces/interface[name=\*]/state/counters     | SAMPLE subscription         |

Please refer to [SONiC Telemetry Subscribe HLD](https://github.com/BRCM-SONIC/sonic_doc_private/blob/master/manageability/mgmt-framework/Telemetry_Subscribe_RPC.md) for details.

## 2 OpenConfig YANG Modules

### 2.1 openconfig-network-instance

#### 2.1.1 IGMP Snooping

ON_CHANGE subscription are supported for following paths.

```text
openconfig-network-instance:network-instances/network-instance[name=*]/protocols/protocol[identifier=*][name=*]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/config

openconfig-network-instance:network-instances/network-instance[name=*]/protocols/protocol[identifier=*][name=*]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/state

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=*][name=*]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/config

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=*][name=*]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/state

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/config

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/state

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=<vlan-intf-name>]/config

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=<vlan-intf-name>]/state

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/config/version

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/state/version

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=<vlan-intf-name>]/config/version

openconfig-network-instance:network-instances/network-instance[name=*]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/config/query-max-response-time

openconfig-network-instance:network-instances/network-instance[name=*]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/state/query-max-response-time

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=*][name=*]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/config/query-interval

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=*][name=*]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/state/query-interval

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=<vlan-intf-name>]/config/last-member-query-interval

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=<vlan-intf-name>]/state/last-member-query-interval

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=<vlan-intf-name>]/config/fast-leave

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=<vlan-intf-name>]/state/fast-leave

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=<vlan-intf-name>]/state/last-member-query-interval

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=<vlan-intf-name>]/config/fast-leave

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=<vlan-intf-name>]/state/fast-leave

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/staticgrps

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/staticgrps/static-multicast-group[group=*][source-addr=*]/config

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/staticgrps/static-multicast-group

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/staticgrps/static-multicast-group[group=*][source-addr=*]/config/outgoing-interface

openconfig-network-instance:network-instances/network-instance[name=default]/protocols/protocol[identifier=IGMP_SNOOPING][name=IGMP-SNOOPING]/openconfig-network-instance-deviation:igmp-snooping/interfaces/interface[name=*]/staticgrps/static-multicast-group[group=*][source-addr=*]/state/outgoing-interface
```

### 2.2 openconfig-tam

All paths of **openconfig-tam** module supports wildcard path subscriptions.
ON_CHANGE subscription is also supported for all paths except for the following:

```text
/openconfig-tam:tam/flowgroups
/openconfig-tam:tam/flowgroups/flowgroup[name=*]
/openconfig-tam:tam/flowgroups/flowgroup[name=*]/state
/openconfig-tam:tam/flowgroups/flowgroup[name=*]/state/statistics
/openconfig-tam:tam/flowgroups/flowgroup[name=*]/state/statistics/packets
/openconfig-tam:tam/flowgroups/flowgroup[name=*]/state/statistics/bytes
```

Flowgroup statistics are actually ACL counters and are read from COUNTERS DB.
ON_CHANGE is disabled to avoid possibility of continuous notifications.
