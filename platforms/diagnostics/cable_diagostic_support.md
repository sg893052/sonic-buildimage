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
| BER          | Bit Error Rate                                               |
| CDR          | Optical clock and data recovery circuit                      |
| QSFP         | Quad Small Form-factor Pluggable                             |
| RX_LOL       | Rx CDR Loss of Lock                                          |
| RX_LOS       | Rx Loss of signal                                            |
| SFP          | Small form-factor pluggable transceiver                      |
| SNR          | Signal-to-Noise Ratio                                        |
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
3. In the case of Copper SFP transceivers, the diagnostic result should be cleared upon module removal.

# 2 Functionality

## 2.1 Target Deployment Use Cases

The cable-diagnostics would be ideal for initial switch deployment, diagnosing link/connection failures
within the network topology, and ensuring link quality.

## 2.2 Functional Description

This document provides functional design and specifications of cable-diagnostics that helps users easily identify the cause of link failures. Based on the platform hardware designs, the front panel ports can be categorized as below:

1. **Native RJ45:** This is a port with both MAC and PHY mounted on the box, the connections could be easily established by cat5e and cat6 Ethernet cables. When the cable-diagnostics test is conducted on the native RJ45, the orchagent will dispatches the request to the external PHY drivers, and it could either be the SAI library on the syncd or the PAI library on the GearBox, it depends on the platform designs.

2. **External Copper SFP:** This is the so-called PHYless switch design, the front panel ports are cages that allow external PHYs on SFP modules to get connected and communicated with the MAC on the box. The conducted cable-diagnostics test will leverage the TDR hardware engine on the copper SFP for this operation.

### 2.2.1 Limitations 

#### TDR diagnostic

TDR diagnostic is only available in the following port types:

1. Native RJ45 
2. 1G copper SFPs (i.e. 1000BASE-T)

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
| XCVR  | Not Supported | TDR is not available on transceiver                   |
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
```

To activate the cable-diagnostics test on Ethernet0
```
sonic-cli# test cable-diagnostics Ethernet 0
```

To activate the cable-diagnostics test on Ethernet0,Ethernet1...Ethernet9
```
sonic-cli# test cable-diagnostics Ethernet 0-9
```

In the case of standard interface-naming mode, the ranged command is as below
```
sonic-cli# test cable-diagnostics Eth 1/1-1/9
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

#### show interface transceiver dom [Ethernet PortID[-PortID]]

This command displays the DOM and Threshold information of one particular port or the ports in a specific range, or all the ports when **Ethernet** is not specified

An example of displaying the DOM and Threshold information of all the ports
```
JioMcLAG# show interface transceiver dom | no-more

-----------------------------------------------------------------------
Ethernet0
-----------------------------------------------------------------------
alarm-rx-power-hi               N/A
alarm-rx-power-lo               N/A
alarm-temp-hi                   N/A
alarm-temp-lo                   N/A
alarm-tx-bias-hi                N/A
alarm-tx-bias-lo                N/A
alarm-tx-power-hi               N/A
alarm-tx-power-lo               N/A
alarm-volt-hi                   N/A
alarm-volt-lo                   N/A
rx-power                        Unknown,N/A,N/A,N/A
temperature                     Unknown
tx-bias                         Unknown,N/A,N/A,N/A
tx-power                        Unknown,N/A,N/A,N/A
type                            SFP
vendor                          FOUNDRY NETWORKS
vendor-part                     33210-100
voltage                         Unknown
warning-rx-power-hi             N/A
warning-rx-power-lo             N/A
warning-temp-hi                 N/A
warning-temp-lo                 N/A
warning-tx-bias-hi              N/A
warning-tx-bias-lo              N/A
warning-tx-power-hi             N/A
warning-tx-power-lo             N/A
warning-volt-hi                 N/A
warning-volt-lo                 N/A

-----------------------------------------------------------------------
Ethernet1
-----------------------------------------------------------------------
alarm-rx-power-hi               N/A
alarm-rx-power-lo               N/A
alarm-temp-hi                   N/A
alarm-temp-lo                   N/A
alarm-tx-bias-hi                N/A
alarm-tx-bias-lo                N/A
alarm-tx-power-hi               N/A
alarm-tx-power-lo               N/A
alarm-volt-hi                   N/A
alarm-volt-lo                   N/A
rx-power                        Unknown,N/A,N/A,N/A
temperature                     Unknown
tx-bias                         Unknown,N/A,N/A,N/A
tx-power                        Unknown,N/A,N/A,N/A
type                            SFP
vendor                          FOUNDRY NETWORKS
vendor-part                     33210-100
voltage                         Unknown
warning-rx-power-hi             N/A
warning-rx-power-lo             N/A
warning-temp-hi                 N/A
warning-temp-lo                 N/A
warning-tx-bias-hi              N/A
warning-tx-bias-lo              N/A
warning-tx-power-hi             N/A
warning-tx-power-lo             N/A
warning-volt-hi                 N/A
warning-volt-lo                 N/A

-----------------------------------------------------------------------
Ethernet2
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet3
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet4
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet5
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet6
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet7
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet8
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet9
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet10
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet11
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet12
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet13
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet14
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet15
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet16
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet17
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet18
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet19
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet20
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet21
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet22
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet23
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet24
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet25
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet26
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet27
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet28
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet29
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet30
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet31
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet32
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet33
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet34
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet35
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet36
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet37
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet38
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet39
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet40
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet41
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet42
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet43
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet44
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet45
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet46
-----------------------------------------------------------------------
alarm-rx-power-hi               N/A
alarm-rx-power-lo               N/A
alarm-temp-hi                   N/A
alarm-temp-lo                   N/A
alarm-tx-bias-hi                N/A
alarm-tx-bias-lo                N/A
alarm-tx-power-hi               N/A
alarm-tx-power-lo               N/A
alarm-volt-hi                   N/A
alarm-volt-lo                   N/A
rx-power
tx-bias
tx-power
type                            SFP
vendor                          FS
vendor-part                     Q-4SAO05
warning-rx-power-hi             N/A
warning-rx-power-lo             N/A
warning-temp-hi                 N/A
warning-temp-lo                 N/A
warning-tx-bias-hi              N/A
warning-tx-bias-lo              N/A
warning-tx-power-hi             N/A
warning-tx-power-lo             N/A
warning-volt-hi                 N/A
warning-volt-lo                 N/A

-----------------------------------------------------------------------
Ethernet47
-----------------------------------------------------------------------
alarm-rx-power-hi               N/A
alarm-rx-power-lo               N/A
alarm-temp-hi                   N/A
alarm-temp-lo                   N/A
alarm-tx-bias-hi                N/A
alarm-tx-bias-lo                N/A
alarm-tx-power-hi               N/A
alarm-tx-power-lo               N/A
alarm-volt-hi                   N/A
alarm-volt-lo                   N/A
rx-power
tx-bias
tx-power
type                            SFP
vendor                          FS
vendor-part                     Q-4SAO05
warning-rx-power-hi             N/A
warning-rx-power-lo             N/A
warning-temp-hi                 N/A
warning-temp-lo                 N/A
warning-tx-bias-hi              N/A
warning-tx-bias-lo              N/A
warning-tx-power-hi             N/A
warning-tx-power-lo             N/A
warning-volt-hi                 N/A
warning-volt-lo                 N/A

-----------------------------------------------------------------------
Ethernet48
-----------------------------------------------------------------------
alarm-rx-power-hi               5.3950
alarm-rx-power-lo               -13.2975
alarm-temp-hi                   80.0000
alarm-temp-lo                   -10.0000
alarm-tx-bias-hi                12.0000
alarm-tx-bias-lo                3.0020
alarm-tx-power-hi               N/A
alarm-tx-power-lo               N/A
alarm-volt-hi                   3.6304
alarm-volt-lo                   2.9704
rx-power                        -1.8609,-1.4539,-1.3567,-1.4758
temperature                     27.0117
tx-bias                         6.9780,6.9780,6.9780,6.9780
tx-power                        N/A,N/A,N/A,N/A
type                            QSFP28
vendor                          FS
vendor-part                     QSFP28-SR4-100G
voltage                         3.2419
warning-rx-power-hi             2.3955
warning-rx-power-lo             -10.3012
warning-temp-hi                 75.0000
warning-temp-lo                 -5.0000
warning-tx-bias-hi              10.0000
warning-tx-bias-lo              4.4960
warning-tx-power-hi             N/A
warning-tx-power-lo             N/A
warning-volt-hi                 3.4648
warning-volt-lo                 3.1352

-----------------------------------------------------------------------
Ethernet52
-----------------------------------------------------------------------
alarm-rx-power-hi               5.3950
alarm-rx-power-lo               -13.2975
alarm-temp-hi                   80.0000
alarm-temp-lo                   -10.0000
alarm-tx-bias-hi                12.0000
alarm-tx-bias-lo                3.0020
alarm-tx-power-hi               N/A
alarm-tx-power-lo               N/A
alarm-volt-hi                   3.6304
alarm-volt-lo                   2.9704
rx-power                        0.4836,0.6262,0.6915,0.3687
temperature                     27.2422
tx-bias                         6.9780,6.9780,6.9780,6.9780
tx-power                        N/A,N/A,N/A,N/A
type                            QSFP28
vendor                          FS
vendor-part                     QSFP28-SR4-100G
voltage                         3.2433
warning-rx-power-hi             2.3955
warning-rx-power-lo             -10.3012
warning-temp-hi                 75.0000
warning-temp-lo                 -5.0000
warning-tx-bias-hi              10.0000
warning-tx-bias-lo              4.4960
warning-tx-power-hi             N/A
warning-tx-power-lo             N/A
warning-volt-hi                 3.4648
warning-volt-lo                 3.1352

-----------------------------------------------------------------------
Ethernet56
-----------------------------------------------------------------------
alarm-rx-power-hi               5.3950
alarm-rx-power-lo               -13.2975
alarm-temp-hi                   80.0000
alarm-temp-lo                   -10.0000
alarm-tx-bias-hi                12.0000
alarm-tx-bias-lo                3.0020
alarm-tx-power-hi               N/A
alarm-tx-power-lo               N/A
alarm-volt-hi                   3.6304
alarm-volt-lo                   2.9704
rx-power                        -1.5633,-1.3395,-2.2505,-1.8595
temperature                     26.4219
tx-bias                         6.9780,6.9780,6.9780,6.9780
tx-power                        N/A,N/A,N/A,N/A
type                            QSFP28
vendor                          FS
vendor-part                     QSFP28-SR4-100G
voltage                         3.2399
warning-rx-power-hi             2.3955
warning-rx-power-lo             -10.3012
warning-temp-hi                 75.0000
warning-temp-lo                 -5.0000
warning-tx-bias-hi              10.0000
warning-tx-bias-lo              4.4960
warning-tx-power-hi             N/A
warning-tx-power-lo             N/A
warning-volt-hi                 3.4648
warning-volt-lo                 3.1352

-----------------------------------------------------------------------
Ethernet60
-----------------------------------------------------------------------
alarm-rx-power-hi               5.3950
alarm-rx-power-lo               -13.2975
alarm-temp-hi                   80.0000
alarm-temp-lo                   -10.0000
alarm-tx-bias-hi                12.0000
alarm-tx-bias-lo                3.0020
alarm-tx-power-hi               N/A
alarm-tx-power-lo               N/A
alarm-volt-hi                   3.6304
alarm-volt-lo                   2.9704
rx-power                        -0.3203,-0.1403,-0.4701,-0.7053
temperature                     31.0664
tx-bias                         7.0800,7.0800,7.0800,7.0800
tx-power                        N/A,N/A,N/A,N/A
type                            QSFP28
vendor                          FS
vendor-part                     QSFP28-SR4-100G
voltage                         3.2439
warning-rx-power-hi             2.3955
warning-rx-power-lo             -10.3012
warning-temp-hi                 75.0000
warning-temp-lo                 -5.0000
warning-tx-bias-hi              10.0000
warning-tx-bias-lo              4.4960
warning-tx-power-hi             N/A
warning-tx-power-lo             N/A
warning-volt-hi                 3.4648
warning-volt-lo                 3.1352

-----------------------------------------------------------------------
Ethernet64
-----------------------------------------------------------------------
alarm-rx-power-hi               5.3950
alarm-rx-power-lo               -13.2975
alarm-temp-hi                   80.0000
alarm-temp-lo                   -10.0000
alarm-tx-bias-hi                12.0000
alarm-tx-bias-lo                3.0020
alarm-tx-power-hi               N/A
alarm-tx-power-lo               N/A
alarm-volt-hi                   3.6304
alarm-volt-lo                   2.9704
rx-power                        0.6341,0.2214,0.3651,0.2776
temperature                     23.6523
tx-bias                         6.8740,6.8740,6.8740,6.8740
tx-power                        N/A,N/A,N/A,N/A
type                            QSFP28
vendor                          FS
vendor-part                     QSFP28-SR4-100G
voltage                         3.2386
warning-rx-power-hi             2.3955
warning-rx-power-lo             -10.3012
warning-temp-hi                 75.0000
warning-temp-lo                 -5.0000
warning-tx-bias-hi              10.0000
warning-tx-bias-lo              4.4960
warning-tx-power-hi             N/A
warning-tx-power-lo             N/A
warning-volt-hi                 3.4648
warning-volt-lo                 3.1352

-----------------------------------------------------------------------
Ethernet68
-----------------------------------------------------------------------
alarm-rx-power-hi               N/A
alarm-rx-power-lo               N/A
alarm-temp-hi                   N/A
alarm-temp-lo                   N/A
alarm-tx-bias-hi                N/A
alarm-tx-bias-lo                N/A
alarm-tx-power-hi               N/A
alarm-tx-power-lo               N/A
alarm-volt-hi                   N/A
alarm-volt-lo                   N/A
rx-power                        2.3431,-inf,-inf,-inf
temperature                     0.0000
tx-bias                         0.0000,0.0000,0.0000,0.0000
tx-power                        N/A,N/A,N/A,N/A
type                            QSFP+
vendor                          FS
vendor-part                     QSFP-PC03
voltage                         0.0000
warning-rx-power-hi             N/A
warning-rx-power-lo             N/A
warning-temp-hi                 N/A
warning-temp-lo                 N/A
warning-tx-bias-hi              N/A
warning-tx-bias-lo              N/A
warning-tx-power-hi             N/A
warning-tx-power-lo             N/A
warning-volt-hi                 N/A
warning-volt-lo                 N/A

-----------------------------------------------------------------------
Ethernet72
-----------------------------------------------------------------------
alarm-rx-power-hi               N/A
alarm-rx-power-lo               N/A
alarm-temp-hi                   N/A
alarm-temp-lo                   N/A
alarm-tx-bias-hi                N/A
alarm-tx-bias-lo                N/A
alarm-tx-power-hi               N/A
alarm-tx-power-lo               N/A
alarm-volt-hi                   N/A
alarm-volt-lo                   N/A
rx-power                        -inf,-inf,-inf,-inf
temperature                     23.4922
tx-bias                         0.0000,0.0000,0.0000,0.0000
tx-power                        N/A,N/A,N/A,N/A
type                            QSFP28
vendor                          Fiberstore
vendor-part                     Q28-PC05
voltage                         3.2895
warning-rx-power-hi             N/A
warning-rx-power-lo             N/A
warning-temp-hi                 N/A
warning-temp-lo                 N/A
warning-tx-bias-hi              N/A
warning-tx-bias-lo              N/A
warning-tx-power-hi             N/A
warning-tx-power-lo             N/A
warning-volt-hi                 N/A
warning-volt-lo                 N/A

-----------------------------------------------------------------------
Ethernet76
-----------------------------------------------------------------------
alarm-rx-power-hi               4.0000
alarm-rx-power-lo               -18.0134
alarm-temp-hi                   85.0000
alarm-temp-lo                   -10.0000
alarm-tx-bias-hi                15.0000
alarm-tx-bias-lo                0.0000
alarm-tx-power-hi               N/A
alarm-tx-power-lo               N/A
alarm-volt-hi                   3.6000
alarm-volt-lo                   2.9000
rx-power                        0.2510,0.1111,0.3197,-0.1082
temperature                     26.0000
tx-bias                         5.9940,5.9880,5.9800,6.0200
tx-power                        N/A,N/A,N/A,N/A
type                            QSFP28
vendor                          FS
vendor-part                     QSFP28-SR4-100G
voltage                         3.2190
warning-rx-power-hi             2.4000
warning-rx-power-lo             -15.0031
warning-temp-hi                 70.0000
warning-temp-lo                 0.0000
warning-tx-bias-hi              12.0000
warning-tx-bias-lo              2.0000
warning-tx-power-hi             N/A
warning-tx-power-lo             N/A
warning-volt-hi                 3.5000
warning-volt-lo                 3.1000

-----------------------------------------------------------------------
Ethernet80
-----------------------------------------------------------------------
None detected

-----------------------------------------------------------------------
Ethernet81
-----------------------------------------------------------------------
None detected
JioMcLAG#
```

An example of displaying the DOM and Threshold information of Ethernet48
```
JioMcLAG# show interface transceiver dom Ethernet 48 | no-more

-----------------------------------------------------------------------
Ethernet48
-----------------------------------------------------------------------
alarm-rx-power-hi               5.3950
alarm-rx-power-lo               -13.2975
alarm-temp-hi                   80.0000
alarm-temp-lo                   -10.0000
alarm-tx-bias-hi                12.0000
alarm-tx-bias-lo                3.0020
alarm-tx-power-hi               N/A
alarm-tx-power-lo               N/A
alarm-volt-hi                   3.6304
alarm-volt-lo                   2.9704
rx-power                        -1.8675,-1.4539,-1.3501,-1.4758
temperature                     26.7188
tx-bias                         6.9780,6.9780,6.9780,6.9780
tx-power                        N/A,N/A,N/A,N/A
type                            QSFP28
vendor                          FS
vendor-part                     QSFP28-SR4-100G
voltage                         3.2419
warning-rx-power-hi             2.3955
warning-rx-power-lo             -10.3012
warning-temp-hi                 75.0000
warning-temp-lo                 -5.0000
warning-tx-bias-hi              10.0000
warning-tx-bias-lo              4.4960
warning-tx-power-hi             N/A
warning-tx-power-lo             N/A
warning-volt-hi                 3.4648
warning-volt-lo                 3.1352
```

#### show interface transceiver dom summary [Ethernet PortID[-PortID]]

This command displays the DOM information of one particular port or the ports in a specific range, or all the ports when **Ethernet** is not specified

An example of displaying the DOM information of all the ports
```
sonic# show interface transceiver dom summary | no-more

Interface   Type     Vendor           Temp(C) Volt(V) RxPwr(dBm) TxPwr(dBm)
----------- -------- ---------------- ------- ------- ---------- ----------
Ethernet0   SFP      Amphenol         0.0000  0.0000  -inf       -inf
Ethernet1   SFP      Amphenol         0.0000  0.0000  -inf       -inf
Ethernet2   SFP      Amphenol         0.0000  0.0000  -inf       -inf
Ethernet3   SFP      Amphenol         0.0000  0.0000  -inf       -inf
Ethernet4   SFP      FINISAR CORP.    0.0000  0.0000  -inf       -inf
Ethernet5   SFP      FINISAR CORP.    28.4766 3.3018  -30.9691   -2.1488
Ethernet6   SFP      FINISAR CORP.    30.0156 3.2948  -33.9794   -1.9199
Ethernet7   SFP      JDSU             27.0625 3.3053  -29.5861   -4.9026
Ethernet8   SFP      FINISAR CORP.    0.0000  0.0000  -inf       -inf
Ethernet9   SFP      FINISAR CORP.    0.0000  0.0000  -inf       -inf
Ethernet10  SFP      FINISAR CORP.    30.9141 3.3026  -36.9897   -2.0149
Ethernet11  SFP      FINISAR CORP.    30.6602 3.2969  -inf       -1.9091
Ethernet12  SFP      FINISAR CORP.    28.9844 3.3443  -33.9794   -2.1375
```

An example of displaying the DOM information of Ethernet5
```
sonic# show interface transceiver dom summary Ethernet 5

Interface   Type     Vendor           Temp(C) Volt(V) RxPwr(dBm) TxPwr(dBm)
----------- -------- ---------------- ------- ------- ---------- ----------
Ethernet5   SFP      FINISAR CORP.    28.4766 3.3018  -30.9691   -2.1488
```

An example of displaying the DOM information of Ethernet5-12
```
sonic# show interface transceiver dom summary Ethernet 5-12

Interface   Type     Vendor           Temp(C) Volt(V) RxPwr(dBm) TxPwr(dBm)
----------- -------- ---------------- ------- ------- ---------- ----------
Ethernet5   SFP      FINISAR CORP.    28.4766 3.3018  -30.9691   -2.1488
Ethernet6   SFP      FINISAR CORP.    30.0156 3.2948  -33.9794   -1.9199
Ethernet7   SFP      JDSU             27.0625 3.3053  -29.5861   -4.9026
Ethernet8   SFP      FINISAR CORP.    0.0000  0.0000  -inf       -inf
Ethernet9   SFP      FINISAR CORP.    0.0000  0.0000  -inf       -inf
Ethernet10  SFP      FINISAR CORP.    30.9141 3.3026  -36.9897   -2.0149
Ethernet11  SFP      FINISAR CORP.    30.6602 3.2969  -inf       -1.9091
Ethernet12  SFP      FINISAR CORP.    28.9844 3.3443  -33.9794   -2.1375
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
* Verify cable-diagnostics for p2p and breakout interfaces
* Verify failure scenarios for cable-diagnostics (transceiver not present, fiber not plugged in, transceiver not plugged in on peer, etc.)
* Verify graceful exit when test/show is executed on an invalid interface (e.g., something out of range like Ethernet300)

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
