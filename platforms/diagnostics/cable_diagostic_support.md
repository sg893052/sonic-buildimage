# SONiC Cable-Diagnostics Support

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

A time-domain reflectometer (TDR) is an electronic instrument used to determine 
the characteristics of electrical lines by observing reflected waveforms.

In 2002, in order to ease and facilitate the rapid deployment of Gigabit Ethernet,
Marvell Technology Group introduced an innovative new feature for its Alaska® family
of Gigabit Ethernet Physical Layer (PHY) devices, called Virtual Cable Tester™ (VCT)
technology, which is built on top of TDR, and is well-received and widely delopyed
in the enterprise switches.

Nowadays, most of the enterprise switch silicon vendors also provide similar features
to their own Gigabit Ethernet Physical Layer (PHY) devices, and when transition from
copper to fiber, the VCT is also massively integrated into the copper SFPs (i.e. 1000BASE-T),
and hence, it's still commonly available on these devices.

From a SONiC perspective, the addition of this diagnostic feature will enhance
platform development, user deployment, platform support, and serviceability.
In addition to the copper cable-diagnostics, we'll also introduce optical transceiver diagnostics
into SONIC to help users easily identify the cause of link failure.

Furthermore, the SFP and QSFP module diagnostic will also be implemented to help users
easily identify the cause of link failure on the transceivers with DOM and threshold support.

- Native RJ45: The TDR test should be performed for the conducted test requests
- Copper SFP(1000GBASE-T): The TDR test should be performed via the built-in copper register read/write
- Other SFPs: The conducted test requests will check the current DOM information and threshold values to generate the report, and of course, the DOM information and threshold will have to be available on the modules
- QSFP/QSFP+/QSFP28: The conducted test requests will check the current DOM information and threshold values to generate the report, and of course, the DOM information and threshold will have to be available on the modules

## 1.1 Functional Requirements

The functional requirements for adding cable-diagnostics support in SONiC are:

1. Ability to activate the cable/transceiver diagnostics test
2. Display cable/transceiver diagnostics test report
3. Display copper cable length in the case of native RJ45
4. Display the maximum cable length supported in the case of optical transceivers

## 1.2 Configuration and Management Requirements

1. The cable diagnostic support has to be managed using the SONiC management infrastructure.
2. The cable diagnostic is port/transceiver specific, and the test result should not persistent across reboot.
3. In the case of SFP/QSFP transceivers, the diagnostic result should be cleared upon module removal.

# 2 Functionality

## 2.1 Target Deployment Use Cases

The cable-diagnostics would be ideal for initial switch deployment, diagnosing link/connection failures
within the network topology, and ensuring link quality.

## 2.2 Functional Description

This document provides functional design and specifications of cable-diagnostics that helps users easily identify the cause of link failures. Based on the platform hardware designs, the front panel ports can be categorized as below:

1. **Native RJ45:** This is a port with both MAC and PHY mounted on the box, the connections could be easily established by cat5e and cat6 Ethernet cables. When the cable-diagnostics test is conducted on the native RJ45, the orchagent will dispatches the request to the external PHY drivers, and it could either be the SAI library on the syncd or the PAI library on the GearBox, it depends on the platform designs.

2. **External SFP/QSFP ports:** This is the so-called PHYless switch design, the front panel ports are cages that allow external PHYs on SFP/QSFP modules to get connected and communicated with the MAC on the box. The conducted cable-diagnostics test will evaluate the power and thermal readings of the moudule and compared with the threshold values found in the module registers. And hence, it could only work on modules with DOM information and threshold support, which is required to optics and optional to the coppers.

# 3 Design
## 3.1 Overview

The cable-diagnostics can be divided into the following components:

### 3.1.1 Transceiver Daemon

The transceiver daemon (**xcvrd**) is part of the SONIC platform monitor(**pmon**), and it is responsible
for posting/deleting the transceiver information upon transceiver insertion/removal, and to enable the cable
diagnostics support, the xcvrd will monitor the **APPL_DB** for test requets and update the test reports to **STATE_DB**

### 3.1.2 Management Framework

The Industry-Standard CLI of SONiC is provided by the SONiC Management Framework, and the cable-diagnostics will be availble in this user interface.

### 3.1.3 SWitch State Service

The SWitch State Service (SWSS) is a collection of software that provides a database interface for communication with and state representation of network applications and network switch hardware.
The **orchagent** of SWSS will monitor **APPL_DB** and then dispatches the requests to the external PHY devices.

## 3.2 Database

### 3.2.1 APPL_DB

The **cable_diag_status** field of **PORT_TABLE** is to contorl the cable-diagnostics tests.

```
  "PORT_TABLE:Ethernet0": {
    "type": "hash", 
    "value": {
      ...... omitted ......
      "cable_diag_status": "submitted" / "in-progress" / "gearbox" / "completed", 
      ...... omitted ......
    }
  }, 
```

- The **submitted** state shall only be set by the IS-CLI, which is to trigger the cable-diagnostics test
- The **in-progress** state shall only be set by the xcvrd, and it's updated once **submitted** is detected on a particualr port with SFP/QSFP attached. This should take place before performing the cable-diagnostics test on the module.
- The **gearbox** state shall only be set by the xcvrd, and it's updated once **submitted** is detected on a particualr port without SFP/QSFP attached, and the **orchagent** should then send out the requests to external PHYs.
- The **completed** state shall only be set by the xcvrd, and the test report should be posted to the **STATE_DB** when transitioned to this state.

### 3.2.2 STATE_DB

The **CABLE_DIAG** table will have the reports of the conducted cable-diagnostics tests.

```
  "CABLE_DIAG|Ethernet3": {
    "type": "hash", 
    "value": {
      "length": "3m", 
      "part_number": "NDAQGF-0003", 
      "result": "N/A", 
      "timestamp": "29-Apr-2021 00:53:44", 
      "type": "SFP", 
      "vendor_name": "Amphenol"
    }
  }, 
  "CABLE_DIAG|Ethernet30": {
    "type": "hash", 
    "value": {
      "length": "< 50m", 
      "part_number": "FCLF-8521-3", 
      "result": "OK", 
      "timestamp": "29-Apr-2021 00:54:34", 
      "type": "SFP", 
      "vendor_name": "FINISAR CORP."
    }
  }, 
```

## 3.3 Data Model

### 3.3.1 openconfig-platform-diagnostics.yang

There is currently no yang model defined for cable-diagnostics. Therefore, the openconfig-platform-diagnostics will be created.

The YANG tree is as below:

```
module: openconfig-platform-diagnostics
  +--rw cable-diagnostics
  |  +--rw cable-diagnostics-info* [ifname]
  |     +--rw ifname    -> ../config/ifname
  |     +--rw config
  |     |  +--rw ifname?   string
  |     |  +--rw status?   cable-diag-status-type
  |     +--ro state
  |        +--ro ifname?        -> ../../config/ifname
  |        +--ro status?        cable-diag-status-type
  |        +--ro type?          string
  |        +--ro vendor?        string
  |        +--ro vendor-part?   string
  |        +--ro result?        string
  |        +--ro length?        string
  |        +--ro timestamp?     string
```

### 3.3.2 Configuration Commands

The cable-diagnostics commands are only available in exec-mode, as the test operation and report
should not be activated or persistent across reboot.

#### test cable-diagnostics Ethernet <Port-ID>[-<Port-ID>]

This command activates the cable-diagnostics test on one particular port or the ports in a specific range.

To activate the cable-diagnostics test on Ethernet0
```
sonic-cli# test cable-diagnostics Ethernet 0
```

To activate the cable-diagnostics test on Ethernet0,Ethernet1...Ethernet9
```
sonic-cli# test cable-diagnostics Ethernet 0-9
```

### 3.3.3 CLI Show Commands

#### show cable-diagnostics report [Ethernet <Port-ID>[-<Port-ID>]]

This command displays the test report(s) of one particular port or the ports in a specific range, or all the ports when **Ethernet** is not specified

An example of displaying the reports of all the ports
```
sonic# show cable-diagnostics report

Interface     Type      Length  Result        Status        Timestamp
------------  --------  ------  ------------  ------------  ------------------
Ethernet0     SFP       3m      N/A           COMPLETED     29-Apr-2021 08:11:39
Ethernet1     SFP       3m      N/A           COMPLETED     29-Apr-2021 08:11:39
Ethernet2     SFP       3m      N/A           COMPLETED     29-Apr-2021 08:11:40
Ethernet3     SFP       3m      N/A           COMPLETED     29-Apr-2021 08:11:40
Ethernet4     SFP       10000m  N/A           COMPLETED     29-Apr-2021 08:11:40
Ethernet5     SFP       80m     Lo RxPwr      COMPLETED     29-Apr-2021 08:11:40
Ethernet6     SFP       80m     Lo RxPwr      COMPLETED     29-Apr-2021 08:11:41
Ethernet7     SFP       300m    Lo RxPwr      COMPLETED     29-Apr-2021 08:11:41
Ethernet8     SFP       10000m  N/A           COMPLETED     29-Apr-2021 08:11:41
Ethernet9     SFP       10000m  N/A           COMPLETED     29-Apr-2021 08:11:41
Ethernet10    SFP       80m     Lo RxPwr      COMPLETED     29-Apr-2021 08:11:41
Ethernet11    SFP       80m     Lo RxPwr      COMPLETED     29-Apr-2021 08:11:42
Ethernet12    SFP       80m     Lo RxPwr      COMPLETED     29-Apr-2021 08:11:42
```

An example of displaying the reports of Ethernet5
```
sonic# show cable-diagnostics report Ethernet 5

Interface     Type      Length  Result        Status        Timestamp
------------  --------  ------  ------------  ------------  ------------------
Ethernet5     SFP       80m     Lo RxPwr      COMPLETED     29-Apr-2021 08:11:40
```

An example of displaying the reports of Ethernet5,Ethernet6...Ethernet12
```
sonic# show cable-diagnostics report Ethernet 5-12

Interface     Type      Length  Result        Status        Timestamp
------------  --------  ------  ------------  ------------  ------------------
Ethernet5     SFP       80m     Lo RxPwr      COMPLETED     29-Apr-2021 08:11:40
Ethernet6     SFP       80m     Lo RxPwr      COMPLETED     29-Apr-2021 08:11:41
Ethernet7     SFP       300m    Lo RxPwr      COMPLETED     29-Apr-2021 08:11:41
Ethernet8     SFP       10000m  N/A           COMPLETED     29-Apr-2021 08:11:41
Ethernet9     SFP       10000m  N/A           COMPLETED     29-Apr-2021 08:11:41
Ethernet10    SFP       80m     Lo RxPwr      COMPLETED     29-Apr-2021 08:11:41
Ethernet11    SFP       80m     Lo RxPwr      COMPLETED     29-Apr-2021 08:11:42
Ethernet12    SFP       80m     Lo RxPwr      COMPLETED     29-Apr-2021 08:11:42
```


#### show cable-diagnostics cable-length [Ethernet <Port-ID>[-<Port-ID>]]

This command displays the cable-length of one particular port or the ports in a specific range, or all the ports when **Ethernet** is not specified
The meaning of cable-length varies as below:

- Native RJ45: The **cable-length** indicates the length of the cable attached, in the unit of meters.
- SFP/QSFP Coppers: The **cable-length** indicates the length of this DAC module, in the unit of meters.
- SFP/QSFP Optics: The **cable-length** indicates the maximum length of the cable supported by the module.

An example of displaying the cable-length of all the ports
```
sonic# show cable-diagnostics cable-length

Interface     Type      Length
------------  --------  -------------------------------
Ethernet0     SFP       3m
Ethernet1     SFP       3m
Ethernet2     SFP       3m
Ethernet3     SFP       3m
Ethernet4     SFP       10000m
Ethernet5     SFP       80m
Ethernet6     SFP       80m
Ethernet7     SFP       300m
Ethernet8     SFP       10000m
Ethernet9     SFP       10000m
Ethernet10    SFP       80m
Ethernet11    SFP       80m
Ethernet12    SFP       80m
```

An example of displaying the cable-length of Ethernet5
```
sonic# show cable-diagnostics cable-length Ethernet 5

Interface     Type      Length
------------  --------  -------------------------------
Ethernet5     SFP       80m
```

An example of displaying the cable-length of Ethernet0,Ethernet1...Ethernet9, and they are native RJ45 in this case. 
```
sonic# show cable-diagnostics cable-length Ethernet0-9

Interface     Type      Length
------------  --------  -------------------------------
Ethernet0               9m
Ethernet1               9m
Ethernet2               9m
Ethernet3               9m
Ethernet4
Ethernet5
Ethernet6               10m
Ethernet7               10m
Ethernet8               9m
Ethernet9               7m
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
