# SONiC Cable Diagnostic Support

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
  * [Warm Boot Support](#6-Warm-Boot-Support)
  * [Scalability](#7-Scalability)
  * [Unit Test](#8-Unit-Test)

# List of Tables

[Table 1: References](#table-1-references)<br>
[Table 2: Abbreviations](#table-2-abbreviations)

# Revision

| Rev |     Date    |       Author        | Change Description                 |
|:---:|:-----------:|:-------------------:|:-----------------------------------|
| 0.1 | 04/15/2021  | Dante (Kuo-Jung) Su | Initial version                    |

# About this Manual

This document provides functional requirements and high-level design for providing cable diagnostic support in SONiC.

#  References

##  Table 1: References

|       **Document**           | **Location**   |
| ---------------------------- | -------------- |
| Cisco Document ID:SMB5560    | https://www.cisco.com/c/en/us/support/docs/smb/switches/cisco-350-series-managed-switches/smb5560-ipsec-connection-status-on-rv180-and-rv180w-routers.html |
| Marvell Virtual Cable Tester | https://www.teklib.com/library/vct-marvell-virtual-cable-tester-technology-for-gigabit-networks-white-paper/ |

# Scope

This document describes the high level design of cable diagnostic support in SONiC.

# Definition/Abbreviation

## Table 2: Abbreviations

| **Term**     | **Meaning**                                                  |
| ------------ | ------------------------------------------------------------ |
| CDR          | Optical clock and data recovery circuit                      |
| QSFP         | Quad Small Form-factor Pluggable                             |
| RX_LOL       | Rx CDR Loss of Lock                                          |
| RX_LOS       | Rx Loss of signal                                            |
| SFP          | Small form-factor pluggable transceiver                      |
| TDR          | Time Domain Reflectometry                                    |
| TX_DISABLE   | Tx power disabled                                            |
| TX_FAULT     | Tx fault detected                                            |
| TX_LOL       | Tx CDR Loss of Lock                                          |
| TX_LOS       | Tx Loss of signal                                            |
| VCT          | Marvell Virtual Cable Tester                                 |

# 1 Requirements Overview

In 2002, in order to ease and facilitate the rapid deployment of Gigabit Ethernet,
Marvell Technology Group introduced an innovative new feature for its Alaska® family
of Gigabit Ethernet Physical Layer (PHY) devices, called Virtual Cable Tester™ (VCT)
technology, which is well-received and widely delopyed in the enterprise switches.

Nowadays, most of the enterprise switch silicon vendors also provide similar features
to their own Gigabit Ethernet Physical Layer (PHY) devices, and when transition from
copper to fiber, the VCT is also massively integrated into the copper SFPs (i.e. 1000BASE-T),
and hence, it's still commonly available on these devices.

From a SONiC perspective, the addition of this diagnostic feature will enhance
platform development, user deployment, platform support, and serviceability.
In addition to the copper cable diagnostics, we'll also introduce optical transceiver diagnostics
into SONIC to help users easily identify the cause of link failure.

## 1.1 Functional Requirements

The functional requirements for adding cable diagnostics support in SONiC are:

1. Ability to activate the cable/transceiver diagnostics test
2. Display cable/transceiver diagnostics test report
3. Display copper cable length or maximum cable length supported by the optical transceiver


## 1.2 Configuration and Management Requirements

1. The cable diagnostic support has to be managed using the SONiC management infrastructure.
2. The cable diagnostic is port/transceiver specific, and the test result should not persistent across reboot.
3. In the case of SFP/QSFP transceivers, the diagnostic result should be cleared upon module removal.

# 2 Functionality

## 2.1 Target Deployment Use Cases

Cable diagnostic support would be idea for initial switch deployment, diagnosing link/connection failures
within the network topology, and ensuring link quality.



# 3 Design
## 3.1 Overview

Cable diagnostic support can be divided into the following components:

### 3.1.1 Transceiver Daemon

The transceiver daemon (**xcvrd**) is part of the SONIC platform monitor(**pmon**), and it is responsible
for posting/deleting the transceiver information upon transceiver insertion/removal, and it will be furtherly 
enhanced for cable diagnostic support with the per port parameters in **STATE_DB**.

### 3.1.2 SONiC Management Framework
Management framework is a SONiC application which is responsible for providing various common 
North Bound Interfaces (NBIs) for the purposes of managing configuration and status on SONiC switches. 
The application manages coordination of NBI’s to provide a coherent way to validate, apply and show 
configuration. And the cable diagnostic support will only be availble in this user interface.

## 3.2 Database

### 3.2.1 STATE_DB

The **CABLE_DIAG** table will be newly introduced into STATE_DB for the cable diagnostics.
```
  "CABLE_DIAG|Ethernet0": {
    "type": "hash", 
    "value": {
      "scheduled": "false", 
      "type": "QSFP-DD", 
      "vendor_name": "AVAGO", 
      "part_number": "AFCT-93DRPHZ-AZ2", 
      "length": "100m",
      "result": "OK",
      "timestamp": "04/15/2021 00:00:00"
    }
  },
```

## 3.3 Data Model

### 3.3.1 openconfig-platform-diagnostics.yang

There is currently no yang model defined for cable diagnostics. Therefore, the openconfig-platform-diagnostics will be created.

The YANG tree will look as below:
```
module: openconfig-platform-diagnostics
  +--rw cable-diagnostics
     +--rw cable-diagnostics-info* [ifname]
        +--rw ifname    -> ../config/ifname
        +--rw config
        |  +--rw ifname?      string
        |  +--rw scheduled?   boolean
        +--ro state
           +--ro ifname?        -> ../../config/ifname
           +--ro scheduled?     boolean
           +--ro type?          string
           +--ro vendor-name?   string
           +--ro part-number?   string
           +--ro result?        string
           +--ro length?        string
           +--ro timestamp?     string
```

### 3.3.2 Configuration Commands

None of config commands is necessary for cable diagnostics, as the test operation and report
should not be activated or persistent across reboot, and we'll use 'test' command instead.

#### Activate the cable diagnostics test

```
sonic-cli# test cable-diagnostics [interface Ethernet <Port-ID>]
```
- interface: (Optional) Specifies an Ethernet port ID.

This command activates the cable-diagnostics test on the specified transceivers.

### 3.3.3 CLI Show Commands

#### Show cable-diagnostics test report

```
sonic-cli# show cable-diagnostics report [interface Ethernet <Port-ID>]
```
- interface: (Optional) Specifies an Ethernet port ID.

This command shows diagnostics test port of the transceiver. If no specific transceiver is provided,
all diagnostic reports are displayed.

Example output:

```
sonic# show cable-diagnostics report | no-more

Interface     Type      Length        Result        Timestamp
------------  --------  ------------  ------------  -------------------
Ethernet0     SFP       3m            N/A           15-Apr-2021 06:38:11
Ethernet1     SFP       3m            N/A           15-Apr-2021 06:38:11
Ethernet2     SFP       3m            N/A           15-Apr-2021 06:38:11
Ethernet3     SFP       3m            N/A           15-Apr-2021 06:38:11
Ethernet4     SFP       10000m        N/A           15-Apr-2021 06:38:11
Ethernet5     SFP       80m           RX_LOS        15-Apr-2021 06:38:11
Ethernet6     SFP       80m           RX_LOS        15-Apr-2021 06:38:11
Ethernet7     SFP       300m          RX_LOS        15-Apr-2021 06:38:11
Ethernet8     SFP       10000m        N/A           15-Apr-2021 06:38:11
Ethernet9     SFP       10000m        N/A           15-Apr-2021 06:38:11
Ethernet10    SFP       80m           RX_LOS        15-Apr-2021 06:38:11
Ethernet11    SFP       80m           RX_LOS        15-Apr-2021 06:38:11
Ethernet12    SFP       80m           RX_LOS        15-Apr-2021 06:38:11
Ethernet13    N/A       N/A           N/A           15-Apr-2021 06:38:11
Ethernet14    SFP       300m          RX_LOS        15-Apr-2021 06:38:11
Ethernet15    N/A       N/A           N/A           15-Apr-2021 06:38:11
Ethernet16    SFP       100m          OPEN (pair A) 15-Apr-2021 06:38:16
Ethernet17    N/A       N/A           N/A           15-Apr-2021 06:38:16
Ethernet18                            In-Progress
Ethernet19                            In-Progress
Ethernet20                            In-Progress
Ethernet21                            In-Progress
--more--
sonic# show cable-diagnostics report | no-more

Interface     Type      Length        Result        Timestamp
------------  --------  ------------  ------------  -------------------
Ethernet0     SFP       3m            N/A           15-Apr-2021 06:38:11
Ethernet1     SFP       3m            N/A           15-Apr-2021 06:38:11
Ethernet2     SFP       3m            N/A           15-Apr-2021 06:38:11
Ethernet3     SFP       3m            N/A           15-Apr-2021 06:38:11
Ethernet4     SFP       10000m        N/A           15-Apr-2021 06:38:11
Ethernet5     SFP       80m           RX_LOS        15-Apr-2021 06:38:11
Ethernet6     SFP       80m           RX_LOS        15-Apr-2021 06:38:11
Ethernet7     SFP       300m          RX_LOS        15-Apr-2021 06:38:11
Ethernet8     SFP       10000m        N/A           15-Apr-2021 06:38:11
Ethernet9     SFP       10000m        N/A           15-Apr-2021 06:38:11
Ethernet10    SFP       80m           RX_LOS        15-Apr-2021 06:38:11
Ethernet11    SFP       80m           RX_LOS        15-Apr-2021 06:38:11
Ethernet12    SFP       80m           RX_LOS        15-Apr-2021 06:38:11
Ethernet13    N/A       N/A           N/A           15-Apr-2021 06:38:11
Ethernet14    SFP       300m          RX_LOS        15-Apr-2021 06:38:11
Ethernet15    N/A       N/A           N/A           15-Apr-2021 06:38:11
Ethernet16    SFP       100m          OPEN (pair A) 15-Apr-2021 06:38:16
Ethernet17    N/A       N/A           N/A           15-Apr-2021 06:38:16
Ethernet18    SFP       100m          OPEN (pair A) 15-Apr-2021 06:38:20
Ethernet19    N/A       N/A           N/A           15-Apr-2021 06:38:20
Ethernet20    SFP       100m          OPEN (pair A) 15-Apr-2021 06:38:24
Ethernet21    N/A       N/A           N/A           15-Apr-2021 06:38:24
Ethernet22    SFP       100m          OPEN (pair A) 15-Apr-2021 06:38:29
Ethernet23    N/A       N/A           N/A           15-Apr-2021 06:38:29
Ethernet24    N/A       N/A           N/A           15-Apr-2021 06:38:29
Ethernet25    N/A       N/A           N/A           15-Apr-2021 06:38:29
Ethernet26    SFP       100m          OPEN (pair A) 15-Apr-2021 06:38:33
Ethernet27    N/A       N/A           N/A           15-Apr-2021 06:38:33
Ethernet28    SFP       < 50m         OK            15-Apr-2021 06:38:47
Ethernet29    N/A       N/A           N/A           15-Apr-2021 06:38:47
Ethernet30    SFP       < 50m         OK            15-Apr-2021 06:39:15
Ethernet31    N/A       N/A           N/A           15-Apr-2021 06:39:15
Ethernet32    SFP       300m          OK            15-Apr-2021 06:39:15
Ethernet33    N/A       N/A           N/A           15-Apr-2021 06:39:15
Ethernet34    SFP       300m          OK            15-Apr-2021 06:39:15
Ethernet35    N/A       N/A           N/A           15-Apr-2021 06:39:15
Ethernet36    SFP       80m           OK            15-Apr-2021 06:39:15
Ethernet37    N/A       N/A           N/A           15-Apr-2021 06:39:15
Ethernet38    SFP       80m           OK            15-Apr-2021 06:39:15
Ethernet39    N/A       N/A           N/A           15-Apr-2021 06:39:15
Ethernet40    N/A       N/A           N/A           15-Apr-2021 06:39:15
Ethernet41    N/A       N/A           N/A           15-Apr-2021 06:39:15
Ethernet42    N/A       N/A           N/A           15-Apr-2021 06:39:15
Ethernet43    N/A       N/A           N/A           15-Apr-2021 06:39:15
Ethernet44    SFP       1m            N/A           15-Apr-2021 06:39:15
Ethernet45    SFP       1m            N/A           15-Apr-2021 06:39:15
Ethernet46    SFP       1m            N/A           15-Apr-2021 06:39:15
Ethernet47    SFP       1m            N/A           15-Apr-2021 06:39:15
Ethernet48    QSFP28    2000m         RX_LOS (lane 1) 15-Apr-2021 06:39:16
Ethernet52    N/A       N/A           N/A           15-Apr-2021 06:39:16
Ethernet56    QSFP+     100m          OK            15-Apr-2021 06:39:16
Ethernet60    N/A       N/A           N/A           15-Apr-2021 06:39:16
Ethernet64    QSFP+     100m          OK            15-Apr-2021 06:39:16
Ethernet68    N/A       N/A           N/A           15-Apr-2021 06:39:16
Ethernet72    QSFP28    3m            N/A           15-Apr-2021 06:39:16
Ethernet76    QSFP+     1m            N/A           15-Apr-2021 06:39:16
```

#### Show cable-diagnostics cable-length

```
sonic-cli# show cable-diagnostics cable-length [interface Ethernet <Port-ID>]
```
- interface: (Optional) Specifies an Ethernet port ID.

This command shows cable-length of the transceiver. If no specific transceiver is provided,
all cable-length are displayed.

Example output:

```
sonic# show cable-diagnostics cable-length

Interface     Type      Length        Timestamp
------------  --------  ------------  -------------------
Eth1/1
Eth1/2        SFP       3m            15-Apr-2021 06:38:11
Eth1/3
Eth1/4        SFP       3m            15-Apr-2021 06:38:11
Eth1/5        SFP       10000m        15-Apr-2021 06:38:11
Eth1/6        SFP       80m           15-Apr-2021 06:38:11
Eth1/7        SFP       80m           15-Apr-2021 06:38:11
Eth1/8        SFP       300m          15-Apr-2021 06:38:11
Eth1/9        SFP       10000m        15-Apr-2021 06:38:11
Eth1/10       SFP       10000m        15-Apr-2021 06:38:11
Eth1/11       SFP       80m           15-Apr-2021 06:38:11
Eth1/12       SFP       80m           15-Apr-2021 06:38:11
Eth1/13       SFP       80m           15-Apr-2021 06:38:11
Eth1/14       N/A       N/A           15-Apr-2021 06:38:11
Eth1/15       SFP       300m          15-Apr-2021 06:38:11
Eth1/16       N/A       N/A           15-Apr-2021 06:38:11
Eth1/17       SFP       100m          15-Apr-2021 06:38:16
Eth1/18       N/A       N/A           15-Apr-2021 06:38:16
Eth1/19       SFP       100m          15-Apr-2021 06:38:20
Eth1/20       N/A       N/A           15-Apr-2021 06:38:20
Eth1/21       SFP       100m          15-Apr-2021 06:38:24
--more--
```

### 3.3.4 Debug Commands

No debug commands are planned for at this time.

### 3.3.5 REST API Support

This is supported by default with the new management framework.

# 4 Flow Diagrams

None

# 5 Serviceability and Debug

The cable-diagnostics feature and does not require serviceability or debug capability.

# 6 Warm Boot Support

The cable-diagnostics feature does not impact warm boot.

# 7 Scalability

The cable-diagnostics feature is fully scalable on a SONiC platform and is supported on any port/transceiver.

# 8 Unit Test

The following is the list of unit test cases:
* Verify cable-diagnostics CLI test commands.
* Verify cable-diagnostics CLI show commands.

# 9 Internal Design Information
There are no internal design changes relative to the cable-diagnostic support in SONiC. Design architecture
is in accordance with the SONiC design framework.
