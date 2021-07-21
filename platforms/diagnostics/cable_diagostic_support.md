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
| SFP          | Small form-factor pluggable transceiver                      |
| TDR          | Time Domain Reflectometry                                    |
| VCT          | Marvell Virtual Cable Tester                                 |

# 1 Requirements Overview

A time-domain reflectometer (TDR) is an electronic instrument used to determine 
the characteristics of electrical lines by observing reflected waveforms.

In 2002, in order to ease and facilitate the rapid deployment of Gigabit Ethernet,
Marvell Technology Group introduced an innovative new feature for its Alaska® family
of Gigabit Ethernet Physical Layer (PHY) devices, called Virtual Cable Tester™ (VCT)
technology, which is built on top of TDR, and is well-received and widely deployed
in the enterprise switches.

Nowadays, most of the enterprise switch silicon vendors also provide similar features
to their own Gigabit Ethernet Physical Layer (PHY) devices, and when transition from
copper to fiber, the VCT is also massively integrated into the copper SFPs (i.e. 1000BASE-T),
and hence, it's still commonly available on these devices.

From a SONiC perspective, the addition of this diagnostic feature will enhance
platform development, user deployment, platform support, and serviceability.

- Native RJ45: The TDR test should be performed for the conducted test requests
- 1G Copper SFP(1000GBASE-T): The TDR test should be performed via the built-in copper register read/write

## 1.1 Functional Requirements

The functional requirements for adding cable-diagnostics support in SONiC are:

1. Ability to activate the cable diagnostics test
2. Display cable diagnostics test report
3. Display copper cable length in the case of TDR

## 1.2 Configuration and Management Requirements

1. The cable diagnostic support has to be managed using the SONiC management infrastructure.
2. The cable diagnostic is port specific, and the test result should not persist across reboot.
3. The cable diagnostic may cause disruption of traffic, the CLI should provide a prompt confirmation.
4. In the case of Copper SFP transceivers, the diagnostic result should be cleared upon module removal.

# 2 Functionality

## 2.1 Target Deployment Use Cases

The cable-diagnostics would be ideal for initial switch deployment, diagnosing link/connection failures
within the network topology, and ensuring link quality.

## 2.2 Functional Description

This document provides functional design and specifications of cable-diagnostics that helps users easily identify the cause of link failures. Based on the platform hardware designs, the supported port types are as below:

1. **Native RJ45:**
    This is a port with both MAC and PHY mounted on the box, the connections could be easily established by cat5e and cat6 Ethernet cables. When the cable-diagnostics test is conducted on the native RJ45, the orchagent will dispatches the request to the external PHY drivers, and it could either be the SAI library on the syncd or the PAI library on the GearBox, it depends on the platform designs.

2. **1G Copper SFP(i.e. 1000BASE-T):**
    This is the so-called PHYless switch design, the front panel ports are cages that allow external PHYs on SFP modules to get connected and communicated with the MAC on the box. The conducted cable-diagnostics test will leverage the TDR hardware engine on the copper SFP for this operation.

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

The **cable_diag_status** field of **PORT_TABLE** is to control the cable-diagnostics tests.

```
  "PORT_TABLE:Ethernet0": {
    "type": "hash", 
    "value": {
      ...... omitted ......
      "cable_diag_status": "submitted" / "in-progress" / "gearbox" / "completed" / "failed", 
      ...... omitted ......
    }
  }, 
```

- The **submitted** state shall only be set by the management framework, which is to trigger the cable-diagnostics test
- The **in-progress** state shall only be set by the xcvrd, and it's updated once **submitted** is detected on a particualr port with SFP/QSFP attached. This should take place before performing the cable-diagnostics test on the module.
- The **gearbox** state shall only be set by the xcvrd, and it's updated once **submitted** is detected on a particualr port without SFP/QSFP attached, and the **orchagent** should then send out the requests to external PHYs.
- The **completed** state shall only be set by the xcvrd, and the test report should be posted to the **STATE_DB** when transitioned to this state.
- The **failed** state shall only be set by the xcvrd, and it indicates either a failure in the test initiated by writting to the VCT registers on the copper PHY.

### 3.2.2 STATE_DB

The **CABLE_DIAG** table will have the reports of the conducted cable-diagnostics tests.

```
  "CABLE_DIAG|Ethernet30": {
    "type": "hash", 
    "value": {
      "length": "< 50m", 
      "part_number": "FCLF-8521-3", 
      "result": "OK", 
      "timestamp": "29-Apr-2021 00:54:34", 
      "type": "TDR", 
      "vendor_name": "FINISAR CORP."
    }
  }, 
```

1. type: TDR for Time Domain Reflectometry diagnostic
2. length: The detected cable length
3. part_number: The part number of the transceiver (Copper SFP only)
4. vendor_name: The vendor number of the transceiver (Copper SFP only)
5. timestamp: The timestamp of this report
6. result: The test result, the possible test results are as below

| Type  | Result        |  Description                                          |
|:-----:|:-------------:|:------------------------------------------------------|
| TDR   | OK            | Cable has passed the test                             |
| TDR   | OPEN          | Cable is not connected                                |
| TDR   | SHORT         | Short circuit has occurred in the cable               |
| TDR   | FAILED        | Internal test failures at the physical layer          |
| XCVR  | Not Supported | TDR is not available on the transceiver               |
| XCVR  | Not Present   | The transceiver is not detected on the port           |


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

#### test cable-diagnostics [Ethernet PortID[-PortID]]

This command activates the cable-diagnostics test on one particular port or the ports in a specific range, or all the ports when **Ethernet** is not specified

To activate the cable-diagnostics test on all the ports
```
sonic-cli# test cable-diagnostics
!!WARNING!! This operation may cause disruption of traffic, continue? [y/N]:y
```

To activate the cable-diagnostics test on Ethernet0
```
sonic-cli# test cable-diagnostics Ethernet 0
!!WARNING!! This operation may cause disruption of traffic, continue? [y/N]:y
```

To activate the cable-diagnostics test on Ethernet0,Ethernet1...Ethernet9
```
sonic-cli# test cable-diagnostics Ethernet 0-9
!!WARNING!! This operation may cause disruption of traffic, continue? [y/N]:y
```

In the case of standard interface-naming mode, the ranged command is as below
```
sonic-cli# test cable-diagnostics Eth 1/1-1/9
!!WARNING!! This operation may cause disruption of traffic, continue? [y/N]:y
```


### 3.3.3 CLI Show Commands

#### show cable-diagnostics report [Ethernet PortID[-PortID]]

This command displays the test report(s) of one particular port or the ports in a specific range, or all the ports when **Ethernet** is not specified

An example of displaying the reports of all the ports
```
sonic# show cable-diagnostics report

Interface     Type      Length  Result        Status        Timestamp
------------  --------  ------  ------------- ------------  --------------------
Ethernet0     XCVR              Not Supported COMPLETED     29-Apr-2021 08:11:39
Ethernet1     XCVR              Not Supported COMPLETED     29-Apr-2021 08:11:39
Ethernet2     XCVR              Not Supported COMPLETED     29-Apr-2021 08:11:40
Ethernet3     XCVR              Not Supported COMPLETED     29-Apr-2021 08:11:40
Ethernet4     TDR               OPEN          COMPLETED     29-Apr-2021 08:11:40
Ethernet5     TDR       < 50m   OK            COMPLETED     29-Apr-2021 08:11:41
Ethernet6     TDR       < 50m   OK            COMPLETED     29-Apr-2021 08:11:41
Ethernet7     XCVR              Not Supported COMPLETED     29-Apr-2021 08:11:41
```

An example of displaying the reports of Ethernet5
```
sonic# show cable-diagnostics report Ethernet 5

Interface     Type      Length  Result        Status        Timestamp
------------  --------  ------  ------------  ------------  ------------------
Ethernet5     TDR       < 50m   OK            COMPLETED     29-Apr-2021 08:11:41
```

An example of displaying the reports of Ethernet2,Ethernet3...Ethernet7
```
sonic# show cable-diagnostics report Ethernet 2-7

Interface     Type      Length  Result        Status        Timestamp
------------  --------  ------  ------------  ------------  ------------------
Ethernet2     XCVR              Not Supported COMPLETED     29-Apr-2021 08:11:40
Ethernet3     XCVR              Not Supported COMPLETED     29-Apr-2021 08:11:40
Ethernet4     TDR               OPEN          COMPLETED     29-Apr-2021 08:11:40
Ethernet5     TDR       < 50m   OK            COMPLETED     29-Apr-2021 08:11:41
Ethernet6     TDR       < 50m   OK            COMPLETED     29-Apr-2021 08:11:41
Ethernet7     XCVR              Not Supported COMPLETED     29-Apr-2021 08:11:41
```

#### show cable-diagnostics cable-length [Ethernet PortID[-PortID]]

This command displays the cable-length of one particular port or the ports in a specific range, or all the ports when **Ethernet** is not specified

An example of displaying the cable-length of all the ports
```
sonic# show cable-diagnostics cable-length

Interface     Type      Length
------------  --------  -------------------------------
Ethernet0     TDR       < 50m
Ethernet1     TDR       < 50m
Ethernet2     TDR
Ethernet3     XCVR
```

An example of displaying the cable-length of Ethernet1
```
sonic# show cable-diagnostics cable-length Ethernet 1

Interface     Type      Length
------------  --------  -------------------------------
Ethernet1     TDR       < 50m
```

An example of displaying the cable-length of Ethernet0,Ethernet1...Ethernet7.
```
sonic# show cable-diagnostics cable-length Ethernet0-7

Interface     Type      Length
------------  --------  -------------------------------
Ethernet0     TDR       < 50m
Ethernet1     TDR       < 50m
Ethernet2     TDR       < 50m
Ethernet3     TDR       < 50m
Ethernet4     XCVR
Ethernet5     XCVR
Ethernet6     XCVR
Ethernet7     XCVR
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

* Verify 'test cable-diagnostics' with all port
* Verify 'test cable-diagnostics' with a specific port
* Verify 'test cable-diagnostics' with an invalid port
* Verify 'test cable-diagnostics' with a specific range
* Verify 'test cable-diagnostics' with an invalid range
* Verify 'show cable-diagnostics report' with all port
* Verify 'show cable-diagnostics report' with a specific port
* Verify 'show cable-diagnostics report' with an invalid port
* Verify 'show cable-diagnostics report' with a specific range
* Verify 'show cable-diagnostics report' with an invalid range
* Verify 'show cable-diagnostics cable-length' with all port
* Verify 'show cable-diagnostics cable-length' with a specific port
* Verify 'show cable-diagnostics cable-length' with an invalid port
* Verify 'show cable-diagnostics cable-length' with a specific range
* Verify 'show cable-diagnostics cable-length' with an invalid range

# 9 Internal Design Information

This feature is now only available on Buzznik 3.2.x, and it will later be backported to the Cyrus.

As of now, the TDR support for the native RJ45 will only be available on the following platforms

- Accton AS4630

In the case of 1000BASE-T transceivers (i.e. Copper SFPs with RJ45 connectors), it's commonly available on the existing platforms, and here is the list of supported modules

- AVAGO ABCU-5730RZ
- AVAGO ABCU-5740RZ
- AVAGO ABCU-5730GZ
- AVAGO ABCU-5730ARZ
- AVAGO ABCU-5740ARZ
- AVAGO ABCU-5731ARZ
- AVAGO ABCU-5741ARZ
- AVAGO ABCU-5741AGZ
- FINISAR FCLF8520P2BTL
- FINISAR FCLF8521P2BTL
- FINISAR FCLF8522P2BTL

In the case of optical transceivers, while it's commonly available on the existing platforms, it works only with the modules below

- 25G/10G/1G SFPs with DOM and Threshold support
- 100G/40G QSFP with DOM and Threshold support
