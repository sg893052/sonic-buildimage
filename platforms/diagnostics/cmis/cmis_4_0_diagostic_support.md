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
| 0.5 | 1/29/2021 | Dante (Kuo-Jung) Su | 1. Drop the dependency to the SONiC community pull request for the enhanced media support<br>2. Migrate the database from APPL_DB to CONFIG_DB<br>3. Added PRBS support<br>4. Updated the command syntax
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
| host-side    | Refers to the internal connection between the switching silicon (MAC) and the transceiver |
| media-side   | Refers to the external connection between the transceiver and the remote connected device |
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
line-side issues and help isolate failing hardware at various test-points.

![tp_diagram.jpg](images/tp_diagram.jpg)

## 1.1 Functional Requirements

The functional requirements for adding CMIS 4.0 diagnostic support in SONiC are:

1. Display loopback capabilities.
2. Display general pattern capabilities.
3. Display diagnostic reporting capabilities.
4. Display pattern generation and checking capabilities.
5. Display error information (e.g. SNR, BER)
6. Ability to configure / enable the transceiver loopback modes
7. Ability to configure / enable the transceiver pattern generation and checking


## 1.2 Configuration and Management Requirements

1. The CMIS 4.0 diagnostic support has to be managed using the SONiC management infrastructure.
2. The CMIS 4.0 diagnostic loopback should be disabled by default but persistent across reboot. Traffic can be run across the port while in diagnostic mode.


# 2 Functionality

## 2.1 Target Deployment Use Cases

CMIS 4.0 diagnostic support would be ideal for initial switch deployment, diagnosing link/connection failures within the network topology, and ensuring link quality.



# 3 Design
## 3.1 Overview

CMIS 4.0 diagnostic support can be divided into the following components:

### 3.1.1 Transceiver Daemon

The transceiver daemon (**xcvrd**) is part of the SONIC platform monitor(**pmon**), and it is responsible for posting/deleting the transceiver information upon transceiver insertion/removal, and it will be furtherly enhanced for CMIS 4.0 diagnostic support with the per port parameters in **CONFIG_DB** and **STATE_DB**.

### 3.1.2 SONiC Management Framework
Management framework is a SONiC application which is responsible for providing various common North Bound Interfaces (NBIs) for the purposes of managing configuration and status on SONiC switches. The application manages coordination of NBI’s to provide a coherent way to validate, apply and show configuration. And the CMIS 4.0 diagnostic support will only be available in this user interface.

## 3.2 Database

### 3.2.1 CONFIG_DB

The **TRANSCEIVER_DIAG** table will be newly introduced into CONFIG_DB for the diagnostic configurations.
```
  "TRANSCEIVER_DIAG|Ethernet0": {
    "type": "hash",
    "value": {
      "lb_host_input_enabled": "false",
      "lb_host_output_enabled": "false",
      "lb_media_input_enabled": "false",
      "lb_media_output_enabled": "false",
      "prbs_chk_host_enabled": "false",
      "prbs_chk_media_enabled": "false",
      "prbs_gen_host_enabled": "false",
      "prbs_gen_media_enabled": "false"
    }
  }
```


### 3.2.2 STATE_DB

The **TRANSCEIVER_INFO** table will have the following additional entries if the transceiver inserted is CMIS 4.0 compliant:

```
  "TRANSCEIVER_INFO|Ethernet0": {
    "type": "hash",
    "value": {
      "diag_caps_loopback": "['Host Side Input Loopback', 'Media Side Input Loopback']",
      "diag_caps_pattern": "['Gating > 20 ms', 'Latched Error Information', 'Real-time BER Error Count', 'Auto Restart']",
      "diag_caps_pattern_chk_host": "['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']",
      "diag_caps_pattern_chk_media": "['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']",
      "diag_caps_pattern_gen_host": "['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']",
      "diag_caps_pattern_gen_media": "['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']",
      "diag_caps_report": "['Media side SNR measurement', 'Host side SNR measurement', 'BER Error Count/Total Bits', 'BER register']",
    }
  }
```

The **TRANSCEIVER_DIAG** table will be newly introduced if the transceiver inserted is CMIS 4.0 compliant:
```
  "TRANSCEIVER_DIAG|Ethernet160": {
    "type": "hash", 
    "value": {
      "diag_host_ber1": "5.0E-1", 
      "diag_host_ber2": "5.0E-1", 
      "diag_host_ber3": "5.0E-1", 
      "diag_host_ber4": "5.0E-1", 
      "diag_host_ber5": "5.0E-1", 
      "diag_host_ber6": "5.0E-1", 
      "diag_host_ber7": "5.0E-1", 
      "diag_host_ber8": "5.0E-1", 
      "diag_host_snr1": "0", 
      "diag_host_snr2": "0", 
      "diag_host_snr3": "0", 
      "diag_host_snr4": "0", 
      "diag_host_snr5": "0", 
      "diag_host_snr6": "0", 
      "diag_host_snr7": "0", 
      "diag_host_snr8": "0", 
      "diag_media_ber1": "5.0E-1", 
      "diag_media_ber2": "5.0E-1", 
      "diag_media_ber3": "5.0E-1", 
      "diag_media_ber4": "5.0E-1", 
      "diag_media_ber5": "0", 
      "diag_media_ber6": "0", 
      "diag_media_ber7": "0", 
      "diag_media_ber8": "0", 
      "diag_media_snr1": "79.5", 
      "diag_media_snr2": "79.5", 
      "diag_media_snr3": "79.5", 
      "diag_media_snr4": "79.5", 
      "diag_media_snr5": "0", 
      "diag_media_snr6": "0", 
      "diag_media_snr7": "0", 
      "diag_media_snr8": "0",
      "lb_host_input_enabled": "true",
      "lb_media_input_enabled": "true"
    }
  },
```


## 3.3 Data Model

### 3.4.1 openconfig-platform-ext.yang

There is currently no yang model defined for transceiver diagnostics. Therefore, the openconfig-platform/transceiver will be augmented. 

The YANG tree will look as below:
```
module: openconfig-platform
  +--rw components
     +--rw component* [name]
        +--rw oc-transceiver:transceiver
        |  +--rw oc-transceiver-ext:diagnostics
        |     +--rw oc-transceiver-ext:capabilities
        |     |  +--ro oc-transceiver-ext:state
        |     |     +--ro oc-transceiver-ext:loopback?            string
        |     |     +--ro oc-transceiver-ext:pattern?             string
        |     |     +--ro oc-transceiver-ext:report?              string
        |     |     +--ro oc-transceiver-ext:pattern-gen-host?    string
        |     |     +--ro oc-transceiver-ext:pattern-gen-media?   string
        |     |     +--ro oc-transceiver-ext:pattern-chk-host?    string
        |     |     +--ro oc-transceiver-ext:pattern-chk-media?   string
        |     +--rw oc-transceiver-ext:loopbacks
        |     |  +--rw oc-transceiver-ext:config
        |     |  |  +--rw oc-transceiver-ext:lb-host-input-enabled?     boolean
        |     |  |  +--rw oc-transceiver-ext:lb-host-output-enabled?    boolean
        |     |  |  +--rw oc-transceiver-ext:lb-media-input-enabled?    boolean
        |     |  |  +--rw oc-transceiver-ext:lb-media-output-enabled?   boolean
        |     |  +--ro oc-transceiver-ext:state
        |     |     +--ro oc-transceiver-ext:lb-host-input-enabled?     boolean
        |     |     +--ro oc-transceiver-ext:lb-host-output-enabled?    boolean
        |     |     +--ro oc-transceiver-ext:lb-media-input-enabled?    boolean
        |     |     +--ro oc-transceiver-ext:lb-media-output-enabled?   boolean
        |     +--rw oc-transceiver-ext:patterns
        |     |  +--rw oc-transceiver-ext:config
        |     |  |  +--rw oc-transceiver-ext:pattern-gen-host-enabled?    boolean
        |     |  |  +--rw oc-transceiver-ext:pattern-gen-media-enabled?   boolean
        |     |  |  +--rw oc-transceiver-ext:pattern-chk-host-enabled?    boolean
        |     |  |  +--rw oc-transceiver-ext:pattern-chk-media-enabled?   boolean
        |     |  +--ro oc-transceiver-ext:state
        |     |     +--ro oc-transceiver-ext:pattern-gen-host-enabled?    boolean
        |     |     +--ro oc-transceiver-ext:pattern-gen-media-enabled?   boolean
        |     |     +--ro oc-transceiver-ext:pattern-chk-host-enabled?    boolean
        |     |     +--ro oc-transceiver-ext:pattern-chk-media-enabled?   boolean
        |     +--rw oc-transceiver-ext:reports
        |        +--rw oc-transceiver-ext:host
        |        |  +--ro oc-transceiver-ext:state
        |        |     +--ro oc-transceiver-ext:ber1?   string
        |        |     +--ro oc-transceiver-ext:ber2?   string
        |        |     +--ro oc-transceiver-ext:ber3?   string
        |        |     +--ro oc-transceiver-ext:ber4?   string
        |        |     +--ro oc-transceiver-ext:ber5?   string
        |        |     +--ro oc-transceiver-ext:ber6?   string
        |        |     +--ro oc-transceiver-ext:ber7?   string
        |        |     +--ro oc-transceiver-ext:ber8?   string
        |        |     +--ro oc-transceiver-ext:snr1?   string
        |        |     +--ro oc-transceiver-ext:snr2?   string
        |        |     +--ro oc-transceiver-ext:snr3?   string
        |        |     +--ro oc-transceiver-ext:snr4?   string
        |        |     +--ro oc-transceiver-ext:snr5?   string
        |        |     +--ro oc-transceiver-ext:snr6?   string
        |        |     +--ro oc-transceiver-ext:snr7?   string
        |        |     +--ro oc-transceiver-ext:snr8?   string
        |        +--rw oc-transceiver-ext:media
        |           +--ro oc-transceiver-ext:state
        |              +--ro oc-transceiver-ext:ber1?   string
        |              +--ro oc-transceiver-ext:ber2?   string
        |              +--ro oc-transceiver-ext:ber3?   string
        |              +--ro oc-transceiver-ext:ber4?   string
        |              +--ro oc-transceiver-ext:ber5?   string
        |              +--ro oc-transceiver-ext:ber6?   string
        |              +--ro oc-transceiver-ext:ber7?   string
        |              +--ro oc-transceiver-ext:ber8?   string
        |              +--ro oc-transceiver-ext:snr1?   string
        |              +--ro oc-transceiver-ext:snr2?   string
        |              +--ro oc-transceiver-ext:snr3?   string
        |              +--ro oc-transceiver-ext:snr4?   string
        |              +--ro oc-transceiver-ext:snr5?   string
        |              +--ro oc-transceiver-ext:snr6?   string
        |              +--ro oc-transceiver-ext:snr7?   string
        |              +--ro oc-transceiver-ext:snr8?   string
```

### 3.5.3 Configuration Commands

The config commands uses Klish framework. It requires Klish to be in configuration mode (configure terminal).

#### Configure Loopback Controls

```
[no] interface transceiver diagnostics loopback {media-side-input|media-side-output|host-side-input|host-side-output} Ethernet <1|2..N>
```

This command enables the the loopback test mode of the transceiver.

|Loopback Type             |Illustraction                                 |
|--------------------------|----------------------------------------------|
|Media Side Output Loopback|![lb-media-output](images/lb-media-output.png)|
|Media Side Input Loopback |![lb-media-input](images/lb-media-input.png)  |
|Host Side Output Loopback |![lb-host-output](images/lb-host-output.png)  |
|Host Side Input Loopback  |![lb-host-input](images/lb-host-input.png)    |

Use no form of the command to disable:

```
sonic-cli(config)# interface transceiver diagnostics loopback media-side-input Ethernet 112
sonic-cli(config)# no interface transceiver diagnostics loopback media-side-input Ethernet 112
```

#### Configure Pattern Generation and Checking Controls

```
[no] interface transceiver diagnostics pattern {checker-host|checker-media|generator-host|generator-media} Ethernet <1|2..N>
```

This command enables the pattern generation and checking mode.

Use no form of the command to disable:

```
sonic-cli(config)# interface transceiver diagnostics pattern checker-host Ethernet 0
sonic-cli(config)# no interface transceiver diagnostics pattern checker-host Ethernet 0
```

### 3.5.4 CLI Show Commands

#### Show Loopback Capability

```
show interface transceiver diagnostics capability Ethernet <1|2|..N>
```

This command shows diagnostics capability of the transceiver. If no specific transceiver is provided, all CMIS diagnostic supported transceivers are displayed with associated loopback capabilities.

Example output:

```
sonic# show interface transceiver diagnostics capability Ethernet 0 | no-more

--------------------------------------------------------
Ethernet0
--------------------------------------------------------
Loopback Capabilities
    Host Side Input Loopback                    :  Supported
    Host Side Output Loopback                   :  Not Supported
    Media Side Input Loopback                   :  Supported
    Media Side Output Loopback                  :  Not Supported
    Per-lane Host Side Loopback                 :  Not Supported
    Per-lane Media Side Loopback                :  Not Supported
    Simultaneous Host and Media Side Loopback   :  Not Supported

General Pattern Capabilities
    Auto Restart                                :  Supported
    Gating                                      :  Supported
    Latched Error Information                   :  Supported
    Per-lane Gating Timer                       :  Not Supported
    Real-time BER Error Count                   :  Supported

Diagnostic Report Capabilities
    BER Error Count/Total Bits                  :  Supported
    BER register                                :  Supported
    Host side FEC                               :  Not Supported
    Host side Input Peak                        :  Not Supported
    Host side SNR                               :  Supported
    Media side FEC                              :  Not Supported
    Media side Input Peak                       :  Not Supported
    Media side SNR                              :  Supported

Pattern Generation and Checking Capabilities
    Host Side Pattern Checker                   :  ['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']
    Host Side Pattern Generator                 :  ['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']
    Media Side Pattern Checker                  :  ['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']
    Media Side Pattern Generator                :  ['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']
sonic#
```

#### Show Diagnostics Control Status

```
show interface transceiver diagnostics status Ethernet <1|2|..N>
```

This command shows the loopback diagnostic controls of the transceiver. If no specific transceiver is provided, all CMIS diagnostic supported transceivers are displayed with associated control settings.

Example output:

```
sonic# show interface transceiver diagnostics status Ethernet 0 | no-more

Ethernet0
---------------------------------------------------------------------
Attribute                           :  Value/State
---------------------------------------------------------------------
Loopback
    Host Side Input                 :  Enable  ( Enable in Config )
    Host Side Output                :  Disable ( Enable in Config )
    Media Side Input                :  Disable ( Disable in Config )
    Media Side Output               :  Disable ( Disable in Config )

Pattern Generation and Checking
    Host Side Pattern Checker       :  Disable ( Disable in Config )
    Host Side Pattern Generator     :  Disable ( Disable in Config )
    Media Side Pattern Checker      :  Disable ( Disable in Config )
    Media Side Pattern Generator    :  Disable ( Disable in Config )

Error Information
    Host side BER1                  : 5.0E-1
    Host side BER2                  : 5.0E-1
    Host side BER3                  : 5.0E-1
    Host side BER4                  : 5.0E-1
    Host side BER5                  : 5.0E-1
    Host side BER6                  : 5.0E-1
    Host side BER7                  : 5.0E-1
    Host side BER8                  : 5.0E-1
    Media side BER1                 : 5.0E-1
    Media side BER2                 : 5.0E-1
    Media side BER3                 : 5.0E-1
    Media side BER4                 : 5.0E-1
    Media side BER5                 : 0
    Media side BER6                 : 0
    Media side BER7                 : 0
    Media side BER8                 : 0

    Host side SNR1                  : 0
    Host side SNR2                  : 0
    Host side SNR3                  : 0
    Host side SNR4                  : 0
    Host side SNR5                  : 0
    Host side SNR6                  : 0
    Host side SNR7                  : 0
    Host side SNR8                  : 0
    Media side SNR1                 : 10.6
    Media side SNR2                 : 10.6
    Media side SNR3                 : 79.5
    Media side SNR4                 : 79.5
    Media side SNR5                 : 0
    Media side SNR6                 : 0
    Media side SNR7                 : 0
    Media side SNR8                 : 0
sonic#
```

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
* test_cmis_show_diag_capability: Validate the capability output
* test_cmis_show_diag_status: Validate the status output
* test_cmis_config_diag_loopback_host_input: Verify CMIS diagnostics host-side-input-loopback configuration
* test_cmis_config_diag_loopback_host_output: Verify CMIS diagnostics host-side-output-loopback configuration
* test_cmis_config_diag_loopback_media_input: Verify CMIS diagnostics media-side-input-loopback configuration
* test_cmis_config_diag_loopback_media_output: Verify CMIS diagnostics media-side-output-loopback configuration
* test_cmis_config_diag_pattern_checker_host: Verify CMIS diagnostics host-side-pattern-checker configuration
* test_cmis_config_diag_pattern_checker_media: Verify CMIS diagnostics media-side-pattern-checker configuration
* test_cmis_config_diag_pattern_generator_host: Verify CMIS diagnostics host-side-pattern-generator configuration
* test_cmis_config_diag_pattern_generator_media: Verify CMIS diagnostics media-side-pattern-generator configuration

# 9 Internal Design Information
There are no internal design changes relative to the CMIS 4.0 diagnostic support in SONiC.  Design architecture is in accordance with the SONiC design framework.