# Priority-based Flow Control OC YANG and Management Framework CLI Support
# High Level Design Document
#### Rev 0.1

# Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About this Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definitionabbreviation)
  * [1 Overview](#1-overview)
  * [2 Functionality](#2-functionality)
  * [3 Design](#3-design)
  * [4 Flow Diagrams](#4-flow-diagrams)
  * [5 Serviceability and Debug](#5-serviceability-and-debug)
  * [6 Warm Boot Support](#6-warm-boot-support)
  * [7 Scalability](#7-scalability)
  * [8 Unit Test](#8-unit-test)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations) <br>

# Revision
| Rev  |   Date   |   Author   | Change Description |
| :--: | :------: | :--------: | :----------------: |
| 0.1  | 6/11/2020 | David Sips |  Initial version   |
|      |          |            |                    |

# About this Manual
This document provides OpenConfig YANG and Management Framework Config CLI implementation for PFC feature in SONiC. PFC enables a device to not discard frames due to congestion for protocols that require this property.

# References
### Table 1: References
| **Document** | ***Location*** |
| ------------ | -------------- |
| PFC Watchdog Design | https://github.com/Azure/SONiC/wiki/PFC-Watchdog-Design |
| Asymmetric PFC High Level Design | https://github.com/Azure/SONiC/wiki/Asymmetric-PFC-High-Level-Design |
| Priority-based Flow Control (PFC) | IEEE Standard for Local and metropolitan area networks - Bridges and Bridged Networks, Clause 36 |

# Scope
This document covers:

- Manageability Framework CLI config, show commands
- REST access

# Definition/Abbreviation
### Table 2: Abbreviations
| **Term** | ***Meaning***               |
| -------- | --------------------------- |
| MF | Managabilty Framework |
| PFC | Priority-based Flow Control |

# 1 Overview
## 1.1 Functional Requirements
## 1.2 Configuration and Management Requirements
Support OpenConfig based Manageability Framework CLIs for PFC protocol

## 1.3 Scalability Requirements

Not Applicable

## 1.4 Warm Boot Requirements

Not Applicable

# 2 Functionality
## 2.1 Target Deployment Use Case

Buzznik+

# 3 Design
## 3.1 Overview

 

## 3.2 Database

No  configuration entries are added or modified for any SONiC database for the OC YANG and Management Framework CLI support. For reference, this section describes the format of Config DB entries already present in SONiC for PFC.

### 3.2.1 CONFIG DB
Defines schema for PFC configuration attributes
```
Table                  = FLEX_COUNTER_TABLE
Key                    = "PFCWD"
; field                = value
FLEX_COUNTER_STATUS    = "enable" / "disable"            ; Flex counter enable
```
```
Table                  = PFC_WD
Key                    = interface                       ; "Ethernet" + n
; field                = value
action                 = "drop" / "forward" / "alert"    ; PFC pause storm action
detection_time         = uint32                          ; Detection interval for pause storm
restoration_time       = uint32                          ; Time delay before resuming normal PFC operation
```
```
Table                  = PORT
Key                    = interface                       ; "Ethernet" + n
; field                = value
pfc_asym               = "on" / "off"                    ; Enable (on) or disable (off) asymmetric PFC on an interface

Key                    = "GLOBAL"
; field                = value
POLL_INTERVAL          = uint32                          ; Global polling interval
```

### 3.3 Data Model

### 3.3.1 openconfig-pfc.yang

PFC currently has little OpenConfig data modeling defined. Consequently, an OC compatible model will need to be written. The attachment location for this model is to be an extension to openconfig-qos.yang. The YANG tree will look like so:

```
module: openconfig-qos
  +--rw qos
     +--rw config
     +--ro state
     +--rw interfaces
     |  +--rw interface* [interface-id]
     |     +--rw interface-id                      -> ../config/interface-id
     |     +--rw oc-qos-ext:pfc
     |        +--rw oc-qos-ext:config
     |        |  +--rw oc-qos-ext:asymmetric?       boolean
     |        |  +--rw oc-qos-ext:watchdog
     |        |  |  +--rw oc-qos-ext:action?             enumeration
     |        |  |  +--rw oc-qos-ext:detection-time?     uint32
     |        |  |  +--rw oc-qos-ext:restoration-time?   uint32
     |        |  +--rw oc-qos-ext:pfc-priority* [dot1p]
     |        |  |  +--rw oc-qos-ext:dot1p     uint8
     |        |  |  +--rw oc-qos-ext:enable?   boolean
     |        |  +--rw oc-qos-ext:clear-counters?   boolean
     |        +--ro oc-qos-ext:state
     |           +--ro oc-qos-ext:asymmetric?       boolean
     |           +--ro oc-qos-ext:watchdog
     |           |  +--ro oc-qos-ext:action?             enumeration
     |           |  +--ro oc-qos-ext:detection-time?     uint32
     |           |  +--ro oc-qos-ext:restoration-time?   uint32
     |           +--ro oc-qos-ext:pfc-priority* [dot1p]
     |           |  +--ro oc-qos-ext:dot1p     uint8
     |           |  +--ro oc-qos-ext:enable?   boolean
     |           +--ro oc-qos-ext:pfc-priority-stats* [dot1p]
     |           |  +--ro oc-qos-ext:dot1p              uint8
     |           |  +--ro oc-qos-ext:pause-frames-rx?   oc-yang:counter64
     |           |  +--ro oc-qos-ext:pause-frames-tx?   oc-yang:counter64
     |           +--ro oc-qos-ext:pfc-statistics* [dot1p]
     |              +--ro oc-qos-ext:dot1p              uint8
     |              +--ro oc-qos-ext:pause-frames-rx?   oc-yang:counter64
     |              +--ro oc-qos-ext:pause-frames-tx?   oc-yang:counter64
     |              +--ro oc-qos-ext:storm-detected?    oc-yang:counter64
     |              +--ro oc-qos-ext:storm-restored?    oc-yang:counter64
     |              +--ro oc-qos-ext:tx-ok?             oc-yang:counter64
     |              +--ro oc-qos-ext:tx-drop?           oc-yang:counter64
     |              +--ro oc-qos-ext:rx-ok?             oc-yang:counter64
     |              +--ro oc-qos-ext:rx-drop?           oc-yang:counter64
     |              +--ro oc-qos-ext:tx-ok-last?        oc-yang:counter64
     |              +--ro oc-qos-ext:tx-drop-last?      oc-yang:counter64
     |              +--ro oc-qos-ext:rx-ok-last?        oc-yang:counter64
     |              +--ro oc-qos-ext:rx-drop-last?      oc-yang:counter64
     +--rw oc-qos-ext:pfc-watchdog
        +--rw oc-qos-ext:config
        |  +--rw oc-qos-ext:counter
        |     +--rw oc-qos-ext:enable?     boolean
        |     +--rw oc-qos-ext:interval?   uint32
        +--ro oc-qos-ext:state
           +--ro oc-qos-ext:counter
              +--ro oc-qos-ext:enable?     boolean
              +--ro oc-qos-ext:interval?   uint32
```



### 3.3.2 Configuration Commands

The below CLI configuration mode commands are supported. PFC is not configured by default. The administrator may use below commands to configure some elements of priority-based flow control. PFC configuration commands are run in either qos config mode or qos interface configuration mode. qos config mode is new with this feature.

#### 3.3.2.1 QoS Configuration Mode

**Polling Interval** The global polling interval for PFC. This is the interval the monitoring process will periodically poll counters to determine resource usage.

**[no]  pfc  poll-interval  < *time-interval* >**

Use no form of the command to reset the default value.

    sonic-cli(config-qos)# pfc poll-interval 1234
    sonic-cli(config-qos)# no pfc poll-interval



**Flex Counters** Configure PFC to use the flex counters.

**[no]  pfc  flex-counter**

Use no form of the command to reset the default value.

    sonic-cli(config-qos)# pfc flex-counter
    sonic-cli(config-qos)# no pfc flex-counter



#### 3.3.2.2 QoS Interface Configuration Mode

**Asymmetric operation** for PFC. Configure an interface to operate in asymmetric mode. When asymmetric PFC is enabled, the interface handles incoming pause frames on all priorities and generates pause frames on priorities that are configured to be lossless.

**[no]  pfc asymmetric**

Use no form of the command to reset the asymmetric operation.

    sonic-cli(config-qos-Ethernet12)# pfc asymmetric
    sonic-cli(config-qos-Ethernet12)# no pfc asymmetric



**Clear Counters** for PFC. Clear the counters associated to PFC on this interface

**pfc clear counters**

    sonic-cli(config-qos-Ethernet20)# pfc clear counters



**Watchdog** for PFC. Configure PFC Watchdog on the interface. PFC watchdog is designed to detect and mitigate PFC storm received for each port. Action, detection time and restoration time may all be configured. Detection time and restoration time are specified in milliseconds.

**[no]  pfc watchdog action { drop | forward | alert }**

**[no]  pfc watchdog detect-time < *detection_time* >**

**[no]  pfc watchdog restore-time < *restoration _time* >**

***detection_time*** is specified in milliseconds and has a range of 100-5000 while ***restoration_time***, also in milliseconds, has a range of 100-60,000. Use no form of the command to reset the parameters to defaults.

    sonic-cli(config-qos-Ethernet36)# pfc watchdog action alert
    sonic-cli(config-qos-Ethernet36)# pfc watchdog detect-time 500
    sonic-cli(config-qos-Ethernet36)# pfc watchdog restore-time 1000
    sonic-cli(config-qos-Ethernet36)# no pfc watchdog action
    sonic-cli(config-qos-Ethernet36)# no pfc watchdog detect-time
    sonic-cli(config-qos-Ethernet36)# no pfc watchdog restore-time



### 3.3.3 Show Commands

The below CLI show commands are supported.



Show **summary** information for PFC. 

**show  qos  pfc**

```
sonic-cli# show qos pfc

Watchdog Summary
----------------
Polling Interval: 300 second(s)
Flex Counters:    enabled
```



Show **interface** information for the system as related to PFC. 

**show  qos  interface  Ethernet  < *interface-number* > **

```
sonic-cli# show qos interface Ethernet 24 

Asymetric operation: disabled

Watchdog Summary
----------------
Action:              drop
Detection Time:      650ms
Restoration Time:    1000ms
```



Show **statistics** for PFC. 

**show  qos  pfc statistics  [ *interface-name*  [ queue < *queue-id* > ]]  **

```
sonic-cli# show qos pfc statistics

Flow Control frames received
       Port    PFC0    PFC1    PFC2    PFC3    PFC4    PFC5    PFC6    PFC7
-----------  ------  ------  ------  ------  ------  ------  ------  ------
        CPU       0       0       0       0       0       0       0       0
  Ethernet0       0       0       0       0       0       0       0       0
  Ethernet4       0       0       0       0       0       0       0       0
  Ethernet8       0       0       0       0       0       0       0       0

Flow Control frames transmitted
       Port    PFC0    PFC1    PFC2    PFC3    PFC4    PFC5    PFC6    PFC7
-----------  ------  ------  ------  ------  ------  ------  ------  ------
        CPU       0       0       0       0       0       0       0       0
  Ethernet0       0       0       0       0       0       0       0       0
  Ethernet4       0       0       0       0       0       0       0       0
  Ethernet8       0       0       0       0       0       0       0       0

PFC Port:Queue Statistics
                                Storms          Transmitted        Received          TX Last            RX Last 
 Port       Queue   Status Detected Restored    OK      Drop      OK      Drop      OK      Drop       OK      Drop
----------- ----- -------- -------- -------- -------- -------- -------- -------- -------- --------- -------- ---------
 CPU            0       N/A       0        0        0        0        0        0        0         0        0         0
 CPU            1       N/A       0        0        0        0        0        0        0         0        0         0
 CPU            2       N/A       0        0        0        0        0        0        0         0        0         0
 CPU            3       N/A       0        0        0        0        0        0        0         0        0         0
 CPU            4       N/A       0        0        0        0        0        0        0         0        0         0
 CPU            5       N/A       0        0        0        0        0        0        0         0        0         0
 CPU            6       N/A       0        0        0        0        0        0        0         0        0         0
 CPU            7       N/A       0        0        0        0        0        0        0         0        0         0
 CPU            8       N/A       0        0        0        0        0        0        0         0        0         0
 CPU            9       N/A       0        0        0        0        0        0        0         0        0         0
 CPU           10       N/A       0        0        0        0        0        0        0         0        0         0
 CPU           11       N/A       0        0        0        0        0        0        0         0        0         0
 CPU           12       N/A       0        0        0        0        0        0        0         0        0         0
 CPU           13       N/A       0        0        0        0        0        0        0         0        0         0
 CPU           14       N/A       0        0        0        0        0        0        0         0        0         0
 CPU           15       N/A       0        0        0        0        0        0        0         0        0         0
 CPU           16       N/A       0        0        0        0        0        0        0         0        0         0
 CPU           17       N/A       0        0        0        0        0        0        0         0        0         0
 CPU           18       N/A       0        0        0        0        0        0        0         0        0         0
 CPU           19       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0      1       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0      2       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0      3       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0      4       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0      5       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0      6       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0      7       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0      8       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0      9       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0     10       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0     11       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0     12       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0     13       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0     14       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0     15       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0     16       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0     17       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0     18       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet0     19       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      0       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      1       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      2       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      3       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      4       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      5       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      6       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      7       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      8       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      9       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     10       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     11       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     12       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     13       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     14       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     15       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     16       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     17       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     18       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     19       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8      0       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8      1       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8      2       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8      3       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8      4       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8      5       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8      6       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8      7       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8      8       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8      9       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8     10       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8     11       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8     12       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8     13       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8     14       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8     15       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8     16       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8     17       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8     18       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet8     19       N/A       0        0        0        0        0        0        0         0        0         0

```

```
sonic-cli# show qos pfc statistics Ethernet4

Flow Control frames received
       Port    PFC0    PFC1    PFC2    PFC3    PFC4    PFC5    PFC6    PFC7
-----------  ------  ------  ------  ------  ------  ------  ------  ------
  Ethernet4       0       0       0       0       0       0       0       0

Flow Control frames transmitted
       Port    PFC0    PFC1    PFC2    PFC3    PFC4    PFC5    PFC6    PFC7
-----------  ------  ------  ------  ------  ------  ------  ------  ------
  Ethernet4       0       0       0       0       0       0       0       0

PFC Port:Queue Statistics
                                Storms          Transmitted        Received          TX Last            RX Last 
 Port       Queue   Status Detected Restored    OK      Drop      OK      Drop      OK      Drop       OK      Drop
----------- ----- -------- -------- -------- -------- -------- -------- -------- -------- --------- -------- ---------
 Ethernet4      0       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      1       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      2       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      3       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      4       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      5       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      6       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      7       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      8       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4      9       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     10       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     11       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     12       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     13       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     14       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     15       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     16       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     17       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     18       N/A       0        0        0        0        0        0        0         0        0         0
 Ethernet4     19       N/A       0        0        0        0        0        0        0         0        0         0
```

```
sonic-cli# show qos pfc statistics Ethernet16 queue 3

PFC Port:Queue Statistics
                                Storms          Transmitted        Received          TX Last            RX Last 
 Port       Queue   Status Detected Restored    OK      Drop      OK      Drop      OK      Drop       OK      Drop
----------- ----- -------- -------- -------- -------- -------- -------- -------- -------- --------- -------- ---------
 Ethernet16     3       N/A       0        0        0        0        0        0        0         0        0         0
```


## 3.3 REST API Support
Below REST URIs are supported. All are prefixed with /restconf/data/.

```
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/config/asymmetric
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/config/clear-counters
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/config/pfc-priority={dot1p}/enable
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/config/watchdog/action
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/config/watchdog/detection-time
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/config/watchdog/restoration-time
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/asymmetric
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-priority={dot1p}/enable
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/watchdog/action
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/watchdog/detection-time
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/watchdog/restoration-time
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-priority-stats={dot1p}/pause-frames-rx
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-priority-stats={dot1p}/pause-frames-tx
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-statistics={queue}/pause-frames-rx
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-statistics={queue}/pause-frames-tx
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-statistics={queue}/storm-detected 
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-statistics={queue}/storm-restored 
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-statistics={queue}/tx-ok
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-statistics={queue}/tx-drop
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-statistics={queue}/rx-ok
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-statistics={queue}/rx-drop
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-statistics={queue}/tx-ok-last
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-statistics={queue}/tx-drop-last
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-statistics={queue}/rx-ok-last
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-statistics={queue}/rx-drop-last
/openconfig-qos:qos/pfc-watchdog/config/counter/enable
/openconfig-qos:qos/pfc-watchdog/config/counter/interval
/openconfig-qos:qos/pfc-watchdog/state/counter/enable
/openconfig-qos:qos/pfc-watchdog/state/counter/interval
```

# 4 Flow Diagrams
None

# 5 Serviceability and Debug
None

# 6 Warm Boot Support
None

# 7 Scalability
None

# 8 Unit Test
1. Verify all Global Commands via Management Framework
2. Verify Config DB is updated
3. Verify Show Commands
4. Verify all REST URIs

