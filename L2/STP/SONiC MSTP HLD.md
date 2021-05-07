# Feature Name
MSTP - Multiple Spanning Tree Protocol
# High Level Design Document
#### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definitionabbreviation)
  * [Requirements Overview](#requirement-overview)
      * [Functional Requirements](#functional-requirements)
      * [Configuration and Management Requirements](#configuration-and-management-requirements)
      * [Scalability Requirements](#scalability-requirements)
      * [Warm Boot Requirements](#warm-boot-requirements)
  * [Functionality](#-functionality)
      * [Functional Description](#functional-description)
  * [Design](#design)
      * [Overview](#overview)
      * [DB Changes](#db-changes)
          * [CONFIG DB](#config-db)
          * [APP DB](#app-db)
      * [Switch State Service Design](#switch-state-service-design)
          * [Orchestration Agent](#orchestration-agent)
          * [STP Container](#stp-container)
      * [SAI](#sai)
      * [CLI](#cli)
          * [Configuration Commands](#configuration-commands)
          * [Show Commands](#show-commands)
          * [Debug Commands](#debug-commands)
          * [Clear Commands](#clear-commands)
  * [Flow Diagrams](#flow-diagrams)
  * [Serviceability and Debug](#serviceability-and-debug)
  * [Warm Boot Support](#warm-boot-support)
  * [Scalability](#scalability)
        * [Industry standard CLI](#industry-standard-cli)
  * [Unit Test](#unit-test)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev  |    Date    |           Author           | Change Description |
| :--: | :--------: | :------------------------: | ------------------ |
| 0.1  | 05/04/2021 | Divya Kumaran Chandralekha | Initial version    |
|      |            |                            |                    |
|      |            |                            |                    |
|      |            |                            |                    |


# About this Manual
This document provides general information about the MSTP (Multiple spanning tree protocol) feature implementation and running MSTP over L2 MCLAG networks in SONiC.
# Scope
This document describes the high level design of MSTP feature. 

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term** | **Meaning**                          |
| -------- | ------------------------------------ |
| BPDU     | Bridge protocol data unit            |
| CIST     | Common and internal spanning tree    |
| MSTI     | Multiple spanning tree instance      |
| MSTP     | Multiple spanning tree protocol      |
| MCLAG    | Multi-chassis Link Aggregation Group |
| PVST     | Per VLAN spanning tree protocol      |
| REST     | Representational state transfer      |
| STP      | Spanning tree protocol               |


# 1 Requirement Overview
## 1.1 Functional Requirements


1. Support MSTP protocol (IEEE 802.1s, later incorporated in IEEE 802.1Q-2005)
2. Support BPDU guard functionality
3. Support Root guard functionality
4. Support Loop guard functionality
5. Support Bpdu filter functionality
6. Port channel path cost will be same as the member port cost, it will not be cumulative of member ports cost
7. Support protocol operation on dynamic breakout ports
8. Support protocol operation on Port-channel interfaces
9. Support MC-LAG peers acting as a single bridge in the network
      - Support MC-LAG peers in both root bridge and non-root bridge roles in the network
      - Support 2-tier MC-LAG scenarios


## 1.2 Configuration and Management Requirements
This feature will support CLI and REST based configurations.

1. Support CLI configurations as mentioned in section 3.6.2
2. Support show commands as mentioned in section 3.6.3
3. Support debug commands as mentioned in section 3.6.4
4. Support Openconfig yang model
5. Support REST APIs for config and operational data


## 1.3 Scalability Requirements
Support 65 instances - one CIST and 64 MSTIs.

## 1.4 Warm Boot Requirements

Warm boot is not supported in this release. User is expected to do cold reboot when spanning-tree is running so that topology will reconverge and traffic will be redirected via alternate paths.

If spanning-tree is enabled and user tries to perform warm reboot an error will be displayed indicating spanning-tree doesn't support warm reboot.


# 2 Functionality

## 2.1 Functional Description


# 3 Design

## 3.1 Overview

## 3.2 DB Changes

## 3.3 Switch State Service Design

## 3.4 STP Container

## 3.5 SAI

## 3.6 CLI
### 3.6.1 Data Models
Following Openconfig STP yang model will be supported.

TODO

### 3.6.2 Configuration Commands

### 3.6.2.1 Global level 

### 3.6.2.1.1 Global spanning-tree mode
This command allows enabling the spanning tree mode for the device which can be one of the following PVST, Rapid-PVST or MST.

Use the no form of this command to disable the spanning-tree protocol.

**spanning-tree mode {pvst | rapid-pvst | mst}**

**no spanning-tree mode**

**Syntax Description:**

| Keyword    | Description                              |
| ---------- | ---------------------------------------- |
| pvst       | Enable PVST+ (based on IEEE 802.1D)      |
| rapid-pvst | Enable rapid PVST+ (based on IEEE 802.1w) |
| mst        | Multiple Spanning Tree (based on IEEE 802.1s) |

**Command Mode**: Global Config

**Note**: At any given point of time only one of the spanning-tree mode can be enabled.

### 3.6.2.1.2 MST configuration mode
This command allows entering the MST configuration mode. 

**spanning-tree mst configuration**

**no spanning-tree mst configuration**

This mode allows configuring the MST name, revision number and VLAN to instance mapping. All these parameters are used in determining the MST region to which the device belongs. The no form of the command removes the name, revision number and VLAN to instance mapping information and sets to the default values.

**Command Mode**: Global Config

#### 3.6.2.1.2.1 MST region name

This command allows to configure the region name for the MST. 

**name <string>**

**no name**

**Syntax description**: 

| Keyword        | Description                       |
| -------------- | --------------------------------- |
| name <string\> | String can be upto 32 characters. |

**Default**: System mac address

**Command Mode**: MST configuration mode

#### 3.6.2.1.2.2 MST revision

This command allows to configure the revision number for the MST.

**revision <number>**

**no revision**

**Syntax description**: 

| Keyword            | Description          |
| ------------------ | -------------------- |
| revision <number\> | The range is 0-65535 |

**Default**: 0

**Range**: 0 to 65535

**Command Mode**: MST configuration mode

#### 3.6.2.1.2.3 MST instance to VLAN assignment

This command allows to assigning VLANs to an MST instance. 

**instance <id>  vlan <id/range>**

For the no command, if vlan id/range is specified then the VLANs are removed from the corresponding MST instance. If no vlan id/range is specified then all the VLANs currently in the MST instance are removed.

**no instance <id> [vlan <id/range>]**

**Syntax description**: 

| Keyword          | Description                       |
| ---------------- | --------------------------------- |
| instance <id\>   | Instance id ranges from 1 to 4094 |
| vlan <id/range\> | VLAN id ranges from 1 to 4094     |

**Default**: By default, all VLANs are assigned to instance 0 (CIST).

**Command Mode**: MST configuration mode

**Note**:
1) Changing VLAN to MST instance mapping will result in protocol recovergence, so this mapping should be configured before enabling MSTP to avoid traffic disruptions. 
2) VLANs can be assigned to an MST instance before creation of VLAN, this allows to pre-configure the VLAN to instance mapping

#### 3.6.2.1.2.4 MST activate configuration

This command allows to activate the configuration under the MST configuration mode, until this command is executed any modified configurations like MSTP name, region, instance to vlan mapping will not take effect. This helps in ensuring all the devices in the network are configured with appropriate MSTP configurations before they take effect thus reducing the frequent re-convergences.

**activate**

This command is similar to clear commands hence the no form of this command is not supported.

**Syntax description**: 

| Keyword  | Description                              |
| -------- | ---------------------------------------- |
| activate | Activate the modified configurations under MST mode |

**Command Mode**: MST configuration mode

#### 3.6.2.1.2.5 MST abort configuration

This command allows to abort the pending configurations that are not yet activated under the MST configuration mode. The configurations that have already been activated will not be affected by the abort opertion.

**abort**

No form of this command is not supported.

**Syntax description**: 

| Keyword | Description                              |
| ------- | ---------------------------------------- |
| abort   | Remove the modified configurations under MST mode |

**Command Mode**: MST configuration mode

 ### 3.6.2.1.3 Forward-delay

This command allows configuring the forward delay time in seconds (default = 15), range 4-30.

**spanning-tree mst forward-time <seconds\>**

**no spanning-tree mst forward-time <seconds\>**

**Syntax description**: 

| Keyword                | Description                   |
| ---------------------- | ----------------------------- |
| forward-time <seconds> | The range is 4 to 30 seconds. |

**Default**: The forward-time time is 15 seconds

**Command Mode**: Global Config

 ### 3.6.2.1.4 Hello interval
This command allow configuring the hello interval in secs for transmission of bpdus (default = 2), range 1-10.

**spanning-tree mst hello-time <seconds\>**

**no spanning-tree mst hello-time **

**Syntax description**: 

| Keyword              | Description                   |
| -------------------- | ----------------------------- |
| hello-time <seconds> | The range is 1 to 10 seconds. |

**Default**: The hello time is 2 seconds

**Command Mode**: Global Config

 ### 3.6.2.1.5 Max-age
This command allows configuring the maximum time to listen for root bridge in seconds (default = 20), range 6-40.

**spanning-tree mst max-age <seconds\>**

**no spanning-tree mst max-age **

**Syntax description**: 

| Keyword           | Description                   |
| ----------------- | ----------------------------- |
| max-age <seconds> | The range is 6 to 40 seconds. |

Default: The max-age is 20 seconds

**Command Mode**: Global Config

 ### 3.6.2.1.6 Max hops
This command allows configuring the max-hops value for the MST. In MSTP, max-hops specifies the number of hops in a region before the BPDU is discarded and the information is aged out.

**spanning-tree mst max-hops <value\>**

**no spanning-tree mst max-hops **

**Syntax description**: 

| Keyword           | Description       |
| ----------------- | ----------------- |
| max-hops <value\> | The range is 1-40 |

**Default**: 20

**Command Mode**: Global Config

### 3.6.2.1.7 Bridge priority

This command allows configuring the bridge priority in increments of 4096 (default = 32768), range 0-61440. When MST instance id/range is specified, the priority value will be applied only to those MST instance. 

**spanning-tree mst <id/range> priority <value\>**

**no spanning-tree mst <id/range> priority **

**Syntax description**: 

| Keyword           | Description          |
| ----------------- | -------------------- |
| priority <value\> | The range is 0-61440 |

**Default**: The priority is 32768

**Command Mode**: Global Config



 ### 3.6.2.1.8 Loop guard  

This command allows configuring the loop guard on all the ports.

By default, when spanning-tree stops receiving the BPDUs on a blocking port, it transitions to forwarding state which can result in a loop in the network. Loop guard feature when enabled, avoids this transition of non-designated ports to forwarding state and instead moves the port to a loop inconsistent state where the port continues to block the traffic to avoid the loop.

**spanning-tree loopguard default**

**no spanning-tree loopguard default **

**Syntax description**: 

| Keyword           | Description                              |
| ----------------- | ---------------------------------------- |
| loopguard default | Enable loop guard by default on all the ports |

**Default**: Global loop guard is disabled

**Command Mode**: Global Config

 ### 3.6.2.1.9 BPDU filter 

This command allows configuring the BPDU filter at global level, this will be applied only if the port is in operational edge port mode. When global BPDU filter is enabled, around 10 BPDUs will be sent at link up after which the port will stop transmitting the BPDUs. In case BPDUs are received the port will transition from edge port to normal STP port and disables filtering for port to operate like a normal STP port

**spanning-tree edge-port bpdufilter default**

**no spanning-tree edge-port bpdufilter default **

| Keyword            | Description                          |
| ------------------ | ------------------------------------ |
| bpdufilter default | Enable bpdu filter on all edge ports |
**Default**: Disabled

**Command Mode**: Global Config

 ### 3.6.2.2  Interface level 

All interface specific spanning-tree configurations will be allowed only on switchport interfaces, when switchport interface is deleted corresponding spanning-tree configurations will be deleted as well.

 ### 3.6.2.2.1 STP enable/disable on interface

This command allows enabling or disabling of STP on an interface, by default STP will be enabled on the interface if global STP mode is configured.

**spanning-tree enable**

**no spanning-tree enable ** 

**Syntax description**:
This command has no arguments or keywords. 

**Default**: Spanning-tree is enabled by default on L2 interfaces if global STP mode is configured

 ### 3.6.2.2.2 Guard Type
Guard type can configured as root or loop or none. 

Root Guard provides a way to enforce the root bridge placement in the network and allows STP to interoperate with user network bridges while still maintaining the bridged network topology that the administrator requires. When BPDUs are received on a root guard enabled port, the STP state will be moved to "Root inconsistent" state to indicate this condition. Once the port stops receiving superior BPDUs, Root Guard will automatically set the port back to a FORWARDING state after the rcvdWhile timer is expired.

By default, when spanning-tree stops receiving the BPDUs on a blocking port, it transitions to forwarding state which can result in a loop in the network. Loop guard feature when enabled, avoids this transition of non-designated ports to forwarding state and instead moves the port to a loop inconsistent state where the port continues to block the traffic to avoid the loop

This command allows configuring loop guard or root guard on an interface.

**spanning-tree guard {loop | root | none}**

**no spanning-tree guard**

**Syntax description**: 

| Keyword | Description                              |
| ------- | ---------------------------------------- |
| root    | Configures root guard on the interface   |
| loop    | Configures loop guard on the interface   |
| None    | Disable both loop guard and root guard on the interface. This option allows loop guard to be disabled on an interface even while global level loopguard is enabled. |

**Default**: Both loop guard and root guard are disabled on the interface

**Command Mode**: Interface Config

Following syslogs will be generated when entering and exiting root guard condition respectively - 

STP: Root Guard interface Ethernet4, MST0 inconsistent (Received superior BPDU)

STP: Root Guard interface Ethernet4, MST1 consistent

Following syslogs will be generated when entering  loop guard condition- 

STP: Loop Guard interface Ethernet4, MST0 inconsistent 

 ### 3.6.2.2.3 BPDU Guard

BPDU Guard feature disables the connected device ability to initiate or participate in STP on edge ports. When STP BPDUs are received on the port where BPDU guard is enabled the port will be shutdown. User can re-enable the port administratively after ensuring the BPDUs have stopped coming on the port.

Below command allows enabling or disabling of bpdu guard on an interface.

**spanning-tree bpduguard [port-shutdown]**

**no spanning-tree bpduguard [port-shutdown] **


By default, BPDU guard will only generate a syslog indicating the condition, for taking an action like disabling the port below command can be used with port-shutdown option

**spanning-tree bpduguard port-shutdown**

**Syntax description**: 

| Keyword       | Description                              |
| ------------- | ---------------------------------------- |
| bpduguard     | Configures bpdu guard on the interface   |
| port-shutdown | Configure the action (to bring down the interface) to be taken when BPDU guard condition is detected. |

**Default**: Disabled

**Command Mode**: Interface Config

Following syslog will be generated when BPDU guard condition is entered -

STP: BPDU received, interface Ethernet4 disabled due to BPDU guard trigger.

 ### 3.6.2.2.4 BPDU filter

This command allows disabling sending or receiving of BPDUs on an interface. By default, BPDU filtering is disabled on an interface. Enabling BPDU filtering on the non-edge ports can result in loops in the network, as the BPDUs will not be processed resulting in the port always in forwarding state.

Below command allows enabling or disabling of bpdu guard on an interface.

**spanning-tree bpdufilter enable** 

The below command disables BPDU filter on the interface, irrespective of the global level BPDU filter configuration

**spanning-tree bpdufilter disable**

The below command removes the interface specific BPDU filter configuration and applies the global BPDU filter configuration.

**no spanning-tree bpdufilter**

BPDU filter configuration on the interface takes precedence over the global BPDU filter configuration.

**Default**: Disabled

**Command Mode**: Interface Config

 ### 3.6.2.2.5 Port level priority

This command allows to configure the port level priority value, range 0 - 240 (default 128). When configured without specifying the MST instance, port priority value is applied to all the MST instances on the port else it will be applied only to the instance specified in the configuration.

**spanning-tree [mst <id/range>] port-priority <value\>**

**no spanning-tree [mst <id/range>] port-priority **

**Syntax description**: 

| Keyword                | Description        |
| ---------------------- | ------------------ |
| port-priority <value\> | The range is 0-240 |

**Default**: The port-priority is 128

**Command Mode**: Interface Config

 ### 3.6.2.2.5 Port level path cost
This command allows to configure the port level cost value, range 1 - 200000000. When configured without specifying the MST instance, cost value is applied to all the MST instances on the port else it will be applied only to the instance specified in the configuration.

**spanning-tree [mst <id/range>] cost <value\>**

**no spanning-tree [mst <id/range>] cost **


**Syntax description**: 

| Keyword     | Description                              |
| ----------- | ---------------------------------------- |
| cost <cost> | Path cost. The range is 1 to 200000000. Higher values mean higher costs. |


**Default**: 

| Link speed                 | Default path cost values | path cost range          |
| -------------------------- | ------------------------ | ------------------------ |
| < 100 kilo bits per second | 200,000,000              | 20,000,000 - 200,000,000 |
| 1 Megabit per second       | 20,000,000               | 2,000,000 - 200,000,000  |
| 10 Megabits per second     | 2,000,000                | 200,000 - 200,000,000    |
| 100 Megabits per second    | 200,000                  | 20,000 - 200,000,000     |
| 1 Gigabit per second       | 20,000                   | 2,000 - 200,000,000      |
| 10 Gigabits per second     | 2,000                    | 200 - 20,000             |
| 100 Gigabits per second    | 200                      | 20 - 2,000               |
| 1 Terabits per second      | 20                       | 2 - 200                  |
| 10 Terabits per second     | 2                        | 1 - 20                   |

**Command Mode**: Interface Config

### 3.6.2.2.6 Link type

This command allows to configure 

**spanning-tree link-type { point-to-point | shared}**

**no spanning-tree link-type **

**Syntax description**: 

| Keyword        | Description                              |
| -------------- | ---------------------------------------- |
| point-to-point | link type of given interface is point-to-point. |
| shared         | link type of given interface is shared.  |

**Default**: point-to-point

**Command Mode**: Interface Config

### 3.6.2.2.7 Port type

This command allows to configure the port type as edge.

**spanning-tree port type edge**

**no spanning-tree port type **

**Syntax description**: 

| Keyword | Description                     |
| ------- | ------------------------------- |
| edge    | Enable/disable edge port status |

**Default**: The port-priority is 128

**Command Mode**: Interface Config

### 3.6.3 Show Commands

- show spanning-tree 
``` 
show spanning-tree  
##### MST0    Vlans mapped: 1,3-99,101-199,201-4094
Bridge        Address 8000.80a2.3526.0c5e
Root          Address 8000.80a2.3526.0c5e
              Port    Root                Path cost 0
Regional Root Address 8000.80a2.3526.0c5e
              Internal cost 0             Rem hops 20
Operational   Hello Time  2, Forward Delay 15, Max Age 20, Txholdcount 6
Configured    Hello Time  2, Forward Delay 15, Max Age 20, Max Hops 20

Interface        Role       State      Cost   Prio.Nbr Type
---------------  ---------- ---------- ------ -------- ----------------
Ethernet26       DESIGNATED FORWARDING 2000   128.26   P2P 
Ethernet45       DESIGNATED FORWARDING 800    128.45   P2P
PortChannel20    DESIGNATED FORWARDING 2000   128.85   P2P 

##### MST1    Vlans mapped: 2,100,200
Bridge        Address 8001.80a2.3526.0c5e
Root          Address 8001.80a2.3526.0c5e
              Port    Root                Path cost 0        Rem hops 20

Interface        Role       State      Cost   Prio.Nbr Type
---------------  ---------- ---------- ------ -------- ----------------
Ethernet45       DESIGNATED FORWARDING 800    128.45   P2P
PortChannel20    DESIGNATED FORWARDING 2000   128.85   P2P
```

- show spanning-tree mst configuration

``` 
show spanning-tree mst configuration 
Name [Region1]
Revision 1 Instances configured 2
Instance Vlans mapped
Instance  Vlans mapped
-----------------------------------------------------------
0          1,3-99,101-199,201-4094
1          2,100,200
-----------------------------------------------------------
```

- show spanning-tree mst <id/range> 
```
show spanning-tree mst 1
##### MST1    Vlans mapped: 2,100,200
Bridge        Address 8001.80a2.3526.0c5e
Root          Address 8001.80a2.3526.0c5e
              Port    Root                Path cost 0        Rem hops 20

Interface        Role       State      Cost   Prio.Nbr Type
---------------  ---------- ---------- ------ -------- ----------------
Ethernet45       DESIGNATED FORWARDING 800    128.45   P2P
PortChannel20    DESIGNATED FORWARDING 2000   128.85   P2P
```
- show spanning-tree mst <id> detail
```
show spanning-tree mst 1 detail
##### MST1    Vlans mapped: 2,100,200
Bridge        Address 8001.80a2.3526.0c5e
Root          Address 8001.80a2.3526.0c5e
              Port    Root                Path cost 0        Rem hops 20

Ethernet45 is DESIGNATED FORWARDING
Port info           port id 45   priority 128    cost 800
Designated root     Address 8001.80a2.3526.0c5e  cost 0
Designated bridge   Address 8001.80a2.3526.0c5e  port id 45
Timers: forward transitions 0
Bpdus sent 91, received 0

PortChannel20 is DESIGNATED FORWARDING
Port info           port id 85   priority 128    cost 2000
Designated root     Address 8001.80a2.3526.0c5e  cost 0
Designated bridge   Address 8001.80a2.3526.0c5e  port id 85
Timers: forward transitions 0
Bpdus sent 93, received 0
```
- show spanning-tree mst interface <ifname>

```
show spanning-tree mst interface Ethernet45
Link Type: P2P            Bpdu filter: False
Boundary:  internal       Bpdu guard:  False

Instance Role       State      Cost Pri.Nbr  Vlans mapped
-------- ---------- ---------- ---- -------  ------------
0        DESIGNATED FORWARDING 800  128.45   1,3-99,101-199,201-4094
1        DESIGNATED FORWARDING 800  128.45   2,100,200
```

- show spanning-tree bpduguard

  This command displays the interfaces which are BPDU guard enabled and also the state if the interface is disabled due to BPDU guard.

``` 
show spanning-tree bpdu-guard
PortNum            Shutdown      Port shut	
                   Configured    due to BPDU guard
-------------------------------------------------
Ethernet2            Yes          No
Port-Channel2        No           NA
```

- show spanning-tree inconsistentports
  This command displays the interfaces where root/loop guard inconsistant ports

```
show spanning-tree inconsistentports
Loop guard default: Disabled

----------------------------------------------------------------------
PortNum          INST  Inconsistency State
----------------------------------------------------------------------
Ethernet26       0     Root Inconsistent
```

### 3.6.4 Debug Commands
Following debug commands will be supported for enabling additional logging.
- debug spanning-tree
- debug spanning-tree mst <id/all>
- debug spanning-tree interface <ifname>
- debug spanning-tree event
- debug spanning-tree verbose
- debug spanning-tree bpdu {tx\|rx}


Following debug command can be used to enable/disable all the debug flags

- debug spanning-tree reset

### 3.6.5 Clear Commands
Following clear commands will be supported 

The below command allows to clear the spanning-tree counters

- clear spanning-tree counters [ interface <ifname>] 

The below command allows to clear the current protocol detected and restart the protocol migration process

- clear spanning-tree detected-protocol [ interface <ifname>]

### 3.6.6 REST API Support

REST APIs is supported in this release



# 4 Flow Diagrams



# 5 Serviceability and Debug
# 6 Warm Boot Support
Warm boot is not supported

# 7 Scalability
Support 65 instances - one CIST and 64 MSTIs.


# 8 Unit Test
