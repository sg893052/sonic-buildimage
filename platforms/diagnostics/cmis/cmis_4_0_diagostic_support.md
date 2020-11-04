# SONiC CMIS 4.0 Diagnostic Support

## High Level Design Document

### Rev 0.5

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
  * [Warm Boot Support](#6-Warm-Boot-Support)
  * [Scalability](#7-Scalability)
  * [Unit Test](#8-Unit-Test)

# List of Tables

[Table 1: References](#table-1-references)<br>
[Table 2: Abbreviations](#table-2-abbreviations)

# Revision

| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|:-----------------------------------|
| 0.5 | 11/02/2020 | Dante (Kuo-Jung) Su | 1. Drop the dependency to the SONiC community pull request for the enhanced media support<br>2. Migrate the database from APPL_DB to CONFIG_DB<br>3. Updated the command syntax
| 0.4 | 08/05/2020 | William Schwartz | 1. Updated CLI command structure<br>2. Removed references to modifying admin state of the port<br> 3. Updated PORT_TABLE to reflect new change when port is places in diagnostic mode |
| 0.3 | 07/23/2020 | William Schwartz | 1. Updated yang models<br> 2. Updated TRANSCEIVER_DOM_SENSOR table entries to reflect proper values <br> 3. Removed non-supported CMIS CLI commands<br> 4. Resolved multiple review comments. |
| 0.2 | 06/25/2020 | William Schwartz | 1. Removed pattern generation / checker references.<br> 2. Corrected link to CMIS 4.0 specification<br> 3. Updated loopback test CLI mode to remove the individual lane enablement (that will be a future advanced feature enhancement)<br>4. Added loopback test Yang Model |
| 0.1 | 05/15/2020 | William Schwartz | Initial version |

# About this Manual

This document provides functional requirements and high-level design for providing CMIS 4.0 diagnostic support in SONiC.

#  References

##  Table 1: References

| **Document** | **Location** |
| ------------ | -------------- |
| QSFPDD CMIS 4.0 | http://www.qsfp-dd.com/wp-content/uploads/2019/05/QSFP-DD-CMIS-rev4p0.pdf |
| SONiC Management Framework | https://github.com/Azure/SONiC/blob/master/doc/mgmt/Management%20Framework.md |

# Scope

This document describes the high level design of CMIS 4.0 diagnostic support in SONiC.

# Definition/Abbreviation

## Table 2: Abbreviations

| **Term**     | **Meaning**                                                  |
| ------------ | ------------------------------------------------------------ |
| CMIS         | Common Management Interface Specification                    |
| CDB          | Common Data Block                                            |
| BER          | Bit Error Rate                                               |
| SNR          | Signal to Noise Ratio                                        |
| system -side | Refers to the internal connection between the switching silicon (MAC) and the transceiver |
| line-side    | Refers to the external connection between the transceiver and the remote connected device |
| FEC          | Forward Error Correction                                     |
| EPL          | Extended Payload Length                                      |
| COR          | Clear on Read                                                |
| CDR          | Clock and Data Recovery                                      |
| PRBS         | Pseudo Random Binary Sequence                                |

# 1 Requirements Overview

The Common Management Interface Specification (CMIS) version 4.0 (CMIS) was
finalized in May of 2019 and provides a variety of features and support for
different transceiver form factors.  From a software perspective, one of these
new features is the addition of diagnostic support to standardize transceiver
diagnostic implementation.

From a SONiC perspective, the addition of this diagnostic feature will enhance
platform development, user deployment, platform support, and serviceability.
Features such as system-side/line-side loopback testing at the transceiver
layer can quickly be tested by a remote user to diagnose either system-side or
line-side issues and help isolate failing hardware at various test-points shown
in the diagram below.





![Test Point Diagram](images/tp_diagram.jpg)






## 1.1 Functional Requirements

The functional requirements for adding CMIS 4.0 diagnostic support in SONiC are:

1. Display loopback capabilities of the transceiver. Per CMIS 4.0 specification, these modes can be:
    - **Media Side Output Loopback :** Loopback testing will be supported at TP 2 in diagram above.
    - **Media Side Input Loopback :** Loopback testing will be performed at TP3 to TP2 in diagram above.
    - **Host Side Output Loopback :** Loopback testing will be performed at TP 1a to TP4
    - **Host Side Input Loopback** : Loopback testing will be performed at TP1a
2. Display diagnostic reporting capabilities of the transceiver.
3. Display media side FEC support capability.
4. Display host side FEC support capability.
5. Display media side SNR if supported.
6. Display host side SNR if supported.
7. Display host side BER for lanes 1-8 if supported.
8. Display system side BER for lanes 1-8 if supported.
9. Ability to configure / enable the transceiver loopback modes

    



## 1.2 Configuration and Management Requirements

1. The CMIS 4.0 diagnostic support has to be managed using the SONiC management infrastructure.
2. The CMIS 4.0 diagnostic loopback should be disabled by default  but persistent across reboot. Traffic can be run across the port while in diagnostic mode.


# 2 Functionality

## 2.1 Target Deployment Use Cases

CMIS 4.0 diagnostic support would be idea for initial switch deployment ,  diagnosing link / connection failures within the network topology, and ensuring link quality.



# 3 Design
## 3.1 Overview

CMIS 4.0 diagnostic support can be divided into the following components:

### 3.1.1 Transceiver Daemon

The transceiver daemon (**xcvrd**) is part of the SONIC platform monitor(**pmon**), and it is responsible for posting/deleting the transceiver information upon transceiver insertion/removal, and it will be furtherly enhanced for CMIS 4.0 diagnostic support with the per port parameters in **CONFIG_DB** and **STATE_DB**.

### 3.1.2 SONiC Management Framework
Management framework is a SONiC application which is responsible for providing various common North Bound Interfaces (NBIs) for the purposes of managing configuration and status on SONiC switches. The application manages coordination of NBI’s to provide a coherent way to validate, apply and show configuration. And the CMIS 4.0 diagnostic support will only be availble in this Management framework.

## 3.2 Database

### 3.2.1 STATE_DB

The **TRANSCEIVER_DOM_SENSOR** table will have the following additional entries if the transceiver inserted is CMIS 4.0 compliant:
```
    "TRANSCEIVER_DOM_SENSOR|Ethernet1": {
        "type": "hash",
        "value": {
            "simultaneous_host_media_side_loopback_supported": True|False,
            "per_lane_media_side_loopback_supported": True|False,
            "per_lane_host_side_loopback_supported": True|False,
            "host_side_input_loopback_supported": True|False,
            "host_side_output_loopback_supported": True|False,
            "media_side_input_loopback_supported": True|False,
            "media_side_output_loopback_supported": True|False,
            "host_side_input_loopback_enable": True|False,
            "host_side_output_loopback_enable": True|False,
            "media_side_input_loopback_enable": True|False,
            "media_side_output_loopback_enable": True|False,
            }
```

### 3.2.2 CONFIG_DB

The **TRANSCEIVER_DIAG** table will be newly introduced into CONFIG_DB for the diagnostic loopback configurations.

        "TRANSCEIVER_DIAG|Ethernet48": {
            "type": "hash",
            "value": {
                "host_side_input_loopback_mode": True
                "host_side_output_loopback_mode": False
                "media_side_input_loopback_mode": False
                "media_side_output_loopback_mode": False
            }
        },




## 3.3 Data Model

### 3.4.1 openconfig-platform-ext.yang

There is currently no yang model defined for transceiver diagnostics. Therefore, the openconfig-platform/transceiver will be augmented. 

The YANG tree will look as below:
```
module: openconfig-platform
  +--rw components
     +--rw oc-transceiver:transceiver
        |  +--rw oc-transceiver:config
        |  |  +--rw oc-pf-ext:lb-media-side-output-enabled?       boolean
        |  |  +--rw oc-pf-ext:lb-media-side-input-enabled?        boolean
        |  |  +--rw oc-pf-ext:lb-host-side-output-enabled?        boolean
        |  |  +--rw oc-pf-ext:lb-host-side-input-enabled?         boolean
        |  +--ro oc-transceiver:state
        |  |  +--ro oc-pf-ext:lb-media-side-output-enabled?       boolean
        |  |  +--ro oc-pf-ext:lb-media-side-input-enabled?        boolean
        |  |  +--ro oc-pf-ext:lb-host-side-output-enabled?        boolean
        |  |  +--ro oc-pf-ext:lb-host-side-input-enabled?         boolean
        |  |  +--ro oc-pf-ext:lb-media-side-output-support?       boolean
        |  |  +--ro oc-pf-ext:lb-media-side-input-support?        boolean
        |  |  +--ro oc-pf-ext:lb-host-side-output-support?        boolean
        |  |  +--ro oc-pf-ext:lb-host-side-input-support?         boolean
        |  |  +--ro oc-pf-ext:lb-per-lane-host-side-support?      boolean
        |  |  +--ro oc-pf-ext:lb-per-lane-media-side-support?     boolean
        |  |  +--ro oc-pf-ext:lb-simul-host-media-side-support?   boolean
        |  |  +--ro oc-pf-ext:voltage?                            string
        |  |  +--ro oc-pf-ext:temperature?                        string
        |  |  +--ro oc-pf-ext:lol-lane-1?                         string
        |  |  +--ro oc-pf-ext:lol-lane-2?                         string
        |  |  +--ro oc-pf-ext:lol-lane-3?                         string
        |  |  +--ro oc-pf-ext:lol-lane-4?                         string
        |  |  +--ro oc-pf-ext:lol-lane-5?                         string
        |  |  +--ro oc-pf-ext:lol-lane-6?                         string
        |  |  +--ro oc-pf-ext:lol-lane-7?                         string
        |  |  +--ro oc-pf-ext:lol-lane-8?                         string
        |  |  +--ro oc-pf-ext:los-lane-1?                         string
        |  |  +--ro oc-pf-ext:los-lane-2?                         string
        |  |  +--ro oc-pf-ext:los-lane-3?                         string
        |  |  +--ro oc-pf-ext:los-lane-4?                         string
        |  |  +--ro oc-pf-ext:los-lane-5?                         string
        |  |  +--ro oc-pf-ext:los-lane-6?                         string
        |  |  +--ro oc-pf-ext:los-lane-7?                         string
        |  |  +--ro oc-pf-ext:los-lane-8?                         string
        |  |  +--ro oc-pf-ext:tx-power-lane-1?                    string
        |  |  +--ro oc-pf-ext:tx-power-lane-2?                    string
        |  |  +--ro oc-pf-ext:tx-power-lane-3?                    string
        |  |  +--ro oc-pf-ext:tx-power-lane-4?                    string
        |  |  +--ro oc-pf-ext:tx-power-lane-5?                    string
        |  |  +--ro oc-pf-ext:tx-power-lane-6?                    string
        |  |  +--ro oc-pf-ext:tx-power-lane-7?                    string
        |  |  +--ro oc-pf-ext:tx-power-lane-8?                    string
        |  |  +--ro oc-pf-ext:rx-power-lane-1?                    string
        |  |  +--ro oc-pf-ext:rx-power-lane-2?                    string
        |  |  +--ro oc-pf-ext:rx-power-lane-3?                    string
        |  |  +--ro oc-pf-ext:rx-power-lane-4?                    string
        |  |  +--ro oc-pf-ext:rx-power-lane-5?                    string
        |  |  +--ro oc-pf-ext:rx-power-lane-6?                    string
        |  |  +--ro oc-pf-ext:rx-power-lane-7?                    string
        |  |  +--ro oc-pf-ext:rx-power-lane-8?                    string
        |  |  +--ro oc-pf-ext:tx-bias-lane-1?                     string
        |  |  +--ro oc-pf-ext:tx-bias-lane-2?                     string
        |  |  +--ro oc-pf-ext:tx-bias-lane-3?                     string
        |  |  +--ro oc-pf-ext:tx-bias-lane-4?                     string
        |  |  +--ro oc-pf-ext:tx-bias-lane-5?                     string
        |  |  +--ro oc-pf-ext:tx-bias-lane-6?                     string
        |  |  +--ro oc-pf-ext:tx-bias-lane-7?                     string
        |  |  +--ro oc-pf-ext:tx-bias-lane-8?                     string

```
### 3.5.3 Configuration Commands

The config commands uses Klish framework. It requires Klish to be in configuration mode (configure terminal).

#### Configure Loopback Controls

```
[no] interface transceiver diagnostics loopback {media-side-input|media-side-output|host-side-input|host-side-output} Ethernet <1|2..N>
```

This command enables the the loopback test mode of the transceiver.  Please use the test point diagram at the top of this document to reference which part of the transceiver and fiber will be tested according to the loopback modes described below. The interface must first be enabled before configuring a loopback mode.

- **Media Side Output Loopback:** Loopback testing will be supported at TP 2.
- **Media Side Input Loopback:** Loopback testing will be performed at TP3 to TP2.
- **Host Side Output Loopback:** Loopback testing will be performed at TP 1a to TP4.
- **Host Side Input Loopback:** Loopback testing will be performed at TP1a.

Use no form of the command to disable loopback mode:

```
sonic-cli(config)# interface transceiver diagnostics loopback media-side-input Ethernet 112
sonic-cli(config)# no interface transceiver diagnostics loopback media-side-input Ethernet 112
```

### 3.5.4 CLI Show Commands

#### Show Loopback Capability

```
show interface transceiver diagnostics capability Ethernet <1|2|..N>
```

This command shows diagnostics capability of the transceiver. If no specific transceiver is provided, all CMIS diagnostic supported transceivers are displayed with associated loopback capabilities.

Example output:

    sonic# show interface transceiver diagnostics capability Ethernet 112
    
    Ethernet112
    Loopback Support
    ----------------------------------------------------------
    Media Side Input Loopback                 Supported
    Media Side Output Loopback                Not Supported
    Host Side Input Loopback                  Supported
    Host Side Output Loopback                 Not Supported
    Per-lane Host Side Loopback               Not Supported
    Per-lane Media Side Loopback              Not Supported
    Simultaneous Host/Media Side Loopback     Not Supported


#### Show Diagnostics Control Status

```
show interface transceiver diagnostics status Ethernet <1|2|..N>
```

This command shows the loopback diagnostic controls of the transceiver. If no specific transceiver is provided, all CMIS diagnostic supported transceivers are displayed with associated control settings.

Example output:

```
sonic# show interface transceiver diagnostics status Ethernet 112

Ethernet112
Control Status                                  
--------------------------------------------------------
Host Side Input Loopback                Enabled
Media Side Input Loopback               Disabled

```

#### Show Interface Operational Parameters

```
show interface transceiver diagnostics params Ethernet <1|2|..N>
```

This command shows the operational parameters of the transceiver. If no specific transceiver is provided, the operational parameters of all CMIS supported transceivers are displayed.

Example output:

```
sonic# show interface transceiver diagnostics params Ethernet 112
 
Ethernet112
---------------------------------------------------------------------
Attribute                :  Value/State
---------------------------------------------------------------------
LOL1                     :  Deasserted
LOL2                     :  Deasserted
LOL3                     :  Deasserted
LOL4                     :  Deasserted
LOL5                     :  Deasserted
LOL6                     :  Deasserted
LOL7                     :  Deasserted
LOL8                     :  Deasserted
LOS1                     :  Deasserted
LOS2                     :  Deasserted
LOS3                     :  Deasserted
LOS4                     :  Deasserted
LOS5                     :  Deasserted
LOS6                     :  Deasserted
LOS7                     :  Deasserted
LOS8                     :  Deasserted
RX1Power                 :  3.09991928781dBm
RX2Power                 :  2.61999948652dBm
RX3Power                 :  1.86589091272dBm
RX4Power                 :  2.0468962042dBm
RX5Power                 :  No Power
RX6Power                 :  No Power
RX7Power                 :  No Power
RX8Power                 :  No Power
Temperature              :  44.08203125C
TX1Bias                  :  26mA
TX2Bias                  :  28mA
TX3Bias                  :  27mA
TX4Bias                  :  21mA
TX5Bias                  :  0mA
TX6Bias                  :  0mA
TX7Bias                  :  0mA
TX8Bias                  :  0mA
TX1Power                 :  3.15445518225dBm
TX2Power                 :  2.62735779666dBm
TX3Power                 :  2.57198426139dBm
TX4Power                 :  3.06017350084dBm
TX5Power                 :  No Power
TX6Power                 :  No Power
TX7Power                 :  No Power
TX8Power                 :  No Power
Voltage                  :  3.2576Volts

```

#### 

  ### 3.5.4 Debug Commands

  No debug commands are planned for at this time.

  ### 3.5.5 REST API Support

  This is supported by default with the new management framework.

  # 4 Flow Diagrams

None



  # 5 Serviceability and Debug

The CMIS 4.0 diagnostic feature and does not require serviceability or debug capability.

  # 6 Warm Boot Support

The CMIS 4.0 diagnostic feature does not impact warm boot.   

  # 7 Scalability

The CMIS 4.0 diagnostic feature is fully scalable on a SONiC platform and is supported on any port / transceiver which supports the CMIS 4.0 diagnostic features described in this specification.

# 8 Unit Test

The following is the list of unit test cases:  
* Verify CMIS LOS state shows appropriate status when link is up or down (Loss Of Signal = True when link is down/disabled)
* Verify CMIS Voltage is in the normal operating range above 3 vots.
* Verify CMIS diagnostics CLI config commands.
* Verify CMIS diagnostics CLI show commands.
* Verify that CMIS diagnostics is disabled by default after installation.
* Verify CMIS 4.0 transceiver which supports loopback test works properly when configured in loopback mode.

# 9 Internal Design Information
There are no internal design changes relative to the CMIS 4.0 diagnostic support in SONiC.  Design architecture is in accordance with the SONiC design framework.