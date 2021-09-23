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

[Table 1: References](#table-1-references)  
[Table 2: Abbreviations](#table-2-abbreviations)  
[Table 3: Supported platform list](#table-3-supported-platform-list)  

# Revision

| Rev |     Date    |       Author        | Change Description                 |
|:---:|:-----------:|:-------------------:|:-----------------------------------|
| 0.1 | 04/15/2021  | Dante (Kuo-Jung) Su | Initial version                    |
| 0.2 | 08/24/2021  | Dante (Kuo-Jung) Su | Merge the YANG leaf from Google and the relevant logic |
| 0.3 | 09/22/2021  | Dante (Kuo-Jung) Su | Update the implementation details |

# About this Manual

This document provides functional requirements and high-level design for providing Auto-Negotiation and Link Training support in SONiC.

#  References

##  Table 1: References

|       **Document**                                       | **Location**   |
| -------------------------------------------------------- | -------------- |
| Auto-Negotiation (AN) Overview | https://www.ieee802.org/3/by/public/Mar15/booth_3by_01_0315.pdf |
| Auto-Negotiation - What is it and how it fits into 1TPCE | https://www.ieee802.org/3/1TPCESG/public/Lo_02_0514.pdf |
| O.Reilly Ethernet: The Definitive Guide, 2nd Edition | https://www.oreilly.com/library/view/ethernet-the-definitive/9781449362980/ch05.html |
| PAM-4 Auto-Negotiation & Link Training White Paper | https://xenanetworks.com/whitepaper/autoneg-link-training/ |
| 100GBASE-KP4 Link Training | https://www.ieee802.org/3/bj/public/sep12/lusted_3bj_01_0912.pdf |
| PAM4 transmitter training protocol | https://www.ieee802.org/3/cd/public/July16/healey_3cd_01a_0716.pdf |
| 25G & 50G Specification | https://ethernettechnologyconsortium.org/wp-content/uploads/2020/03/25G-50G-Specification-FINAL.pdf |
| Wiki of 10_Gigabit_Ethernet | https://en.wikipedia.org/wiki/10_Gigabit_Ethernet |

# Scope

This document describes the high level design of Auto-Negotiation and Link Training support in SONiC.

# Definition/Abbreviation

## Table 2: Abbreviations

| **Term**     | **Meaning**                                                        |
| ------------ | ------------------------------------------------------------------ |
| AN           | Auto-Negotiation protocol                                          |
| CDR          | Optical clock and data recovery circuit                            |
| CLI          | Command-Line Interface                                             |
| FEC          | Forward Error Correction                                           |
| LT           | Link Training                                                      |
| NRZ          | Non-Return-to-Zero, also called Pulse Amplitude Modulation 2-level |
| PAM4         | Pulse Amplitude Modulation 4-level                                 |
| SAI          | Switch Abstraction Interface                                       |
| SLT          | Standalone Link-Training                                           |
| SONiC        | Software for Open Networking in the Cloud                          |
| SWSS         | SWitch State Service                                               |
| TX FIR       | Transmitter Finite Impulse Response (i.e. Pre-emphasis)            |

# 1 Requirements Overview

Automatic configuration of Ethernet interfaces over twisted-pair links and one fiber 
optic media type is provided by the Auto-Negotiation protocol. Clause 28 for the twisted-pair
links, Clause 37 for the 1000BASE-X fiber optic link, and Clause 73 for backplane/copper >= 1G.
The **Auto-Negotiation system** ensures that devices at each end of a link can automatically
negotiate their configuration to the **highest set of common capabilities**.

- Clause 28: Twisted-pair links
- Clause 37: 1000BASE-X fiber optic link
- Clause 73: Clause 73 for backplane/copper >= 1G.

Link Training(Clause 72 and 93) is another process that can take place when a device is connected to a high-speed Ethernet
port through a copper cable or backplane. In this case it is important that the characteristics of the 
transmitted signal are tuned to be optimally carried over the copper cable.

- IEEE 802.3bj-2014
- Clause 72: 10GBASE-KR
- Clause 93: 100GBASE-KR4

Without Link Training support, the SONiC platforms will have to provide a set of statically tuned
tx signal parameters, it's neither scale nor available on all of the SONiC platforms, hence it's nice to introduce the Link Training support into SONiC.

## 1.1 Functional Requirements

- Both AN and LT are targeting **copper links** (e.g. CX, CR, KX, KR, DAC)
- The LT is always enabled when AN is enabled  
    - LT is performed after the speed is negotiated, hence AN does not guarantee a 
compatible mode of operation will result in a link being established or maintained.
- The LT could be enabled when AN is disabled, and it will be referred as **Standalone Link-Training(SLT)**  
    - When SLT is administratively enabled: LT will be enabled when AN is disabled  
    - When SLT is administratively disabled: LT will be disabled when AN is disabled
- The AN should be disabled by default for the best backward compatibility.
- The LT should be disabled by default for the best backward compatibility.
- The LT should be de-activated for the native copper ports. (e.g. Native Twisted-pair, RJ45)
- The LT is only available for links greater than or equal to 10G.
- The LT is only available if the attached transceiver is with CDR support.
- The AN and SLT should be configurable individually from the CLI.

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

- The AN provides automatic configuration of speed and FEC.
- The LT is to dynamically tune tx signal parameters to drop the requirement of statically tuned parameters
- View the per-port auto-negotiation results
- View the per-port link-training results

# 3 Design

## 3.1 Overview

### 3.1.1 Platform Configuration

The per-port AN default configuration should be defined in the platform specific platform-def.json:

**Format** 
```
    "default-autoneg-mode": {
        "EthernetWX-YZ": {
            "autoneg": "on"
        }
    },
```

**Example**
```
    "default-autoneg-mode": {
        "Ethernet0-47": {
            "autoneg": "on"
        }
    },
```

- The port capabilities will be automatically determined by the SAI library
- The user specified AN/LT configurations in the CONFIG_DB is treated as a **hint**, the final result may fall back to driver defaults if none is applicable (e.g. SFP replacement without reconfiguration)
- The operational AN/LT configuration is available via command **show interface advertise**

## 3.2 Media Type

The link-training requires the media type to be correctly configured as per the transceiver attached, otherwise, it may not be functional properlly.
The supported media types are as below, and it's the **pmon#xcvrd** which is responsible for updating the media type as per the attached transceiver.

**Note: The following medium types are Broadcom specific**

- Backplane
	- Backplane
	- CAUI-x C2C
	- CAUI-x C2M (Optics with a CDR/retimer)
- Copper
	- Direct Attached Cable
	- Passive Copper Cable
	- Active Copper Cable (ACC)
- Optic **(AN/LT is not supported in this case)**
	- 10G/40G optics without a CDR/retimer
	- 10G/40G Active Optic Cable(AOC) using SFI or nPPI

## 3.3 Database

### 3.3.1 Auto-negotiation and link-training in CONFIG_DB
```
key             = PORT:EthernetXY

;field          = value
autoneg         = on|off
link_training   = on|off   ; This is the standalone-link-training
adv_speeds      = SPEED1,SPEED2...
; SPEED<n>      = 400000|100000|40000|25000|10000|1000|100|10

e.g.
......
"PORT|Ethernet128": {
    "type": "hash", 
    "value": {
      "autoneg": "on", 
      "link_training": "off",
      "adv_speeds": "100000,40000"
    }
}, 
......
```

### 3.3.1 Auto-negotiation and link-training in APPL_DB
```
key             = PORT_TABLE:EthernetXY

;field             = value
autoneg            = on|off
link_training      = on|off     ; Administratively configured standalone link-training
oper_link_training = on|off     ; Operational link-training state
oper_speed         = 400000|100000|40000|25000|10000|1000|100|10  ; Operational port speed
oper_adv_speeds    = SPEED1,SPEED2...
; SPEED<n>         = 400000|100000|40000|25000|10000|1000|100|10
oper_fec           = rc|fc|none ; Operational FEC mode

e.g.
......
"PORT_TABLE:Ethernet128": {
    "type": "hash", 
    "value": {
      "autoneg": "on", 
      "link_training": "off", 
      "oper_adv_speeds": "100000,40000", 
      "oper_fec": "rc", 
      "oper_link_training": "on", 
      "oper_speed": "100000", 
      "oper_status": "up", 
      ......
    }
}, 
......
```

## 3.4 PMON#XCVRD

The **pmon#xcvrd** process will be enhanced for the following activities:

- Publishing the media type of the attached transceiver to APPL_DB
- Publishing the static information of the attached transceiver to STATE_DB

## 3.5 SWSS#ORCHAGENT

The **swss#orchagent** process will be enhanced for the following activities:

- Subscribing APPL_DB for the **medium**, and send the media type updates to syncd.
- Subscribing APPL_DB for the **adv_speeds**, and send the AN advertisement requests to syncd.
- Subscribing APPL_DB for the **autoneg**, and send the AN requests to syncd.
- Subscribing APPL_DB for the **link_training**, and send the LT requests to syncd.
- Publishing the operational link speed (**oper_speed**) to APPL_DB
- Publishing the operational local AN advertisement (**oper_adv_speeds**) to APPL_DB
- Publishing the operational remote AN advertisement (**remote_adv_speeds**) to APPL_DB
- Publishing the operational LT state (**oper_link_training**) to APPL_DB
- Publishing the operational FEC mode (**oper_fec**) to APPL_DB

## 3.4 SAI

The AN/LT support is built on top the following SAI port attributes

- SAI_PORT_ATTR_ADVERTISED_SPEED
- SAI_PORT_ATTR_AUTO_NEG_MODE
- SAI_PORT_ATTR_MEDIA_TYPE
- SAI_PORT_ATTR_REMOTE_ADVERTISED_SPEED
- SAI_PORT_ATTR_REMOTE_ADVERTISED_HALF_DUPLEX_SPEED
- SAI_PORT_ATTR_REMOTE_ADVERTISED_MEDIA_TYPE
- SAI_PORT_ATTR_SPEED

## 3.5 User Interface
*Please follow the SONiC Management Framework Developer Guide - https://drive.google.com/drive/folders/1J5_VVuwoJBa69UZ2BoXLYW8PZCFIi76K*

### 3.5.1 OC YANG

```
module: openconfig-interfaces
  +--rw interfaces
     +--rw interface* [name]
        +--rw name                    -> ../config/name
        |  ............
        +--rw oc-eth:ethernet
        |  +--rw oc-eth:config
        |  |  +--rw oc-eth:auto-negotiate?                  boolean
        |  |  +--rw oc-eth-ext2:standalone-link-training?   boolean
        |  |  +--rw oc-eth-ext2:advertised-speed?           string
        |  +--ro oc-eth:state
        |  |  +--ro oc-eth:auto-negotiate?                  boolean
        |  |  +--ro oc-eth:negotiated-port-speed?           identityref
        |  |  +--ro oc-eth-ext2:standalone-link-training?   boolean
        |  |  +--ro oc-eth-ext2:advertised-speed?           string
        |  |  +--ro oc-eth-ext2:negotiated-port-fec?        identityref
        |  |  +--ro oc-eth-ext2:oper-link-training?         boolean
        |  |  +--ro oc-eth-ext2:oper-advertised-speed?      string
        |  |  +--ro oc-eth-ext2:remote-advertised-speed?    string
```

### 3.5.2 CLI

#### 3.5.2.1 Configuration Commands

**link-training**

**Default** Auto mode is activated  
**Format**  [no] standalone-link-training  
**Mode**    Interface Config Mode  

Using this command to enable or disable standalone-link-training and set the mode for the port.
This command is only applicable to physical ports, as it's to tune the hardware signal.

Example:

**Enable Standalone Link-Training on Ethernet0**  

```
sonic# configure terminal
sonic(config)# interface Ethernet 0
sonic(conf-if-Ethernet0)# standalone-link-training
```

**Disable Standalone Link-Training on Ethernet0**  
```
sonic# configure terminal
sonic(config)# interface Ethernet 0
sonic(conf-if-Ethernet0)# no standalone-link-training
```

**Enable Standalone Link-Training on Ethernet0,Ethernet1...Ethernet16**  

```
sonic# configure terminal
sonic(config)# interface range Ethernet 0-16
%Info: Configuring only existing interfaces in range
sonic(conf-if-range-eth**)# standalone-link-training
```

**Disable Standalone Link-Training on Ethernet0,Ethernet1...Ethernet16**  

```
sonic# configure terminal
sonic(config)# interface range Ethernet 0-16
%Info: Configuring only existing interfaces in range
sonic(conf-if-range-eth**)# no standalone-link-training
```

**speed**

Using this command to enable or disable auto-negotiation and set the speed that will be
advertised by that port.

Use the auto keyword to enable auto-negotiation on the port. Use the command without the
**auto** keyword to disable auto-negotiation and to set the port speed and mode according
to the command values. If the auto-negotiation is disabled, the speed must be set.

**Default** Auto-negotiation is disabled  
**Format**  speed {SPEED}  
            speed auto {SPEED}[,SPEED]  
**Mode**    Interface Config Mode  

| Parameter | Description         |
| :-------: | :------------------ |
| SPEED     | Port speed in Mbps  |

Example:

**Enable auto-negotiation and advertise all the speeds supported in the hardware**  
```
sonic(conf-if-Ethernet0)# speed auto
```

**Enable auto-negotiation and advertise 10G and 1G**  
```
sonic(conf-if-Ethernet0)# speed auto 10000,1000
```

**Enable auto-negotiation and advertise 1G only**  
```
sonic(conf-if-Ethernet0)# speed auto 1000
```

**Disable auto-negotiation and configure the port speed to 25G**  
```
sonic(conf-if-Ethernet0)# speed 25000
```

**Disable auto-negotiation and restore port speed as-is before auto-negotiation activation**  
```
sonic(conf-if-Ethernet0)# no speed auto
```

**Disable auto-negotiation and configure the port speed to default value**  
```
sonic(conf-if-Ethernet0)# no speed
```

#### 3.5.2.2 Show Commands

**show interface status**

Using this command to display the link and auto-negotiation status.

**Format**  show interface status  
**Mode**    EXEC Mode  

Example:
```
sonic# show interface status
-----------------------------------------------------------------------------------------------------------------------------
Name                Description                   Admin     Oper      AutoNeg   Speed          MTU            Alternate Name
-----------------------------------------------------------------------------------------------------------------------------
Ethernet0           -                             down      down      on        -              9100           Eth1/1/1
Ethernet1           -                             down      down      on        -              9100           Eth1/1/2
Ethernet2           -                             down      down      on        -              9100           Eth1/1/3
Ethernet3           -                             down      down      on        -              9100           Eth1/1/4
Ethernet8           -                             down      down      on        -              9100           Eth1/2/1
Ethernet9           -                             down      down      on        -              9100           Eth1/2/2
Ethernet10          -                             down      down      on        -              9100           Eth1/2/3
Ethernet11          -                             down      down      on        -              9100           Eth1/2/4
Ethernet16          -                             down      down      on        -              9100           Eth1/3/1
Ethernet17          -                             down      down      off       25000          9100           Eth1/3/2
Ethernet18          -                             down      down      off       25000          9100           Eth1/3/3
Ethernet19          -                             down      down      off       25000          9100           Eth1/3/4
Ethernet24          -                             down      down      off       25000          9100           Eth1/4/1
```

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

**Display the Auto-Negotiation configuration of all ports**  
```
sonic# show interface advertise

Interface       Type                      Auto Neg  Oper Advertised Speed
--------------  ------------------------  --------  ----------------------------
Ethernet0       QSFP28 100GBASE-SR4       on        40000,100000
Ethernet4                                 on        40000,100000
Ethernet8       QSFP28 100GBASE-SR4       on        40000,100000
Ethernet12                                on        40000,100000
Ethernet16                                on        40000,100000
Ethernet20                                on        40000,100000
Ethernet24                                on        40000,100000
Ethernet28                                on        40000,100000
Ethernet32                                on        40000,100000
Ethernet36                                on        40000,100000
Ethernet40                                on        40000,100000
Ethernet44                                on        40000,100000
Ethernet48                                on        40000,100000
Ethernet52                                on        40000,100000
Ethernet56                                on        40000,100000
Ethernet60                                on        40000,100000
Ethernet64                                on        40000,100000
Ethernet68                                on        40000,100000
Ethernet72                                on        40000,100000
Ethernet76                                on        40000,100000
Ethernet80                                on        40000,100000
--more--
```

**Display the Auto-Negotiation configuration of Ethernet0...Ethernet12**  
```
sonic# show interface advertise Ethernet 0-12

Interface       Type                      Auto Neg  Oper Advertised Speed
--------------  ------------------------  --------  ----------------------------
Ethernet0       QSFP28 100GBASE-SR4       on        40000,100000
Ethernet4                                 on        40000,100000
Ethernet8       QSFP28 100GBASE-SR4       on        40000,100000
Ethernet12                                on        40000,100000
sonic#
```

**Display the Auto-Negotiation configuration of one specific port**  
```
sonic# show interface advertise Ethernet 8

Name: Ethernet8
Type: QSFP28 100GBASE-SR4
Admin State: DOWN
Link Status: DOWN
Auto Negotiation: ON
Operational FEC Mode:

                            400G 100G 40G  25G  10G  1G   100f 100h 10f  10h
                            ---- ---- ---- ---- ---- ---- ---- ---- ---- ----
Admin Local Advertisement   no   no   no   no   no   no   no   no   no   no
Oper Local Advertisement    no   yes  yes  no   no   no   no   no   no   no
Oper Remote Advertisement   -    -    -    -    -    -    -    -    -    -
sonic#
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

- AN/LT is not available on the management ports of the switch silicon.
- LT is available only when per-lane speed >= 10Gb
- LT is available only when the media type of the transceiver is either backplane or copper (Broadcom specific)
- LT finds appropriate TX FIR that's sufficient to bring up the link, instead of the optimal TX FIR
- LT requires a longer time for PAM4 links compared to NRZ links. (e.g. Max. 15 sec for 400G)
- AN/LT is not working on an **DR link** between TD4/TH3 and TD3/TH2/TH  
		- The DR1 modules are with hardware FEC engine on the PHY and it can't be disabled, hence the MAC will need to have FEC disabled  
		- The system side of DR1 module is actually NRZ mode while the line side is PAM4 mode  
		- The DR4 modules are without hardware FEC engine on the PHY, hence the MAC will need to have FEC enabled  
		- The system side and line side of DR4 module is PAM4 mode  
		- Hence, this AN/LT is not working on a transceiver like this  
- AN/LT is not working on an **AOC link** between TD4(DELL Z9432) and TH3(DELL Z9332)  
```
Ethernet8: SFP EEPROM detected
        Application Advertisement:
                1: 400GAUI-8 C2M (Annex 120E) | Active Cable assembly with BER < 2.6x10^-4
                2: 200GAUI-4 C2M (Annex 120E) | Active Cable assembly with BER < 2.6x10^-4
                3: 100GAUI-2 C2M (Annex 135G) | Active Cable assembly with BER < 2.6x10^-4
                4: 50GAUI-1 C2M (Annex 135G) | Active Cable assembly with BER < 2.6x10^-4
        Connector: No separable connector
        Identifier: QSFP56-DD
        Implemented Memory Pages:
                Bank 0 Implemented
        Length Cable Assembly(m): 3.0
        Module State: Ready State
        Revision Compliance: 3.0
        Vendor Date Code(YYYY-MM-DD Lot): 2020-05-08:01
        Vendor Name: DELL EMC
        Vendor OUI: 00-72-65
        Vendor PN: DH11M
        Vendor Rev: A1
        Vendor SN: CN0F9KR70580072
```
- AN remote advertisement is not supported on LTSW switches (e.g. TD4, TH4)  
```
$SDK/src/bcm/ltsw/port.c:
int
bcm_ltsw_port_autoneg_ability_remote_get(int unit, bcm_port_t port,
                                     int max_num_ability, bcm_port_speed_ability_t *abilities,
                                     int *actual_num_ability)
{
    return SHR_E_UNAVAIL;
}
```
- AN/LT will not work at 4x100G mode in the case of self-loop
- AN/LT will not work on the Cu SFPs
- AN/LT does not work in the case of medium/interface mismatch
- AN can not change the current medium/interface type, it has to be configured before enabling AN
- AN/LT will not work between DELL and non-DELL platform, this is due to the missing interface type support on the non-DELL platforms  
		- While the code changes are ready for this, there are connection failures between AS7816 and AS7326 in the sanity testbed, will circle back on this later.
- TD3-X7 supports only 10G and 1G for 10G ports and 25G only for 25G ports, this is a VCO hardware constraint.  
		- 4x10G: Valid advertisement: 10G and 1G  
		- 4x25G: Valid advertisement: 25G  

# 9 Unit Test

- Verify the links are coming up with IXIA with default config.
- Verify AN is able to get the link up with 400G by having 400G advertised on both local and remote
- Verify AN is able to get the link up with 100G by having 100G and 40G advertised on local, and 100G,40G from the remote peer
- Verify AN is able to get the link up with 100G by having 100G and 40G advertised on local, and 100G only from the remote peer
- Verify AN is able to get the link up with 40G by having 100G and 40G advertised on local, and 40G only from the remote peer
- Verify AN is able to get the link up with 40G by having only 40G advertised on local, and 40G only from the remote peer
- Verify AN is able to get the link up with 10G by having 10G,1G advertised on local, and 10G,1G from the remote peer
- Verify AN is able to get the link up with 10G by having 10G,1G advertised on local, and 10G from the remote peer
- Verify AN is able to get the link up with 10G by having 10G advertised on local, and 10G from the remote peer
- Verify AN is able to get the link up with 1G by having 10G,1G advertised on local, and 1G from the remote peer
- Verify AN is able to get the link up with 1G by having 1G advertised on local, and 1G from the remote peer
- Verify AN is able to get the link up with expected FEC, in the case that both DUTs are Broadcom switch
- Verify LT should always be disabled if both AN and SLT are administratively disabled.
- Verify LT should always be enabled if either AN or SLT is administratively enabled.
- Verify the port is using the same pre-emphasis values defined in the media_settings.json when LT is de-activated
- Verify PHY registers to check if LT is correctly activated if either AN or SLT is administratively enabled.

# 10 Internal Design Information

In the case of Cyrus, this feature is primarily targeting TD4 and TH3 with TD3 on a best-effort basis.

## Table 3: Supported platform list

| **Vendor**  |  **Platform**  | **Switch** | **Gearbox**  | **AN/LT Support**   |
|:------------|:---------------|:-----------|:-------------|:--------------------|
| Accton      | AS4630-54PE    | TD3-X3(HX5)| No           | Yes                 |
| Accton      | AS5835-54X     | TD3-X5(MV2)| No           | Yes                 |
| Accton      | AS5835-54T     | TD3-X5(MV2)| Yes          | No                  |
| Accton      | AS7326-56X     | TD3-X7     | No           | Yes                 |
| Accton      | AS7712-32X     | TH         | No           | No                  |
| Accton      | AS7726-32X     | TD3-X7     | No           | Yes                 |
| Accton      | AS7816-64X     | TH2        | No           | No                  |
| Accton      | AS9716-32D     | TH3        | No           | Yes                 |
| AlphaNetworks | BES2348t     | TD3-X3(HR4)| Yes(Ethernet0-23) | Yes (Only available for non-Gearbox ports) |
| Celestica   | BELGITE        | TD3-X3(HX5)| No           | Yes                 |
| Dell        | S5212          | TD3-X7     | No           | Yes                 |
| Dell        | S5224          | TD3-X7     | No           | Yes                 |
| Dell        | S5232          | TD3-X7     | No           | Yes                 |
| Dell        | S5248          | TD3-X7     | No           | Yes                 |
| Dell        | S5296          | TD3-X7     | No           | Yes                 |
| Dell        | Z9100          | TH         | No           | No                  |
| Dell        | Z9264          | TH2        | No           | No                  |
| Dell        | Z9332          | TH3        | No           | Yes                 |
| Dell        | Z9432          | TD4        | No           | Yes                 |
| Quanta      | IX4            | TH2        | No           | No                  |
| Quanta      | IX7            | TD3-X7     | No           | Yes                 |
| Quanta      | IX7_BWDE       | TD3-X7     | No           | Yes                 |
| Quanta      | IX7t (SLX9250) | TD3-X7     | No           | Yes                 |
| Quanta      | IX8            | TD3-X7     | No           | Yes                 |
| Quanta      | IX8-B          | TD3-X7     | No           | Yes                 |
| Quanta      | IX8A           | TD3-X5(MV2)| No           | Yes                 |
| Quanta      | IX8f (SLX9150) | TD3-X7     | No           | Yes                 |
| Quanta      | IX9            | TH3        | No           | Yes                 |
