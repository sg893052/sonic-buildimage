# Configurable Drop Counters in SONiC

# High Level Design Document
#### Rev 1.1
# Table of Contents
* [List of Tables](#list-of-tables)
* [List of Figures](#list-of-figures)
* [Revision](#revision)
* [About this Manual](#about-this-manual)
* [Scope](#scope)
* [Defintions/Abbreviation](#definitionsabbreviation)
* [1 Overview](#1-overview)
    - [1.1 Use Cases](#11-use-cases)
        - [1.1.1 A flexible "drop filter"](#111-a-flexible-"drop-filter")
        - [1.1.2 A helpful debugging tool](#112-a-helpful-debugging-tool)
        - [1.1.3 More sophisticated monitoring schemes](#113-more-sophisticated-monitoring-schemes)
        - [1.1.4 Capture drop frames](#114-capture-drop-frames)
* [2 Requirements](#2-requirements)
    - [2.1 Functional Requirements](#21-functional-requirements)
    - [2.2 Configuration and Management Requirements](#22-configuration-and-management-requirements)
    - [2.3 Scalability Requirements](#23-scalability-requirements)
    - [2.4 Supported Debug Counters](#24-supported-debug-counters)
* [3 Design](#3-design)
    - [3.1 CLI (and usage example)](#31-cli-and-usage-example)
        - [3.1.1 Displaying available counter capabilities](#311-displaying-available-counter-capabilities)
        - [3.1.2 Displaying current counter configuration](#312-displaying-current-counter-configuration)
        - [3.1.3 Displaying the current counts](#313-displaying-the-current-counts)
        - [3.1.4 Clearing the counts](#314-clearing-the-counts)
        - [3.1.5 Configuring counters from the CLI](#315-configuring-counters-from-the-CLI)
    - [3.2 Config DB](#32-config-db)
        - [3.2.1 DEBUG_COUNTER Table](#321-debug_counter-table)
        - [3.2.2 PACKET_DROP_COUNTER_REASON Table](#322-packet_drop_counter_reason-table)
    - [3.3 State DB](#33-state-db)
        - [3.3.1 DEBUG_COUNTER_CAPABILITIES Table](#331-debug-counter-capabilities-table)
        - [3.3.2 SAI APIs](#332-sai-apis)
    - [3.4 Counters DB](#34-counters-db)
    - [3.5 SWSS](#35-swss)
        - [3.5.1 SAI APIs](#351-sai-apis)
    - [3.6 syncd](#36-syncd)
    - [3.7 SAI](#37-sai)
    - [3.8 KLISH CLI](#38-klish-cli)
        - [3.8.1 Show Commands](#381-show-commands)
            - [3.8.1.1 Show available counter capabilities](#3811-show-available-counter-capabilities)
            - [3.8.1.2 Show current counter configuration](#3812-show-current-counter-configuration)
            - [3.8.1.3 Show interface drop counters](#3813-show-interface-drop-counters)
            - [3.8.1.4 Show switch drop counters](#3814-show-switch-drop-counters)
        - [3.8.2 Clear Commands](#382-clear-commands)
            - [3.8.2.1 Clear drop counters](#3821-clear-drop-counters)
        - [3.8.3 Config Commands](#383-config-commands)
            - [3.8.3.1 Create or Delete drop counter](#3831-create-or-delete-drop-counter)
            - [3.8.3.2 Add or delete drop reasons](#3832-add-or-delete-drop-reasons)
            - [3.8.3.3 Add or delete description](#3833-add-or-delete-description)
            - [3.8.3.4 Set drop counter type](#3834-set-drop-counter-type)
            - [3.8.3.5 Add or delete group](#3835-add-or-delete-group)
            - [3.8.3.6 Add or delete alias](#3836-add-or-delete-alias)
            - [3.8.3.7 Add or delete mirror session](#3837-add-or-delete-mirror-session)
            - [3.8.3.8 Activate Drop counter](#3838-activate-drop-counter)
    - [3.9 SONIC Yang model](#39-sonic-yang-model)
    - [3.10 Open config Yang model](#310-open-config-yang-model)
* [4 Flows](#4-flows)
    - [4.1 General Flow](#41-general-flow)
* [5 Warm Reboot Support](#5-warm-reboot-support)
* [6 Unit Tests](#6-unit-tests)
* [7 Platform Support](#7-platform-support)
    - [7.1 Known Limitations](#7.1-known-limitations)
* [8 Open Questions](#8-open-questions)
* [9 Acknowledgements](#9-acknowledgements)
* [10 References](#10-references)
* [11 Sample Configuration](#11-sample-configuration)
- [Broadcom Internal Information : To be removed before publishing externally.](#broadcom-internal-information---to-be-removed-before-publishing-externally)
# List of Tables
* [Table 1: Abbreviations](#definitionsabbreviation)

# List of Figures
* [Figure 1: General Flow](#41-general-flow)

# Revision
| Rev | Date     | Author      | Change Description        |
|:---:|:--------:|:-----------:|---------------------------|
| 0.1 | 07/30/19 | Danny Allen | Initial version           |
| 0.2 | 09/03/19 | Danny Allen | Review updates            |
| 0.3 | 09/19/19 | Danny Allen | Community meeting updates |
| 1.0 | 11/19/19 | Danny Allen | Code review updates       |
| 1.1 | 21/05/21 | Rupesh Kumar| Add enhancements          |

# About this Manual
This document provides an overview of the implementation of configurable packet drop counters in SONiC.

# Scope
This document describes the high level design of the configurable drop counter feature.

# Definitions/Abbreviation
| Abbreviation | Description     |
|--------------|-----------------|
| RX           | Receive/ingress |
| TX           | Transmit/egress |

## 1 Overview
The main goal of this feature is to provide better packet drop visibility in SONiC by providing a mechanism to count, classify and capture packet drops that occur due to different reasons.

The other goal of this feature is for users to be able to track the types of drop reasons that are important for their scenario. Because different users have different priorities, and because priorities change over time, it is important for this feature to be easily configurable. Users can configure mirroring selected/all drop frames.

We will accomplish both goals by adding support for SAI debug counters to SONiC.
* Support for creating and configuring port-level and switch-level debug counters will be added to orchagent and syncd.
* A CLI tool will be provided for users to manage and configure their own drop counters
* CLI tool also takes care of configuring mirroring for specific or all drops.

## 1.1 Use Cases
There are a couple of potential use cases for these drop counters. Use cases till 1.1.3 are supported by community sonic and rest are implemented in this feature.

### 1.1.1 A flexible "drop filter"
One potential use case is to use the drop counters to create a filter of sorts for the standard STAT_IF_IN/OUT_DISCARDS counters. Say, for example:
- Packets X, Y, and Z exist in our system
- Our switches should drop X, Y, and Z when they receive them

We can configure a drop counter (call it "EXPECTED_DROPS", for example) that counts X, Y, and Z. If STAT_IF_IN_DISCARDS = EXPECTED_DROPS, then we know our switch is healthy and that everything is working as intended. If the counts don't match up, then there may be a problem.

### 1.1.2 A helpful debugging tool
Another potential use case is to configure the counters on the fly in order to help debug packet loss issues. For example, if we're consistently experiencing packet loss in your system, we might try:
- Creating a counter that tracks L2_ANY and a counter that tracks L3_ANY
- L2_ANY is incrementing, so we delete these two counters and create MAC_COUNTER that tracks MAC-related reasons (SMAC_EQUALS_DMAC, DMAC_RESERVED, etc.), VLAN_COUNTER that tracks VLAN related reasons, (INGRESS_VLAN_FILTER, VLAN_TAG_NOT_ALLOWED), and OTHER_COUNTER that tracks everything else (EXCEEDS_L2_MTU, FDB_UC_DISCARD, etc.)
- OTHER_COUNTER is incrementing, so we delete the previous counters and create a counter that tracks the individual reasons from OTHER_COUNTER
- We discover that the EXCEEDS_L2_MTU counter is increasing. There might be an MTU mismatch somewhere in our system!

### 1.1.3 More sophisticated monitoring schemes
Some have suggested other deployment schemes to try to sample the specific types of packet drops that are occurring in their system. Some of these ideas include:
- Periodically (e.g. every 30s) cycling through different sets of drop counters on a given device
- "Striping" drop counters across different devices in the system (e.g. these 3 switches are tracking VLAN drops, these 3 switches are tracking ACL drops, etc.)
- An automatic version of [1.1.2](#112-a-helpful-debugging-tool) that adapts the drop counter configuration based on which counters are incrementing

### 1.1.4 Capture drop frames
Capturing dropped frames is useful in some cases to enhance debugging. The captured frames can be mirrored to CPU or any other physical port and can be analysed further.

Frames mirrored to CPU are mirrored to specific drop counter queue. proc entry will be added to display the captured frames. The frames trapped on specific drop counter queue are not sent to the Linux stack and will be dropped in the knet driver. Users can however capture the frames by reading proc entry. 
The proc entry shows last 512 frames captured on the drop counter queue.

DUT# cat /proc/bcm/knet-cb/drop_pkt_dump

# 2 Requirements

## 2.1 Functional Requirements
1. CONFIG_DB can be configured to create debug counters
2. STATE_DB can be queried for debug counter capabilities
3. Users can access drop counter information via a Click/ KLISH, GNMI, REST interfaces.
    1. Users can see what capabilities are available to them
        1. Types of counters (i.e. port-level and/or switch-level)
        2. Number of counters
        3. Supported drop reasons
    2. Users can see what types of drops each configured counter contains
    3. Users can see which drop frames can be mirrored.
    4. Users can add and remove drop reasons from each counter
    5. Users can read the current value of each counter
    6. Users can assign aliases to counters
    7. Users can clear counters
    8. Users can mirror all/selected drop frames.

## 2.2 Configuration and Management Requirements
Configuration of the drop counters can be done via:
* config_db.json
* Click CLI
* KLISH CLI
* REST
* GNMI
* Open config Yang

## 2.3 Scalability Requirements
Users must be able to use all debug counters and drop reasons provided by the underlying hardware.

Interacting with debug counters will not interfere with existing hardware counters (e.g. portstat). Likewise, interacting with existing hardware counters will not interfere with debug counter behavior.

## 2.4 Supported Debug Counters
* PORT_INGRESS_DROPS: port-level ingress drop counters
* PORT_EGRESS_DROPS: port-level egress drop counters
* SWITCH_INGRESS_DROPS: switch-level ingress drop counters
* SWITCH_EGRESS_DROPS: switch-level egress drop counters

# 3 Design

## 3.1 CLI (and usage example)
The CLI tool will provide the following functionality:
* See available drop counter capabilities: `show dropcounters capabilities`
* See drop counter config: `show dropcounters configuration`
* Show drop counts: `show dropcounters counts`
* Clear drop counters: `sonic-clear dropcounters`
* Initialize a new drop counter: `config dropcounters install`
* Add drop reasons to a drop counter: `config dropcounters add_reasons`
* Remove drop reasons from a drop counter: `config dropcounters remove_reasons`
* Delete a drop counter: `config dropcounters delete`

### 3.1.1 Displaying available counter capabilities
```
admin@sonic:~$ show dropcounters capabilities
Counter Type                         Total
--------------------                 -------
PORT_INGRESS_DROPS                   10
SWITCH_EGRESS_DROPS                  2
PORT_MIRROR_SUPPORTED_INGRESS_DROPS  5

PORT_INGRESS_DROPS:
      L2_ANY
      SMAC_MULTICAST
      SMAC_EQUALS_DMAC
      INGRESS_VLAN_FILTER
      EXCEEDS_L2_MTU
      SIP_CLASS_E
      SIP_LINK_LOCAL
      DIP_LINK_LOCAL
      UNRESOLVED_NEXT_HOP
      DECAP_ERROR

SWITCH_EGRESS_DROPS:
      L2_ANY
      L3_ANY
      A_CUSTOM_REASON

PORT_MIRROR_SUPPORTED_INGRESS_DROPS:
      L2_ANY
      SMAC_MULTICAST
      SMAC_EQUALS_DMAC
      INGRESS_VLAN_FILTER
      EXCEEDS_L2_MTU

```

### 3.1.2 Displaying current counter configuration
```
admin@sonic:~$ show dropcounters configuration
Counter   Alias     Group  Type                Mirror          Reasons              Description
--------  --------  -----  ------------------  -------         -------------------  --------------
DEBUG_0   RX_LEGIT  LEGIT  PORT_INGRESS_DROPS  Session1        SMAC_EQUALS_DMAC     Legitimate port-level RX pipeline drops
                                                               INGRESS_VLAN_FILTER
DEBUG_1   TX_LEGIT  None   SWITCH_EGRESS_DROPS NA              EGRESS_VLAN_FILTER   Legitimate switch-level TX pipeline drops

admin@sonic:~$ show dropcounters configuration -g LEGIT
Counter   Alias     Group  Type                Mirror          Reasons              Description
--------  --------  -----  ------------------  -------         -------------------  --------------
DEBUG_0   RX_LEGIT  LEGIT  PORT_INGRESS_DROPS  Session1        SMAC_EQUALS_DMAC     Legitimate port-level RX pipeline drops
                                                               INGRESS_VLAN_FILTER
```

### 3.1.3 Displaying the current counts

```
admin@sonic:~$ show dropcounters counts
    IFACE    STATE    RX_ERR    RX_DROPS    TX_ERR    TX_DROPS   RX_LEGIT
---------  -------  --------  ----------  --------  ----------  ---------
Ethernet0        U        10         100         0           0         20
Ethernet4        U         0        1000         0           0        100
Ethernet8        U       100          10         0           0          0

DEVICE  TX_LEGIT
------  --------
sonic       1000

admin@sonic:~$ show dropcounters counts -g LEGIT
    IFACE    STATE    RX_ERR    RX_DROPS    TX_ERR    TX_DROPS   RX_LEGIT
---------  -------  --------  ----------  --------  ----------  ---------
Ethernet0        U        10         100         0           0         20
Ethernet4        U         0        1000         0           0        100
Ethernet8        U       100          10         0           0          0

admin@sonic:~$ show dropcounters counts -t SWITCH_EGRESS_DROPS
DEVICE  TX_LEGIT
------  --------
sonic       1000
```

### 3.1.4 Clearing the counts
```
admin@sonic:~$ sonic-clear dropcounters
Cleared drop counters
```

### 3.1.5 Configuring counters from the CLI
```
admin@sonic:~$ sudo config dropcounters install DEBUG_2 PORT_INGRESS_DROPS [EXCEEDS_L2_MTU,DECAP_ERROR] -d "More port ingress drops" -g BAD -a BAD_DROPS
admin@sonic:~$ sudo config dropcounters add_reasons DEBUG_2 [SIP_CLASS_E]
admin@sonic:~$ sudo config dropcounters remove_reasons DEBUG_2 [SIP_CLASS_E]
admin@sonic:~$ sudo config dropcounters delete DEBUG_2

admin@sonic:~$ sudo config dropcounters install DEBUG_2 PORT_MIRROR_SUPPORTED_INGRESS_DROPS [EXCEEDS_L2_MTU,DECAP_ERROR] -d "More port ingress drops" -g BAD -a BAD_DROPS -m Mirror1
```

## 3.2 Config DB
Two new tables will be added to Config DB:
* DEBUG_COUNTER to store general debug counter metadata
* DEBUG_COUNTER_DROP_REASON to store drop reasons for debug counters that have been configured to track packet drops

### 3.2.1 DEBUG_COUNTER Table
Example:
```
{
    "DEBUG_COUNTER": {
        "DEBUG_0": {
            "alias": "PORT_RX_LEGIT",
            "type": "PORT_INGRESS_DROPS",
            "desc": "Legitimate port-level RX pipeline drops",
            "group": "LEGIT"
            "Mirror": "Session1"
        },
        "DEBUG_1": {
            "alias": "PORT_TX_LEGIT",
            "type": "PORT_EGRESS_DROPS",
            "desc": "Legitimate port-level TX pipeline drops"
            "group": "LEGIT"
        },
        "DEBUG_2": {
            "alias": "SWITCH_RX_LEGIT",
            "type": "SWITCH_INGRESS_DROPS",
            "desc": "Legitimate switch-level RX pipeline drops"
            "group": "LEGIT"
        }
    }
}
```

### 3.2.2 DEBUG_COUNTER_DROP_REASON Table
Example:
```
{
    "DEBUG_COUNTER_DROP_REASON": {
        "DEBUG_0|SMAC_EQUALS_DMAC": {},
        "DEBUG_0|INGRESS_VLAN_FILTER": {},
        "DEBUG_1|EGRESS_VLAN_FILTER": {},
        "DEBUG_2|TTL": {},
    }
}
```

## 3.3 State DB
State DB will store information about:
* What types of drop counters are available on this device
* How many drop counters are available on this device
* What drop reasons are supported by this device

### 3.3.1 DEBUG_COUNTER_CAPABILITIES Table
Example:
```
{
    "DEBUG_COUNTER_CAPABILITIES": {
        "SWITCH_INGRESS_DROPS": {
            "count": "3",
            "reasons": "[L2_ANY, L3_ANY, SMAC_EQUALS_DMAC]"
        },
        "SWITCH_EGRESS_DROPS": {
            "count": "3",
            "reasons": "[L2_ANY, L3_ANY]"
        }
        "PORT_INGRESS_DROPS": {
            "count": "10",
            "reasons": "[L2_ANY, SMAC_MULTICAST, SMAC_EQUALS_DMAC, INGRESS_VLAN_FILTER, EXCEEDS_L2_MTU, SIP_CLASS_E, SIP_LINK_LOCAL, DIP_LINK_LOCAL, UNRESOLVED_NEXT_HOP, DECAP_ERROR]"
        }
        "PORT_MIRROR_SUPPORTED_INGRESS_DROPS": {
            "count": "5",
            "reasons": "[L2_ANY, SMAC_MULTICAST, SMAC_EQUALS_DMAC, INGRESS_VLAN_FILTER, EXCEEDS_L2_MTU]"
        }
    }
}
```

This information will be populated by the orchestrator (described later) on startup.

### 3.3.2 SAI APIs
We will use the following SAI APIs to get this information:
* `sai_query_attribute_enum_values_capability` to query support for different types of counters
* `sai_object_type_get_availability` to query the amount of available debug counters

## 3.4 Counters DB
The contents of the drop counters will be added to Counters DB by flex counters.

Additionally, we will add a mapping from debug counter names to the appropriate port or switch stat index called COUNTERS_DEBUG_NAME_PORT_STAT_MAP and COUNTERS_DEBUG_NAME_SWITCH_STAT_MAP respectively.

## 3.5 SWSS
A new orchestrator will be created to handle debug counter creation and configuration. Specifically, this orchestrator will support:
* Creating a new counter
* Deleting existing counters
* Adding drop reasons to an existing counter
* Removing a drop reason from a counter

### 3.5.1 SAI APIs
This orchestrator will interact with the following SAI Debug Counter APIs:
* `sai_create_debug_counter_fn` to create/configure new drop counters.
* `sai_remove_debug_counter_fn` to delete/free up drop counters that are no longer being used.
* `sai_get_debug_counter_attribute_fn` to gather information about counters that have been configured (e.g. index, drop reasons, etc.).
* `sai_set_debug_counter_attribute_fn` to re-configure drop reasons for counters that have already been created.

## 3.6 syncd
Flex counter will be extended to support switch-level SAI counters.

## 3.7 SAI

[SAI Debug Counter Proposal](https://github.com/itaibaz/SAI/blob/a612dd21257cccca02cfc6dab90745a56d0993be/doc/SAI-Proposal-Debug-Counters.md) captures all API and drop counter lists.
Here we are capturing new changes proposed for this feature.

Mirror session attribute is added to allow quering whether the drop can be mirrored.
```
List of drop reasons which can support mirroring will be returned for SAI_DEBUG_COUNTER_ATTR_TYPE 

typedef enum _sai_debug_counter_attr_t
{
    /**
     * @brief List of drop reasons that can be mirrored
     *
     * @type sai_s32_list_t sai_in_drop_reason_t
     * @flags CREATE_AND_SET
     * @default empty
     */
    SAI_DEBUG_COUNTER_ATTR_DROP_REASON_MIRROR_LIST,
}
```
## 3.8 KLISH CLI
The CLI tool will provide the following functionality
* See available drop counter capabilities
* See drop counter config
* Show Interface level drop counts 
* Show Switch level drop counts
* Clear drop counters
* Clear Switch drop counters
* Initialize a new drop counter
* Add drop reasons to a drop counter
* Remove drop reasons from a drop counter
* Delete a drop counter
* Add mirror session to drop counter
* Delete mirror session from drop counter

### 3.8.1 Show Commands

#### 3.8.1.1 Show available counter capabilities
| Mode   | Exec |
| ------ | ------------------- |
| Syntax | SONiC# **show** **drop-counters** **capabilities**  |


***Sample Output:***
```
sonic# show drop-counters capabilities
Counter Type                      Total
--------------------              -------
PORT_INGRESS_DROPS                 10
SWITCH_EGRESS_DROPS                2
PORT_MIRROR_SUPPORTED_INGRESS_DROPS  5

PORT_INGRESS_DROPS:
      L2_ANY
      SMAC_MULTICAST
      SMAC_EQUALS_DMAC
      INGRESS_VLAN_FILTER
      EXCEEDS_L2_MTU
      SIP_CLASS_E
      SIP_LINK_LOCAL
      DIP_LINK_LOCAL
      UNRESOLVED_NEXT_HOP
      DECAP_ERROR

SWITCH_EGRESS_DROPS:
      L2_ANY
      L3_ANY
      A_CUSTOM_REASON

PORT_MIRROR_SUPPORTED_INGRESS_DROPS:
      L2_ANY
      SMAC_MULTICAST
      SMAC_EQUALS_DMAC
      INGRESS_VLAN_FILTER
      EXCEEDS_L2_MTU
```
#### 3.8.1.2 Show current counter configuration
| Mode   | Exec |
| ------ | ------------------- |
| Syntax | SONiC# **show** **drop-counters** **configuration** [ **group** ∗NAME∗] |
```
sonic# show drop-counters configuration
Counter   Alias     Group  Type                Mirror          Reasons              Description
--------  --------  -----  ------------------  -------         -------------------  --------------
DEBUG_0   RX_LEGIT  LEGIT  PORT_INGRESS_DROPS  Session1        SMAC_EQUALS_DMAC     Legitimate port-level RX pipeline drops
                                                               INGRESS_VLAN_FILTER
DEBUG_1   TX_LEGIT  None   SWITCH_EGRESS_DROPS NA              EGRESS_VLAN_FILTER   Legitimate switch-level TX pipeline drops

sonic# show drop-counters configuration group LEGIT
Counter   Alias     Group  Type                Mirror          Reasons              Description
--------  --------  -----  ------------------  -------         -------------------  --------------
DEBUG_0   RX_LEGIT  LEGIT  PORT_INGRESS_DROPS  Session1        SMAC_EQUALS_DMAC     Legitimate port-level RX pipeline drops
                                                               INGRESS_VLAN_FILTER
```
#### 3.8.1.3 Show interface drop counters

The following command lists the interface drop counters 

```
# display all interface drop counters
sonic#show interface drop-counters

# display specific interface drop counters
sonic#show interface drop-counters Ethernet 0

# display specific group drop counters
sonic#show interface drop-counters group <GRP_NAME>

```
Sample usage shown below.

```
sonic#show interface drop-counters
    IFACE    STATE    RX_ERR    RX_DROPS    TX_ERR    TX_DROPS   RX_LEGIT
---------  -------  --------  ----------  --------  ----------  ---------
Ethernet0        U        10         100         0           0         20
Ethernet4        U         0        1000         0           0        100
Ethernet8        U       100          10         0           0          0

sonic#show interface drop-counters Ethernet 0
    IFACE    STATE    RX_ERR    RX_DROPS    TX_ERR    TX_DROPS   RX_LEGIT
---------  -------  --------  ----------  --------  ----------  ---------
Ethernet0        U        10         100         0           0         20

sonic#show interface drop-counters group RX_LEGIT
    IFACE  RX_LEGIT
---------  -------- 
Ethernet0       20
Ethernet4       20

```
#### 3.8.1.4 Show switch drop counters

The following command lists the switch level drop counters. Please refer SAI spec for supported drop counters at switch level.

```
sonic#show switch drop-counters
```
Sample usage shown below

```
sonic#show switch drop-counters
DEVICE  TX_LEGIT
------  --------
sonic       1000
```
### 3.8.2 Clear Commands

All clear commands are listed below.

### 3.8.2.1 Clear drop counters
The following command clears all/ specific interface drop counters.

```
sonic#clear drop-counters [ all| interface [all|<interface_name>]]
```

Sample usage shown below.
```
sonic#clear drop-counters all
Clear all drop counters [confirm y/N]: y
sonic#clear drop-counters interface all
Clear all interface drop counters [confirm y/N]: y
sonic#clear drop-counters interface Ethernet 0
Clear counters for Ethernet0 [confirm y/N]:
```
### 3.8.3 Config Commands
### 3.8.3.1 Create or Delete drop counter

The command syntax for creating/deleting drop counter is as follows:
```
sonic (config)# [no] drop-counters *NAME*
```
| **Attribute**                 | **Description**                         |
|--------------------------|-------------------------------------|
| `drop_counter_name`               | Name of the drop counter. String max 32 characters. Must begin with alpha numeric character |


### 3.8.3.2 Add or delete drop reasons

The command syntax for adding/deleting drop-reason to a drop-counter is as follows.
Drop reasons can be added or deleted when the drop counter is active. The counters however will not be cleared during adding or deleting a drop counter.

```
sonic(config-drop-counters-NAME)# **add-reason** *DROP_REASON_LIST* 

sonic(config-drop-counters-NAME)# **delete-reason**  *DROP_REASON_LIST* 
```
| **Attribute**                 | **Description**                         |
|--------------------------|-------------------------------------|
| `DROP_REASON_LIST`               |List of supported drop reason from **show dropreasons capabilities** |

### 3.8.3.3 Add or delete description 

The command syntax for adding/deleteing description to a drop-counter is as follows.

```
sonic(config-drop-counters-NAME)# **description** *STRING* 
```
| **Attribute**                 | **Description**                         |
|--------------------------|-------------------------------------|
| `STRING`               |A string describing the Drop counter. Must be in double quotes if it contains spaces.|

### 3.8.3.4 Set drop counter type 

The command syntax for setting drop-counter type to a drop-counter is as follows.

```
sonic(config-drop-counters-NAME)# [no] **type** *TYPE*
```
| **Attribute**                 | **Description**                         |
|--------------------------|-------------------------------------|
| `TYPE`               |Drop Counter type.(Ex: PORT_INGRESS_DROPS)|

### 3.8.3.5 Add or delete group

The command syntax for adding/deleting group to a drop-counter is as follows.
All drop counters which are legitimate drops, can be  added to RX_LEGIT group. This group will be shown later in show drop counter commands.

```
sonic(config-drop-counters-NAME)# [no] **group** *STRING*
```
| **Attribute**                 | **Description**                         |
|--------------------------|-------------------------------------|
| `STRING`               |A string describing the Drop counter group.|

### 3.8.3.6 Add or delete Alias 

The command syntax for adding/deleting alias to a drop-counter is as follows. Alias can be configured to have user friendly name to a drop counter.

When alias is not configured, group is used as alias for the drop-counter.

```
sonic(config-drop-counters-NAME)# [no] **alias** *STRING*
```
| **Attribute**                 | **Description**                         |
|--------------------------|-------------------------------------|
| `STRING`               |Alias string for the Drop counter|

### 3.8.3.7 Add or delete mirror session 

The command syntax for adding/deleting mirror-session to a drop-counter. Mirror session has to be created before this command. If the session is active, then the drop-counter will be installed in ASIC.
when session goes active/inactive, the drop counter config will be configured/removed from ASIC.

```
sonic(config-drop-counters-NAME)# [no] **mirror-session** *NAME*
```
| **Attribute**                 | **Description**                         |
|--------------------------|-------------------------------------|
| `NAME`               |Mirror session name to be associated with the Drop counter|

### 3.8.3.8 Activate Drop counter

The command syntax for activating drop counter is as follows, when mandatory parameters are not present, it throws out error. Drop counter Type and group and atleast one drop reason are mandatory parameters.

```
sonic(config-drop-counters-NAME)# [no] **activate**
```

## 3.9 SONIC Yang model

Will be updated later
## 3.10 Open config Yang model

Will be updated later
# 4 Flows
## 4.1 General Flow
![alt text](./drop_counters_general_flow.png)
The overall workflow is shown above in figure 1.

(1) Users configure drop counters using the CLI. Configurations are stored in the DEBUG_COUNTER Config DB table.

(2) The debug counts orchagent subscribes to the Config DB table. Once the configuration changes, the orchagent uses the debug SAI API to configure the drop counters.

(3) The debug counts orchagent publishes counter configurations to Flex Counter DB.

(4) Syncd subscribes to Flex Counter DB and sets up flex counters. Flex counters periodically query ASIC counters and publishes data to Counters DB.

(5) CLI uses counters DB to satisfy CLI requests.

(6) (not shown) CLI uses State DB to display hardware capabilities (e.g. how many counters are available, supported drop reasons, etc.)

# 5 Warm Reboot Support

No special handling is done for the warm boot case. The drop counter configuration is restored from the Config DB and drop counter functionality will continue to work as it is through a warm boot.
Counters will be cleared during warm-reboot same as other interface counters

# 6 Unit Tests
This feature comes with a full set of virtual switch tests in SWSS.
```
=============================================================================================== test session starts ===============================================================================================
platform linux2 -- Python 2.7.15+, pytest-3.3.0, py-1.8.0, pluggy-0.6.0 -- /usr/bin/python2
cachedir: .cache
rootdir: /home/daall/dev/sonic-swss/tests, inifile:
collected 14 items

test_drop_counters.py::TestDropCounters::test_deviceCapabilitiesTablePopulated remove extra link dummy
PASSED                                                                                                                       [  7%]
test_drop_counters.py::TestDropCounters::test_flexCounterGroupInitialized PASSED                                                                                                                            [ 14%]
test_drop_counters.py::TestDropCounters::test_createAndRemoveDropCounterBasic PASSED                                                                                                                        [ 21%]
test_drop_counters.py::TestDropCounters::test_createAndRemoveDropCounterReversed PASSED                                                                                                                     [ 28%]
test_drop_counters.py::TestDropCounters::test_createCounterWithInvalidCounterType PASSED                                                                                                                    [ 35%]
test_drop_counters.py::TestDropCounters::test_createCounterWithInvalidDropReason PASSED                                                                                                                     [ 42%]
test_drop_counters.py::TestDropCounters::test_addReasonToInitializedCounter PASSED                                                                                                                          [ 50%]
test_drop_counters.py::TestDropCounters::test_removeReasonFromInitializedCounter PASSED                                                                                                                     [ 57%]
test_drop_counters.py::TestDropCounters::test_addDropReasonMultipleTimes PASSED                                                                                                                             [ 64%]
test_drop_counters.py::TestDropCounters::test_addInvalidDropReason PASSED                                                                                                                                   [ 71%]
test_drop_counters.py::TestDropCounters::test_removeDropReasonMultipleTimes PASSED                                                                                                                          [ 78%]
test_drop_counters.py::TestDropCounters::test_removeNonexistentDropReason PASSED                                                                                                                            [ 85%]
test_drop_counters.py::TestDropCounters::test_removeInvalidDropReason PASSED                                                                                                                                [ 92%]
test_drop_counters.py::TestDropCounters::test_createAndDeleteMultipleCounters PASSED                                                                                                                        [100%]

=========================================================================================== 14 passed in 113.65 seconds ===========================================================================================
```

A separate test plan will be uploaded and review by the community. This will consist of system tests written in pytest that will send traffic to the device and verify that the drop counters are updated correctly.

New tests will be added using spytest framework.

1. Verify all show CLI and expected outputs.
2. Configure drop counters and verify that configuration is properly shown in show CLI outputs.
3. Configure drop counters and verify mirror config is applied properly.
4. Add each drop reason and send the drop frames and verify that they are counted properly.
5. Add each drop reason and verify that drop frames are mirrored properly.
6. Enable all drops and send all possible drop frames and verify counters
7. Enable all drops and send all possible drops and verify all frames get mirrored.
8. Add REST cases to cover above use cases.
9. Add GNMI tests to cover above use cases.

# 7 Platform Support
In order to make this feature platform independent, we rely on SAI query APIs (described above) to check for what counter types and drop reasons are supported on a given device. As a result, drop counters are only available on platforms that support both the SAI drop counter API as well as the query APIs, in order to preserve safety.

# 7.1 Known Limitations
* BRCM SAI:
    - ACL_ANY, DIP_LINK_LOCAL, SIP_LINK_LOCAL, and L3_EGRESS_LINK_OWN are all based on the same underlying counter in hardware, so enabling any one of these reasons on a drop counter will (implicitly) enable all of them.

More limitations will be updated here.

# 8 Open Questions
- How common of an operation is configuring a drop counter? Is this something that will usually only be done on startup, or something people will be updating frequently?

# 9 Acknowledgements
I'd like to thank the community for all their help designing and reviewing this new feature! Special thanks to Wenda, Ying, Prince, Guohan, Joe, Qi, Renuka, and the team at Microsoft, Madhu and the team at Aviz, Ben, Vissu, Salil, and the team at Broadcom, Itai, Matty, Liat, Marian, and the team at Mellanox, and finally Ravi, Tony, and the team at Innovium.

# 10 References
[1] [SAI Debug Counter Proposal](https://github.com/itaibaz/SAI/blob/a612dd21257cccca02cfc6dab90745a56d0993be/doc/SAI-Proposal-Debug-Counters.md)

# 11 Sample Configuration

```
admin@sonic:~$ sudo config dropcounters install DEBUG_2 PORT_INGRESS_DROPS [EXCEEDS_L2_MTU,DECAP_ERROR] -d "More port ingress drops" -g BAD -a BAD_DROPS
admin@sonic:~$ sudo config dropcounters add_reasons DEBUG_2 [SIP_CLASS_E]
admin@sonic:~$ sudo config dropcounters remove_reasons DEBUG_2 [SIP_CLASS_E]
admin@sonic:~$ sudo config dropcounters delete DEBUG_2

admin@sonic:~$ sudo config dropcounters install DEBUG_2 PORT_INGRESS_DROPS [EXCEEDS_L2_MTU,DECAP_ERROR] -d "More port ingress drops" -g BAD -a BAD_DROPS -m Mirror1

# Create and install drop counter
SONiC(config)# drop-counters DEBUG_2
SONiC(config-drop-counters-DEBUG2)#description "More port ingress drops"
SONiC(config-drop-counters-DEBUG2)#group "BAD"
SONiC(config-drop-counters-DEBUG2)#alias "BAD_DROPS"
SONiC(config-drop-counters-DEBUG2)#add-reason EXCEEDS_L2_MTU,DECAP_ERROR,SIP_CLASS_E
SONiC(config-drop-counters-DEBUG2)#type PORT_MIRROR_SUPPORTED_INGRESS_DROPS
SONiC(config-drop-counters-DEBUG2)#mirror Session1
SONiC(config-drop-counters-DEBUG2)#install
SONiC(config-drop-counters-DEBUG2)#add SIP_CLASS_E

# Delete drop counter
SONiC(config)# no drop-counters DEBUG_2

# Delete drop reasons from counter
SONiC(config)# drop-counters DEBUG_2
SONiC(config-drop-counters-DEBUG2)# delete EXCEEDS_L2_MTU
```

# Broadcom Internal Information : To be removed before publishing externally.

Supported drop counters and mirror support per each ASIC will be documented later and updated here.