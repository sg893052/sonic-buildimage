# Priority-based Flow Control OC YANG and Management Framework CLI Support
# High Level Design Document
#### Rev 0.2

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
| Rev | Date | Author | Change Description |
| :--: | :------: | :--------: | :----------------: |
| 0.1 | 6/11/2020 | David Sips | Initial version |
| 0.2 | 6/19/2020 | David Sips | Revisions Made based on review comments from Ashok Daparthi , Amit Kaushik and Michael Li |
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
| OpenConfig Style Guide | https://www.openconfig.net/docs/style-guide/ |
| Arista: Configuring Priority-Flow-Control | https://www.arista.com/en/um-eos/eos-section-17-4-configuring-priority-flow-control-pfc. |
| Arista: Configuring PFC Watchdog | https://www.arista.com/en/um-eos/eos-section-17-5-configuring-pfc-watchdog. |
| Cisco: Configuring Priority Control | https://www.cisco.com/c/en/us/td/docs/switches/datacenter/sw/5_x/nx-os/qos/configuration/guide/nx-os_qos_book/qos_pfc.html |

# Scope
This document covers:

- Manageability Framework CLI config, show commands
- REST access

# Definition/Abbreviation
### Table 2: Abbreviations
| **Term** | ***Meaning*** |
| -------- | -------------- ------------- |
| MF | Manageability Framework |
| PFC | Priority-based Flow  Control |

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

No configuration entries are added or modified for any SONiC database for the OC YANG and Management Framework CLI support. For reference, this section describes the format of Config DB entries already present in SONiC for PFC.

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
POLL_INTERVAL          = uint32                          ; Global PFC Watchdog polling interval
```

### 3.3 Data Model

### 3.3.1 openconfig-qos-pfc.yang

PFC currently has little OpenConfig data modeling defined. Consequently, an OC compatible model will need to be written. The attachment location for this model is to be an extension to openconfig-qos.yang. The YANG trees will look as so:

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
     |        |  +--rw oc-qos-ext:asymmetric?   boolean
     |        +--ro oc-qos-ext:state
     |        |  +--ro oc-qos-ext:asymmetric?   boolean
     |        +--rw oc-qos-ext:priority
     |        |  +--rw oc-qos-ext:priority* [cos]
     |        |     +--rw oc-qos-ext:cos       -> ../config/cos
     |        |     +--rw oc-qos-ext:config
     |        |     |  +--rw oc-qos-ext:cos?      uint8
     |        |     |  +--rw oc-qos-ext:enable?   boolean
     |        |     +--ro oc-qos-ext:state
     |        |        +--ro oc-qos-ext:cos?               uint8
     |        |        +--ro oc-qos-ext:enable?            boolean
     |        |        +--ro oc-qos-ext:pause-frames-rx?   oc-yang:counter64
     |        |        +--ro oc-qos-ext:pause-frames-tx?   oc-yang:counter64
     |        +--rw oc-qos-ext:watchdog
     |        |  +--rw oc-qos-ext:config
     |        |  |  +--rw oc-qos-ext:action?             enumeration
     |        |  |  +--rw oc-qos-ext:detection-time?     uint32
     |        |  |  +--rw oc-qos-ext:restoration-time?   uint32
     |        |  +--ro oc-qos-ext:state
     |        |     +--ro oc-qos-ext:action?             enumeration
     |        |     +--ro oc-qos-ext:detection-time?     uint32
     |        |     +--ro oc-qos-ext:restoration-time?   uint32
     |        +--rw oc-qos-ext:pfc-queue
     |           +--rw oc-qos-ext:pfc-queue* [queue]
     |              +--rw oc-qos-ext:queue     -> ../config/queue
     |              +--rw oc-qos-ext:config
     |              |  +--rw oc-qos-ext:queue?   uint8
     |              +--ro oc-qos-ext:state
     |                 +--ro oc-qos-ext:queue?             uint8
     |                 +--ro oc-qos-ext:pause-frames-rx?   oc-yang:counter64
     |                 +--ro oc-qos-ext:pause-frames-tx?   oc-yang:counter64
     |                 +--ro oc-qos-ext:storm-detected?    oc-yang:counter64
     |                 +--ro oc-qos-ext:storm-restored?    oc-yang:counter64
     |                 +--ro oc-qos-ext:tx-ok?             oc-yang:counter64
     |                 +--ro oc-qos-ext:tx-drop?           oc-yang:counter64
     |                 +--ro oc-qos-ext:rx-ok?             oc-yang:counter64
     |                 +--ro oc-qos-ext:rx-drop?           oc-yang:counter64
     |                 +--ro oc-qos-ext:tx-ok-last?        oc-yang:counter64
     |                 +--ro oc-qos-ext:tx-drop-last?      oc-yang:counter64
     |                 +--ro oc-qos-ext:rx-ok-last?        oc-yang:counter64
     |                 +--ro oc-qos-ext:rx-drop-last?      oc-yang:counter64
     +--rw oc-qos-ext:pfc-watchdog
        +--rw oc-qos-ext:config
        |  +--rw oc-qos-ext:enable?          boolean
        |  +--rw oc-qos-ext:poll-interval?   uint32
        +--ro oc-qos-ext:state
           +--ro oc-qos-ext:enable?          boolean
           +--ro oc-qos-ext:poll-interval?   uint32
```
This model meets the requirements of the OpenConfig Style Guide.


### 3.3.2 Configuration Commands

The below CLI configuration mode commands are supported. Priority-based Flow Control is not configured by default. The administrator may use below commands to configure some elements of priority-based flow control. Priority-based Flow Control configuration commands are run in either config mode or interface configuration mode.

#### 3.3.2.1 Configuration Mode

**Watchdog Polling Interval** The global watchdog polling interval for Priority-based Flow Control. This is the interval the monitoring process will periodically poll counters to determine resource usage.

**[no]  priority-flow-control  [^2]  watchdog  polling-interval  < *time-interval* >**

Use no form of the command to reset the default value.

    sonic-cli(config)# priority-flow-control watchdog polling-interval 1234
    sonic-cli(config)# no priority-flow-control watchdog polling-interval



**Flex Counters** Configure Priority-based Flow Control to use the flex counters.

**[no]  priority-flow-control watchdog counter-poll**

Use no form of the command to reset the default value.

    sonic-cli(config)# priority-flow-control watchdog counter-poll
    sonic-cli(config)# no priority-flow-control watchdog counter-poll



#### 3.3.2.2 QoS Interface Configuration Mode

**Enable** Enable Priority-based Flow Control on an interface. [^1]



**Asymmetric mode** for Priority-based Flow Control. Configure an interface to operate in asymmetric mode. When asymmetric Priority-based Flow Control is enabled, the interface handles incoming pause frames on all priorities and generates pause frames on priorities that are configured to be lossless.

**[no]  priority-flow-control asymmetric**

Use no form of the command to reset the asymmetric mode.

    sonic-cli(conf-if-Ethernet12)# priority-flow-control asymmetric
    sonic-cli(conf-if-Ethernet12)# no priority-flow-control asymmetric



**Watchdog** for Priority-based Flow Control. Configure Priority-based Flow Control Watchdog on the interface. Priority-based Flow Control watchdog is designed to detect and mitigate Priority-based Flow Control storm received for each port. Action, detection time and restoration time may all be configured. Detection time and restoration time are specified in milliseconds.

**[no]  priority-flow-control [^2] watchdog action { drop | forward | alert }**

**[no]  priority-flow-control watchdog detect-time < *detection_time* >**

**[no]  priority-flow-control watchdog restore-time < *restoration _time* >**

**priority-flow-control watchdog { on | off }**

***detection_time*** is specified in milliseconds and has a range of 100-5000 while ***restoration_time***, also in milliseconds, has a range of 100-60000. Use no form of the command to reset the parameters to defaults. ***on*** or ***off*** will enable or disable Priority-based Flow Control watchdog operation on an interface.

    sonic-cli(conf-if-Ethernet36)# priority-flow-control watchdog action alert
    sonic-cli(conf-if-Ethernet36)# priority-flow-control watchdog detect-time 500
    sonic-cli(conf-if-Ethernet36)# priority-flow-control watchdog restore-time 1000
    sonic-cli(conf-if-Ethernet36)# priority-flow-control watchdog on
    sonic-cli(conf-if-Ethernet36)# no priority-flow-control watchdog action
    sonic-cli(conf-if-Ethernet36)# no priority-flow-control watchdog detect-time
    sonic-cli(conf-if-Ethernet36)# no priority-flow-control watchdog restore-time



**Clear Counters** for Priority-based Flow Control. Clear the counters associated to Priority-based Flow Control on this interface

**clear priority-flow-control counters [ interface ]**

    sonic-cli# clear priority-flow-control counters Ethernet12

A SONiC YANG RPC call will be used to fulfil this command.



[^1]: Perhaps a required capability however it is not clear how this is achieved. Might be done elsewhere?
[^2]: Arista inserts **pause** into some commands. i.e. **"priority-flow-control pause watchdog ..."**. Cisco has no such capability.



### 3.3.3 Show Commands

The below CLI show commands are supported.



Show **summary** information for Priority-based Flow Control. 

**show  qos  priority-flow-control**

```
sonic-cli# show qos priority-flow-control

Watchdog Summary
----------------
Polling Interval: 300 second(s)
Flex Counters:    enabled
```



Show **interface** information for the system as related to Priority-based Flow Control. 

**show  qos  interface  Ethernet  < *interface-number* > **

```
sonic-cli# show qos interface Ethernet 24 

Asymetric mode: disabled

Watchdog Summary
----------------
Action:              drop
Detection Time:      650ms
Restoration Time:    1000ms
```



Show **statistics** for Priority-based Flow Control. 

**show  qos  priority-flow-control statistics  [ *interface-name*  [ queue < *queue-id* > ]] **

```
sonic-cli# show qos priority-flow-control statistics

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

Priority-based Flow Control Watchdog Port:Queue Statistics
                                Storms          Transmitted        Received          TX Last            RX Last 
 Port       Queue   Status Detected Restored    OK      Drop      OK      Drop      OK      Drop       OK      Drop
----------- ----- -------- -------- -------- -------- -------- -------- -------- -------- --------- -------- ---------
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
sonic-cli# show qos priority-flow-control statistics Ethernet4

Flow Control frames received
       Port    PFC0    PFC1    PFC2    PFC3    PFC4    PFC5    PFC6    PFC7
-----------  ------  ------  ------  ------  ------  ------  ------  ------
  Ethernet4       0       0       0       0       0       0       0       0

Flow Control frames transmitted
       Port    PFC0    PFC1    PFC2    PFC3    PFC4    PFC5    PFC6    PFC7
-----------  ------  ------  ------  ------  ------  ------  ------  ------
  Ethernet4       0       0       0       0       0       0       0       0

Priority-based Flow Control Watchdog Port:Queue Statistics
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
sonic-cli# show qos priority-flow-control statistics Ethernet16 queue 3

Priority-based Flow Control Port:Queue Statistics
                                Storms          Transmitted        Received          TX Last            RX Last 
 Port       Queue   Status Detected Restored    OK      Drop      OK      Drop      OK      Drop       OK      Drop
----------- ----- -------- -------- -------- -------- -------- -------- -------- -------- --------- -------- ---------
 Ethernet16     3       N/A       0        0        0        0        0        0        0         0        0         0
```


## 3.3 REST API Support
Below REST URIs are supported. All are prefixed with /restconf/data/.

```
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/config/asymmetric
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/asymmetric
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/priority={cos}/config/cos
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/priority={cos}/config/enable
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/priority={cos}/state/cos
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/priority={cos}/state/enable
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/priority={cos}/state/pause-frames-rx
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/priority={cos}/state/pause-frames-tx
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/watchdog/config/action
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/watchdog/config/detection-time
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/watchdog/config/restoration-time
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/watchdog/state/action
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/watchdog/state/detection-time
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/watchdog/state/restoration-time
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-queue={queue}/queue
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-queue={queue}/config/queue
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-queue={queue}/state/queue
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-queue={queue}/state/pause-frames-tx
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-queue={queue}/state/storm-detected 
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-queue={queue}/state/storm-restored 
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-queue={queue}/state/tx-ok
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-queue={queue}/state/tx-drop
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-queue={queue}/state/rx-ok
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-queue={queue}/state/rx-drop
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-queue={queue}/state/tx-ok-last
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-queue={queue}/state/tx-drop-last
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-queue={queue}/state/rx-ok-last
/openconfig-qos:qos/interfaces/interface={interface-id}/oc-qos-ext:pfc/state/pfc-queue={queue}/state/rx-drop-last
/openconfig-qos:qos/pfc-watchdog/config/enable
/openconfig-qos:qos/pfc-watchdog/config/poll-interval
/openconfig-qos:qos/pfc-watchdog/state/enable
/openconfig-qos:qos/pfc-watchdog/state/poll-interval
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


