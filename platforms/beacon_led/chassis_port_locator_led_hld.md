# Chassis Locater LED and Port Locator LED

## High Level Design Document
**Rev 2.0**

## Table of Contents

* [List of Tables](#list-of-tables)
* [Revision](#revision)
* [About This Manual](#about-this-manual)
* [Scope](#scope)
* [Definition/Abbreviation](#definitionabbreviation)
* [Feature Overview](#feature-overview)
    * [Functional Requirements](#functional-requirements)
* [Design](#design)
    * [Chassis Locator LED](#chassis-locator-led)
    * [Port Locator LED](#port-led-design)
* [CLI](#cli)
  * [CLICK](#click)
  * [KLISH](#klish)
  * [Data Models](#data-models) 
  * [REST API Support](#rest-api-support) 
* [Serviceability and Debug](#Serviceability-and-Debug)
* [System Reboot](#system-reboot)
* [Config Reload](#config-reload)
* [Scalability](#Scalability)
* [Limitations](#Limitations)
* [Unit Test](#Unit-Test)
* [Internal Design Information](#internal-design)


# List of Tables

* [Table 1: Abbreviations](#table-1-abbreviations)
* [Table 2: Chassis Locator LED supported list](#table-2-chassis-locator-led-supported-list)
* [Table 3: Port Locator LED supported list](#table-3-port-locator-led-supported-list)

# Revision

Rev   |   Date     |  Author   | Change Description
:---: | :-----:    | :------:  | :---------
1.0   | 12/16/2020 | Precy Lee | Initial version
2.0   | 04/15/2021 | Dante Su  | added port_led
2.0   | 05/15/2021 | Precy Lee | Added Chassis Locator LED timer support
2.1   | 08/13/2021 | Dante Su  | Updated Port Locator for SAI, database, CLI, limitations and detailed supported platform list
2.2   | 10/13/2021 | Dante Su  | Updated Port Locator LED behavior as per requests, add details of Broadcom port-locator firmware

# About this Manual

This document describes the Chassis Locator LED Feature and Port Locator LED Feature in the SONiC Network Operating System(NOS).

# Scope

This document describes the high level design of Chassis Locator LED feature and Port Locator LED Feature. It describes the CLI and KLISH commands used to enable, disable, and show status of Locator LED. 

# Definition/Abbreviation

### Table 1: Abbreviations

| **Term**     |  **Meaning**                  |
|:-------------|:------------------------------|
| LOC LED      | Locator LED                   |
| API          | Application Programming Interface                            |
| ASIC         | Application Specific Integrated Circuit                      |
| CLI          | Command-Line Interface                                       |
| LED          | Light-Emitting Diode                                         |
| NPU          | Network Processing Unit                                      |
| SAI          | Switch Abstraction Interface                                 |
| SONiC        | Software for Open Networking in the Cloud                    |
| SWSS         | SWitch State Service                                         |


# Feature Overview 

Chassis Locator LED feature provides an adminstrator to identify a particular switch among others. Chassis Locator LED is typically located on the front panel of a switch and labelled as LOC or loc. The LED status provides a visual indication for the location of a switch. An adminstrator can execute commands to enable, disable, or show the LOC LED. Some of platforms do not have the LOC LED. Another front panel LED will be used as a locator LED.        

Port Locator LED is an easy mechanism to identify ports that are the result of networ cabling errors and/or cabling complications (miss-wiring). The
design is to provide a command that blinks a single interface's LED or the LEDs of multiple interfaces and turn off all other interface LEDs so that
the network manager can easily see the miss-wired interface or interfaces.

## Functional Requirements

**Chassis Locator LED** 

Functional requirements include capabilities of operating the Chassis Locator LED via CLI and KLISH to

 - Enable Chassis Locator LED: Turn on the LED forever until OFF command is executed or reboot. The color pattern of the LED is platform specific.     
 - Enable Chassis Locator LED with Timer: Turn on the LED for a specified number of minutes (1 through 120). The color pattern of the LED is platform specific.     
 - Disable Chassis Locator LED: Turn off the LED immediately. 
 - Show the State Chassis Locator LED: Find out the status of the LED, on or off. 

**Port Locator LED** 

When the first interface locator is enabled, all other interface LEDs are turned OFF except
those interfaces that are linked up. Subsequent interface locator enables do not modify the
previous interfaces that were enabled. When the last interface locator is disabled, port LEDs
return to normal operation.

The port-locator may be enabled on a physical port, or on a group of physical ports as allowed
through the interface command. Likewise, the port-locator may be disabled on a physical port,
or disabled on a group of physical ports as allowed through the interface command.

In the case of 100G ports that have hardware support for an LED for 25G lane,
enabling port-locator on a 100G port will only turn on the first LED. Consequently, if
a 100G port has been expanded to 4x25G, each 25G lane's LED can be enabled/disabled individually.

Several CLI configuration commands support this feature. The commands are available in
exec-mode.

- A CLI show command lists which ports have their LED ON.
- A ranged disable command to restore LEDs to their normal behavior.

The port-locator function does not interfere with any aspect of the data plane or interface
with any protocols of SONiC, therefore the port-locator feature bypasses the configurator
application and is not affected by the switch configuration application.

- Clearing the switch configuration will not restore the LEDs to their normal operating mode.
- Saving the switch configuration will not save any port-locator settings.
- Showing the switch configuration will not show any port-locator settings.
- The LEDs of all ports should revert to normal behavior during hard and soft reboots.

# Design

## Chassis Locator LED 

Chassis Locator LED feature supports the platform base classes from src/sonic-platform-common.  
Two abstract methods and Locator LED related class variables are proposed in the ChassisBase class in sonic-platform-common/sonic_platform_base/chassis_base.py
Since each platform has a specific chassis locator LED pattern, vendors are responsible for defining the value of LOCATOR_LED_ON from the possible
locator color list in the derived class. The default value of LOCATOR_LED_OFF is STATUS_LED_COLOR_OFF. Vendors can overwrite the value if it is
different. The list of possible predefined state LED color is defined in sonic-platform-common/sonic_platform_base/device_base.py.  

  ```

  sonic-platform-common/sonic_platform_base/device_base.py:

  class DeviceBase(object):

      # Possible status LED colors
      STATUS_LED_COLOR_GREEN = "green"
      STATUS_LED_COLOR_AMBER = "amber"
      STATUS_LED_COLOR_RED = "red"
      STATUS_LED_COLOR_BLUE="blue"
      STATUS_LED_COLOR_YELLOW="yellow"
      STATUS_LED_COLOR_GREEN_BLINK="green_blink"
      STATUS_LED_COLOR_AMBER_BLINK="amber_blink"
      STATUS_LED_COLOR_RED_BLINK="red_blink"
      STATUS_LED_COLOR_BLUE_BLINK="blue_blink"
      STATUS_LED_COLOR_YELLOW_BLINK="yellow_blink"
      STATUS_LED_COLOR_OFF="off"

  sonic-platform-common/sonic_platform_base/chassis_base.py:

  class ChassisBase(device_base.DeviceBase):

     ##############################################
     # Chassis Locator/LOC LED methods
     ##############################################

     def __init__(self):
        # Variable that holds a platform specific locator color from the Locator LED color list   
        self.LOCATOR_LED_ON = None
        self.LOCATOR_LED_OFF = self.STATUS_LED_COLOR_OFF 

     def set_locator_led(self, color):
         """
         Sets the state of the Chassis Locator LED

         Args:
            color: A string representing the color with which to set the Chassis Locator LED

         Returns:
            bool: True if the Chassis Locator LED state is set successfully, False if not
         """
         raise NotImplementedError

      def get_locator_led(self):
          """
          Gets the state of the Chassis Locator LED

          Returns:
            LOCATOR_LED_ON or LOCATOR_LED_OFF  
          """
          raise NotImplementedError

  ```
### SONiC Platforms with the platform APIs
   
For SONiC Platforms that support the platfrorm APIs, vendors need to initialize the LOCATOR_LED_ON variable and provide the implementations of two
methods. 

  ```
  class Chassis(ChassisBase):
     def __init__(self):
        # assign the value to LOCATOR_LED_ON
        self.LOCATOR_LED_ON = STATUS_LED_COLOR_BLUE

     ##############################################
     # Chassis Locator/LOC LED methods
     ##############################################

     def set_locator_led(self, color):
         """
         provide implementation
         """
     def get_locator_led(self):
         """
         provide implementation
         """
  ```

### SONiC PDDF Platforms with the platform APIs

Platforms that bring up with the PDDF drivers do not need to do anything to support the Chassis Locator LED feature. The implmentations are provided in
the PddfChassis class in platform/pddf/platform-api-pddf-base/sonic_platform_pddf_base/pddf_chassis.py. 
   
### SONiC Platforms with the 1.0 plugins 

Some SONiC platforms are still using the 1.0 plugins. There is no chassis base class in the 1.0 plugins. The proposed implementation is that a
new file, chassisutil.py, is created in the plugins. When a ChassisUtil class is created, vendors need to initialize the LOCATOR_LED_ON variables and provide platform specific implementations. 

  ```
  chassisutil.py

    class ChassisUtil():
        def __init__(self):
            # assign the value to LOCATOR_LED_ON, color may vary
            self.LOCATOR_LED_ON = "Green"

        ##############################################
        # Chassis Locator/LOC LED methods
        ##############################################

        def set_locator_led(self, color):
            """
            provide implementation
            """
        def get_locator_led(self):
            """
            provide implementation
            """
  ```

### Chassis Locator LED CLIs 

- Chassis Locator LED related operations are implemented through a CLI called 'chassis'. 

- Supported operations:

  ```
    locator-led chassis on   => Switch on the Chassis Locator LED 
    locator-led chassis on <1-120>  => Switch on the Chassis Locator LED for a specified number of minutes 
    locator-led chassis off  => Switch off the Chassis Locator LED
    show locator-led chassis => Show the current color of the Chassis Locator LED 

  ```

## Port Locator LED 

- The normal port LED indication will be disabled if the port-locator is enabled on anyone of the interfaces.
- The blink rate will be 1 second on followed by 1 second off.
- The interfaces that are linked up will have their LEDs solidly lit if port-locator is disabled on that interface.
- The interfaces that are linked down will have their LEDs solidly extinguished if port-locator is disabled on that interface.
- Traffic present on any interface will not blink the LED to indicate traffic.
- A port-locator enabled interface will blink and not light solid regardless of the link state.
- In the case that an interface has two LEDs, one for link and a second for activity,
only the link LED is used for the port-locator function. The activity LED will be
turned off while the port-locator firmware is active.
- In the case that an interface has one LED for both link and activity, the LED will not
blink if activity is present on the interface while the port-locator firmware is active.

| **Port-Locator** | **Link Status** | **SOLID OFF** | **SOLID ON**  | **BLINKING** |
|:----------------:|:---------------:|:-------------:|:-------------:|:------------:|
| Disabled         | DOWN            | X             |               |              |
| Disabled         | UP              |               | X             |              |
| Enabled          | DOWN            |               |               | X            |
| Enabled          | UP              |               |               | X            |

Port Locator support can be divided into the following components:

#### Switch Abstraction Interface (SAI)

The Switch Abstraction Interface(SAI) defines the API to provide a
vendor-independent way of controlling forwarding elements, such as
a switching ASIC, an NPU or a software switch in a uniform manner,
and new SAI port attributes will be introduced for port-locator.

##### SAI Profile

- **SAI_LED_PORT_LOCATOR_FW_FILE**

    This key provides the path to custom port-locator firmware, and it defaults to **'/usr/share/sonic/platform/port-locator.bin'**.<br>
    If the firmware does not exist, the generic port-locator firmware will be activated and the platform configuration must be supplied via SAI_LED_PORT_LOCATOR_CONFIG_FILE

- **SAI_LED_PORT_LOCATOR_CONFIG_FILE**

    This key provides the path to the platform-specific config file for the generic port-locator firmware, and it's unused in the case of custom port-locator firmware.<br>
    It defaults to **'/usr/share/sonic/platform/port-locator.soc'**

Note: Either one of the above file must be present on the platform to enable the port-locator support.

##### SAI Attributes

- SAI_PORT_ATTR_LED_LOCATOR_MODE

    This attribute is to activate/de-activate the locator mode on the port. Setting this attribute to activate the locator mode on the port will only toggle the BLINKING behavior in the locator mode, it will not activate the port-locator firmware mode.

- SAI_SWITCH_ATTR_LED_LOCATOR_MODE

    This attribute is to activate/de-activate the port-locator firmware, the LED behavior of all switch ports will be affected, and the normal operation firmware should be restored upon device reset or port-locator disabled.

#### SWitch State Service (SWSS)

The SWitch State Service (SWSS) is a collection of software that provides a database
interface for communication with and state representation of network applications and
network switch hardware.

For port-locator support, the **orchagent** will be enhanced to support the new table
in APPL_DB and the new SAI port attributes in SAI.

#### SONiC Management Framework

Management framework is a SONiC application which is responsible for providing various common
North Bound Interfaces (NBIs) for the purposes of managing configuration and status on SONiC switches.
The application manages coordination of NBI<92>s to provide a coherent way to validate, apply and show
configuration.

The Industry Standard CLI in SONiC is built on top of this framework, and the port-locator commands
will only be availble in this CLI.

### Database

#### APPL_DB

##### PORT_TABLE

The **port_locator** attribute of the **PORT_TABLE** serves as requests to orchagent and syncd/SAI, and the port-locator firmware will be activated if either one of the port is in locator mode.

e.g.
```
    "PORT_TABLE:Ethernet16": {
        "type": "hash",
        "value": {
            ...... omitted ......
            "port_locator": "on|off",
            ...... omitted ......
        }
    },
```

##### PORT_LOCATOR

This table serves as requests from CLI to the port-locator daemon in the pmon, which is responsible for the expiration timeout checks and actually send out the requests to orchagent for the port-locator mode.
If the **expired** is not specified or an empty string, it will never be expired.
If the **expired** is a malformed datetime string, it will be expired immediately.

Note: The port-locator expiration timer is vulnerable to the time-shift operations (e.g. NTP or manually updated), please either disable all the port-locator or reset the device after any date and time updates.

e.g.
```
    "PORT_LOCATOR:Ethernet16": {
        "type": "hash",
        "value": {
            "mode": "on|off",
            "expired": "YYYY-mm-dd HH:MM:SS"
        }
    },
```

# CLI:

## CLICK 

**Enable Chassis Locator LED**

 This command runs at root privileges. 

- Examples on supported platforms:
  ```
  root:~$ locator-led chassis on --help
  Usage: locator-led chassis on [OPTIONS]

    Enable Locator Chassis LED

    Options:
      -t, --timer INTEGER  timer value in minutes: 1-120
      --help             Show this message and exit.

  root:~$ locator-led chassis on
  Success

  root:~$ locator-led chassis on --timer 5 
  Success
  Locator LED will be off after 5 minutes

  Note: LOC LED will be off automatically after a period of 5 minutes

  root:~$ locator-led chassis on
  Failed 

  root:~$ locator-led chassis on --timer 5 
  Failed 

  root:~$ locator-led chassis on --timer 0 
  Usage: locator-led chassis on [OPTIONS]

  Error: Invalid value for "-t" / "--timer": 0 is not in the valid range of 1 to 120.

  ```
- Examples on unsupported platforms:
  ```
  root:~$ locator-led chassis on
  Not supported

  root:~$ locator-led chassis on --timer 5
  Not supported

  ```


**Disable Chassis Locator LED**

 This command runs at root privileges. 

- Examples on supported platforms:
  ```
  root:~$ locator-led chassis off 
  Success

  root:~$ chassis locator-led off 
  Failed 

  ```

- Examples on unsupported platforms:
  ```
  root:~$ locator-led chassis off
  Not supported

  ```
**Show Chassis Locator LED**

 This command runs at root/admin privileges. 

- Examples on supported platforms:

  ```
  admin:~$ show locator-led chassis 
  State    Color
  -------  -------
  on       blue 

  
  Note: Chassis Locator LED color is platform specific. The output of color can vary 

  admin:~$ show locator-led chassis 
  State    Color
  -------  -------
  off      off

  Note: Chassis Locator LED is turned off 

  ```
- Examples on unsupported platforms:

  ```
  root:~$ show locator-led chassis
  Not supported

  ```

## KLISH

**Enable Chassis Locator LED**

- Examples on supported platforms:
  ```
  sonic# locator-led chassis on
  Success

  sonic# locator-led chassis on timer 5
  Success
  Locator LED will be off after 5 minutes

  Note: LOC LED will be off automatically after a period of 5 minutes

  sonic# locator-led chassis on
  Failed 

  sonic# locator-led chassis on timer 5
  Failed 


  ```

- Examples on unsupported platforms:
  ```
  sonic# locator-led chassis on
  Not supported

  sonic# locator-led chassis on timer 5
  Not supported

  ```

**Disable Chassis Locator LED**

- Examples on supported platforms:
  ```
  sonic# locator-led chassis off 
  Success

  sonic# locator-led chassis off 
  Failed 

  ```
- Examples on unsupported platforms:
  ```
  sonic# locator-led chassis off
  Not supported

  ```

**Show Chassis Locator LED**


- Examples on supported platforms:
  ```
  sonic# show locator-led chassis 
  State    Color
  -------  -------
  on       blue 
  
  Note: Chassis Locator LED color is platform specific. The output of color can vary 

  sonic# show locator-led chassis 
  State    Color
  -------  -------
  off      off 

  Note: Chassis Locator LED is turned off 

  sonic# show locator-led chassis 
  State   Color
  ------- -------
  off     Unknown
  Color may vary with respect to system states

  Note: If the software cannot retrieve the locator LED color, unknown is displayed. The LED color is lit on the
  switch.

  ```


- Examples on unsupported platforms:
  ```
  sonic# show locator-led chassis
  Not supported

  ```

**Port Locator LED Configuration Commands**

This feature does not interact with the configurator application and therefore
any port-locator settings are not saved across a reset and also will not be
displayed in **show running-config**, and the CLIs should be executed in exec-mode
rather than config-mode.

##### [no] interface port-locator [Ethernet PortID[-PortID] [timer MINUTE]]

To enable the port-locator on all interfaces, please use the following command.

```
sonic# interface port-locator
```

To enable the port-locator on all interfaces with a timer to automatically have the port-locator disabled after 5 minutes timeout.

```
sonic# interface port-locator timer 5
```

To disable the port-locator on all interfaces, please use the following command.

```
sonic# no interface port-locator
```


To enable/disable the port-locator on one particular interface, please use the following command.

```
sonic# interface port-locator Ethernet 0
sonic# no interface port-locator Ethernet 0
```

To enable/disable the port-locator on Ethernet4,Ethernet5...Ethernet64, please use the following command.

```
sonic# interface port-locator Ethernet 4-64
sonic# no interface port-locator Ethernet 4-64
```

To enable/disable the port-locator on Ethernet4,Ethernet9...Ethernet12, please use the following command.

```
sonic# interface port-locator Ethernet 4,9-12
sonic# no interface port-locator Ethernet 4,9-12
```

**show interface port-locator**

This command displays which interface or interfaces currently have the port-locator mode enabled.

```
sonic# show interface port-locator

Interface       Locator Mode  Expiration Time
--------------  ------------  -------------------
Eth1/1          Disabled
Eth1/2          Disabled
Eth1/3          Disabled
Eth1/4          Enabled
Eth1/5          Enabled
Eth1/6          Enabled
Eth1/7          Enabled       2021-08-13 02:52:46
Eth1/8          Disabled
......
```

To disply only the port-locator status of the ports in a particular ranges.

```
sonic# show interface port-locator Eth 1/4-1/6,1/12

Interface       Locator Mode  Expiration Time
--------------  ------------  -------------------
Eth1/4          Enabled
Eth1/5          Enabled
Eth1/6          Enabled
Eth1/12         Disabled
sonic#
```

## Data Models:

#### openconfig-platform-diagnostics.yang

The Port Locator LED YANG tree is as below:
```
TBD
```

## REST API Support:

**Enable Chassis Locator LED**

- Curl

  ```
  curl -X POST "https://<switch ip>/restconf/operations/openconfig-system-ext:locator-led-chassis-on" -H "accept: application/yang-data+json" -H
  "Authorization: Basic YWRtaW46YnJvYWRjb20=" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-system-ext:input\":{}}"

  ```

- Request URL

  ```

  https://<switch ip>/restconf/operations/openconfig-system-ext:locator-led-chassis-on

  ```

- Response Body

  ```
  {
      "openconfig-system-ext:output": {
          "result": "\nSuccess"
      }
  }

  {
      "openconfig-system-ext:output": {
           "result": "\nFailed"
      }
  }

  ```

**Disable Chassis Locator LED**

- Curl

  ```
  curl -X POST "https://<switch ip>/restconf/operations/openconfig-system-ext:locator-led-chassis-off" -H "accept: application/yang-data+json" -H
  "Authorization: Basic YWRtaW46YnJvYWRjb20=" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-system-ext:input\":{}}"

  ```

- Request URL

  ```
  https://<switch ip>/restconf/operations/openconfig-system-ext:locator-led-chassis-off

  ```

- Response Body

  ```
  {
      "openconfig-system-ext:output": {
          "result": "\nSuccess"
      }
  }

  {
      "openconfig-system-ext:output": {
           "result": "\nFailed"
      }
  }

  ```

**Show Chassis Locator LED**

- Curl

  ```
  curl -X POST "https://<switch ip>/restconf/operations/openconfig-system-ext:show-locator-led-chassis" -H "accept: application/yang-data+json" -H
  "Authorization: Basic YWRtaW46YnJvYWRjb20=" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-system-ext:input\":{}}"

  ```

- Request URL

  ```
  https://<switch ip>/restconf/operations/openconfig-system-ext:show-locator-led-chassis

  ```

- Response Body

  ```
  {
       "openconfig-system-ext:output": {
            "status-detail": [
                "State    Color",
                "-------  -------",
                "off      off"
             ]
       }
  }

  {
       "openconfig-system-ext:output": {
            "status-detail": [
                "State    Color",
                "-------  -------",
                "on       blue"
             ]
       }
  }

  ```

# Serviceability and Debug

Existing serviceability and debug applicable to both features. No new debugging commands are added.

# System Reboot 

A switch can restart through warm reboot, cold reboot, or power cycle. Chassis Locator LED and Port Locator LEDs are back to OFF state and the OFF timer will be cacneled after a system reboot. 

# Config Reload

Chassis Locator LED state and Port Locatr LED state remain the same after config realod.
 
# Scalability

No impact to scalability. Existing scale numbers will be supported with both features.

# Limitations

## Chassis Locator LED

- Not every platform supports Chassis Locator LED. Please refer to the Table 2 for the supported platform list.

## Port Locator LED

- As of now, only the Broadcom CMCIx platform (e.g.TH3/TD3/TD4) is supported.
- It's known issue that Accton platforms are using compact special coding for the LED bitstream, hence it's not going to be supported by the generic port-locator firmware, a platform-specific custom firmware will be necessary in this case.
- The port-locator expiration timer is vulnerable to the time-shift operations (e.g. NTP or manually updated), please either disable all the port-locator or reset the device after any date and time updates.

# Unit Test 

**Chassis Locater LED**

Run each unit test on both Chassis Locator LED supported platforms and unsupported platforms. 

* CLI: Enable Chassis Locator LED       
* CLI: Disable Chassis Locator LED  
* CLI: Show Status of Chassis Locator LED   
* KLISH: Enable Chassis Locator LED  
* KLISH: Disable Chassis Locator LED 
* KLISH: Show Status of Chassis Locator LED 
* Verify Chassis Locator LED after Warm/Cold Reboot
* Verify Chassis Locator LED after Config Reload 
* Verify Chassis Locator LED after Power Cycle
* CLI: Enable Chassis Locator LED with Timer      
* KLISH: Enable Chassis Locator LED with Timer      

**Port Locator LED**

The following is the list of unit test cases:

* Verify port-locator KLISH configuration commands to all ports.
* Verify port-locator KLISH configuration commands to one port.
* Verify port-locator KLISH configuration commands to the ports in a particular range.
* Verify port-locator KLISH show commands to all ports.
* Verify port-locator KLISH show commands to one port.
* Verify port-locator KLISH show commands to the ports in a particular range.
* Verify port-locator firmware platform configurations.
* Verify port-locator firmware should be activated if either one of the port is in locator mode.
* Verify port-locator firmware should be de-activated if none of the port is in locator mode.
* Verify port-locator expiration timer support to one port.
* Verify port-locator expiration timer support to all ports.
* Verify port-locator expiration timer support to the ports in a particular range.

# Internal Design Information:

## Chassis Locator LED Supported Platforms

Not every platform supports Chassis Locator LED. Please refer to the Table 2 for platforms supported Locator LED in the 3.2 release.
For example, AS4630-54NP platform does not have the LOC LED, therefore, PRI LED is selected to act as a locator LED when the feature is enabled.

### Table 2: Chassis Locator LED supported list

| **Vendor**  |  **Platform**  |  **Chassis Locator Support** | **Color** | **Blinking** | **LED Label** |
|:------------|:---------------|:--------------------------|:-------------|:-------------|---------------|
| Accton      | AS7712-32X     | Yes                       | Blue         |   No         |   LOC         |
| Accton      | AS5712-54X     | Yes                       | Amber        |   Yes        |   LOC         |
| Accton      | AS7326-56X     | Yes                       | Blue         |   Yes        |   LOC         |
| Accton      | AS7816-64X     | Yes                       | Amber        |   No         |   LOC         |
| Accton      | AS9716-32D     | Yes                       | Amber        |   Yes        |   LOC         |
| Accton      | AS7726-32X     | Yes                       | Blue         |   No         |   LOC         |
| Accton      | AS5835-54X     | Yes                       | Amber        |   No         |   LOC         |
| Accton      | AS5835-54T     | Yes                       | Amber        |   No         |   LOC         |
| Accton      | AS4630-54NP    | Yes                       | Amber        |   No         |   PRI         |
| Dell        | Z9100          | Yes                       | Blue         |   No         |   LOC         |
| Dell        | S6100          | Yes                       | Blue         |   No         |   LOC         |
| Dell        | S5232          | Yes                       | Blue         |   No         |   LOC         |
| Dell        | Z9332          | No                        | N/A          |   N/A        |   N/A         |
| Dell        | Z9264          | Yes                       | Blue         |   No         |   LOC         |
| Dell        | N3248TE        | Yes                       | Blue         |   No         |   LOC         |
| Dell        | S5212          | Yes                       | Blue         |   No         |   LOC         |
| Dell        | S5224          | Yes                       | Blue         |   No         |   LOC         |
| Dell        | S5248          | Yes                       | Blue         |   No         |   LOC         |
| Dell        | S5296          | Yes                       | Blue         |   No         |   LOC         |
| Dell        | Z9432          | Yes                       | Blue         |   No         |   LOC         |
| Dell        | S6000          | Yes                       | Blue         |   No         |   LOC         |
| Quanta      | IX7T           | No                        | N/A          |   N/A        |   N/A         |
| Quanta      | IX8T           | No                        | N/A          |   N/A        |   N/A         |
| Quanta      | IX4            | Yes                       | Blue         |   Yes        |   BMC         |
| Quanta      | IX7            | Yes                       | Blue         |   Yes        |   BMC         |
| Quanta      | IX7_BWDE       | Yes                       | Blue         |   Yes        |   BMC         |
| Quanta      | IX8            | Yes                       | Blue         |   Yes        |   BMC         |
| Quanta      | IX8-B          | Yes                       | Blue         |   Yes        |   BMC         |
| Quanta      | IX8A_BWDE      | Yes                       | Blue         |   Yes        |   BMC         |
| Quanta      | IX9            | Yes                       | Blue         |   Yes        |   BMC         |


## Port Locator LED Supported Platforms

Not every platform supports Port Locator LED. Please refer to the Table 3 for the supported platform list.
As of now, only the Broadcom CMCIx platform (e.g.TH3/TD3/TD4) is supported, we'll circle back on CMICd platform(TH/TH2/TD2) support later.

### Table 3: Port Locator LED supported list
| **Vendor**  |  **Platform**  |  **Port Locator Support** | **Custom Firmware** | **Note**     |
|:------------|:---------------|:--------------------------|:--------------------|:-------------|
| Accton      | AS7712-32X     | No                        |                     | TH           |
| Accton      | AS5712-54X     | No                        |                     | TD2          |
| Accton      | AS7326-56X     | Yes                       | Yes                 |              |
| Accton      | AS7816-64X     | No                        |                     | TH2          |
| Accton      | AS9716-32D     | Yes                       | Yes                 |              |
| Accton      | AS7726-32X     | Yes                       | Yes                 |              |
| Accton      | AS5835-54X     | Yes                       | Yes                 |              |
| Accton      | AS5835-54T     | Yes                       | Yes                 |              |
| Accton      | AS4630-54NP    | Yes                       | Yes                 |              |
| Dell        | Z9100          | No                        |                     | TH           |
| Dell        | S6100          | No                        |                     | TH           |
| Dell        | S5232          | Yes                       | No                  |              |
| Dell        | Z9332          | Yes                       | No                  |              |
| Dell        | Z9264          | No                        |                     | TH2          |
| Dell        | N3248TE        | Yes (Not Ready)           |                     | Awaits DELL development support |
| Dell        | S5212          | Yes (Not Ready)           | No                  | Awaits DELL development support |
| Dell        | S5224          | Yes (Not Ready)           | No                  | Awaits DELL development support |
| Dell        | S5248          | Yes (Not Ready)           | No                  | Awaits DELL development support |
| Dell        | S5296          | Yes (Not Ready)           | No                  | Awaits DELL development support |
| Dell        | Z9432          | Yes                       | No                  |              |
| Dell        | S6000          | No                        |                     | TH           |
| Quanta      | IX7t (SLX9250) | Yes                       | No                  |              |
| Quanta      | IX8f (SLX9150) | Yes                       | No                  |              |
| Quanta      | IX4            | No                        |                     | TH2          |
| Quanta      | IX7            | Yes                       | No                  |              |
| Quanta      | IX7_BWDE       | Yes                       | No                  |              |
| Quanta      | IX8            | Yes                       | No                  |              |
| Quanta      | IX8-B          | Yes                       | No                  |              |
| Quanta      | IX8A_BWDE      | Yes                       | No                  |              |
| Quanta      | IX9            | Yes                       | No                  |              |

## Port Locator LED - Custom Firmware Developer Guide

While there is a generic port-locator firmware available in the Broadcom SAI, it only works if the
platform-specific LED hardware design strictly maintain 1:1 mapping between physical ports and LED ports.
This could be easily identified by the SAI built-in command, **xled plcfg**, that's to help users quickly
generate the port-locator.soc for the generic firmware.

### A platform that can be supported by the generic port-locator firmware    

Here is a case that each physical ports could be mapped to different LED ports (Pattern RAM)

Instructions to generate the port-locator.soc:

- Disable all ports on the DUT
- Issue **bcmsh** to break into Broadcom Debug Shell (DO NOT USE bcmcmd for this!!!)
- Issue xled plcfg, and answer 'y' to start the testing.
- Review the logs, manually update the generated script, and save it as a port-locator.soc in the platform folders

```
admin@sonic:~$ sudo config interface startup Ethernet0-999
admin@sonic:~$ bcmsh
drivshell>xled plcfg
xled plcfg
Is the custom_led.bin activated? [y/n]y
y
phys   1 --> patt   1 (0x3 -> 0x2)
phys   5 --> patt   5 (0x3 -> 0x2)
phys   9 --> patt   9 (0x3 -> 0x2)
phys  13 --> patt  13 (0x3 -> 0x2)
phys  17 --> patt  17 (0x3 -> 0x2)
phys  21 --> patt  21 (0x3 -> 0x2)
phys  25 --> patt  25 (0x3 -> 0x2)
phys  29 --> patt  29 (0x3 -> 0x2)
phys  33 --> patt  33 (0x3 -> 0x2)
phys  37 --> patt  37 (0x3 -> 0x2)
phys  41 --> patt  41 (0x3 -> 0x2)
phys  45 --> patt  45 (0x3 -> 0x2)
phys  49 --> patt  49 (0x3 -> 0x2)
phys  53 --> patt  53 (0x3 -> 0x2)
phys  57 --> patt  57 (0x3 -> 0x2)
phys  61 --> patt  61 (0x3 -> 0x2)
phys 129 --> patt 129 (0x3 -> 0x2)
phys  65 --> patt  65 (0x3 -> 0x2)
phys  69 --> patt  69 (0x3 -> 0x2)
phys  73 --> patt  73 (0x3 -> 0x2)
phys  77 --> patt  77 (0x3 -> 0x2)
phys  81 --> patt  81 (0x3 -> 0x2)
phys  85 --> patt  85 (0x3 -> 0x2)
phys  89 --> patt  89 (0x3 -> 0x2)
phys  93 --> patt  93 (0x3 -> 0x2)
phys  97 --> patt  97 (0x3 -> 0x2)
phys 101 --> patt 101 (0x3 -> 0x2)
phys 105 --> patt 105 (0x3 -> 0x2)
phys 109 --> patt 109 (0x3 -> 0x2)
phys 113 --> patt 113 (0x3 -> 0x2)
phys 117 --> patt 117 (0x3 -> 0x2)
phys 121 --> patt 121 (0x3 -> 0x2)
phys 125 --> patt 125 (0x3 -> 0x2)
phys 128 --> patt 130 (0x3 -> 0x1)  <--- 2nd pattern observed on physical port 128


ACCU_BASE: 0x00009000
PATT_BASE: 0x0000a000


rc.soc:

# LED interface controls
config add sai_led_intf_enable_0=1
config add sai_led_intf_head_0=1
config add sai_led_intf_tail_0=64
config add sai_led_intf_bits_0=2

config add sai_led_intf_enable_1=1
config add sai_led_intf_head_1=65
config add sai_led_intf_tail_1=130
config add sai_led_intf_bits_1=2

# LED data patterns for ON/OFF
config add sai_led_patt_off_0=0x3
config add sai_led_patt_on_0=0x2    <--- Only the 1st pattern is generated automatically, please have it updated manually

# Physical Port --> LED Port (1-based indexing)
# QSFP [1-4]
config add sai_led_portmap_1=2
config add sai_led_portmap_2=3
config add sai_led_portmap_3=4
config add sai_led_portmap_4=5
# QSFP [5-8]
config add sai_led_portmap_5=6
config add sai_led_portmap_6=7
config add sai_led_portmap_7=8
config add sai_led_portmap_8=9
...... omitted ......
# QSFP [45-48]
config add sai_led_portmap_45=46
config add sai_led_portmap_46=47
config add sai_led_portmap_47=48
config add sai_led_portmap_48=49
# QSFP [49-52]
config add sai_led_portmap_49=50
config add sai_led_portmap_50=51
config add sai_led_portmap_51=52
config add sai_led_portmap_52=53
# QSFP [53-56]
config add sai_led_portmap_53=54
config add sai_led_portmap_54=55
config add sai_led_portmap_55=56
config add sai_led_portmap_56=57
# QSFP [57-60]
config add sai_led_portmap_57=58
config add sai_led_portmap_58=59
config add sai_led_portmap_59=60
config add sai_led_portmap_60=61
# QSFP [61-64]
config add sai_led_portmap_61=62
config add sai_led_portmap_62=63
config add sai_led_portmap_63=64
config add sai_led_portmap_64=65
# 1-lane port [129-129]
config add sai_led_portmap_129=130
# QSFP [65-68]
config add sai_led_portmap_65=66
config add sai_led_portmap_66=67
config add sai_led_portmap_67=68
config add sai_led_portmap_68=69
...... omitted ......
NOTE: Please review the generated script and have it updated if necessary
drivshell>
```

The port-locator.soc needs to be manually updated as below

```
# LED interface controls
config add sai_led_intf_enable_0=1
config add sai_led_intf_head_0=1
config add sai_led_intf_tail_0=64
config add sai_led_intf_bits_0=2

config add sai_led_intf_enable_1=1
config add sai_led_intf_head_1=65
config add sai_led_intf_tail_1=130
config add sai_led_intf_bits_1=2

# LED data patterns for ON/OFF
config add sai_led_patt_off_0=0x3
config add sai_led_patt_on_0=0x2
config add sai_led_patt_off_1=0x3     <-- This must be done manually!!
config add sai_led_patt_on_1=0x1      <-- This must be done manually!!

# Physical Port --> LED Port (1-based indexing)
# QSFP [1-4]
config add sai_led_portmap_1=2
config add sai_led_portmap_2=3
config add sai_led_portmap_3=4
config add sai_led_portmap_4=5
...... omitted ......
# 1-lane port [128-128]
config add sai_led_portmap_128=131
config add sai_led_pattmap_128=1      <-- This must be done manually!!
...... omitted ......
```

Properties of the port-locator.soc

| Property | Description |
|:---------|:------------|
|sai_led_intf_enable_[led-interface-id]=[value] | 1 for enabled, otherwise disabled |
|sai_led_intf_head_[led-interface-id]=[value]   | The 1st (inclusive) word index of PATTERN RAM |
|sai_led_intf_tail_[led-interface-id]=[value]   | The last (inclusive) word index of PATTERN RAM |
|sai_led_intf_bits_[led-interface-id]=[value]   | The number of bits will be shifted out (LSB) |
|sai_led_patt_off_[pattern-id]=[value] | The id of the data pattern to turn OFF the LED |
|sai_led_patt_on_[pattern-id]=[value]  | The id of the data pattern to turn ON the LED |
|sai_led_portmap_[physical-port-id]=[led-port-id] | Mapping Broadcom Physical Port to LED Port |
|sai_led_pattmap_[physical-port-id]=[led-port-id] | Selecting which data pattern should be adapted for the physical port|

### A platform that's NOT compatible with the generic port-locator firmware    

Here is a case that multiple physical ports share the same LED port (Pattern RAM) with different
bitmask values for each port.

```
drivshell>xled plcfg
xled plcfg
Is the custom_led.bin activated? [y/n]y
y
phys   1 --> patt   0 (0xfff -> 0xffb)
phys   2 --> patt   0 (0xfff -> 0xffe)
phys   3 --> patt   0 (0xfff -> 0xfbf)
phys   4 --> patt   0 (0xfff -> 0xfef)
phys   5 --> patt   0 (0xfff -> 0xbff)
phys   6 --> patt   0 (0xfff -> 0xeff)
phys   7 --> patt   1 (0xfff -> 0xffb)
phys   8 --> patt   1 (0xfff -> 0xffe)
phys   9 --> patt   1 (0xfff -> 0xfbf)
phys  10 --> patt   1 (0xfff -> 0xfef)
phys  11 --> patt   1 (0xfff -> 0xbff)
phys  12 --> patt   1 (0xfff -> 0xeff)
phys  13 --> patt   2 (0xfff -> 0xffb)
phys  14 --> patt   2 (0xfff -> 0xffe)
phys  15 --> patt   2 (0xfff -> 0xfbf)
phys  16 --> patt   2 (0xfff -> 0xfef)
phys  17 --> patt   2 (0xfff -> 0xbff)
phys  18 --> patt   2 (0xfff -> 0xeff)
phys  19 --> patt   3 (0xfff -> 0xffb)
phys  20 --> patt   3 (0xfff -> 0xffe)
phys  21 --> patt   3 (0xfff -> 0xfbf)
phys  22 --> patt   3 (0xfff -> 0xfef)
phys  23 --> patt   3 (0xfff -> 0xbff)
phys  24 --> patt   3 (0xfff -> 0xeff)
phys  29 --> patt  70 (0xfff -> 0xffd)
phys  33 --> patt  71 (0xfff -> 0xffd)
phys  37 --> patt  69 (0xfff -> 0xffd)
phys  38 --> patt  69 (0xfff -> 0xfef)
phys  39 --> patt  69 (0xfff -> 0xf7f)
phys  40 --> patt  69 (0xfff -> 0xbff)
phys  41 --> patt  74 (0xfff -> 0xffd)
phys  42 --> patt  74 (0xfff -> 0xfef)
phys  43 --> patt  74 (0xfff -> 0xf7f)
phys  44 --> patt  74 (0xfff -> 0xbff)
phys  45 --> patt  73 (0xfff -> 0xffd)
phys  49 --> patt  72 (0xfff -> 0xffd)
phys  53 --> patt  64 (0xfff -> 0xffb)
phys  54 --> patt  64 (0xfff -> 0xffe)
phys  55 --> patt  64 (0xfff -> 0xfbf)
phys  56 --> patt  64 (0xfff -> 0xfef)
phys  57 --> patt  64 (0xfff -> 0xbff)
phys  58 --> patt  64 (0xfff -> 0xeff)
phys  59 --> patt  65 (0xfff -> 0xffb)
phys  60 --> patt  65 (0xfff -> 0xffe)
phys  61 --> patt  65 (0xfff -> 0xfbf)
phys  62 --> patt  65 (0xfff -> 0xfef)
phys  63 --> patt  65 (0xfff -> 0xbff)
phys  64 --> patt  65 (0xfff -> 0xeff)
phys  65 --> patt  66 (0xfff -> 0xffb)
phys  66 --> patt  66 (0xfff -> 0xffe)
phys  67 --> patt  66 (0xfff -> 0xfbf)
phys  68 --> patt  66 (0xfff -> 0xfef)
phys  69 --> patt  66 (0xfff -> 0xbff)
phys  70 --> patt  66 (0xfff -> 0xeff)
phys  71 --> patt  67 (0xfff -> 0xffb)
phys  72 --> patt  67 (0xfff -> 0xffe)
phys  73 --> patt  67 (0xfff -> 0xfbf)
phys  74 --> patt  67 (0xfff -> 0xfef)
phys  75 --> patt  67 (0xfff -> 0xbff)
phys  76 --> patt  67 (0xfff -> 0xeff)

...... omitted ......

NOTE: Please review the generated script and have it updated if necessary
drivshell>
```

In case that generic port-locator firmware is not suitable to the target platform,
a custom port-locator firmware will be necessary, and it should be placed in the <platform> folder
e.g.
```
ds952811@qemu:sonic-buildimage$ ls -l device/accton/x86_64-accton_as5835_54t-r0/port-locator.bin
-rwxrwxr-x 1 ds952811 ds952811 608 Aug 16 12:26 device/accton/x86_64-accton_as5835_54t-r0/port-locator.bin
```

Here is an pseudo code for your reference

```
#include <shared/cmicfw/cmicx_led_public.h>
 
/* Check for locator mode */
#define LED_LOCATOR(ctrl, port) (*(uint16 *)((ctrl)->pat_ram_base + ((512 + (port) - 1) << 2)) & 0x8000)

/* Fetch the port status from the ACCU_RAM */
#define READ_LED_ACCU_DATA(base, port) (*((uint16 *)(base + (((port) - 1) * sizeof(uint32)))))

void custom_led_handler(soc_led_custom_handler_ctrl_t *ctrl, uint32 activity_count)
{
    ......
    accu_val = READ_LED_ACCU_DATA(ctrl->accu_ram_base, port);
    if (LED_LOCATOR(ctrl, port))
    {
        if ((activity_count >> 5) & 1)
        {
            /* LED ON  */
        }
        else
        {
            /* LED OFF */
        }
    }
    else if ((accu_val & LED_OUTPUT_LINK_UP) && (accu_val & LED_OUTPUT_LINK_ENABLE))
    {
        /* LED ON  */
    }
    else
    {
        /* LED OFF */
    }
    ......
}
```
