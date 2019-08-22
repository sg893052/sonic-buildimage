# Feature Name
Open Config Platform Model Support in SONiC

## Table of Contents
 * [Revision](#revision)
 * [About This Manual](#about-this-manual)
 * [Requirements Overview](#1-requirements-overview)
	 * [Open Config Platform Model Support](#1_1-open-config-platform-model-support)
*	[Design](#2-design)
	*	[Platform daemons and Utils](#2_1-platform-daemons-and-utils)
	*	[DB Schema for Platform related data](#2_2-db-schema-for-platform-related-data)
	*	[SONiC Plugins](#2_3-sonic-plugins)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 07/17/2019  |  Babu Rajaram     | Initial version                   |

# About this Manual
This document describes the support for open config platform models in SONiC, based on the old platform APIs. This support is based on the enhanced 1.0 platform model APIs, and follows the highlighted design in the new PMON enhancement design here:
https://github.com/Azure/SONiC/blob/master/doc/pmon/pmon-enhancement-design.md]

## 1 Requirements Overview
Support Open Config Platform data models in SONiC for the following components:
 - System EEPROM
 - FAN	
 - PSU (Power supply units)
 - Transceivers
### 1.1 Open Config Platform Model Support
Support a subset of open config platform attributes for the following components:
 - System EEPROM
	- base_mac_addr     : base mac address from syseeprom
	- mac_addr_num      : mac address numbers from syseeprom
	- manufacture_date  : manufacture date from syseeprom 	
	- manufacturer      : manufacturer from syseeprom 	
	- platform_name     : platform name from syseeprom 	
	- onie_version      : onie version from syseeprom

 - FAN
	- status : Operational state of Fan
	- speed : Fan speed in RPM 
	- speed_rear : Rear Fan speed in RPM 
	- direction : Fan Airflow direction (Intake/Exhaust)
	- presence : Fan presence check
 - PSU
	- status : Operational state of PSU
	- presence : PSU presence check
	- model : PSU model name
	- serial : PSU serial number
	- mfr_id : Manufacturer Id of PSU
	- output_voltage  : Output voltage in mV
	- output_current : Output current in mA
	- output_power : Output power in mW
	- fan : Name of the PSU-fan
	- fan_status : Operational state of Fan
	- fan_speed : Fan speed in RPM 
	- fan_direction : Fan Airflow direction (Intake/Exhaust)
-	Transceiver
<**TBD**> Need more time to analyze the open config model and the SONiC capabilities. Requires all round analysis.

## 2 Design
The design will follow the design proposed here:
[https://github.com/Azure/SONiC/blob/master/doc/pmon/pmon-enhancement-design.md](https://github.com/Azure/SONiC/blob/master/doc/pmon/pmon-enhancement-design.md)

### 2.1 Platform daemons and Utils
Currently, these platform daemons would rely on the 1.0 platform APIs. In future, these would be migrated to the newer 2.0 platform APIs

Following new daemons would be added:
	-	fand - Poll and populate the FAN attributes

Following daemons would be modified to support newer attributes
	- psud - PSU daemon
	- xcvrd - Transceiver daemon

Following util would be added to support System EEPROM:
	- syseeprom_db_util - To populate System EEPROM data in DB. Since this is a static data, this can be initialized once pmon start up. 

### 2.2 DB Schema for Platform related data
The DB schema proposed in this design would be leveraged. Any new additions are highlighted below:

#### 2.2.1 Chassis Table
This chassis table would be used to store the System EEPROM information identified above

#### 2.2.2 Fan Table
This table would be used to store the FAN related information. This includes both Fan, and PSU Fans. Following attributes will be leveraged from PMON enhancement design.  
```
; Defines information for a fan
key                     = FAN_INFO|fan_name              ; information for the fan
; field                 = value
presence                = BOOLEAN                        ; presence of the fan
status                  = BOOLEAN                        ; status of the fan
direction               = STRING                         ; direction of the fan
speed                   = INT                            ; fan speed in RPM (front fan in case of 2-fan tray)
speed_rear              = INT                            ; rear fan speed in RPM
```
#### 2.2.3 PSU Table
This table would be used to store the PSU information. Following attributes shall be leveraged. 

```
; Defines information for a PSU
key                     = PSU_INFO|psu_name              ; information for the PSU
; field                 = value
presence                = BOOLEAN                        ; presence of the fan
status                  = BOOLEAN                        ; status of the fan
```
Following attributes are proposed to be added to the PMON enhancement design:
```
; field                 = value
mfr_id					= STRING                        ; Manufacturer Id of the PSU
output_voltage          = STRING                        ; Output voltage in mV
output_current          = STRING                        ; Output current in mA
output_power            = STRING                        ; Output power in mW
```
Following PSU Fan related information shall be stored in the FAN table
```
; Defines information for a PSU fan
key                     = PSU_FAN_INFO|fan_name          ; information for the fan
; field                 = value
presence                = BOOLEAN                        ; presence of the PSU-fan
status                  = BOOLEAN                        ; status of the PSU-fan
direction               = STRING                         ; direction of the PSU-fan
speed                   = INT                            ; PSU-fan speed in RPM
```
### 2.3 SONiC Plugins
SONiC 1.0 platform APIs  support only the following plugins:

-	PsuBase
-	SfpUtilBase

SONiC 2.0 platform APIs support newer plugins. However to support platforms implementing 1.0 APIs, following changes are proposed:

-	FanBase - Add a new FanBase plugin to support Fan APIs
-	PsuBase - Define new set of APIs to be implemented. Since it would break other platforms, new APIs added would not be abstract methods

#### 2.3.1 FanBase
```
class FanBase(object):
    @abc.abstractmethod
    def get_num_fans(self):
    
    @abc.abstractmethod
    def get_status(self, index):
    
    @abc.abstractmethod
    def get_presence(self, index):
    
    @abc.abstractmethod
    def get_direction(self, index):
    
    @abc.abstractmethod
    def get_speed(self, index):
    
    @abc.abstractmethod
    def get_speed_rear(self, index):

    @abc.abstractmethod
    def set_speed(self, val):
```

#### 2.3.2 PsuBase
```
class PsuBase:

    @abc.abstractmethod
    def get_num_psus(self):

    @abc.abstractmethod
    def get_psu_status(self, index):

    @abc.abstractmethod
    def get_psu_presence(self, index):
    
	; New APIs
	
    def get_model(self, idx):

    def get_mfr_id(self, idx):

    def get_serial(self, idx):

    def get_output_voltage(self, idx):

    def get_output_current(self, idx):

    def get_output_power(self, idx):

    def get_fan_rpm(self, psu_idx, fan_index):
	
    def get_direction(self, idx):
	
   ```

