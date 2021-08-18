# Chassis Locater LED

## High Level Design Document
**Rev 1.0**

## Table of Contents

* [List of Tables](#list-of-tables)
* [Revision](#revision)
* [About This Manual](#about-this-manual)
* [Scope](#scope)
* [Definition/Abbreviation](#definitionabbreviation)
* [Feature Overview](#feature-overview)
  * [Functional Requirements](#functional-requirements)
* [Design](#design)
  * [SONiC Platforms with the platform APIs](#platforms)
  * [SONiC PDDF Platforms with the platform APIs](#pddf-platforms)
  * [SONiC Platforms with the 1.0 plugins](#plugins-1-0)
  * [Chassis Locator LED CLIs](#chassis-locator-led-clis)
* [CLI](#cli)
* [KLISH CLI](#klish-cli)
* [Openconfig URI](#openconfig-uri)
* [System Reboot](#system-reboot)
* [Unit Test](#unit-test)
* [Internal Design Information](#internal-design)


# Revision

Rev   |   Date   |  Author   | Change Description
:---: | :-----:  | :------:  | :---------
1.0   | 12/16/20 | Precy Lee | Initial version


# About this Manual

This document describes the Chassis Locator LED Feature in the SONiC Network Operating System(NOS). This feature helps an user to identify a particular switch in a lab or data center setting.

# Scope

This document describes the high level design of Chassis Locator LED feature. It describes the CLI and KLISH commands used to enable, disable, and show status of Chassis Locator LED. 

# Definition/Abbreviation

### Table 1: Abbreviations

| **Term**     |  **Meaning**                  |
|:-------------|:------------------------------|
| LOC LED      | Locator LED                   |

# Feature Overview 

Chassis Locator LED feature provides an adminstrator to identify a particular switch among others. Chassis Locator LED is typically located on the front panel of a switch and labelled as LOC or loc. The LED status provides a visual indication for the location of a switch. An adminstrator can execute commands to enable, disable, or show the LOC LED. Some of platforms do not have the LOC LED. Another front panel LED will be used as a locator LED.        

## Functional Requirements

Functional requirements include capabilities of operating the Chassis Locator LED via CLI and KLISH to

 - Enable Chassis Locator LED: Turn on the LED forever until OFF command is executed or reboot. The color pattern of the LED is platform specific.     
 - Disable Chassis Locator LED: Turn off the LED immediately. 
 - Show the State Chassis Locator LED: Find out the status of the LED, on or off. 

# Design

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
## SONiC Platforms with the platform APIs
   
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

## SONiC PDDF Platforms with the platform APIs

Platforms that bring up with the PDDF drivers do not need to do anything to support the Chassis Locator LED feature. The implmentations are provided in
the PddfChassis class in platform/pddf/platform-api-pddf-base/sonic_platform_pddf_base/pddf_chassis.py. 
   
## SONiC Platforms with the 1.0 plugins 

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

## Chassis Locator LED CLIs 

- Chassis Locator LED related operations are implemented through a CLI called 'chassis'. 

- Supported operations:

  ```
    locator-led chassis on   => Switch on the Chassis Locator LED 
    locator-led chassis off  => Switch off the Chassis Locator LED
    show locator-led chassis => Show the current color of the Chassis Locator LED 

  ```

 
# CLI:

**Enable Chassis Locator LED**

 This command runs at root privileges. 

- Examples on supported platforms:
  ```
  root:~$ locator-led chassis on
  Success

  root:~$ locator-led chassis on
  Failed 

  ```
- Examples on unsupported platforms:
  ```
  root:~$ locator-led chassis on
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
  on      blue 

  
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

# KLISH CLI:

**Enable Chassis Locator LED**

- Examples on supported platforms:
  ```
  sonic# locator-led chassis on
  Success

  sonic# locator-led chassis on
  Failed 

  ```

- Examples on unsupported platforms:
  ```
  sonic# locator-led chassis on
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
  on      blue 
  
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
# Openconfig URI:

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
# Chassis Locator LED after System Reboot 

A switch can restart through warm reboot, cold reboot, or power cycle. Chassis Locator LED is back to OFF state after a system reboot. 

# Config Reload

Chassis Locator LED state remains the same after config realod.
 
# Unit Test 

Run each unit test on both Chassis Locator LED supported platforms and unsupported platforms. 

  | SNO |  Unit Testcase 
 :------| :----------------------------------------------------
    1   | CLI: Enable Chassis Locator LED       
    2   | CLI: Disable Chassis Locator LED  
    3   | CLI: Show Status of Chassis Locator LED   
    4   | KLISH: Enable Chassis Locator LED  
    5   | KLISH: Disable Chassis Locator LED 
    6   | KLISH: Show Status of Chassis Locator LED 
    7   | Verify Chassis Locator LED after Warm/Cold Reboot
    8   | Verify Chassis Locator LED after Config Reload 
    9   | Verify Chassis Locator LED after Power Cycle


# Internal Design Information:

## Supported Platforms

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



