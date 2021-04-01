# Feature Name
### Portchannel Enhancements in SONiC

# High Level Design Document
#### Rev 0.3

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definition_abbreviation)
  * [1 Feature Overview](#1-feature-overview)
    * [1.1 Requirements](#1_1-requirements)
      * [1.1.1 Functional Requirements](#1_1_1-functional-requirements)
        * [1.1.1.1 SONiC 3.1.0](#1_1_1_1-sonic-3_1_0)
          * [1.1.1.1.1 Port Channel graceful shutdown mode](#1_1_1_1_1-port-channel-graceful-shutdown-mode)
      * [1.1.2 Configuration and Management Requirements](#1_1_2-configuration-and-management-requirements)
      * [1.1.3 Scalability Requirements](#1_1_3-scalability-requirements)
      * [1.1.4 Warm Boot Requirements](#1_1_4-warm-boot-requirements)
        * [1.1.4.1 SONiC 3.1.0](#1_1_4_1-sonic-3_1_0)
          * [1.1.4.1.1 Port Channel graceful shutdown](#1_1_4_1_1-port-channel-graceful-shutdown)
        * [1.1.4.2 SONiC 3.1.1](#1_1_4_2-sonic-3_1_1)
    * [1.2 Design Overview](#1_2-design-overview)
      * [1.2.1 Basic Approach](#1_2_1-basic-approach)
      * [1.2.2 Container](#1_2_2-container)
      * [1.2.3 SAI Overview](#1_2_3-sai-overview)
  * [2 Functionality](#2-functionality)
    * [2.1 Target Deployment Use Cases](#2_1-target-deployment-use-cases)
    * [2.2 Functional Description](#2_2-functional-description)
  * [3 Design](#3-design)
    * [3.1 Overview](#3_1-overview)
      * [3.1.1 Teamd configuration](#3_1_1-teamd-configuration)
      * [3.1.2 Team Manager](#3_1_2-team-manager)
    * [3.2 DB Changes](#3_2-db-changes)
      * [3.2.1 CONFIG DB](#3_2_1-config-db)
      * [3.2.2 APP DB](#3_2_2-app-db)
      * [3.2.3 STATE DB](#3_2_3-state-db)
      * [3.2.4 ASIC DB](#3_2_4-asic-db)
      * [3.2.5 COUNTER DB](#3_2_5-counter-db)
    * [3.3 Switch State Service Design](#3_3-switch-state-service-design)
      * [3.3.1 Orchestration Agent](#3_3_1-orchestration-agent)
      * [3.3.2 Other Process](#3_3_1-other-process)
    * [3.4 SyncD](#3_4-syncd)
    * [3.5 SAI](#3_5-sai)
    * [3.6 CLI](#3_6-cli)
      * [3.6.1 IS-CLI Compliance](#3_6_1-is_cli-compliance)
      * [3.6.2 Data Models](#3_6_2-data-models)
      * [3.6.3 Configuration Commands](#3_6_3-configuration-commands)
      * [3.6.4 Show Commands](#3_6_4-show-commands)
      * [3.6.5 Debug Commands](#3_6_5-debug-commands)
      * [3.6.6 Rest API Support](#3_6_6-rest-api-support)
  * [4 Flow Diagrams](#4-flow-diagrams)
    * [4.1 Port Channel Creation](#4_1-port-channel-creation)
    * [4.2 Port Channel Member Addition](#4_2_port-channel-member-addition)
  * [5 Error Handling](#5-error-handling)
  * [6 Serviceability and Debug](#6-serviceability-and-debug)
  * [7 Warm Boot Support](#7-warm-boot-support)
    * [7.1 LACPDU transmission using KNET](#7_1-lacpdu-transmission-using-knet)
    * [7.2 Warm boot operation with Port Channel graceful shutdown](#7_2-warm-boot-operation-with-port-channel-graceful-shutdown)
  * [8 Scalability](#8-scalability)
  * [9 Unit Test](#9-unit-test)
    * [9.1 Port Channel graceful shutdown](#9_1-port-channel-graceful-shutdown)
      * [9.1.1 Enable Port Channel graceful shutdown mode](#9_1_1-enable-port-channel-graceful-shutdown-mode)
      * [9.1.2 Disable Port Channel graceful shutdown mode](#9_1_2-disable-port-channel-graceful-shutdown-mode)
    * [9.2 Warm boot](#9_2-warm-boot)
  * [10 References](#references)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 04/22/2020  |      Madhukar K    | Initial version                   |
| 0.2 | 07/25/2020  |      Madhukar K    | Adding portchannel level commands |
| 0.3 | 10/07/2020  |      Madhukar K    | SONiC 3.1.1 related enhancements  |

# About this Manual
This document provides details on Port Channel enhancements in SONiC.

# Scope
This document describes the high level design of Port Channel enhancements in SONiC.

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term**                 | **Meaning**                          |
|--------------------------|--------------------------------------|
| LAG                      | Link Aggregation Group               |
| LACP                     | Link Aggregation Control Protocol    |
| LACPDU                   | LACP Data Unit                       |
| MCLAG                    | Multi-Chassis Link Aggregation Group |

# 1 Feature Overview

## 1.1 Requirements

### 1.1.1 Functional Requirements

#### 1.1.1.1 SONiC 3.1.0

##### 1.1.1.1.1 Port Channel graceful shutdown mode
Upon enabling PortChannel graceful shutdown mode, all the portchannels in the system should be operationally down and stop traffic transmission and reception.

### 1.1.2 Configuration and Management Requirements
Provide configuration to enable/disable Port Channel graceful shutdown mode globally and at portchannel level with the KLISH and click CLI.

### 1.1.3 Scalability Requirements
All the supported portchannels in the device should enable/disable graceful shutdown mode upon user trigger.

### 1.1.4 Warm Boot Requirements

#### 1.1.4.1 SONiC 3.1.0

##### 1.1.4.1.1 Port Channel graceful shutdown
The portchannels should continue to be operationally down when warm boot is triggered by the user in the Port Channel graceful shutdown mode.

##### 1.1.4.2 SONiC 3.1.1
The portchannel should continue to be operationally UP (on actor and partner LAG devices) when user triggers the warm boot.

## 1.2 Design Overview
### 1.2.1 Basic Approach
When the Port Channel graceful shutdown mode is enabled/disabled, team manager will consume the user configuration from Config DB and notify teamd process of individual portchannel to start/stop its operation.

### 1.2.2 Container
An instance of Teamd process is created when a Port Channel (Static or Dynamic) is created. Teamd process runs as part of the Teamd docker. When the Port Channel graceful shutdown mode is enabled, each teamd process receives a signal to stop the LACP state machine.

### 1.2.3 SAI Overview
There are no changes to SAI headers/implementation to support this feature.

# 2 Functionality
## 2.1 Target Deployment Use Cases
In MCLAG topologies, one of the two MCLAG nodes is put into graceful shutdown mode in order to upgrade the firmware or resolve any issues observed on that device. The MCLAG node that enters the graceful shutdown mode stops all the traffic reception and transmission.

## 2.2 Functional Description
In MCLAG topologies, the Port Channel graceful shutdown mode allows users to upgrade the switch software by forwarding the data through the peer MCLAG switch. The goal is to maintain the data connectivity while the software is being upgraded on one of the MCLAG nodes. In order to achieve this, all the portchannels in that MCLAG node are brought down operationally when the device enters the Port Channel graceful shutdown mode.
In the Port Channel graceful shutdown mode:

  - any newly created portchannel will be operationally down
  - existing portchannel memberships(of the ports) can be changed by the user
  - existing portchannels can be deleted by the user

The Port Channel graceful shutdown mode can be enabled/disabled for individual portchannels; portchannel level configuration is applicable only if Port Channel graceful shutdown is enabled globally.
Note: User should disable the Port Channel graceful shutdown at the portchannel level before enabling globally, if a given portchannel is desired to be operationally up.

# 3 Design

## 3.1 Overview
On SONiC device, when user wants to upgrade the firmware or resolve any issue on that device, all the portchannels on the switch that are operationally up are brought down. In order to achieve this, when user triggers the Port Channel graceful shutdown mode:

  - the member ports of all the portchannel transmit an LACPDU (per port) by resetting the SYNCHRONIZATION, COLLECTING and DISTRIBUTING flags. Upon receiving the LACPDU, the partner LAG device will stop reception and trasmission of the traffic on the ports connected towards the device that is entering Port Channel graceful shutdown mode
  - the member ports of the portchannels on the device stop the running the LACP state machine
  - since the LACP state machine for all the member ports is stopped, the portchannels that are operationally up become down
  - LACPDUs received on the member ports of the portchannels are silently discarded
  - If user triggers a config save followed with a reload (config-reload or cold-reboot) - after reload, the device will come up with all the configured portchannels in graceful shutdown mode. The portchannels will be operationally down.
  - the portchannels that have portchannel level graceful shutdown disabled are not altered; they continue to be in the same operational status

Upon exiting the Port Channel graceful shutdown mode:
  - LACP state machine is started afresh for all the portchannel member ports that are link up
  - LACPDUs are transmitted and received on the all the portchannel member ports that are link up
  - Portchannels will be operationally up if the LACP convergence succeeds

If the graceful-shutdown is enabled globally:
  - If there is no configuration at the portchannel level - all the portchannels in the system are brought DOWN
      - If graceful-shutdown is disabled on a given portchannel(say PortChannel5) - all the portchannels except PortChannel5 are brought DOWN
          - enable graceful-shutdown on the portchannel which had graceful-shutdown disabled - PortChannel5 is brought DOWN

If the graceful-shutdown is disabled globally:
    - If graceful-shutdown is enabled at a portchannel level - this is a NO-OP; the portchannel continues to be UP
    - If graceful-shutdown is disabled at a portchannel level - this is a NO-OP; the portchannel continues to be UP

### 3.1.1 Teamd configuration
The user will be able to enable/disable the Port Channel graceful shutdown mode.

Note: Port Channel graceful shutdown mode is disabled by default.

### 3.1.2 Team Manager
Team Manager listens to the enable/disable modes of the Port Channel graceful shutdown mode.

## 3.2 DB Changes
### 3.2.1 CONFIG DB
A new table PORTCHANNEL_GLOBAL is introduced. In the PORTCHANNEL_GLOBAL table:
  - the "graceful_shutdown_mode" field is set to "enable" if user enables the Port Channel graceful shutdown mode globally
  - the "graceful_shutdown_mode" field is set to "disable" if user disables the Port Channel graceful shutdown mode globally

In the PORTCHANNEL table:
- the "graceful_shutdown_mode" field is set to "enable" if user enables the Port Channel graceful shutdown mode at the portchannel level
- the "graceful_shutdown_mode" field is set to "disable" if user disables the Port Channel graceful shutdown mode at the portchannel level

### 3.2.2 APP DB
No new tables are needed.

### 3.2.3 STATE DB
No new tables are needed.

### 3.2.4 ASIC DB
No new tables are needed.

### 3.2.5 COUNTER DB
No new tables are needed.

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
No changes are needed.

### 3.3.2 Other Process
No changes are needed.

## 3.4 SyncD
No changes are needed.

## 3.5 SAI
No changes are needed.

## 3.6 CLI

### 3.6.1 IS-CLI Compliance
Not applicable.

### 3.6.2 Data Models

### 3.6.3 Configuration Commands

Click CLI

Enable/disable the Port Channel graceful shutdown mode globally:
```
root@sonic:/home/admin# config portchannel graceful-shutdown <enable/disable>
```

Enable/disable the Port Channel graceful shutdown mode for a given portchannel:
```
root@sonic:/home/admin# config portchannel graceful-shutdown <enable/disable> <portchannel-name>
```

KLISH CLI

Enable PortChannel graceful shutdown mode globally:
```
sonic(config)# portchannel graceful-shutdown
```

Enable PortChannel graceful shutdown mode for a given portchannel:
```
sonic(config)# interface PortChannel  6
sonic(conf-if-po6)# graceful-shutdown
```

Disable PortChannel graceful shutdown mode globally:
```
sonic(config)# no portchannel graceful-shutdown
```

Disable PortChannel graceful shutdown mode for a given portchannel:
```
sonic(config)# interface PortChannel  6
sonic(conf-if-po6)# no graceful-shutdown
```

### 3.6.4 Show Commands
KLISH CLI

The output of show interface portchannel is modified to display:
1. Portchannel graceful shutdown mode
2. LACP Fallback operational status, if fallback is enabled.

Graceful shutdown is disabled. Fallback config is enabled; it is non-operational:
```
sonic# show interface PortChannel
PortChannel5 is up, line protocol is down, mode LACP
Minimum number of links to bring PortChannel up is 1
Fallback: Enabled, Nonoperational
Graceful shutdown: Disabled
MTU 9100
LACP mode ACTIVE interval SLOW priority 65535 address 3c:2c:99:2d:81:35
Members in this channel: Ethernet220
LACP Actor port 221  address 3c:2c:99:2d:81:35 key 5
LACP Partner port 0  address 00:00:00:00:00:00 key 0
Last clearing of "show interface" counters: 1970-01-01 00:00:00
Input statistics:
          0 packets, 0 octets
0 Multicasts, 0 Broadcasts, 0 Unicasts
0 error, 0 discarded
Output statistics:
          0 packets, 0 octets
0 Multicasts, 0 Broadcasts, 0 Unicasts
0 error, 0 discarded

sonic#

```

Graceful shutdown is disabled. Fallback config is enabled; it is operational:
```
sonic# show interface PortChannel
PortChannel5 is up, line protocol is up, mode LACP
Minimum number of links to bring PortChannel up is 1
Fallback: Enabled, Operational
Graceful shutdown: Disabled
MTU 9100
LACP mode ACTIVE interval SLOW priority 65535 address 3c:2c:99:2d:81:35
Members in this channel: Ethernet220
LACP Actor port 221  address 3c:2c:99:2d:81:35 key 5
LACP Partner port 0  address 00:00:00:00:00:00 key 0
Last clearing of "show interface" counters: 1970-01-01 00:00:00
Input statistics:
          0 packets, 0 octets
0 Multicasts, 0 Broadcasts, 0 Unicasts
0 error, 0 discarded
Output statistics:
          0 packets, 0 octets
0 Multicasts, 0 Broadcasts, 0 Unicasts
0 error, 0 discarded

sonic#

```

Graceful shutdown is enabled. Fallback is disabled:
```
sonic# show interface PortChannel
PortChannel5 is up, line protocol is down, mode LACP
Minimum number of links to bring PortChannel up is 1
Fallback: Disabled
Graceful shutdown: Enabled
MTU 9100
LACP mode ACTIVE interval SLOW priority 65535 address 3c:2c:99:2d:81:35
Members in this channel: Ethernet220
LACP Actor port 221  address 3c:2c:99:2d:81:35 key 5
LACP Partner port 0  address 00:00:00:00:00:00 key 0
Last clearing of "show interface" counters: 1970-01-01 00:00:00
Input statistics:
          0 packets, 0 octets
0 Multicasts, 0 Broadcasts, 0 Unicasts
0 error, 0 discarded
Output statistics:
          0 packets, 0 octets
0 Multicasts, 0 Broadcasts, 0 Unicasts
0 error, 0 discarded

sonic#

```
### 3.6.5 Debug Commands
No new debug commands are added.

### 3.6.6 Rest API Support
PATCH

Enable graceful shutdown mode globally:
```
/openconfig-aggregate-ext:aggregate/config/graceful-shutdown-mode
```

Enable graceful shutdown mode for a given portchannel:
```
/openconfig-interfaces:interfaces/interface={name}/openconfig-if-aggregate:aggregation/config/openconfig-interfaces-ext:graceful-shutdown-mode
```

GET

Get global graceful shutdown mode:
```
/openconfig-aggregate-ext:aggregate/config/graceful-shutdown-mode
```

Get graceful shutdown mode for a given portchannel:
```
/openconfig-interfaces:interfaces/interface={name}/openconfig-if-aggregate:aggregation/config/openconfig-interfaces-ext:graceful-shutdown-mode
```

Get LACP fallback operational status:
```
/openconfig-interfaces:interfaces/interface={name}/openconfig-if-aggregate:aggregation/state/openconfig-interfaces-ext:fallback
```

# 4 Flow Diagrams
NA

# 5 Error Handling
No new error handling support is added.

# 6 Serviceability and Debug
No new Serviceability or Debug support are added.

# 7 Warm Boot Support
When the user issues warm-boot on the SONiC device, each teamd process (corresponding to each portchannel in the system) receives SIGUSR1 signal. Upon receiving the SIGUSR1, each teamd process does below actions for all the portchannel member ports:
  - transmit an LACPDU.
    - Note: upon receving an LACPDU, the partner LAG device resets the peer timeout to the LACP timeout duration (default 90-seconds for long timeout); the portchannel on the partner device continues to be operationally UP for the next 90-seconds.
  - save the current LACP state and the last received LACPDU packet into a file. After the system is ready (post warm boot), this file will be read to restore the LACP state of the portchannel member port.
  - starting SONiC 3.1.1, the last transmitted LACPDU is saved into a file. The saved LACPDU is used to transmit an LACPDU (using KNET) after system is UP post warm boot.

Once the system is ready (after warm-boot), teammgr launches the teamd process for each portchannel in the system. Each teamd process reads the saved portchannel information from the file system and does below actions:
  - create the portchannel netdevice in the kernel; the portchannel operational state (link status) is restored
  - restore the member port LACP states
    - LACP partner information (partner system id, partner key, partner port, etc) is restored by reading the saved rx LACPDU
  - continue the LACP state machine with the restored LACP states

By doing above, the LACP state machine for all the portchannel member ports is restored. After restoring the LACP state information, the LACP state machine continues on each member port.
The LACPDU transmission occurs when member port netdevice is available (and link UP) in the kernel. There can be a delay in creation (OR to become link UP) of the member port netdevice; this may result in the portchannel going DOWN on the partner device. To handle this, starting SONiC 3.1.1, once the system is UP post warm boot - one LACPDU is transmitted per port using KNET.

Note: With LACP short timeout configured, the portchannel becomes operationally down when the warm boot is triggered. The portchannel becomes operationally up once the system is ready post warm boot operation.

## 7.1 LACPDU transmission using KNET
As mentioned in the previous section, there might be a delay for the portchannel member port netdevice to become operational (netdevice creation and link UP in the kernel) post warm boot. If the time gap between the moment last LACPDU is transmitted before system going down (due to warm boot) and the 1st LACPDU tranmitted after the system is ready is larger than the LACP timeout (90 seconds), the partner LACP device resets the LACP states and the portchannel on the partner would go DOWN operationally. This results in trafffic forwarding failure through the portchannel during warm boot operation.

To handle this issue, starting SONiC 3.1.1, post warm boot the LACP helper script reads the saved tx LACPDU on a given port and transmit an LACPDU using KNET transmit path per each member port of the portchannel. Only one LACPDU is transmitted using the KNET transmit path. The LACPDU transmitted from the LACP helper resets the LACP timeout to 90-seconds on the partner LAG device. Once the system is ready (post warm boot) and the portchannel member port netdevices become operational (member port netdevice is created and link UP in the kernel), the teamd process transmits the LACPDUs as part of its regular LACP state machine operations; the LACPDU transmission happens using a raw socket per the portchannel member port netdevice.

Note: Even though LACP helper script transmits an LACPDU before the member port netdevice is operational (in the kernel), if there is a delay in the member port netdevice to become operational in the kernel (link UP), the portchannel on the partner device will become operationally down (due to LACP timeout) and traffic on that portchannel is dropped.

## 7.2 Warm boot operation with Port Channel graceful shutdown
In the Port Channel graceful shutdown mode, the portchannels continue to be operationally down if user triggers warm boot of the device.

# 8 Scalability
All the supported portchannels in the device enter the Port Channel graceful shutdown mode when enabled by the user.

# 9 Unit Test

## 9.1 Port Channel graceful shutdown

### 9.1.1 Enable Port Channel graceful shutdown mode.
  - Verify that "graceful_shutdown_mode" entry is set to "enable" in the PORTCHANNEL_GLOBAL table in CONFIG_DB.
  - Verify that all the portchannels in the system are Down in kernel, APPL_DB.
  - Verify that all the trunks in the hardware have no member ports.
  - Verify that member ports can be added/removed when the Port Channel graceful shutdown mode is enabled.
  - Verify that existing portchannels can be deleted and new portchannels can be created when Port Channel graceful shutdown mode is enabled.
  - With Port Channel graceful shutdown enabled, verify that portchannels continue to be operationally DOWN after config save and reload.

### 9.1.2 Disable Port Channel graceful shutdown mode.
  - Verify that "graceful_shutdown_mode" entry is set to "disable" in the PORTCHANNEL_GLOBAL table in CONFIG_DB.
  - Verify that all the portchannels in the system start LACP state machine are programmed in kernel, APPL_DB if LACP convergence succeeds.
  - Verify that all the trunks in the hardware are programmed with member ports if LACP convergence succeeds.

## 9.2 Warm boot
  - Verify that portchannel on the partner LAG device continues to be operationally UP when warm boot is issued
  - Verify that LACP helper transmits only one LACPDU per portchannel member port after system comes up post warm boot

# 10 References
None
