# VRRP Manageability  

### Rev 0.1

## Table of Contents
- [1. Revision](#1-revision)
- [2. Functional Description](#3-functional-description)
  - [2.1 REST URI Supported for VRRP](#31-ztp-json)
  - [2.2 VRRP schema in database](#32-ztp-plugins)
  - [2.3 KLISH CLI for VRRP](#33-dynamic-content)
- [3. Unit Test Summary](#4-chronology-of-events)

## 1. Revision
| Rev | Date     |  Author       | Change Description                |
|:---:|:------------:|:------------------:|-----------------------------------|
| v0.1 |   04/27/2020   |   Vijay Kumar Vasantha   | Initial version                   |
| v0.2 | 06/01/2020 | Vijay Kumar Vasnatha | Reworked on review comments |

## 2. Functional Description

This document describes the manageability support for VRRP, here VRRP refers to both IPv4 and IPv6 VRRP. VRRP manageability will be based on open-config yang model and an equivalent KLISH CLI support will be added.

## 2.1 REST URI supported for VRRP

Below are the REST URI supported for VRRP,

| Operations supported for below URI are GET, HEAD, PUT, PATCH, DELETE |
| ------------------------------------------------------------ |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id} |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/config |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/config/virtual-address |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/config/priority |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/config/preempt |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/config/advertisement-interval |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/openconfig-interfaces-ext:vrrp-track-interface={track-intf}/config/priority-increment |

| VRRP IPv6 URIs                                               |
| ------------------------------------------------------------ |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id} |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/config |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/config/virtual-address |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/config/priority |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/config/preempt |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/config/advertisement-interval |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/openconfig-interfaces-ext:vrrp-track-interface={track-intf}/config/priority-increment |

| Operations supported are GET, HEAD                           |
| ------------------------------------------------------------ |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/state |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/state/virtual-router-id |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/state/virtual-address |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/state/priority |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/state/preempt |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/state/advertisement-interval |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/state/current-priority |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/openconfig-interfaces-ext:vrrp-track-interface={track-intf}/state |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv4/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/openconfig-interfaces-ext:vrrp-track-interface={track-intf}/state/priority-increment |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/state |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/state/virtual-router-id |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/state/virtual-address |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/state/priority |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/state/preempt |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/state/advertisement-interval |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/state/current-priority |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/openconfig-interfaces-ext:vrrp-track-interface={track-intf}/state |
| /restconf/data/openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface={index}/openconfig-if-ip:ipv6/addresses/address={ip}/vrrp/vrrp-group={virtual-router-id}/openconfig-interfaces-ext:vrrp-track-interface={track-intf}/state/priority-increment |

## 2.2 VRRP Schema in database

Below is a sample of VRRP schema and its representation in config database

```json
key "VRRP|Ethernet64|1"
{
"vip@"
"10.0.0.5,"
"priority"
"100"
"adv_interval"
"1"
"state"
""
"version"
"2"
"pre_empt"
"True"
}

key "VRRP_TRACK|Ethernet64|1|Ethernet12"
"priority_increment"
"2"

key "VRRP_TRACK|Ethernet64|1|Ethernet16"
"priority_increment"
"15"

key "VRRP6|Ethernet64|5"
{
"vip@"
"10::5,"
"priority"
"100"
"adv_interval"
"1"
"state"
""
"pre_empt"
"True"
}

key "VRRP_TRACK6|Ethernet64|5|Ethernet16"
"priority_increment"
"30"
```

## 2.3 KLISH CLI for VRRP

Below is the syntax of klish CLI and its comparison with existing click CLI. 

VRRP commands will be in vrrp mode on an interface.

- [ ] | KLISH CLI                                               | CLICK CLI                                                    |
  | ------------------------------------------------------- | ------------------------------------------------------------ |
  | **[no] vrrp** <vrid>  **address-family {ipv4 \| ipv6}** | **configure interface [vrrp\|vrrp6] {add\|remove}**<ifname> <vrid> |
  | **priority** <priority-value>                           | **configure interface vrrp priority** <ifname> <vrid> <priority-value> |
  | **advertisement-interval** <interval-value>             | **configure interface vrrp adv_interval** <ifname> <vrid> <interval-value> |
  | **[no] vip** <vip>                                      | **configure interface vrrp vip {add\| remove}** <ifname> <vrid> <vip> |
  | **[no] preempt**                                        | **configure interface vrrp pre_empt {enable \| disable} <ifname> <vrid>** |
  | **[no] track-interface** <track-ifname> <wt-value>      | **configure interface vrrp track_interface {add\| remove} <ifname> <vrid> <track-interface> <weight>** |
  | **[no] vrrpv3**                                         | **configure interface vrrp version** {2 \| 3}                |

Example of config command,

```json
sonic-cli(conf-if-Vlan10)# vrrp 1 address-family ipv4
sonic-cli(conf-if-Vlan10-vrrp-ipv4-1)#vip 10.0.0.10
sonic-cli(conf-if-Vlan10-vrrp-ipv4-1)#priority 90
sonic-cli(conf-if-Vlan10-vrrp-ipv4-1)#advertisement-interval 5

```



The show command and its output will remain same as CLICK CLI. Show commands added will be

| KLISH CLI                                             | CLICK CLI                              |
| ----------------------------------------------------- | -------------------------------------- |
| **show vrrp interface <interface-name> vrid<vrid>**   | **show vrrp <interface-name> <vrid>**  |
| **show vrrp6 interface <interface-name> vrid <vrid>** | **show vrrp6 <interface-name> <vrid>** |
| **show vrrp**                                         | **show vrrp**                          |
| **show vrrp6**                                        | **show vrrp6**                         |

Example output of show command,

```json
sonic-cli#show vrrp interface Vlan10 vrid 1
VRRP instances
--------------------------------------------------------------------
Vlan10, VRID 1
Version is 2
State is Master
Virtual IP address:
  10.0.0.10
Virtual MAC address is 0000.5e00.0101
Configured Priority is 90, Current Priority is 100
Track interface:
  Intfname       State Priority
  Vlan10         Up    10
Advertisement interval is 3 sec
Preemption is enabled


sonic-cli#show vrrp6 interface Vlan10 vrid 5
VRRP instances
--------------------------------------------------------------------
Vlan10, VRID 5
Version is 3
State is Master
Virtual IP address:
  10::5
Virtual MAC address is 0000.5e00.0205
Configured Priority is 90, Current Priority is 100
Track interface:
  Intfname       State Priority
  Vlan10         Up    10
Advertisement interval is 3 sec
Preemption is enabled
```

## 3 Unit Test Summary

| TEST ID | Description                                                  | Result |
| ------- | ------------------------------------------------------------ | ------ |
| 1.      | Add VRRP session <1-n> on Ethernet interface. Check that it is configured. | Pass   |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |
|         |                                                              |        |


