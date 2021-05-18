# Telemetry Subscribe YANG Paths

This document lists Telemetry ON_CHANGE enabled or disabled YANG paths.

## Table of Contents

- [1 Overview](#1-overview)
- [2 OpenConfig YANG Modules](#2-openconfig-yang-modules)
  - [2.1 openconfig-network-instance](#21-openconfig-network-instance)
    - [2.1.1 IGMP Snooping](#211-igmp-snooping)
  - [2.2 openconfig-tam](#22-openconfig-tam)

## Revision

| Rev |     Date    |       Author       | Change Description                           |
|-----|-------------|--------------------|----------------------------------------------|
| 0.1 | 05/18/2021  | Sachin Holla       | Initial version                              |

## References

SONiC Telemetry Subscribe HLD<br>
https://github.com/BRCM-SONIC/sonic_doc_private/blob/master/manageability/mgmt-framework/Telemetry_Subscribe_RPC.md

gNMI Specification<br>
https://github.com/openconfig/reference/blob/master/rpc/gnmi/gnmi-specification.md

## 1 Overview

This document intends to list Telemetry ON_CHANGE disabled or enabled YANG paths.
However listing every YANG path and its preference is not practical.
Hence this document lists YANG modules and captures the special cases for each.
Eg. if ON_CHANGE is not supported for only few paths, then it can just list the ON_CHANGE unsupported pats.

Note that if ON_CHANGE is not supported for a node, ON_CHANGE subscription cannot be supported
for its child paths or paths poiting to its parent containers.
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
| ON_CHANGE      | /interfaces/interface[name=\*]           | error (counters does not support ON_CHANGE) |
| ON_CHANGE      | /interfaces/interface[name=\*]/config    | ON_CHANGE subscription                      |
| ON_CHANGE      | /interfaces/interface[name=\*]/state     | error (counters does not support ON_CHANGE) |
| ON_CHANGE      | /interfaces/interface[name=\*]/state/oper-status  | ON_CHANGE  subscription            |
| ON_CHANGE      | /interfaces/interface[name=\*]/state/counters     | error (counters does not support ON_CHANGE) |
| ON_CHANGE      | /interfaces/interface[name=\*]/state/counters/in-octets | error (counters does not support ON_CHANGE) |
| SAMPLE         | /interfaces/interface[name=\*]           | SAMPLE subscription                         |
| TARGET_DEFINED | /interfaces/interface[name=\*]           | SAMPLE subscription (counters does not support ON_CHANGE) |
| TARGET_DEFINED | /interfaces/interface[name=\*]           | SAMPLE subscription for /interfaces/interface[name=\*]/state/counters;<br>ON_CHANGE subscription for other paths |
| TARGET_DEFINED | /interfaces/interface[name=\*]/config    | ON_CHANGE subscription                      |
| TARGET_DEFINED | /interfaces/interface[name=\*]/state     | SAMPLE subscription for /interfaces/interface[name=\*]/state/counters;<br>ON_CHANGE subscription for other paths |
| TARGET_DEFINED | /interfaces/interface[name=\*]/state/enabled      | ON_CHANGE subscription             |
| TARGET_DEFINED | /interfaces/interface[name=\*]/state/counters     | SAMPLE subscription                |

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

All paths of openconfig-tam module supports ON_CHANGE except for the following paths.

```text
/openconfig-tam:tam/flowgroups

/openconfig-tam:tam/flowgroups/flowgroup[name=*]

/openconfig-tam:tam/flowgroups/flowgroup[name=*]/state

/openconfig-tam:tam/flowgroups/flowgroup[name=*]/state/statistics

/openconfig-tam:tam/flowgroups/flowgroup[name=*]/state/statistics/packets

/openconfig-tam:tam/flowgroups/flowgroup[name=*]/state/statistics/bytes
```
