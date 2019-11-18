
# Feature Name
Open Config Platform Model Support in SONiC

## Table of Contents
 * [Revision](#revision) 
 * [About This Manual](#about-this-manual)
 * [Requirements Overview](#1-requirements-overview)
	* [Open Config Platform Model Support](#1_1-open-config-platform-model-support)
 * [Design](#2-design)
	* [Platform daemons and Utils](#2_1-platform-daemons-and-utils)
	* [DB Schema for Platform related data](#2_2-db-schema-for-platform-related-data)
	* [SONiC Plugins](#2_3-sonic-plugins)
	* [SONiC CLI (Click-based) Support](#2_4-sonic-cli-click-based-support)

# Revision <a name="revision"></a>
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 07/17/2019  |  Babu Rajaram     | Initial version                   |
| 0.2 | 08/23/2019  |  Syd Logan        | Describe CLI support              |
| 0.3 | 11/18/2019  |  Fuzail Khan      | Daemon Changes                    |

# About this Manual <a name="about-this-manual"></a>
This document describes the support for open config platform models in SONiC, based on the new platform APIs. This support is based on the 2.0 platform model APIs, and follows the highlighted design in the new PMON enhancement design here:
https://github.com/Azure/SONiC/blob/master/doc/pmon/pmon-enhancement-design.md]

## 1 Requirements Overview <a name="1-requirements-overview"></a>
Support Open Config Platform data models in SONiC for the following components:
    - System EEPROM
    - FAN	
    - PSU (Power supply units)

### 1.1 Open Config Platform Model Support <a name="1_1-open-config-platform-model-support"></a> 
Support a subset of open config platform attributes for the following components:
    - System EEPROM
	    - base_mac_addr     : base mac address from syseeprom
	    - mac_addr_num      : mac address numbers from syseeprom
	    - manufacture_date  : manufacture date from syseeprom 	
	    - manufacturer      : manufacturer from syseeprom 	
	    - platform_name     : platform name from syseeprom 	
	    - onie_version      : onie version from syseeprom

    - FAN
        - name : Name of the fan
        - model : Model/Part number of the fan
        - serial : Serial number of the fan
	    - status : Operational state of Fan
	    - speed : Fan speed in RPM 
	    - direction : Fan Airflow direction (Intake/Exhaust)
	    - presence : Fan presence check
        - target_speed : Expected speed of the fan in percentage
        - speed_tolerance : Speed tolerance of the fan in percentage of variance from target speed
    - PSU
	    - status : Operational state of PSU
        - status_led : State of the PSU status LED
	    - presence : PSU presence check
	    - model : PSU model name
	    - serial : PSU serial number
	    - mfr_id : Manufacturer Id of PSU
	    - output_voltage  : Output voltage in mV
	    - output_current : Output current in mA
	    - output_power : Output power in mW
	    - fan : Name of the PSU-fan
        - num_fans : Number of fan modules available on the PSU
	    - fan_status : Operational state of Fan
	    - fan_speed : Fan speed in RPM 
	    - fan_direction : Fan Airflow direction (Intake/Exhaust)

These attributes are part of new platform APIs. 


## 2 Design <a name="2-design"></a>
2.0 platform APIs support many attributes for a component. Daemons are enhanced to store all these attribute information in state database. Since not all the platforms supports 2.0 APIs, daemons are modified to support fetching the info from both 2.0 or 1.0 platform API implementation. Show CLIs are provided to read this info from state database and print it for the users.


### 2.1 Platform daemons and Utils <a name="2_1-platform-daemons-and-utils"></a>
Currently, platform daemons support both 1.0 as well as 2.0 platform APIs. The reason for this kind of design is that not all the platforms have adopted the 2.0 platform APIs implementation.

Following new daemons would be added:
    - fand - Poll and populate the FAN attributes

Following daemons would be modified to support newer attributes
    - psud - PSU daemon

Following util would be added to support System EEPROM:
    - sysupdate.py - To populate System EEPROM data in DB. Since this is a static data, this will be initialized during pmon start up. 

### 2.2 DB Schema for Platform related data <a name="2_2-db-schema-for-platform-related-data"></a>
The DB schema proposed in this design would be leveraged. Any new additions are highlighted below:

#### 2.2.1 Chassis Table

The chassis table would be used to store the System EEPROM information as below:
```
; Defines information for a chassis
key                     = CHASSIS_INFO|chassis_name      ; infomation for the chassis
; field                 = value
model                   = STRING                         ; model number from syseeprom
serial_num              = STRING                         ; serial number from syseeprom
part_num                = STRING                         ; Part number from syseeprom
base_mac_addr           = STRING                         ; base mac address from syseeprom
product_name            = STRING                         ; product name from syseeprom
mac_addr_num            = INT                            ; mac address numbers from syseeprom
manufacture_date        = STRING                         ; manufature date from syseeprom
manufacturer            = STRING                         ; manufacturer from syseeprom
platform_name           = STRING                         ; platform name from syseeprom
hardware_version        = STRING                         ; Hardware revision (Label Revision) from syseeprom
onie_version            = STRING                         ; onie version from syseeprom
crc32_checksum          = INT                            ; CRC-32 checksum from syseeprom

```


#### 2.2.2 Fan Table
This table would be used to store the FAN related information. All the supported attributes in 2.0 platform API woule be stored. Information regarding PSU fans are stored in 'PSU_FAN_INFO' table.
```
; Defines information for a fan
key                     = FAN_INFO|fan_name              ; information for the fan
; field                 = value
name                    = STRING                         ; Name of the FAN
model                   = STRING                         ; model name of the fan
serial                  = STRING                         ; serial numeber of the fan
presence                = BOOLEAN                        ; presence of the fan
status                  = BOOLEAN                        ; status of the fan
direction               = STRING                         ; direction of the fan
speed                   = INT                            ; fan speed in RPM
speed_tolerance         = INT                            ; speed tolerance in percentage of variance from target speed
target_speed            = INT                            ; expected speed of fan in percentage
```
#### 2.2.3 PSU Table
This table would be used to store the PSU information. All the attributes supported in 2.0 platform API would be stored. 

```
; Defines information for a PSU
key                     = PSU_INFO|psu_name            ; information for the PSU
; field                 = value
presence                = BOOLEAN                      ; presence of the fan
status                  = BOOLEAN                      ; status of the fan
mfr_id					= STRING                       ; Manufacturer Id of the PSU
model					= STRING                       ; Model/part number of the PSU
serial					= STRING                       ; Serial Number of the PSU
output_voltage          = FLOAT                        ; Output voltage in mV
output_current          = FLOAT                        ; Output current in mA
output_power            = FLOAT                        ; Output power in mW
num_fans                = INT                          ; Number of fan modules available on the PSU
```

PSU Fan related information shall be stored in the PSU_FAN table.

### 2.3 SONiC Device Object Classes <a name="2_3-sonic-device-object-classes"></a>
SONiC 2.0 platform APIs uses the base classes for various components of the system:

    - PsuBase
    - FanBase

We added the following methods in **FanBase** and **PsuBase** respectively as 2.0 platform APIs 
do not provide them.

```
    def get_speed_rpm(self):
        """
        Retrieves the speed of fan in RPM

        Returns:
            An integer, Speed of fan in RPM
        """
```

```
    def get_mfr_id(self):
        """
        Retrieves the manufacturer id of the device

        Returns:
            string: Manufacturer Id of device
        """
```

### 2.4 SONiC CLI (Click-based) Support <a name="2_4-sonic-cli-click-based-support"></a>

The following "show" CLI commands are added to support display of
psu and fan data. These commands obtain the data displayed directly
from the Redis DB as described above. The commands are:

    - show platform psusummary
    - show platform fanstatus

#### 2.4.1 show platform psusummary

Usage: 

```
show platform psusummary
```

On platforms that do not support the additional PSU summary attributes, the following would be displayed:

```
root@sonic:~# show platform psusummary
PSU 1: OK
Manufacturer Id: None
Model: None
Serial Number: None
Output Voltage (mV): None
Output Current (mA): None
Output Power (mW): None
Fan Direction: None
Fan Speed (RPM): None

PSU 2: NOT OK
root@sonic:~# 
```

When supported:

```
root@sonic:~# show platform psusummary
PSU 1: OK
Manufacturer Id: 3Y POWER
Model: YM-2651
Serial Number: SA250N091714082869
Output Voltage (mV): 11953.0
Output Current (mA): 9984.0
Output Power (mW): 117000.0
Fan Direction: INTAKE
Fan Speed (RPM): 4896

PSU 2: NOT OK
root@sonic:~# 
```

Note that in the above, PSU 1 is present and has a status of 'true' in 
the database, while PSU 2 is present but has a status of 'false'.

#### 2.4.2 show platform fanstatus

Usage:

```
show platform fanstatus
```

On platforms that do not support the Fan plugin, the command generates no output:

```
root@sonic:~# show platform fanstatus
root@sonic:~# 
```

When supported, a list of fans and associated status are displayed:

```
root@sonic:~# show platform fanstatus
FAN    Status    Speed (RPM)          Direction
-----  --------  -------------------  -----------
FAN 1  OK        7700                 INTAKE
FAN 2  OK        7100                 INTAKE
FAN 3  OK        7700                 INTAKE
FAN 4  OK        7100                 INTAKE
FAN 5  OK        7600                 INTAKE
FAN 6  OK        6900                 INTAKE
FAN 7  OK        7500                 INTAKE
FAN 8  OK        6900                 INTAKE
FAN 9  OK        7800                 INTAKE
FAN 10 OK        7000                 INTAKE
FAN 11 OK        7700                 INTAKE
FAN 12 OK        6900                 INTAKE

```

