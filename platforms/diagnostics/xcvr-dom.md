# SONiC Transceiver DOM Information Support

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
| 0.1 | 07/19/2021  | Dante (Kuo-Jung) Su | Initial version                    |

# About this Manual

This document provides functional requirements and high-level design of transceiver DOM information support in SONiC.

#  References

##  Table 1: References

|       **Document**           | **Location**   |
| ---------------------------- | -------------- |
| Wiki                         | https://en.wikipedia.org/wiki/Small_form-factor_pluggable_transceiver |
| SFF-8436                     | https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwj89oqR__PxAhXj73MBHetUDh8QFjABegQIBBAD&url=https%3A%2F%2Fmembers.snia.org%2Fdocument%2Fdl%2F25896&usg=AOvVaw3vtBDXq9qHp8qK0lnVcNnI |
| SFF-8472                     | https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjg-7jC_vPxAhVUbSsKHcXfAhUQFjAAegQIBRAD&url=https%3A%2F%2Fmembers.snia.org%2Fdocument%2Fdl%2F25916&usg=AOvVaw1_H0SPUbcdr31PrbjqvBsM |
| SFF-8636                     | https://members.snia.org/document/dl/26418 |
| Management Interface - QSFP-DD | https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwio5oGc__PxAhXE4XMBHe33Bj0QFjABegQIBhAD&url=http%3A%2F%2Fwww.qsfp-dd.com%2Fwp-content%2Fuploads%2F2019%2F05%2FQSFP-DD-CMIS-rev4p0.pdf&usg=AOvVaw3lYx52UIlwNx85gyyWS_GE |

# Scope

This document describes the high level design of transceiver DOM information support in SONiC.

# Definition/Abbreviation

## Table 2: Abbreviations

| **Term**     | **Meaning**                                                  |
| ------------ | ------------------------------------------------------------ |
| SFP          | Small form-factor pluggable transceiver with 1G speed        |
| SFP+         | Small form-factor pluggable transceiver with 10G speed       |
| SFP28        | Small form-factor pluggable transceiver with 25G speed       |
| QSFP         | Quad Small Form-factor Pluggable transceiver with 4G speed   |
| QSFP+        | Quad Small Form-factor Pluggable transceiver with 40G speed  |
| QSFP28       | Quad Small Form-factor Pluggable transceiver with 100G speed |
| QSFP-DD      | Quad Small Form-factor Pluggable Double Density transceiver  |
| DOM          | Digital Optical Monitoring                                   |
| XCVR         | Transceiver                                                  |

# 1 Requirements Overview

Transceiver DOM information is a technology which allows you to monitor important 
parameters of the transceiver module in real-time. You can use DOM to monitor the
TX (transmit) and RX (receive) ports of the module, as well as input/output power,
temperature, and voltage. According to these monitored parameters, network technicians
are able to check and ensure that the module is functioning well. 

# 2 Functionality

## 2.1 Target Deployment Use Cases

The transceiver DOM information support is ideal for initial switch deployment, diagnosing link/connection failures
within the network topology, and ensuring link quality.

## 2.2 Functional Description

1. Periodically sampling the DOM information from the Transceiver and upload to the RedisDB
2. Display the Transceiver DOM information
3. Transceiver DOM information is enabled by default, none of configuration is necessary.
4. The show command of DOM is available in both CLICK and KLISH CLI.

# 3 Design

## 3.1 Overview

- The pmon#xcvrd is responsible of periodically fetching the DOM information from the Transceiver and upload the data to the STATE_DB of RedisDB
- The CLICK CLI will directly fecth the DOM information from STATE_DB and display the data on the console.
- The KLISH CLI will communiate with REST server for the DOM information and display the data on the console.
- The REST server will use YANG model for the DOM information stored in the STATE_DB, and use HTTP protocol to communicate with KLISH CLI.

## 3.2 Database

### 3.2.1 STATE_DB
Format
```
  "TRANSCEIVER_DOM_SENSOR|EthernetXY": {
    "rx*power": "<dBm>",
    "tx*power": "<dBm>",
    "tx*bias": "<mA>",
    "temperature": "<degreeC>",
    "voltage": "<voltage>",
    "****highalarm": "<dBm/mA/degreeC/voltage>",
    "****highwarning": "<dBm/mA/degreeC/voltage>",
    "****lowalarm": "<dBm/mA/degreeC/voltage>", 
    "****lowwarning": "<dBm/mA/degreeC/voltage>",
  }
```

Example
```
  "TRANSCEIVER_DOM_SENSOR|Ethernet128": {
    "type": "hash", 
    "value": {
      "rx1power": "-36.9897", 
      "rx2power": "-40.0000", 
      "rx3power": "-40.0000", 
      "rx4power": "-36.9897", 
      "rx5power": "-inf", 
      "rx6power": "-inf", 
      "rx7power": "-inf", 
      "rx8power": "-inf", 
      "rxpowerhighalarm": "5.5000", 
      "rxpowerhighwarning": "4.5000", 
      "rxpowerlowalarm": "-8.9008", 
      "rxpowerlowwarning": "-6.8994", 
      "temperature": "36.2734", 
      "temphighalarm": "75.0000", 
      "temphighwarning": "70.0000", 
      "templowalarm": "-10.0000", 
      "templowwarning": "-5.0000", 
      "tx1bias": "0.0000", 
      "tx1power": "-40.0000", 
      "tx2bias": "0.0000", 
      "tx2power": "-40.0000", 
      "tx3bias": "0.0000", 
      "tx3power": "-40.0000", 
      "tx4bias": "0.0000", 
      "tx4power": "-40.0000", 
      "tx5bias": "0.0000", 
      "tx5power": "-inf", 
      "tx6bias": "0.0000", 
      "tx6power": "-inf", 
      "tx7bias": "0.0000", 
      "tx7power": "-inf", 
      "tx8bias": "0.0000", 
      "tx8power": "-inf", 
      "txbiashighalarm": "130.0000", 
      "txbiashighwarning": "125.0000", 
      "txbiaslowalarm": "10.0000", 
      "txbiaslowwarning": "15.0000", 
      "txpowerhighalarm": "5.5000", 
      "txpowerhighwarning": "4.5000", 
      "txpowerlowalarm": "-3.8998", 
      "txpowerlowwarning": "-2.8997", 
      "vcchighalarm": "3.6300", 
      "vcchighwarning": "3.4650", 
      "vcclowalarm": "3.0500", 
      "vcclowwarning": "3.1350", 
      "voltage": "3.2910"
    }
  }, 
```

## 3.3 Data Model

### 3.3.1 openconfig-platform-diagnostics.yang

```
  module: openconfig-platform-diagnostics
  +--rw transceiver-dom
     +--rw transceiver-dom-info* [ifname]
        +--rw ifname    -> ../config/ifname
        +--rw config
        |  +--rw ifname?   string
        +--ro state
           +--ro ifname?                -> ../../config/ifname
           +--ro type?                  string
           +--ro vendor?                string
           +--ro vendor-part?           string
           +--ro memory-type?           string
           +--ro temperature?           string
           +--ro voltage?               string
           +--ro rx-power?              string
           +--ro tx-bias?               string
           +--ro tx-power?              string
           +--ro alarm-temp-hi?         string
           +--ro alarm-temp-lo?         string
           +--ro alarm-volt-hi?         string
           +--ro alarm-volt-lo?         string
           +--ro alarm-rx-power-hi?     string
           +--ro alarm-rx-power-lo?     string
           +--ro alarm-tx-bias-hi?      string
           +--ro alarm-tx-bias-lo?      string
           +--ro alarm-tx-power-hi?     string
           +--ro alarm-tx-power-lo?     string
           +--ro warning-temp-hi?       string
           +--ro warning-temp-lo?       string
           +--ro warning-volt-hi?       string
           +--ro warning-volt-lo?       string
           +--ro warning-rx-power-hi?   string
           +--ro warning-rx-power-lo?   string
           +--ro warning-tx-bias-hi?    string
           +--ro warning-tx-bias-lo?    string
           +--ro warning-tx-power-hi?   string
           +--ro warning-tx-power-lo?   string
           +--ro timestamp?             string
```

Example:

```
{
  "openconfig-platform-diagnostics:transceiver-dom-info": [
    {
      "config": {
        "ifname": "Ethernet128"
      },
      "ifname": "Ethernet128",
      "state": {
        "alarm-rx-power-hi": "5.5000",
        "alarm-rx-power-lo": "-8.9008",
        "alarm-temp-hi": "75.0000",
        "alarm-temp-lo": "-10.0000",
        "alarm-tx-bias-hi": "130.0000",
        "alarm-tx-bias-lo": "10.0000",
        "alarm-tx-power-hi": "5.5000",
        "alarm-tx-power-lo": "-3.8998",
        "alarm-volt-hi": "3.6300",
        "alarm-volt-lo": "3.0500",
        "ifname": "Ethernet128",
        "memory-type": "Paged",
        "rx-power": "-36.9897,-40.0000,-40.0000,-36.9897,-inf,-inf,-inf,-inf",
        "temperature": "36.2734",
        "tx-bias": "0.0000,0.0000,0.0000,0.0000,0.0000,0.0000,0.0000,0.0000",
        "tx-power": "-40.0000,-40.0000,-40.0000,-40.0000,-inf,-inf,-inf,-inf",
        "type": "QSFP-DD",
        "vendor": "DELL EMC",
        "vendor-part": "6MGDY",
        "voltage": "3.2910",
        "warning-rx-power-hi": "4.5000",
        "warning-rx-power-lo": "-6.8994",
        "warning-temp-hi": "70.0000",
        "warning-temp-lo": "-5.0000",
        "warning-tx-bias-hi": "125.0000",
        "warning-tx-bias-lo": "15.0000",
        "warning-tx-power-hi": "4.5000",
        "warning-tx-power-lo": "-2.8997",
        "warning-volt-hi": "3.4650",
        "warning-volt-lo": "3.1350"
      }
    }
  ]
}
```

### 3.3.2 Configuration Commands

The DOM command is only available in EXEC-mode, none of command is available in CONFIG-mode.

### 3.3.3 CLICK CLI Show Commands

#### show interfaces transceiver eeprom --dom [EthernetXY]

Example
```
admin@sonic:~$ show interfaces transceiver eeprom --dom Ethernet0
Ethernet0: SFP EEPROM detected
        Application Advertisement:
                1: 400GAUI-8 C2M (Annex 120E) | 400GBASE-DR4 (Cl 124)
                2: 100GAUI-2 C2M (Annex 135G) | 100G-FR/100GBASE-FR1 (Cl 140)
        Connector: SN (Mini CS)
        Identifier: QSFP56-DD
        Implemented Memory Pages:
                Bank 0 Implemented
                Diagnostic Pages Implemented
                Page 03h Implemented
        Length SMF(km): 0.0
        Module State: Ready State
        Revision Compliance: 4.0
        Vendor Date Code(YYYY-MM-DD Lot): 2020-10-07
        Vendor Name: AVAGO
        Vendor OUI: 00-17-6A
        Vendor PN: AFCT-93DRPHZ-AZ2
        Vendor Rev: 01
        Vendor SN: FD2038FG0FY
        ChannelMonitorValues:
                RX1Power: 3.2611dBm
                RX2Power: 2.0782dBm
                RX3Power: -40.0000dBm
                RX4Power: -40.0000dBm
                RX5Power: -infdBm
                RX6Power: -infdBm
                RX7Power: -infdBm
                RX8Power: -infdBm
                TX1Bias: 44.9420mA
                TX1Power: 2.8829dBm
                TX2Bias: 47.4440mA
                TX2Power: 3.0432dBm
                TX3Bias: 47.4440mA
                TX3Power: 2.8959dBm
                TX4Bias: 49.9460mA
                TX4Power: 2.7147dBm
                TX5Bias: 0.0000mA
                TX5Power: -infdBm
                TX6Bias: 0.0000mA
                TX6Power: -infdBm
                TX7Bias: 0.0000mA
                TX7Power: -infdBm
                TX8Bias: 0.0000mA
                TX8Power: -infdBm
        ChannelThresholdValues:
                RxPowerHighAlarm  : 7.5000dBm
                RxPowerHighWarning: 4.4999dBm
                RxPowerLowAlarm   : -10.4001dBm
                RxPowerLowWarning : -6.4016dBm
                TxBiasHighAlarm   : 75.0000mA
                TxBiasHighWarning : 70.0000mA
                TxBiasLowAlarm    : 10.0000mA
                TxBiasLowWarning  : 15.0000mA
                TxPowerHighAlarm  : 6.9999dBm
                TxPowerHighWarning: 3.9999dBm
                TxPowerLowAlarm   : -6.4016dBm
                TxPowerLowWarning : -2.4003dBm
        ModuleMonitorValues:
                Temperature: 27.8203C
                Vcc: 3.2852Volts
        ModuleThresholdValues:
                TempHighAlarm  : 75.0000C
                TempHighWarning: 70.0000C
                TempLowAlarm   : -5.0000C
                TempLowWarning : 0.0000C
                VccHighAlarm   : 3.6300Volts
                VccHighWarning : 3.4650Volts
                VccLowAlarm    : 2.9700Volts
                VccLowWarning  : 3.1350Volts
```

### 3.3.3 KLISH CLI Show Commands

#### show interface transceiver dom summary [Ethernet XY[-XY]]

This command displays the DOM information of one particular port or the ports in a specific range, or all ports when **Ethernet XY** is not specified

- Displaying the DOM information summary of all ports
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
- Displaying the DOM information summary of Ethernet5
```
sonic# show interface transceiver dom summary Ethernet 5

Interface   Type     Vendor           Temp(C) Volt(V) RxPwr(dBm) TxPwr(dBm)
----------- -------- ---------------- ------- ------- ---------- ----------
Ethernet5   SFP      FINISAR CORP.    28.4766 3.3018  -30.9691   -2.1488
```
- Displaying the DOM information summary of Ethernet5-12
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

#### show interface transceiver dom [Ethernet XY[-XY]]

This command displays the DOM and Threshold information of one particular port or the ports in a specific range, or all ports when **Ethernet XY** is not specified

- Displaying the DOM and Threshold information of a port without transceiver attached
```
sonic# show interface transceiver dom Ethernet 0

-----------------------------------------------------------------------
Ethernet0
-----------------------------------------------------------------------
    None detected
```

- Displaying the DOM and Threshold information of a QSFP-DD transceiver without DOM support
```
sonic# show interface transceiver dom Ethernet 0

-----------------------------------------------------------------------
Ethernet0
-----------------------------------------------------------------------
Identifier: QSFP-DD
Vendor Name: Molex
Vendor Part: 2015911005
DOM is not supported
```
Note: In the case of QSFP-DD, both DOM and Threshold are located at upper page 11h, that could be unavailable on certain QSFP-DD transceivers.

- Displaying the DOM and Threshold information of a QSFP transceiver without Threshold support
```
sonic# show interface transceiver dom Ethernet 12

-----------------------------------------------------------------------
Ethernet12
-----------------------------------------------------------------------
Identifier: QSFP28
Vendor Name: Molex
Vendor Part: 1002971051
ChannelMonitorValues:
Rx1Power: -inf dBm
Rx2Power: -inf dBm
Rx3Power: -inf dBm
Rx4Power: -inf dBm
Tx1Bias: 0.0000 mA
Tx2Bias: 0.0000 mA
Tx3Bias: 0.0000 mA
Tx4Bias: 0.0000 mA
Tx1Power: N/A dBm
Tx2Power: N/A dBm
Tx3Power: N/A dBm
Tx4Power: N/A dBm
DOM Threshold is not supported
```
Note: In the case of QSFP, while the threshold is located at upper page 03h, the tx/rx power is located at lower page, that's always readable.

- Displaying the DOM and Threshold information of Ethernet80
```
sonic# show interface transceiver dom Ethernet 80

----------------------------------------------------------------------
Ethernet80
-----------------------------------------------------------------------
    Identifier: QSFP-DD
    Vendor Name: AVAGO
    Vendor Part: AFCT-93DRPHZ-AZ2
    ChannelMonitorValues:
        Rx1Power: -33.9794 dBm
        Rx2Power: -23.2790 dBm
        Rx3Power: -32.2185 dBm
        Rx4Power: -22.8400 dBm
        Rx5Power: -inf dBm
        Rx6Power: -inf dBm
        Rx7Power: -inf dBm
        Rx8Power: -inf dBm
        Tx1Bias: 34.9920 mA
        Tx2Bias: 37.4960 mA
        Tx3Bias: 54.9520 mA
        Tx4Bias: 37.4960 mA
        Tx5Bias: 0.0000 mA
        Tx6Bias: 0.0000 mA
        Tx7Bias: 0.0000 mA
        Tx8Bias: 0.0000 mA
        Tx1Power: 3.0425 dBm
        Tx2Power: 3.1135 dBm
        Tx3Power: 2.7960 dBm
        Tx4Power: 3.0880 dBm
        Tx5Power: -inf dBm
        Tx6Power: -inf dBm
        Tx7Power: -inf dBm
        Tx8Power: -inf dBm
    ChannelThresholdValues:
        RxPowerHighAlarm  : 7.5000 dBm
        RxPowerHighWarning: 4.4999 dBm
        RxPowerLowAlarm   : -10.4001 dBm
        RxPowerLowWarning : -6.4016 dBm
        TxBiasHighAlarm   : 75.0000 mA
        TxBiasHighWarning : 70.0000 mA
        TxBiasLowAlarm    : 10.0000 mA
        TxBiasLowWarning  : 15.0000 mA
        TxPowerHighAlarm  : 6.9999 dBm
        TxPowerHighWarning: 3.9999 dBm
        TxPowerLowAlarm   : -6.4016 dBm
        TxPowerLowWarning : -2.4003 dBm
    ModuleMonitorValues:
        Temperature: 30.7773 C
        Vcc: 3.3027 Volts
    ModuleThresholdValues:
        TempHighAlarm  : 75.0000 C
        TempHighWarning: 70.0000 C
        TempLowAlarm   : -5.0000 C
        TempLowWarning : 0.0000 C
        VccHighAlarm   : 3.6300 Volts
        VccHighWarning : 3.4650 Volts
        VccLowAlarm    : 2.9700 Volts
        VccLowWarning  : 3.1350 Volts
```

- Displaying the DOM and Threshold information of all ports
```
sonic# show interface transceiver dom

-----------------------------------------------------------------------
Ethernet0
-----------------------------------------------------------------------
    None detected

-----------------------------------------------------------------------
Ethernet8
-----------------------------------------------------------------------
    None detected

-----------------------------------------------------------------------
Ethernet16
-----------------------------------------------------------------------
    Identifier: QSFP-DD
    Vendor Name: FIT HON TENG
    Vendor Part: CU4EP54-01000-EF
    DOM is not supported

...... omitted ......
----------------------------------------------------------------------
Ethernet80
-----------------------------------------------------------------------
    Identifier: QSFP-DD
    Vendor Name: AVAGO
    Vendor Part: AFCT-93DRPHZ-AZ2
    ChannelMonitorValues:
        Rx1Power: -33.9794 dBm
        Rx2Power: -23.2790 dBm
        Rx3Power: -32.2185 dBm
        Rx4Power: -22.8400 dBm
        Rx5Power: -inf dBm
        Rx6Power: -inf dBm
        Rx7Power: -inf dBm
        Rx8Power: -inf dBm
        Tx1Bias: 34.9920 mA
        Tx2Bias: 37.4960 mA
        Tx3Bias: 54.9520 mA
        Tx4Bias: 37.4960 mA
        Tx5Bias: 0.0000 mA
        Tx6Bias: 0.0000 mA
        Tx7Bias: 0.0000 mA
        Tx8Bias: 0.0000 mA
        Tx1Power: 3.0425 dBm
        Tx2Power: 3.1135 dBm
        Tx3Power: 2.7960 dBm
        Tx4Power: 3.0880 dBm
        Tx5Power: -inf dBm
        Tx6Power: -inf dBm
        Tx7Power: -inf dBm
        Tx8Power: -inf dBm
    ChannelThresholdValues:
        RxPowerHighAlarm  : 7.5000 dBm
        RxPowerHighWarning: 4.4999 dBm
        RxPowerLowAlarm   : -10.4001 dBm
        RxPowerLowWarning : -6.4016 dBm
        TxBiasHighAlarm   : 75.0000 mA
        TxBiasHighWarning : 70.0000 mA
        TxBiasLowAlarm    : 10.0000 mA
        TxBiasLowWarning  : 15.0000 mA
        TxPowerHighAlarm  : 6.9999 dBm
        TxPowerHighWarning: 3.9999 dBm
        TxPowerLowAlarm   : -6.4016 dBm
        TxPowerLowWarning : -2.4003 dBm
    ModuleMonitorValues:
        Temperature: 30.7773 C
        Vcc: 3.3027 Volts
    ModuleThresholdValues:
        TempHighAlarm  : 75.0000 C
        TempHighWarning: 70.0000 C
        TempLowAlarm   : -5.0000 C
        TempLowWarning : 0.0000 C
        VccHighAlarm   : 3.6300 Volts
        VccHighWarning : 3.4650 Volts
        VccLowAlarm    : 2.9700 Volts
        VccLowWarning  : 3.1350 Volts

...... omitted ......
```
- Displaying the DOM and Threshold information of Ethernet16-80
```
sonic# show interface transceiver dom Ethernet 16-80

-----------------------------------------------------------------------
Ethernet16
-----------------------------------------------------------------------
    Identifier: QSFP-DD
    Vendor Name: FIT HON TENG
    Vendor Part: CU4EP54-01000-EF
    DOM is not supported

-----------------------------------------------------------------------
Ethernet24
-----------------------------------------------------------------------
    None detected

-----------------------------------------------------------------------
Ethernet32
-----------------------------------------------------------------------
    None detected

...... omitted ......
----------------------------------------------------------------------
Ethernet80
-----------------------------------------------------------------------
    Identifier: QSFP-DD
    Vendor Name: AVAGO
    Vendor Part: AFCT-93DRPHZ-AZ2
    ChannelMonitorValues:
        Rx1Power: -33.9794 dBm
        Rx2Power: -23.2790 dBm
        Rx3Power: -32.2185 dBm
        Rx4Power: -22.8400 dBm
        Rx5Power: -inf dBm
        Rx6Power: -inf dBm
        Rx7Power: -inf dBm
        Rx8Power: -inf dBm
        Tx1Bias: 34.9920 mA
        Tx2Bias: 37.4960 mA
        Tx3Bias: 54.9520 mA
        Tx4Bias: 37.4960 mA
        Tx5Bias: 0.0000 mA
        Tx6Bias: 0.0000 mA
        Tx7Bias: 0.0000 mA
        Tx8Bias: 0.0000 mA
        Tx1Power: 3.0425 dBm
        Tx2Power: 3.1135 dBm
        Tx3Power: 2.7960 dBm
        Tx4Power: 3.0880 dBm
        Tx5Power: -inf dBm
        Tx6Power: -inf dBm
        Tx7Power: -inf dBm
        Tx8Power: -inf dBm
    ChannelThresholdValues:
        RxPowerHighAlarm  : 7.5000 dBm
        RxPowerHighWarning: 4.4999 dBm
        RxPowerLowAlarm   : -10.4001 dBm
        RxPowerLowWarning : -6.4016 dBm
        TxBiasHighAlarm   : 75.0000 mA
        TxBiasHighWarning : 70.0000 mA
        TxBiasLowAlarm    : 10.0000 mA
        TxBiasLowWarning  : 15.0000 mA
        TxPowerHighAlarm  : 6.9999 dBm
        TxPowerHighWarning: 3.9999 dBm
        TxPowerLowAlarm   : -6.4016 dBm
        TxPowerLowWarning : -2.4003 dBm
    ModuleMonitorValues:
        Temperature: 30.7773 C
        Vcc: 3.3027 Volts
    ModuleThresholdValues:
        TempHighAlarm  : 75.0000 C
        TempHighWarning: 70.0000 C
        TempLowAlarm   : -5.0000 C
        TempLowWarning : 0.0000 C
        VccHighAlarm   : 3.6300 Volts
        VccHighWarning : 3.4650 Volts
        VccLowAlarm    : 2.9700 Volts
        VccLowWarning  : 3.1350 Volts

...... omitted ......
```

# 4 Flow Diagrams

None

# 5 Serviceability and Debug

#### sfputil show eeprom --dom -p EthernetXY

In the SONiC design, the DOM information will be gathered by the pmon#xcvrd with 60 seconds interval, hence the DOM readings may or may not be up-to-date.
In order to have DB access isolated when investigating a defect, it's better to directly decode the DOM registers and check the results.

Example:

```
admin@sonic:~$ sudo sfputil show eeprom --dom -p Ethernet80
Ethernet80: SFP EEPROM detected
        Connector: N/A
        application_advertisement: {1: {'host_if': '400GAUI-8 C2M (Annex 120E)', 'media_if': '400GBASE-DR4 (Cl 124)'}, 2: {'host_if': '100GAUI-2 C2M (Annex 135G)', 'media_if': '100G-FR/100GBASE-FR1 (Cl 140)'}}
        cable_length: N/A
        cable_type: N/A
        diag_caps_loopback: ['Host Side Input Loopback', 'Media Side Input Loopback']
        diag_caps_pattern: ['Gating > 20 ms', 'Latched Error Information', 'Real-time BER Error Count', 'Auto Restart']
        diag_caps_pattern_chk_host: ['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']
        diag_caps_pattern_chk_media: ['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']
        diag_caps_pattern_gen_host: ['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']
        diag_caps_pattern_gen_media: ['PRBS-31Q', 'PRBS-23Q', 'PRBS-15Q', 'PRBS-13Q', 'PRBS-9Q', 'PRBS-7Q', 'SSPRQ']
        diag_caps_report: ['Media side SNR measurement', 'Host side SNR measurement', 'BER Error Count/Total Bits', 'BER register']
        encoding: N/A
        ext_identifier: N/A
        ext_rateselect_compliance: N/A
        hardwarerev: 01
        manufacturename: AVAGO
        media_type: sm_media_interface
        memory_pages: ['Diagnostic Pages Implemented', 'Page 03h Implemented', 'Bank 0 Implemented']
        modelname: AFCT-93DRPHZ-AZ2
        module_state: Ready State
        nominal_bit_rate: N/A
        power_class: Unknown
        revision_compliance: 4.0
        serialnum: FD2038FG0FK
        specification_compliance: N/A
        type: QSFP-DD Double Density 8X Pluggable Transceiver
        type_abbrv_name: QSFP-DD
        vendor_date: N/A
        vendor_oui: N/A
        rx1power: -33.9794dBm
        rx2power: -23.2790dBm
        rx3power: -32.2185dBm
        rx4power: -22.8400dBm
        rx5power: -infdBm
        rx6power: -infdBm
        rx7power: -infdBm
        rx8power: -infdBm
        temperature: 31.1562C
        tx1bias: 34.9920mA
        tx1power: 2.9671dBm
        tx2bias: 37.4960mA
        tx2power: 3.1823dBm
        tx3bias: 54.9520mA
        tx3power: 2.7690dBm
        tx4bias: 37.4960mA
        tx4power: 3.0576dBm
        tx5bias: 0.0000mA
        tx5power: -infdBm
        tx6bias: 0.0000mA
        tx6power: -infdBm
        tx7bias: 0.0000mA
        tx7power: -infdBm
        tx8bias: 0.0000mA
        tx8power: -infdBm
        voltage: 3.3037Volts

``` 

#### sfputil dump -p EthernetXY

Please note the DOM software actually does nothing but decode the values provided by the attached transceiver, the DOM information displayed could be insane if the values provided by the transceiver are incorrect.
In order to narrow down the root cause to either a software issue in the SFP parser or the hardware in the attached transceiver, it's desirable to dump the raw registers.

Example:
```
admin@sonic:~$ sudo sfputil dump -p Ethernet80
Ethernet80:
00000000  18 40 00 06 ff 00 00 00  00 00 00 00 00 00 1f 08 |.@..............|
00000010  81 03 0e 40 10 68 00 00  00 00 00 00 00 00 00 00 |...@.h..........|
00000020  00 ff 00 00 00 00 00 50  18 00 00 00 00 00 00 00 |.......P........|
00000030  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
00000040  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
00000050  00 00 00 00 00 02 11 1c  84 01 0d 15 21 55 ff 00 |............!U..|
00000060  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
00000070  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
00000080  18 41 56 41 47 4f 20 20  20 20 20 20 20 20 20 20 |.AVAGO          |
00000090  20 00 17 6a 41 46 43 54  2d 39 33 44 52 50 48 5a | ..jAFCT-93DRPHZ|
000000a0  2d 41 5a 32 30 31 46 44  32 30 33 38 46 47 30 46 |-AZ201FD2038FG0F|
000000b0  4b 20 20 20 20 20 32 30  31 30 30 37 20 20 20 20 |K     201007    |
000000c0  20 20 20 20 20 20 20 20  a0 30 00 26 00 00 00 00 |        .0.&....|
000000d0  00 00 f0 00 06 00 00 00  00 00 00 00 00 00 dc 00 |................|
000000e0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
000000f0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
```


#### 5.1 Debugging SFP DOM information

##### Tx/Rx power looks suspicious

If the Tx/Rx power does not look good, please try the instructions below

1. Dump the address A2h
```
admin@sonic:~$ sudo sfputil dump -p Ethernet15 -s 0x100
Ethernet15:
00000100  4e 00 f3 00 49 00 f8 00  90 88 71 48 8c a0 75 30 |N...I.....qH..u0|
00000110  17 0c 07 d0 15 18 09 c4  20 7e 09 d0 19 cf 0c 5a |........ ~.....Z|
00000120  27 10 00 64 1f 07 00 9e  00 00 00 00 00 00 00 00 |'..d............|
00000130  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
00000140  00 00 00 00 3f 80 00 00  00 00 00 00 01 00 00 00 |....?...........|
00000150  01 00 00 00 01 00 00 00  01 00 00 00 00 00 00 ff |................|
00000160  1d 62 80 82 0f 7c 19 d1  00 04 00 00 00 00 32 00 |.b...|........2.|
00000170  00 40 00 00 02 40 00 00  ff ff ff ff ff ff ff 01 |.@...@..........|
00000180  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
00000190  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
000001a0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
000001b0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
000001c0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
000001d0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
000001e0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
000001f0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
```

2. Manually decode the Tx/Rx power
```
txpower = ((REG(0x166) << 8 | REG(0x167)) * 0.0001) mW
rxpower = ((REG(0x168) << 8 | REG(0x169)) * 0.0001) mW
```

3. Convert mW to dBm
```
if mW == 0:
    dBm = '-inf'
elif mW < 0:
    dBm = 'NaN'
else:
    dBm = 10.0 * log10(mW)
```

4. Double check with the output of 'sfputil show eeprom --dom'

#### 5.2 Debugging QSFP DOM information

##### Tx/Rx power looks suspicious

If the Tx/Rx power does not look good, please try the instructions below

1. Dump the lower page
```
admin@sonic:~$ sudo sfputil dump -p Ethernet72
Ethernet72:
00000000  11 08 02 01 00 01 00 00  00 50 00 00 00 00 00 00 |.........P......|
00000010  00 00 00 00 00 00 24 3b  00 00 81 05 00 00 00 00 |......$;........|
00000020  00 00 00 00 00 00 00 00  00 00 b4 7e 00 00 00 00 |...........~....|
00000030  00 00 46 4f 00 00 00 00  00 00 00 00 00 00 00 00 |..FO............|
00000040  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
00000050  00 00 00 00 00 00 00 00  00 00 00 00 00 04 00 00 |................|
00000060  00 00 ff 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
00000070  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
00000080  11 ce 07 80 00 00 00 00  00 00 00 08 ff 00 02 00 |................|
00000090  00 00 00 64 46 49 54 20  20 20 20 20 20 20 20 20 |...dFIT         |
000000a0  20 20 20 20 00 ec 01 e2  41 46 43 54 2d 38 39 53 |    ....AFCT-89S|
000000b0  46 44 5a 2d 41 5a 31 20  30 31 66 58 05 14 00 69 |FDZ-AZ1 01fX...i|
000000c0  25 0b 35 da 46 44 31 39  34 32 41 5a 32 52 54 20 |%.5.FD1942AZ2RT |
000000d0  20 20 20 20 31 39 31 30  32 31 20 20 3c 12 67 cf |    191021  <.g.|
000000e0  00 12 00 0f 01 01 02 b2  01 01 02 b0 00 00 00 00 |................|
000000f0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
```
2. Manually decode the Tx/Rx power
```
tx1power = ((REG(0x32) << 8 | REG(0x33)) * 0.0001) mW
tx2power = ((REG(0x34) << 8 | REG(0x35)) * 0.0001) mW
tx3power = ((REG(0x36) << 8 | REG(0x37)) * 0.0001) mW
tx4power = ((REG(0x38) << 8 | REG(0x39)) * 0.0001) mW

rx1power = ((REG(0x22) << 8 | REG(0x23)) * 0.0001) mW
rx2power = ((REG(0x24) << 8 | REG(0x25)) * 0.0001) mW
rx3power = ((REG(0x26) << 8 | REG(0x27)) * 0.0001) mW
rx4power = ((REG(0x28) << 8 | REG(0x29)) * 0.0001) mW
```
3. Convert mW to dBm
```
if mW == 0:
    dBm = '-inf'
elif mW < 0:
    dBm = 'NaN'
else:
    dBm = 10.0 * log10(mW)
```
4. Double check with the output of 'sfputil show eeprom --dom'

##### "DOM Threshold is not supported" is displayed

If the "DOM Threshold is not supported" is displayed, please try the instructions below

1. Dump the lower page
```
admin@sonic:~$ sudo sfputil dump -p Ethernet124
Ethernet124:
00000000 11 08 06 00 00 00 00 00 00 00 00 00 00 00 00 00 |................|
00000010 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 |................|
00000020 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 |................|
00000030 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 |................|
00000040 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 |................|
00000050 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 |................|
00000060 00 00 00 00 00 00 00 00 00 00 00 00 00 02 30 00 |..............0.|
00000070 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 |................|
00000080 11 00 23 88 00 00 00 00 00 00 00 00 ff 00 00 00 |..#.............|
00000090 00 16 01 a0 54 45 20 43 6f 6e 6e 65 63 74 69 76 |....TE Connectiv|
000000a0 69 74 79 20 00 00 22 a7 32 38 32 31 39 38 37 2d |ity ..".2821987-|
000000b0 32 20 20 20 20 20 20 20 31 20 04 05 07 0b 00 33 |2 1 .....3|
000000c0 0b 00 00 00 31 38 32 35 39 30 32 32 20 20 20 20 |....18259022 |
000000d0 20 20 20 20 31 38 30 36 32 38 30 30 00 00 d4 15 | 18062800....|
000000e0 66 61 20 20 20 20 20 20 20 20 20 20 20 20 20 20 |fa |
000000f0 20 20 00 00 00 00 00 00 00 00 00 00 00 00 00 00 | ..............|
```

2. Check the BIT2 of REG(0x02), the upper page 03h will be unavailable if BIT2=1, hence the "DOM Threshold is not supported" will be displayed.

#### 5.3 Debugging QSFP-DD DOM information

##### Tx/Rx power looks suspicious

If the Tx/Rx power does not look good, please try the instructions below

1. Dump the page 11h
```
admin@sonic:~$ sudo sfputil dump -p Ethernet0 -s 0x900 -l 128
Ethernet0:
00000900  44 44 44 44 0f 00 00 00  00 00 00 00 00 00 00 00 |DDDD............|
00000910  00 00 00 0c 0c 00 0c 00  0c 00 4b de 4e 52 4c 18 |..........K.NRL.|
00000920  48 fc 00 00 00 00 00 00  00 00 57 c7 5c aa 5c aa |H.........W.\.\.|
00000930  61 8d 00 00 00 00 00 00  00 00 47 53 3f f5 00 01 |a.........GS?...|
00000940  00 01 00 00 00 00 00 00  00 00 00 00 00 00 20 20 |..............  |
00000950  24 24 28 28 2c 2c ff 00  00 00 00 00 00 ff ff 33 |$$((,,.........3|
00000960  33 33 33 22 22 22 22 22  22 22 22 00 00 00 00 00 |333"""""""".....|
00000970  11 12 13 14 00 00 00 00  11 12 13 14 00 00 00 00 |................|
```
2. Manually decode the Tx/Rx power
```
tx1power = ((REG(0x91a) << 8 | REG(0x91b)) * 0.0001) mW
tx2power = ((REG(0x91c) << 8 | REG(0x91d)) * 0.0001) mW
tx3power = ((REG(0x91e) << 8 | REG(0x91f)) * 0.0001) mW
tx4power = ((REG(0x920) << 8 | REG(0x921)) * 0.0001) mW
tx5power = ((REG(0x922) << 8 | REG(0x923)) * 0.0001) mW
tx6power = ((REG(0x924) << 8 | REG(0x925)) * 0.0001) mW
tx7power = ((REG(0x926) << 8 | REG(0x927)) * 0.0001) mW
tx8power = ((REG(0x928) << 8 | REG(0x929)) * 0.0001) mW

rx1power = ((REG(0x93a) << 8 | REG(0x93b)) * 0.0001) mW
rx2power = ((REG(0x93c) << 8 | REG(0x93d)) * 0.0001) mW
rx3power = ((REG(0x93e) << 8 | REG(0x93f)) * 0.0001) mW
rx4power = ((REG(0x940) << 8 | REG(0x941)) * 0.0001) mW
rx5power = ((REG(0x942) << 8 | REG(0x943)) * 0.0001) mW
rx6power = ((REG(0x944) << 8 | REG(0x945)) * 0.0001) mW
rx7power = ((REG(0x946) << 8 | REG(0x947)) * 0.0001) mW
rx8power = ((REG(0x948) << 8 | REG(0x949)) * 0.0001) mW
```
3. Convert mW to dBm
```
if mW == 0:
    dBm = '-inf'
elif mW < 0:
    dBm = 'NaN'
else:
    dBm = 10.0 * log10(mW)
```
4. Double check with the output of 'sfputil show eeprom --dom'

##### "DOM is not supported" is displayed

If the "DOM is not supported" is displayed, please try the instructions below

1. Dump the lower page
```
admin@sonic:~$ sudo sfputil dump -p Ethernet144
Ethernet144:
00000000  18 30 80 00 00 00 00 00  00 00 00 00 00 00 00 00 |.0..............|
00000010  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
00000020  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
00000030  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
00000040  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
00000050  00 00 00 00 00 03 18 01  88 01 00 00 00 01 00 00 |................|
00000060  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
00000070  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
00000080  18 46 49 54 20 48 4f 4e  20 54 45 4e 47 20 20 20 |.FIT HON TENG   |
00000090  20 ec 01 e2 43 55 34 45  50 35 34 2d 30 31 30 30 | ...CU4EP54-0100|
000000a0  30 2d 45 46 41 30 4e 30  31 30 5a 38 33 35 30 31 |0-EFA0N010Z83501|
000000b0  30 30 30 33 30 20 31 38  30 38 32 37 20 20 00 00 |00030 180827  ..|
000000c0  00 00 00 00 00 00 00 00  00 01 41 23 06 08 0c 00 |..........A#....|
000000d0  00 00 00 02 0a 00 00 00  00 00 00 00 00 00 00 00 |................|
000000e0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
000000f0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00 |................|
```

2. Check the BIT7 of REG(0x02), the upper page 11h will be unavailable if BIT7=1, hence the "DOM is not supported" will be displayed.

# 6 Warm Boot Support

None

# 7 Scalability

None

# 8 Unit Test

- Verify 'show interface transceiver dom' with all ports
- Verify 'show interface transceiver dom' with a specific port
- Verify 'show interface transceiver dom' with a specific range
- Verify 'show interface transceiver dom' with an invalid port
- Verify 'show interface transceiver dom' with an invalid range
- Verify 'show interface transceiver dom summary' with all ports
- Verify 'show interface transceiver dom summary' with a specific port
- Verify 'show interface transceiver dom summary' with a specific range
- Verify 'show interface transceiver dom summary' with an invalid port
- Verify 'show interface transceiver dom summary' with an invalid range
