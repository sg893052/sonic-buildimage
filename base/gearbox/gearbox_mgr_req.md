# Feature Name
SONiC Gearbox Manager
# Functional Requirements
#### Rev 0.3

## Overview

Provide configuration and monitoring capabilities for External PHYs utilizing the new OCP External PHY Abstraction Interface.

## High-level requirements

- Introduce configuration schema supporting external PHY configurations
- Gearbox Manager to administer CRUD services for PHY configuration
- Gearbox Manager to administer Re-timer configuration
- Necessary code changes providing state and statistics as well
- Coordinates port configuration between system-side (MAC) and External PHY.
- Abstracts the system side configuration from the SONiC application.  For example, port enable affects only the line side.
- Support removable line cards with any PHY configuration
- Warmboot support

## Detailed requirements

### Overview

The Ethernet switches of today have evolved and are entering several key segments providing switch chips for enterprise, data-center, and carrier, as well as optical transceivers, Gearbox PHYs and 25Gbps Re-timers.

If the platform/hardware supports it, the PHY may be configurable to speeds including 10G, 25G, 40G, 50G, and 100G, and beyond. Some platforms contain and external PHY, while others have PHYs embedded in the switch ASIC (Internal PHY). An External PHY is used to serve different purposes like gearbox, Re-timer, MACSEC and multi gigabit Ethernet PHY transceivers, etc. 

The Abstraction Interface contains a set of SAI APIs providing new functionality in support of the most recent PHY advancements and requirements.

Through utilizing this new external PHY interface, this project adds configuration and management capabilities for these new external PHY requirements.

#### Fundamental Functional Requirements

| Id | Requirement                                                    |
|----|----------------------------------------------------------------|
| 1  | PHY support is vendor agnostic                                 |
| 2  | Support Firmware load type and load methods                    |
| 3  | System-side abstraction (applications aware of line-side only) |
| 4  | Platform specific External PHY configuration (gearbox.ini)     |
| 5  | Orchagent notification upon PHY configuration completion       |
| 6  | Utilizes new OCP External SAI Interface                        |
| 7  | Dedicated PHY SAI stack                                        |
   
#### External PHY Configuration Requirements
Based on the available PHY capabilities and their associated interfaces, the following PHY configurations may be available;

| Id | Requirement            | Notes                                  | MAC/Line | Dynamic |
|----|------------------------|----------------------------------------|----------|---------|
| 8  | Lane Mapping           | System side                            | MAC      | No      |
| 9  | Lane Mapping           | Line side                              | Line     | Yes     |
| 10 | Lane Polarity Settings | Set/Get on line side for Tx and Rx     | Both     | No      |
| 11 | Pre-emphasis Settings  | System side                            | MAC      | No      |
| 12 | Pre-emphasis Settings  | Line side                              | Line     | Yes     |
| 13 | PHY Enable/Disable     | Enable PHY is needed to transmit       | Line     | Yes     |
| 14 | Interface Settings     | Typically;  Speed, Type, Clock, Aux    | MAC      | No      |
| 15 | Interface Settings     | Typically;  Speed, Type, Clock, Aux    | Line     | Yes     |
| 16 | Auto Negotiation       | Enable, Disable                        | MAC      | No      |
| 17 | Auto Negotiation       | Enable, Disable                        | Line     | yes     |
| 18 | FEC                    | Forward Error Correction               | Mac      | No      |
| 19 | FEC                    | Forward Error Correction               | Line     | yes     |
| 20 | Loopback               | Various types; Remote, Local, Digital  | Line     | yes     |
| 21 | EEE                    | Not supported at this time             | -        | -       |
| 22 | SyncE                  | Not supported at this time             | -        | -       |
| 23 | PTP                    | Not supported at this time             | -        | -       |
| 24 | MACSEC                 | Not part of the External PHY Interface | Line     | Future  |
| 25 | Fanout                 | Break-out capability                   | Line     | Future  |
| 26 | Cable Diagnostics      | Not supported at this time             | -        | -       |
| 27 | PRBS                   | Not supported at this time             | -        | -       |

#### External PHY State Requirements

| Id | Requirement            | Notes                                          |
|----|------------------------|------------------------------------------------|
| 28 | Display PHY ID         | -                                              |
| 29 | Firmware status        | Download and running                           |
| 30 | Firmware major version | -                                              |
| 31 | Firmware minor version | -                                              |
| 32 | PCS                    | Physical Coding Sub-layer info                 |
| 33 | PMD                    | Signal strength and signal lock info           |
| 34 | Port Link Status       | Up when ASIC, system-side and line-side are up |

#### External PHY Statistics Requirements

| Id | Requirement        | Notes                    |
|----|--------------------|--------------------------|
| 35 | Interface Counters | System-side              |
| 36 | Interface Counters | Line-side                |
| 37 | FEC Counters       | Forward Error Correction |
| 38 | PTP Counters       | Not support at this time |
| 39 | MACSEC Counters    | Future                   |

#### External PHY Specification
The initial specification of the external PHY is contained in a new configuration file and changes to some existing configuration files;
- sai.profile
- port_config.ini - sonic user port config 
- lanemap.ini - line side
- \<platform\>\<hwsku\>.bcm
- gearbox.ini
  * firmware location
  * lane maps - system-side of MAC and PHY
  * external PHY and ASIC topology
  * external PHY library name

#### External PHY Firmware Management
Firmware must be downloaded and initialized before any PHY configurations, etc.

**PHY firmware boot methods:**

- From flash chip connected to the external EEPROM using SPI
- From PHY’s IRAM and DRAM via the MDIO interface

**Typical Firmware download methods:**

| Method      | Description     |
|-------------|-----------------|
| No Download | Do not download |
| Internal    | MDIO download   |
| EPROM       | From Flash      |

**Typical Firmware load methods:**

| Method | Description                        |
|--------|------------------------------------|
| Force  | Always download                    |
| Skip   | If firmware exits, do not download |
| Auto   | Version mismatch                   |

The following CLI commands are provided to assist with firmware download and initialization;

- config (Not required at this time)
- show (future)

### Functionality

The following is a summary of modifications made to new and existing components.

#### ***orchagent (existing)***
The orchagent manages various daemons; portsyncd, portmgrd, intfmgrd, etc. Enhancements are made to detect and manage the new Gearbox agent; gearsyncd and is only executed on platforms configured with External PHYs.

#### ***gearsyncd (new)***
In order to separate feature and function, the gearsyncd is a new/separate daemon running in coordination with the Orchestration Agent under the SWSS Docker. After reading and processing the gearbox and phy configuration files, similar to the portsyncd daemon, it creates all host interfaces and signals the orchagent indicating that port creation and initialization is done.

#### ***portsyncd (existing)***
The existing port configuration functionality is disabled when Gearbox is enabled. However, all Netlink event processing is still performed.

#### ***portsorch (existing/new)***
A new Gearbox's doGearTask is spawned from within the portsorch agent when Gearbox is enabled. Similar to doPortTask, doGearTask creates and initializes the associated SAI ports but for Gearbox.

### Serviceability
Standard logging is instrumented for existing components (as well as the Gearbox Manager), for example;
1. SWSS/orchagents:
  * *SWSS_LOG_XXX*
2. syncd:
  * *SAI_LOG_LEVEL_XXX*
3. Will make use of the new SONiC Debug Framework

 
### Platforms
The Gearbox Manager and its implementation is not specific to any platform, but rather the existence of External PHYs.

### Limitations
The Gearbox Manager is limited to the existence of specific External PHY platform/hardware capabilities. The Gearbox Manager is loaded and initialized **only upon detection of configured External PHYs**.

### References

OCP External PHY Abstraction Interface Change Proposal

 <font color='red'>Will need to change this to the public version on git hub, once posted.</font>

https://broadcom.ent.box.com/file/510010544241

