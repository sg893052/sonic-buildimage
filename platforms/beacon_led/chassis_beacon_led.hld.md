# Chassis Locater LED

## High Level Design Document
**Rev 0.1**

## Table of Contents

* [List of Tables](#list-of-tables)
* [Revision](#revision)
* [About This Manual](#about-this-manual)
* [Scope](#scope)
* [Definition/Abbreviation](#definitionabbreviation)
* [Feature Overview](#feature-overview)
  * [Functional Requirements](#functional-requirements)
* [CLI](#cli)
* [KLISH CLI](#klish-cli)
* [Unit Test](#unit-test)


# Revision

Rev   |   Date   |  Author   | Change Description
:---: | :-----:  | :------:  | :---------
1.0   | 12/16/20 | Precy Lee | Initial version


# About this Manual

This document describes the Chassis Locator LED Feature in the SONiC Network Operating System(NOS). This feature helps an user to identify a particular switch in a lab or data center setting.

# Scope

This document describes the high level design of Chassis Locator LED feature. It describes the CLICK and KLISH commands used to enable, disable, and show status of Chassis Locator LED. 

# Definition/Abbreviation

### Table 1: Abbreviations

| **Term**     |  **Meaning**                  |
|:-------------|:------------------------------|
| LOC LED      | Locator LED                   |

# Feature Overview 

Chassis Locator LED feature provides a administrator to identiy a particular switch among others. Chassis Locator LED is typically located on the front panel of a switch and labelled as LOC or loc. The LED status provides a visual indication for the location of a switch. An adminstrator can execute commands to enable, disable, or show the LOC LED.      

## Functional Requirements

Functional requirements include capabilities of operating the Chassis Locator LED via CLI and KLISH to

 - Enable Chassis Locator LED: Turn on the LED. The color pattern of the LED is platform specific.     
 - Disable Chassis Locator LED: Turn off the LED. 
 - Show the State Chassis Locator LED: Find out the status of the LED, on or off. 

Not every platform supports Chassis Locator LED. Please refer to the [platform-list](https://docs.google.com/spreadsheets/d/1NuhoA2utcDPIIiM6dwrAY8FWrX_21KDfcR3Fw_OEwyE/edit#gid=0) that supports Locator LED in this release. 


# CLI:

**Enable Chassis Locator LED**

 This command runs at root privileges. 

- Examples on supported platforms:
  ```
  root:~$ chassis locator-led on
  Success

  root:~$ chassis locator-led on
  Failed 

  ```

- Examples on non-supported platforms:
  ```
  root:~$ chassis locator-led on
  Not supported

  ```

**Disable Chassis Locator LED**

 This command runs at root privileges. 

- Examples on supported platforms:
  ```
  root:~$ chassis locator-led off 
  Success

  root:~$ chassis locator-led off 
  Failed 

  ```

- Examples on unsupported platforms:
  ```
  root:~$ chassis locator-led off 
  Not supported

  ```

**Show Chassis Locator LED**

 This command runs at root/admin privileges. 

- Examples on supported platforms:
  ```
  admin:~$ show chassis locator-led 
  green 
  
  Note: Chassis Locator LED color is platform specific. The output of color can vary 

  ```

- Examples on unsupported platforms:
  ```
  admin:~$ show chassis locator-led 
  Not supported

  ```
# KLISH CLI:

**Enable Chassis Locator LED**

- Examples on supported platforms:
  ```
  sonic# chassis locator-led on
  Success

  sonic# chassis locator-led on
  Failed 

  ```

- Examples on unsupported platforms:
  ```
  sonic# chassis locator-led on
  Not supported

  ```

**Disable Chassis Locator LED**


- Examples on supported platforms:
  ```
  sonic# chassis locator-led off 
  Success

  sonic# chassis locator-led off 
  Failed 

  ```

- Examples on unsupported platforms:
  ```
  sonic# chassis locator-led off 
  Not supported

  ```

**Show Chassis Locator LED**


- Examples on supported platforms:
  ```
  sonic# show chassis locator-led 
  green 
  
  Note: Chassis Locator LED color is platform specific. The output of color can vary 

  ```

- Examples on non-supported platforms:
  ```
  sonic# show chassis locator-led 
  Not supported


## SONiC image NetInstall

- SONiC image netinstall clears up the Chassis Locator Led operation.  


## SONiC image upgrade 

- SONiC image upgrade clears up the Chassis Locator Led operation.  

## System Reboot
- System reboot clears up the Chassis Locator Led operation.  

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



