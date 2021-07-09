# SONiC Auto-Negotiation and Link Training

## High Level Design Document

### Rev 0.1

# Table of Contents

* [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definition-abbreviation)
  * [Requirements](#1-Requirements-Overview)
  * [Functionality](#2-Functionality)
  * [Design](#3-Design)
  * [Flow Diagrams](#4-Flow-Diagrams)
  * [Serviceability and Debug](#5-Serviceability-and-Debug)
  * [Warm Boot Support](#6-Error-Handling)
  * [Scalability](#7-Scalability)
  * [Limitations](#8-Limitations)
  * [Unit Test](#9-Unit-Test)

# List of Tables

[Table 1: References](#table-1-references)<br>
[Table 2: Abbreviations](#table-2-abbreviations)

# Revision

| Rev |     Date    |       Author        | Change Description                 |
|:---:|:-----------:|:-------------------:|:-----------------------------------|
| 0.1 | 04/15/2021  | Dante (Kuo-Jung) Su | Initial version                    |

# About this Manual

This document provides functional requirements and high-level design for providing Auto-Negotiation and Link Training support in SONiC.

#  References

##  Table 1: References

|       **Document**                                       | **Location**   |
| -------------------------------------------------------- | -------------- |
| Auto-Negotiation (AN) Overview                           | https://www.ieee802.org/3/by/public/Mar15/booth_3by_01_0315.pdf |
| Auto-Negotiation - What is it and how it fits into 1TPCE | https://www.ieee802.org/3/1TPCESG/public/Lo_02_0514.pdf |
| O’Reilly Ethernet: The Definitive Guide, 2nd Edition     | https://www.oreilly.com/library/view/ethernet-the-definitive/9781449362980/ch05.html |
| PAM-4 Auto-Negotiation & Link Training White Paper       | https://xenanetworks.com/whitepaper/autoneg-link-training/ |

# Scope

This document describes the high level design of Auto-Negotiation and Link Training support in SONiC.

# Definition/Abbreviation

## Table 2: Abbreviations

| **Term**     | **Meaning**                                                        |
| ------------ | ------------------------------------------------------------------ |
| AN           | Auto-Negotiation protocol                                          |
| CDR          | Optical clock and data recovery circuit                            |
| CLI          | Command-Line Interface                                             |
| LT           | Link Training                                                      |
| NRZ          | Non-Return-to-Zero, also called Pulse Amplitude Modulation 2-level |
| PAM4         | Pulse Amplitude Modulation 4-level                                 |
| SAI          | Switch Abstraction Interface                                       |
| SONiC        | Software for Open Networking in the Cloud                          |
| SWSS         | SWitch State Service                                               |
| TX FIR       | Transmitter Finite Impulse Response (i.e. Pre-emphasis)            |

# 1 Requirements Overview

Automatic configuration of Ethernet interfaces over twisted-pair links and one fiber 
optic media type is provided by the Auto-Negotiation protocol. Clause 28 for the twisted-pair
links, Clause 37 for the 1000BASE-X fiber optic link, and Clause 73 for backplane/copper links >= 1G.
The Auto-Negotiation system ensures that devices at each end of a link can automatically negotiate their
configuration to the highest set of common capabilities.

Link Training is another process that can take place when a device is connected to a high-speed Ethernet
port through a copper cable or backplane. In this case it is important that the characteristics of the 
transmitted signal are tuned to be optimally carried over the copper cable.

Without Link Training support, the SONiC platforms will have to provide a set of statically tuned
tx signal parameters, it's neither scale and nor available on all of the SONiC platforms, and hence,
it's nice to introduce the Link Training support into SONiC.

## 1.1 Functional Requirements

Link Training could be enabled with or without AN Clause 73, it's performed after the speed is negotiated,
and hence, AN does not guarantee a compatible mode of operation will result in a link being established or
maintained.

- The AN and LT should be configurable individually from the CLI.
- The AN defaults to be disabled for the best backward compatibility.
- The LT defaults to auto mode, the LT will be enabled/disabled as per the configuration in media_settings.json, or being activated for PAM4 links if media_settings.json does not exist.
- The LT should never be activated for the native copper ports (e.g. RJ45)
- When the AN is administratively enabled, it shall update the advertised capabilities as per the transceiver attached.
- When the LT is administratively enabled, it shall be activated on PAM4 link and de-activated for NRZ links.
- When the LT is administratively disabled, it shall be consistently deactivated.
- The LT should be de-activated when CDR is not available on the attached transceiver.

## 1.2 Configuration and Management Requirements

Several CLI configuration commands support this feature. The commands are available in
interface config mode.

- A CLI show command lists the per-interface AN and LT configuration and status.
- A set of CLI config commands to configure the per-interface AN and LT configuration.

## 1.3 Design Overview

### 1.3.1 Basic Approach

The AN/LT functionality on a switch platform is enabled by a dedicated MAC/PHY controller. The MAC/PHY controller provides various configuration options to enable, disable and fine tune the AN/LT functionality required for the connected devices.

The communication details to MAC/PHY controller is hidden in the SAI library, while the orchagent of swss communicates with user interface via RedisDB.

For each switch platform that supports AN/LT, the port capabilities should be defined in the file *platform-def.json*, which is located in the *hwsku* directory.

### 1.3.2 Container

The AN/LT support is part of the swss docker container. On switch platforms which do support AN/LT functionality, the AN/LT gets activated when the swss container starts.

# 2 Functionality

## 2.1 Target Deployment Use Cases

The AN/LT feature allows an administrator to perform following actions:

- Automatic configuration of Ethernet interfaces over native RJ45 links and fiber optic media (i.e.SFPs)
- Dynamically tune tx signal parameters to drop the requirement of statically tuned parameters
- View the per-port auto-negotiation results
- View the per-port link-training results

# 3 Design

## 3.1 Overview

### 3.1.1 Platform Configuration

The AN port capabilities should be defined in the platform specific platform-def.json:

**Format** 
```
    "port-autoneg-capabilities": {
        "EthernetXY": {
            "Z-lane": [
                PORTMODE, PORTMODE, ...
            ]

    XYZ: Integer

    PORTMODE = "speed[:fec[:duplex]]"

        fec defaults to "none" is not specified
        duplex defaults to "full-duplex" is not specified
```

**Example**
```
    "port-autoneg-capabilities": {
        "Ethernet0-255": {
            "1-lane": [
                "50000:rs", "50000:none",
                "25000:rs", "25000:fc", "25000:none",
                "10000:rs", "10000:fc", "10000:none"
            ],
            "2-lane": [
                "100000:rs", "100000:none",
                "50000:rs",  "50000:none"
            ],
            "4-lane": [
                "200000:rs", "200000:none",
                "100000:rs", "100000:none",
                "40000:rs", "40000:fc", "40000:none"
            ],
            "8-lane": [
                "400000:rs"
            ]
        }
    }
```

- The port capabilities of the **MAC** device is specified in **platform-def.json**, and it will later be published to PORT_TABLE of APPL_DB by the **pmon#xcvrd** when pmon startup.
- The port capabilities of the **PHY** device is determined by the **pmon#xcvrd** as per the transceiver attached to the port, and it will later be published to TRANSCEIVER_INFO of STATE_DB by the **pmon#xcvrd**.
- The **swss#orchagent** will first select the per-lane speed from "lane_speed" of APPL_DB, and then validates the per-port speed based on the lane numbers available on the port and check if it's listed in the "port_speed" of APPL_DB, and finally generate the speed capabilities for the AN local advertisement.
- The **swss#orchagent** will reference the "fec-mode" of platform-def.json for the FEC types of the AN advertisement.
- The "lane_speed" could be eliminated for the 1-lane ports, in this case, all the speeds listed in "port_speed" will be flagged in the AN local advertisement.

## 3.2 Media Type

The link-training requires the media type to be correctly configured as per the transceiver attached, otherwise, it may not be functional properlly.
The supported media types are as below, and it's the **pmon#xcvrd** which is responsible fo

- Backplane
The media type of the interface should be configured as Backplane if the attached transceiver is one of the following
	- Backplane
	- CAUI-x C2C
	- CAUI-x C2M (Optics without a CDR/retimer)
	- 25G/50G Active Optic Cable(AOC) using CAUI-x C2M
- Copper
	- Direct Attached Cable
	- Passive Copper Cable
	- Active Copper Cable
- Optic
	- 10G/40G optics without a CDR/retimer
	- 10G/40G Active Optic Cable(AOC) using SFI or nPPI

## 3.3 Database

### 3.3.1 Auto-negotiation and link-training in CONFIG_DB
```
key              = PORT:EthernetXY

;field           = value
autoneg          = ON/OFF
linktrn          = AUTO/ON/OFF
medium           = BACKPLANE/COPPER/OPTIC
```

### 3.3.1 Auto-negotiation and link-training in APPL_DB
```
key               = PORT_TABLE:EthernetXY

;field            = value
autoneg           = ON/OFF
admin_advertise   = PORTMODE,PORTMODE....
linktrn           = AUTO/ON/OFF
medium            = BACKPLANE/COPPER/OPTIC

; PORTMODE = "speed[:fec[:duplex]]"
;        fec defaults to "none" is not specified
;        duplex defaults to "full-duplex" is not specified
```

### 3.3.2 Auto-negotiation status in STATE_DB
```
db                = STATE_DB
key               = PORT:EthernetXY

;field            = value
autoneg           = ON/OFF
admin_advertise       = PORTMODE,PORTMODE....
oper_local_advertise  = PORTMODE,PORTMODE....
oper_remote_advertise = PORTMODE,PORTMODE....
speed             = SPEED
linktrn           = ON/OFF
medium            = BACKPLANE/COPPER/OPTIC
txfir_pre2        = 5*DIGIT
txfir_pre1        = 5*DIGIT
txfir_main        = 5*DIGIT
txfir_post1       = 5*DIGIT
txfir_post2       = 5*DIGIT
txfir_post3       = 5*DIGIT

; PORTMODE = "speed[:fec[:duplex]]"
;        fec defaults to "none" is not specified
;        duplex defaults to "full-duplex" is not specified
```

## 3.4 PMON#XCVRD

The **pmon#xcvrd** process will be enhanced for the following activities:

- Publishing the media type of the attached transceiver to STATE_DB
- Publishing the supported speed of the attached transceiver to STATE_DB

## 3.5 SWSS#ORCHAGENT

The **swss#orchagent** process will be enhanced for the following activities:

- Upon swss startup, it fetches the TX FIR/Pre-emphasis values from MAC/PHY via SAI calls, and get TX FIR published to the STATE_DB
- Subscribing APPL_DB for the **medium** updates, and performs the tasks below.
	- Communicating with syncd to update the media type of the port via SAI calls.
	- Fetching the TX FIR/Pre-emphasis values from MAC/PHY via SAI library, and get it published to the STATE_DB
- Subscribing APPL_DB for the **preemphasis** updates, and performs the tasks below
	- Communicating with syncd to update the pre-emphasis of the port via SAI calls.
	- Having the pre-emphasis of the port published to the STATE_DB
- Subscribing APPL_DB for the **speed/intf_type** updates, and performs the tasks below
	- Communicating with syncd to update the speed/interface-type of the port via SAI calls.
	- Fetching the TX FIR/Pre-emphasis values from APPL_DB, update the pre-emphasis of the port via SAI calls, and get it published to the STATE_DB

## 3.4 SAI

The AN/LT support is built on top the following SAI port attributes, hence these should be implemented in the SAI library

- SAI_PORT_ATTR_SUPPORTED_SPEED
- SAI_PORT_ATTR_SUPPORTED_FEC_MODE
- SAI_PORT_ATTR_SUPPORTED_HALF_DUPLEX_SPEED
- SAI_PORT_ATTR_SUPPORTED_AUTO_NEG_MODE
- SAI_PORT_ATTR_SUPPORTED_MEDIA_TYPE
- SAI_PORT_ATTR_REMOTE_ADVERTISED_SPEED
- SAI_PORT_ATTR_REMOTE_ADVERTISED_FEC_MODE
- SAI_PORT_ATTR_REMOTE_ADVERTISED_HALF_DUPLEX_SPEED
- SAI_PORT_ATTR_REMOTE_ADVERTISED_AUTO_NEG_MODE
- SAI_PORT_ATTR_REMOTE_ADVERTISED_MEDIA_TYPE

## 3.5 User Interface
*Please follow the SONiC Management Framework Developer Guide - https://drive.google.com/drive/folders/1J5_VVuwoJBa69UZ2BoXLYW8PZCFIi76K*

### 3.5.1 Data Models

```
module: openconfig-interfaces
  +--rw interfaces
     +--rw interface* [name]
        +--rw name                    -> ../config/name
        |  ............
        +--rw oc-eth:ethernet
        |  +--rw oc-eth:config
        |  |  ............
        |  |  +--rw oc-eth:auto-negotiate?             boolean
        |  |  +--rw oc-eth:duplex-mode?                enumeration
        |  |  +--rw oc-eth:port-speed?                 identityref
        |  |  ............
        |  |  +--rw oc-eth-ext2:link-training?         boolean
        |  |  +--rw oc-eth-ext2:port-fec?              identityref
        |  |  ............
        |  +--ro oc-eth:state
        |  |  +--ro oc-eth:auto-negotiate?             boolean
        |  |  +--ro oc-eth:duplex-mode?                enumeration
        |  |  +--ro oc-eth:port-speed?                 identityref
        |  |  +--ro oc-eth:negotiated-duplex-mode?     enumeration
        |  |  ............
        |  |  +--ro oc-eth-ext2:link-training?         boolean
        |  |  +--ro oc-eth-ext2:oper-link-training?    boolean
        |  |  +--ro oc-eth-ext2:tx-fir-pre2?           int16
        |  |  +--ro oc-eth-ext2:tx-fir-pre1?           int16
        |  |  +--ro oc-eth-ext2:tx-fir-main?           int16
        |  |  +--ro oc-eth-ext2:tx-fir-post1?          int16
        |  |  +--ro oc-eth-ext2:tx-fir-post2?          int16
        |  |  +--ro oc-eth-ext2:tx-fir-post3?          int16
        |  |  +--ro oc-eth-ext2:port-fec?              identityref
```

### 3.5.2 CLI

#### 3.5.2.1 Configuration Commands

**link-training**

**Default** Auto mode is activated
**Format**  [no] link-training {auto|on|off}
**Mode**    Interface Config Mode

Using this command to enable or disable link-training and set the mode for the port.

| Parameter | Description                                                       |
| :-------: | :---------------------------------------------------------------- |
| auto      | Link-training will be enabled or disabled per the configuration found in the media_setting.json, and will be enabled if attached transceiver is not specified in the media_settings.json |
| on        | Link-training will always be enabled if applicable (e.g. Link-training is not available on native RJ45) |
| off       | Link-training will always be disabled if applicable (e.g. Link-training is not available on native RJ45) |

Example:

Having link-training auto mode activated on the port
```
(config)# link-training auto
```

Having link-training enabled on the port, regardless of the configurations in the media_settings.json
```
(config)# link-training on
```

Having link-training disabled on the port, regardless of the configurations in the media_settings.json
```
(config)# link-training off
```

**speed**

Using this command to enable or disable auto-negotiation and set the speed that will be
advertised by that port.

Use the auto keyword to enable auto-negotiation on the port. Use the command without the
**auto** keyword to disable auto-negotiation and to set the port speed and mode according
to the command values. If the auto-negotiation is disabled, the speed and duplex mode must
be set.

**Default** Auto-negotiation is disabled
**Format**  speed {SPEED}
            speed auto {SPEED}[,SPEED]
**Mode**    Interface Config Mode

| Parameter | Description                                                       |
| :-------: | :---------------------------------------------------------------- |
| SPEED     | Port speed in Mbps, the postfix f/h could be used for duplex mode, and it's full-duplex if not specified |

| Speed     | Description         |
| :-------: | :------------------ |
| 400000    | 400G full-duplex    |
| 100000    | 100G full-duplex    |
| 25000     | 25G full-duplex     |
| 10000     | 10G full-duplex     |
| 1000      | 1G full-duplex      |
| 100       | 100M full-duplex    |
| 100f      | 100M full-duplex    |
| 100h      | 100M half-duplex    |
| 10        | 10M full-duplex     |
| 10f       | 10M full-duplex     |
| 10h       | 10M half-duplex     |

Example:

Having auto-negotiation enabled with 25G,10G and 1G in the advertisement
```
(config)# speed auto 25000,10000,1000
```

Having auto-negotiation disabled with 25G full-duplex
```
(config)# speed 25000
```

Having auto-negotiation disabled with 100M full-duplex
```
(config)# speed 100
```

Having auto-negotiation disabled with 100M half-duplex
```
(config)# speed 100h
```

#### 3.5.2.2 Show Commands

**show interface advertise**

Using this command to display the local administrative link advertisement configuration,
local operational link advertisement, and the link partner advertisement for an interface.

If the link is down, a dash is displayed against the Oper Peer Advertisement.

If this command is executed without the optional *Interface* parameter, then it displays
the auto-negotiation state and operational local link advertisement for all the ports.
Operational link advertisement will display speed only if it is supported by both local
as well as link partner. If auto-negotiation is disabled, then operational link advertisement
is not displayed.

**Format**  show interface advertise [Interface]
**Mode**    EXEC Mode

| Parameter | Description                                                                |
| :-------: | :------------------------------------------------------------------------- |
| Interface | Physical interface name (e.g. Ethernet N)                                  |

Example:

```
(sonic)# show interface advertise

Name          Type              Auto-Neg  Operational Link Advertisement
------------  ----------------  --------  -------------------------------------
Ethernet0     1000BASE-T        Enabled   1G,100f,100h,10f,10h
Ethernet1     1000BASE-T        Enabled   1G,100f,100h,10f,10h
Ethernet2     1000BASE-T        Disabled
Ethernet3     1000BASE-T        Enabled   1G,100f,100h,10f,10h

(sonic)# show interface advertise Ethernet 3

Name: Ethernet3
Type: 1000BASE-T
Link State: Down
Auto Negotiation: Enabled
Admin Link Training: Disabled
Oper Link Training: Disabled
                            400G 100G 40G  25G  10G  1G   100f 100h 10f  10h
                            ---- ---- ---- ---- ---- ---- ---- ---- ---- ----
Admin Local Advertisement   no   no   no   no   no   yes  yes  no   yes  no
Oper Local Advertisement    no   no   no   no   no   yes  yes  no   yes  no
Oper Peer Advertisement     no   no   no   no   no   no   yes  yes  yes  yes
```

**show interface preemphasis**

Using this command to display the current transmitter equalization parameter,
admin link-training configuration, and the operational link-training status for an interface.

If this command is executed without the optional *Interface* parameter, then it displays
the link-training state and pre-emphasis(i.e. TX FIR) for all the ports.

If the port type is native copper, TX FIR will be blank and Oper Link Training should be disabled.

**Format**  show interface preemphasis [Interface]
**Mode**    EXEC Mode

| Parameter | Description                                                                |
| :-------: | :------------------------------------------------------------------------- |
| Interface | Physical interface name (e.g. Ethernet N)                                  |

Example:

```
(sonic)# show interface preemphasis

Name          Type              Admin LT  Oper LT   TX FIR
------------  ----------------  --------  --------  -----------------------------
Ethernet0     QSFP56-DD         Disabled  Disabled  0,-16,152,0,0,0
Ethernet1     QSFP56-DD         Disabled  Disabled  0,-16,152,0,0,0
Ethernet2     QSFP56-DD         Disabled  Disabled  0,-16,152,0,0,0
Ethernet3     QSFP56-DD         Disabled  Disabled  0,-16,152,0,0,0

(sonic)# show interface preemphasis Ethernet 3

Name: Ethernet3
Type: QSFP56-DD
Link State: Down
Admin Link Training: Disabled
Oper Link Training: Disabled

pre2      pre1      main      post1     post2     post3
--------  --------  --------  --------  --------  --------
0         -16       152       0         0         0
```


## 3.6 Warm Boot Support

When a SONiC switch undergoes a warm reboot, none of link-flaps should be observed due to autonegotiation.

# 4 Flow Diagrams

None

# 5 Serviceability and Debug

None

# 6 Error Handling

None

# 7 Scalability

None

# 8 Limitations

- Link-training is supported only when per-lane speed >= 10Gb
- Link-training is supported if the transceiver is a backplane, copper or optic with CDR
- Link-training is not supported if the transceiver is an optic without CDR
- Link-training is not targeted to find the optimal TX FIR, it only finds appropriate TX FIR that's sufficient to bring up the link.
- Link-training requires a longer time for PAM4 links compared to NRZ links.

# 9 Unit Test

- Verify the links are coming up with IXIA with default config.
- Verify "show interface preemphasis" works fine with default config, and the TX FIR readings are properlly displayed.
- Verify "show interface preemphasis" works fine when LT is disabled, and the TX FIR readings are properlly displayed.
- Verify "show interface preemphasis" works fine when LT is enabled, and the TX FIR readings are properlly displayed.
- Verify "show interface preemphasis" to ensure we're having different TX FIR upon LT enabled/disabled while the port with physical links established 
- Verify AN is able to get the link up with max. speed by having 100G and 40G advertised on local, and 40G only from the remote peer, and check if this is up with 40G
- Verify AN is able to get the link up with max. speed by having 25G and 10G advertised on local, and 10G only from the remote peer, and check if this is up with 10G
- Verify AN is able to get the link up with expected FEC by having 100G:RS and 100G:NONE advertised on local, and 100G:NONE only from the remote peer, and check if this is up with 100G:NONE
- Verify AN is able to get the link up with expected FEC by having 100G:RS and 100G:NONE advertised on local, and 100G:RS only from the remote peer, and check if this is up with 100G:RS
- Verify LT should always be disabled if it's administratively disabled.
- Verify LT should always be enabled if it's administratively enabled.
- Verify LT is correctly enabled/disabled as per the media_settings.json, in the case of auto mode.
- Verify LT is correctly enabled/disabled as per the port speed, if auto mode is activated and media_settings.json does not exist.
- Verify LT is always disabled for port speed < 10G, regardless of the settings in media_settings.json

# 10 Internal Design Information

In the case of Cyrus, this feature will only be available on the TD4-X11 and TH3 platforms.
